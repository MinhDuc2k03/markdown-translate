# Unorthodox Eloquent

Eloquent is a razor-sharp tool that is adored by many. It allows you to carry out database operations with ease while maintaining an easy-to-use API. Implementing the Active Record (AR) pattern, as decribed by Fowler in PoEAA, it is one of the best AR implementations that is available today.

In this blog post, I'd like to go over a few tips and tricks I have learned along the way while experimenting with different options. For example, have you ever considered sharing your eager loads one way or another? No? Well, then I'm pretty sure you'll learn at least a thing or two so make sure you stick around until the end!

Like every tool in existence, Eloquent comes with its own set of trade-offs. As responsible developers, we should always be aware of the things we are trading off against. If you'd like to learn more about AR and its design philosophy, I highly recommend this [***article by Shawn McCool***](https://shawnmc.cool/2023-02-13_active-record-how-we-got-persistence-perfectly-wrong).



## Tappable scopes
Traditionally, reusable query scopes have always been defined within the target model itself using the magical `scopeXXX` syntax, macros or a dedicated `Builder` class. The problem with the first two approaches is that they both rely on opaque runtime magic making it (almost) impossible to receive IDE assistance. Even worse, naming clashes can occur in the case of macro registrations. However, there is a fourth—and in my opinion superior—approach: tappable scopes. Tappable scopes is one of those hidden gems that is extremely valuable, but at the same time completely unknown to the masses because [**it is not documented anywhere**](https://laravel.com/docs/10.x/eloquent#query-scopes).


### Explained by example
Let's take the following Controller method as an example:

```php
public function index(): Collection
{
    return Company::query()
        ->oldest()
        ->whereNull('user_id')
        ->whereNull('verified_at')
        ->get();
}
```

We can see that it applies some conditions to the `Builder` instance, and returns the result as-is without any transformation. While this is a perfectly valid way to write a query, it leaks internals and the where clauses do not tell us anything about the domain language. Perhaps the requirement was: "Create an endpoint that returns the oldest orphaned & unverified companies". Orphaned in this case means that a company was abandoned during registration and it is the notion that our domain experts use. Thus we can do much better:

```php
public function index(): Collection
{
    return Company::query()
        ->oldest()
        ->tap(new Orphan())
        ->tap(new Unverified())
        ->get();
}
```

This almost reads like the requirement itself, right? Now, if we quickly take a gander at one of these tappable scopes:

```php
final readonly class Orphan
{
    public function __invoke(Builder $builder): void
    {
        $builder->whereNull('user_id');
    }
}
```

That's it! This simplicity allows us to compose queries in any way, shape or form we'd like and not be restricted to using a particular trait or something that pollutes our Eloquent models.

Now imagine that a new requirement comes in that demands a brand new endpoint that should list orphaned members that belong to a certain company. At this point we might start sweating because there is no commonality between a `Company` and a `Member`, but don't fret! Tappable scopes to the rescue! Let's just reuse the scope and call it a day:

```php
public function index(Request $request): Collection
{
    return Member::query()
        ->whereBelongsTo($request->company)
        ->tap(new Orphan())
        ->get();
}
```

**This** is the power of tappable scopes. I think it should also be mentioned that this example requires both models to possess the concept of being "orphaned" , which is the state of abandonment after an initiated registration process, as signalled by a nullable `user_id` column. The registration is complete once a user is linked to all models. Needless to say, you can't just go and use any scope with any model. It must be supported by the backing database table.


### Specification pattern note
Have you ever heard about the [Specification pattern](https://www.martinfowler.com/apsupp/spec.pdf) and tried applying it [dogmatically](https://laracasts.com/series/design-patterns-in-php/episodes/6)? As you may know, dogma is the root of all evil. This way of applying query constraints offers the best of many worlds.


### Are you a package author?
Tappable scopes are especially useful for package authors who'd like to share reusable scopes alongside their packages. Let's take [laravel-adjacency-list](https://github.com/staudenmeir/laravel-adjacency-list) as an example. [`scopeIsRoot`](https://github.com/staudenmeir/laravel-adjacency-list/blob/master/src/Eloquent/Traits/HasRecursiveRelationshipScopes.php#L94) could be refactored as follows:

```php
final readonly class IsRoot
{
    public function __invoke(Builder $builder): void
    {
        $builder->whereNull('parent_id');
    }
}
```

This approach also solves the [issue of clashing method and scope names](https://github.com/staudenmeir/laravel-adjacency-list/pull/84#issuecomment-1037416147), thanks to simply avoiding the ancient magics that are still available in the framework from the early days. All in all, the utilization of tappable scopes yields a net positive for 90% of use cases out there.



## Not-so-global global scopes
I know that the title doesn't make a lot of sense when read out of context, but please bear with me. Every now and then a couple of [posts regarding global scopes appear](https://twitter.com/stef_r/status/1627692788673392641) on my X (formerly Twitter) timeline. The general sentiment is always along the lines of "[global scopes are bad, local scopes are good](https://twitter.com/djgeisi/status/1648643569027100672)". The reason for this is that the [Laravel documentation on global scopes](https://laravel.com/docs/10.x/eloquent#global-scopes) does them a huge disfavor by making it look like the documented way of applying global scopes is the only way, but nothing could be further from the truth.

A while ago, I was thinking about this common complaint and then it struck me: what happens if you flout this convention? I took a gander at the global scopes API and quickly realized that it was in fact not a requirement to declare the scopes within your Eloquent models' `booted` lifecycle method. As a matter of fact, there are no restrictions at all! They can be applied in a `ServiceProvider`, `Middleware`, `Job` etc. the possibilities are endless. The best use, however, is—in my opinion—when combined with middleware. So let's take a look at an example.


### Explained by example: country restriction
Imagine that you are working on an application like [IMDb](https://imdb.com/) that has a front-facing public website and an internal administration panel. One of the requirements might be that certain movies should only be shown to users if the user's country is available in a certain whitelist, otherwise it should be as if the movie doesn't exist at all. Long story short, you have to partition the data based on the origin country. However, this restriction should only be applied to the public website, **not** the internal administration panel. An effortless way to implement this requirement is by leveraging *not-so-global global scopes*.

First, create your global scope like you always would:

```php
final readonly class CountryRestrictionScope implements Scope
{
    public function __construct(private Country $country) {}

    public function apply(Builder $builder, Model $model): void
    {
        // pseudocode: do the actual country-based filtering here
        $builder->isAvailableIn($this->country);
    }
}
```

Next, create an HTTP middleware whose responsibility is going to be applying the scope to the relevant models:

```php
final readonly class RestrictByCountry
{
    public const NAME = 'country.restrict';

    public function __construct(private Repository $geo) {}

    public function handle(Request $request, Closure $next): mixed
    {
        $scope = new CountryRestrictionScope($this->geo->get());

        Movie::addGlobalScope($scope);
        Rating::addGlobalScope($scope);
        Review::addGlobalScope($scope);

        return $next($request);
    }
}
```

Note: *`Repository`* in this example can be anything that returns the user's country, like [laravel-geo](https://github.com/dive-be/laravel-geo).

Finally, open up your `web.php` routes file and apply the middleware to the relevant group:

```php
$router->group([
    'middleware' => ['web', RestrictByCountry::NAME],
], static function ($router) {
    $router->resource('movies', Site\MovieController::class);
    $router->resource('ratings', Site\RatingController::class);
    $router->resource('reviews', Site\ReviewController::class);
	
    // Front-facing public website routes...
});

$router->group([
    'middleware' => ['web', 'auth'],
    'prefix' => 'admin',
], static function ($router) {
    $router->resource('movies', Admin\MovieController::class);
    $router->resource('ratings', Admin\RatingController::class);
    $router->resource('reviews', Admin\ReviewController::class);
	
    // Admin routes...
});
```

Pay close attention to the fact that the middleware only gets applied to the public website routes. This has the following implications:

 - Whenever a user visits any of the public website routes, the content will be automatically filtered based on country. This might result in harmless 404 pages.
 - Whenever new routes need to be added due to new feature requests, the developers do not have to remember the fact that each model should be filtered based on the user's country. This has been dealt with already and there's no way this restriction can be bypassed unless done deliberately.
 - Whenever developers use a REPL like tinker, they won't be caught off-guard because a hidden, nasty global scope was altering the queries. Remember, our global scopes are not-so-global.
 - Whenever an administrator visits the internal administration panel, they are always going to see all content regardless of their origin. This is exactly what we want.

To put it succinctly, if we embrace the global nature of global scopes while thinking about the precise placement and its area of effect, we can actually create joyful development experiences while eliminating potential frustrations in the future. It doesn't have to be infuriating!



## Phantom properties
In a fairly recent project I worked on, I had to display a huge amount of markers on an interactive map like [Google Maps](https://developers.google.com/maps), [Leaflet](https://leafletjs.com/) or [Mapbox](https://www.mapbox.com/developers). These interactive maps accept a list of geometry types according to the [GeoJSON spec](https://geojson.org/). A `Point` type, which is exactly what I needed, must provide a `coordinates` property with a tuple as its value representing **(lat,lon)** respectively. The problem here is that `coordinates` represents a composite value, whereas the data is flattened as `addresses:id,latitude,longitude` in the database. The table was designed that way because of the chosen administration panel: [Laravel Nova](https://nova.laravel.com/). It's much easier to handle record creations in [Nova](https://nova.laravel.com/) if you keep the structure as flat as possible. I could have simply dealt with this problem in an [Eloquent Resource](https://laravel.com/docs/10.x/eloquent-resources) (aka a transformer), but the curious programmer in me told me that there ought to be a better way. The inner me was definitely right: there is a better way thanks to—what I call—*Phantom properties*.


### Explained by example: `Coordinates`
To solve the problem at hand, we first need to create the `ValueObject` that will represent address `Coordinates`:

```php
final readonly class Coordinates implements JsonSerializable
{
    private const LATITUDE_MIN  = -90;
    private const LATITUDE_MAX = 90;
    private const LONGITUDE_MIN = -180;
    private const LONGITUDE_MAX = 180;

    public float $latitude;

    public float $longitude;

    public function __construct(float $latitude, float $longitude)
    {
        Assert::greaterThanEq($latitude, self::LATITUDE_MIN);
        Assert::lessThanEq($latitude, self::LATITUDE_MAX);
        Assert::greaterThanEq($longitude, self::LONGITUDE_MIN);
        Assert::lessThanEq($longitude, self::LONGITUDE_MAX);

        $this->latitude  = $latitude;
        $this->longitude = $longitude;
    }

    public function jsonSerialize(): array
    {
        return [$this->latitude, $this->longitude];
    }

    public function __toString(): string
    {
        return "({$this->latitude},{$this->longitude})";
    }
}
```

Next, we should define our `AsCoordinates` object cast:

```php
final readonly class AsCoordinates implements CastsAttributes
{
    public function get(
        $model, 
        string $key,
        $value, 
        array $attributes,
    ): Coordinates {
        return new Coordinates(
            (float) $attributes['latitude'], 
            (float) $attributes['longitude'],
        );
    }

    public function set(
        $model, 
        string $key,
        $value, 
        array $attributes,
    ): array {
        return $value instanceof Coordinates ? [
            'latitude' => $value->latitude,
            'longitude' => $value->longitude,
        ] : throw new InvalidArgumentException('Invalid value.');
    }
}
```

Finally, we should assign it as a cast in our `Address` model:

```php
final class Address extends Model
{
    protected $casts = [
        'coordinates' => AsCoordinates::class,
    ];
}
```

Now, we can just simply use it in our Eloquent Resource:

```php
/** @mixin \App\Models\Address */
final class FeatureResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'geometry' => [
                'type' => 'Point',
                'coordinates' => $this->coordinates,
            ],
            'properties' => $this->only('name', 'phone'),
            'type' => 'Feature',
        ];
    }
}
```

 - `Coordinates` is now fully responsible for the concept of `Coordinates` (representing a two-dimensional point on earth).
 - We don't have to call `jsonSerialize()` by ourselves because of the implemented interface. It'll be taken care of for us due to Laravel calling `json_encode` somewhere in the call stack.
 - If something were to change about `Coordinates`, it'll be trivial to find where the concept of `Coordinates` is being used.

This is what we ended up with as expected:

```json
{
    "geometry": {
        "type": "Point",
        "coordinates": [4.5, 51.5]
    },
    "properties": {
        "name": "Acme Ltd.",
        "phone": "123 456 789 0"
    },
    "type": "Feature"
}
```


### Explained by another example: rendering address lines
Another handy way of using phantom properties is to help you with template rendering. Normally, if you wanted to render the address as HTML, you'd have to do something like this:

```php
<address>
  <span>{{ $address->line_one }}</span>
  @if($two = $address->line_two)<span>{{ $two }}</span>@endif
  @if($three = $address->line_three)<span>{{ $three }}</span>@endif
</address>
```

As you can see, it can get out of hand really quickly. While I do recognize this is a rather contrived example, as addresses are generally rendered differently based on country, it helps with painting the picture that it can become a mess rather fast. What if we could do something like this:

```php
<address>
  @foreach($address->lines as $line)
  <span>{{ $line }}</span>
  @endforeach
</address>
```

Much nicer, right? Our template is no longer concerned with how complex it can suddenly become due to different rules in different nations. It does what it does best: rendering. The phantom property responsible for this could look as follows:

```php
final readonly class AsLines implements CastsAttributes
{
    public function get(
        $model, 
        string $key,
        $value, 
        array $attributes,
    ): array {
        return array_filter([
            $attributes['line_one'],
            $attributes['line_two'],
            $attributes['line_three'],
        ]);
    }

    public function set(
        $model, 
        string $key,
        $value, 
        array $attributes,
    ): never {
        throw new RuntimeException('Set the lines explicitly.');
    }
}
```

If we had to swap `line_two` and `line_three` for Antarctica for example, we can do the adjustment in `AsLines` and won't have to adjust the blade template at all. Thinking out-of-the-box can greatly simplify how we can render UIs and prevent us from creating overly smart ones which is generally frowned upon and considered an anti-pattern.



## Fluent query objects
I already mentioned custom `Builder` classes in the first section [tappable scopes](https://muhammedsari.me/unorthodox-eloquent#tappable-scopes). While they're a first good step towards more readable and maintainable queries, I think that they quickly fall apart when a lot of custom constraints need to be added to the custom `Builder` classes. They just tend to become another type of [God object](https://en.wikipedia.org/wiki/God_object). It's also a lot of hassle to get the IDE to the point where it starts helping you with suggestions. You could also create a dedicated `Repository` for your models, but I dislike that option heavily. You see, to my eye, a `Repository` and `Eloquent` are mutually exclusive.—Before you take out the pitchforks, I know that it's not strictly true. However, if you know why `ActiveRecord` exists and why a `Repository` exists, then you'll understand where I'm coming from.—You can read more on that [here](https://muhammedsari.me/repositories-and-their-true-purpose).

Another alternative is the utilization of what is known as a `QueryObject`. It is an object that's responsible for the composition and execution of a single query kind. While this doesn't fully conform to the [definition of Martin Fowler in PoEAA](https://www.martinfowler.com/eaaCatalog/queryObject.html), it is close enough and I think that borrowing the notion for this particular purpose is fine. If you have a Laracasts subscription, you might have already seen the [lesson that's available on this subject](https://laracasts.com/series/whip-monstrous-code-into-shape/episodes/17). Even though the philosophy and the way of thinking are identical, I'd like to present an alternate API that's much, much nicer to use: fluent query objects.


### Explained by example: notification center
Imagine that we have an SPA, powered by an HTTP JSON API, that has a notification bell at the top. The back-end exposes an endpoint that we can use to retrieve unread notifications of the logged in user. The `Controller` method responsible for retrieving unread notifications might look as follows:

```php
public function index(Request $request): AnonymousResourceCollection
{
    $notifications = Notification::query()
        ->whereBelongsTo($request->user())
        ->latest()
        ->whereNull('read_at')
        ->get();

    return NotificationResource::collection($notifications);
}
```

This was all straightforward until a new feature request came in that required us to create a dedicated page to manage all of the notifications: read, unread, type of notification etc. To make the lives of our front-end developers easier, we decided to create a dedicated endpoint per view type. One of them, which is responsible for retrieving read notifications, might look as follows:

```php
public function index(Request $request): AnonymousResourceCollection
{
    $notifications = Notification::with('notifiable')
        ->whereBelongsTo($request->user())
        ->latest()
        ->whereNotNull('read_at')
        ->get();

    return NotificationResource::collection($notifications);
}
```

Eagle-eyed readers may already have noticed that everything in this snippet is the same as the previous one except the `whereNotNull` clause and the eager loading of the `notifiable` relation. Now we have to repeat this process for the other types as well:

```php
public function index(Request $request): AnonymousResourceCollection
{
    $notifications = Notification::query()
        ->whereBelongsTo($request->user())
        ->latest()
        ->where('data->type', '=', $request->type)
        ->get();

    return NotificationResource::collection($notifications);
}
```

I think you get the gist of it. This is too much repetition, and something must be done about it. Enter **fluent query objects**. First, we're going to create the query class that will be responsible for "getting my notifications":

```php
final readonly class GetMyNotifications
{
}
```

Next, we're going to move the base query (the conditions that'll need to be applied at all times) to the **`constructor`** of our brand new, shiny object:

```php
final readonly class GetMyNotifications
{
    private Builder $builder;

    private function __construct(User $user)
    {
        $this->builder = Notification::query()
            ->whereBelongsTo($user)
            ->latest();
    }

    public static function query(User $user): self
    {
        return new self($user);
    }
}
```

Now, we need to tap into the prowess of composition by utilizing the [`ForwardsCalls`](https://github.com/laravel/framework/blob/10.x/src/Illuminate/Support/Traits/ForwardsCalls.php) trait:

```php
/** @mixin \Illuminate\Database\Eloquent\Builder */
final readonly class GetMyNotifications
{
    use ForwardsCalls;

    // omitted for brevity

    public function __call(string $name, array $arguments): mixed
    {
        return $this->forwardDecoratedCallTo(
            $this->builder, 
            $name, 
            $arguments,
        );
    }
}
```

Observations:
 - `ForwardsCalls` allows us to treat the class as if it's a part of the "base class" `\Illuminate\Database\Eloquent\Builder` even though there is no inheritance in place. I love composition.
 - The `@mixin` annotation will help the IDE to provide us with useful autocompletion suggestions.
 - You could also choose to add `Conditionable` to have an even more fluent API, but it's not really necessary here due to our design choice (a distinct endpoint per view type).

The only things remaining now are the custom query constraints, so let's add them:

```php
/** @mixin \Illuminate\Database\Eloquent\Builder */
final readonly class GetMyNotifications
{
    // omitted for brevity
	
    public function ofType(NotificationType ...$types): self
    {
        return $this->whereIn('data->type', $types);
    }

    public function read(): self
    {
        return $this->whereNotNull('read_at');
    }

    public function unread(): self
    {
        return $this->whereNull('read_at');
    }
	
    // omitted for brevity
}
```

Awesome. We now have everything that's needed to properly implement the feature "get my notifications" aka the notification center. So with everything stitched together:

```php
/** @mixin \Illuminate\Database\Eloquent\Builder */
final readonly class GetMyNotifications
{
    use ForwardsCalls;

    private Builder $builder;

    private function __construct(User $user)
    {
        $this->builder = Notification::query()
            ->whereBelongsTo($user)
            ->latest();
    }

    public static function query(User $user): self
    {
        return new self($user);
    }

    public function ofType(NotificationType ...$types): self
    {
        return $this->whereIn('data->type', $types);
    }

    public function read(): self
    {
        return $this->whereNotNull('read_at');
    }

    public function unread(): self
    {
        return $this->whereNull('read_at');
    }

    public function __call(string $name, array $arguments): mixed
    {
        return $this->forwardDecoratedCallTo(
            $this->builder, 
            $name, 
            $arguments,
        );
    }
}
```

Let's refactor one of the previous **`Controller`** s and see how it looks:

```php
public function index(Request $request): AnonymousResourceCollection
{
    $notifications = GetMyNotifications::query($request->user())
        ->read()
        ->with('notifiable')
        ->get();

    return NotificationResource::collection($notifications);
}
```

I don't know about you, but this piece of elegant code is just beautiful to look at. You can keep using all of the regular [`Illuminate\Database\Eloquent\Builder`](https://laravel.com/api/10.x/Illuminate/Database/Eloquent/Builder.html) methods while also having the ability to call enhanced, specific methods dedicated to this distinct query alone. Takeaways:
 - Key concepts are encapsulated behind meaningful interfaces.
 - Adheres to [SRP](https://en.wikipedia.org/wiki/Single-responsibility_principle)
 - Groks the framework and its tools instead of fighting against it like using a `Repository`
 - Easily reusable in multiple places
 - Easily testable


### Note on using a `Pipeline`
There are [plenty of tutorials on YouTube](https://www.youtube.com/watch?v=9E5IevdHPwA) that show how we could be using a `Pipeline` to logically split a chain of operations, or do some complex filtering. Some readers might think that it's a waste of time to deal with your own `QueryObject`. The thing is though, I don't believe that a `Pipeline` and a `QueryObject` are mutually exclusive. They can be complementary to eachother and help one another to complete the task more efficiently. Instead of type-hinting `Builder` in the pipes, we can type-hint our custom `QueryObject`s. Essentially building our own [laravel-query-builder](https://github.com/spatie/laravel-query-builder), but with a more specific API.

The `Pipeline` could look as follows:

```php
$orders = Pipeline::send(
    GetMyOrders::query($request->user())
)->through([
    Filter\Cancelled::class,
    Filter\Delayed::class,
    Filter\Shipped::class,
])->thenReturn()->get();
```

A `Pipe` could look as follows:

```php
final readonly class Cancelled
{
    public function __construct(private Request $request) {}

    public function handle(GetMyOrders $query, Closure $next): mixed
    {
        if ($this->request->boolean('cancelled')) {
            $query->cancelled();
        }

        return $next($query);
    }
}
```

There's nothing wrong with amalgamating different concepts to reach your end goal. Just make sure it makes sense for your current context and that you're not introducing accidental complexity.



## Sharing eager loads
This is a concise, but indispensable one (for me, at least). At some point you probably found yourself wondering how you could be sharing eager loads, especially those that do some additional refining, but nevertheless still ended up just copy-pasting the relevant code. While copy-paste is a perfectly valid option to choose, there are in fact better ways to solve this issue. It can quickly become cumbersome to repeat these kinds of eager loads because of the application of additional query constraints. This could be the case when using Spatie's phantasmagorical package [laravel-medialibrary](https://github.com/spatie/laravel-medialibrary), for example.

Imagine that you have 10 different models. Each model defines multiple, distinct `MediaCollections` and each model also defines a `thumbnail` for display purposes on the storefront. For various reasons, `Controller` code etc. cannot be shared (you shouldn't anyway). The package works with one big `media` relation that loads in all attached `Media` objects and uses `Collection` magic in the background in order to partition them. Eager loading the entire `media` relation can quickly become a problem on an index page that lists a model with tons of `MediaCollections`. After all, the only thing we need on an index page is the model's `thumbnail`. In order to solve this problem, we can apply a query constraint like so:

```php
public function index(): View
{
    $products = Product::with([
        'categories',
        'media' => static function (MorphMany $query) {
            $query->where('collection_name', 'thumbnail')
        },
        'variant.media' => static function (MorphMany $query) {
            $query->where('collection_name', 'thumbnail')
        },
    ])->tap(new Available())->get();

    return $this->view->make('products.index', compact('products'));
}
```

While this does solve the problem of overfetching, it is not pretty to look at. Now repeat this 9 more times. Yuck! Actually it is very, very straightforward to properly tackle this problem. First, think about what you want to eager load. Got it? `LoadThumbnail`. Then, create a class that represents this constraint:

```php
final readonly class LoadThumbnail implements Arrayable
{
    public function __invoke(MorphMany $query): void
    {
        $query->where('collection_name', 'thumbnail');
    }

    public function toArray(): array
    {
        return ['media' => $this];
    }
}
```

Now simply use it:

```php
public function index(): View
{
    $products = Product::with([
        'categories',
        'media' => new LoadThumbnail(),
        'variant.media' => new LoadThumbnail(),
    ])->tap(new Available())->get();

    return $this->view->make('products.index', compact('products'));
}
```

Amazing, right? You may have also noticed the `toArray` at the bottom. That would come in handy if you'd like to define eager loads 1 relation at a time with consecutive `with((new LoadThumbnail)->toArray())` calls. This technique is so simple to execute it's just unfair almost. Please don't overfetch and instead make sure minimal data is returned over the wire from the database. Laziness is no excuse!



## Invokable accessors
We've already talked about techniques like [Phantom properties](https://muhammedsari.me/unorthodox-eloquent#phantom-properties). If you haven't read that section yet, please read it first and come back to this one. Anyway, the biggest flaw with [Phantom properties](https://muhammedsari.me/unorthodox-eloquent#phantom-properties) is that it requires us to define a `set` (inbound) cast even if we won't be using it, like the `$address->lines` example; and also that it has no mechanism that automatically memoizes the computed results. It's a bummer that there's no `CastsOutboundAttributes`, but that's where invokeable accessors shine. The main benefits are:
 - Memoization
 - No model clutter
 - Testable units
 - Composable, like any other object

An example definition (`Attribute::get` is real, by the way):

```php
final class File extends Model
{
    protected function stream(): Attribute
    {
        return Attribute::get(new StreamableUrl($this));
    }
}
```

That's all it takes to define an invokeable accessor. Take note of the constructor argument, because that's a repeating pattern with invokeable accessors. It's necessary to gain access to the model that's being used, otherwise we won't be able to gather contextual information necessary to carry out our tasks. In this example, `StreamableUrl` is responsible for—you guessed it—generating streamable URLs. We could have inlined the logic and used the [classic `Closure` way](https://laravel.com/docs/10.x/eloquent-mutators#defining-an-accessor), but that would start filling up our model rather quickly. The actual model this snippet is coming from, has fourteen other accessors (!). A sneak peek from this particular invokeable accessor:

```php
final readonly class StreamableUrl
{
    private const S3_PROTOCOL = 's3://';

    public function __construct(private File $file) {}

    public function __invoke(): string
    {
        $basePath = UuidPathGenerator::getInstance()
            ->getPath($this->file);

        if ($this->file->supports('s3')) {
            return self::S3_PROTOCOL 
                . $this->file->bucket 
                . DIRECTORY_SEPARATOR 
                . $basePath 
                . $this->file->basename;
        }

        return Storage::disk($this->file->disk)
            ->url($basePath . rawurlencode($this->file->basename));
    }
}
```

The exact details are not that important, but the takeaway is that it properly encapsulates the logic for generating optimized streamable URLs. Returning direct `s3://` paths is much more efficient for streaming files from [S3](https://aws.amazon.com/s3/).

The main point is, imagine if we had defined this code inside a traditional `Closure` within the accessor on the model itself, and also done something similar for the other thirteen accessors. Our model would have very quickly become overstuffed. Using invokable accessors also allows us to extract out such code and keep our models clean and tidy.



## Multiple read models for the same table
This is one of those rare moments where I actually appreciated the limitations of [Laravel Nova](https://muhammedsari.me/nova.laravel.com), which is normally notoriously difficult to customize compared to other administration panel solutions. It allowed me to discover a novel use case for read-only models.

Recently, a feature request came in that required us to create a fully-fledged file explorer in order to share files with third parties and customers. Rolling out our own file management solution was out of the question because 1. it is a solved problem and 2. it is a complex and edgecase-ridden problem. We decided to go with [laravel-medialibrary](https://github.com/spatie/laravel-medialibrary) (as any other sane person would, thanks Spatie!), but there was a huge hurdle to overcome. We had to create a UX-friendly interface in Nova under the `Directory` resource, which would be housing the `File`s that belonged to that particular `Directory` and it had to be sortable. While the default `Media` model did its job well, it was incompatible with Nova's most popular sorting library (also from Spatie). We had to come up with an original solution. That's when it suddenly struck me to create a read-only model for the `media` table and put the theory to the test:

```php
final class File extends Model implements Sortable
{
    use SortableTrait;

    public array $sortable = [
        'order_column_name' => 'order_column';
    ];

    protected $table = 'media';

    public function buildSortQuery(): Builder
    {
        return $this->newQuery()->where($this->only('model_id'));
    }
}
```

While this was already looking promising, there was another hurdle that had to be overcome. This model could be used to query **anything** in the `media` table which could have resulted in unexpected data losses. This was not acceptable of course, because this model specifically represents a `File` which is actually a `Media` object that fulfills two criteria:

 - It must belong to the model `Directory`
 - The `collection_name` must be `file`

I decided to create a true global scope and register it in a `ServiceProvider` to enforce these rules at all times (another rare moment where a truly global scope actually makes sense):

```php
final class FileScope implements Scope
{
    /** @param File $model */
    public function apply(Builder $builder, Model $model): void
    {
        $builder
            ->where($model->qualifyColumn('model_type'), 'directory')
            ->where($model->qualifyColumn('collection_name'), 'file');
    }
}
```

Models that did not fit these criteria were no longer being returned and instead started throwing `ModelNotFoundException`s. This was exactly what we wanted. Perfect, but we couldn't declare victory just yet. The Nova interface required a bunch of information which were simply not possible to extract from the default `Media` model. But then it struck me again: since this was our custom model, I could do whatever I wanted! I could even declare it as a relation now in the `Directory` model:

```php
public function files(): HasMany
{
    return $this->hasMany(File::class, 'model_id')->ordered();
}
```

Did you notice something "weird"? No? Take a look at the relationship type. If you know how `MediaLibrary` works, you'd know that the `media` table actually makes use of a `MorphMany` relationship. But since we defined a global `FileScope` that always refines the queries on `model_type`, we can simply use the `HasMany` relation type by itself and everything just works. This is when my mind was blown. Calling `$directory->files` would now return a Collection of `File` objects and not `Media` objects. Long story short, `File` now possessed everything that was needed to serve a `FileSharing` context. We didn't need to alter any configuration or something else—nothing. Just some cleverness and novel approaches. The end result was pure excellence.

e.g. I could also add a bunch of [(invokeable) accessors](https://muhammedsari.me/unorthodox-eloquent#invokeable-accessors) in order to fulfill the UI needs:

```php
// other accessors ommitted, there's simply too many

protected function realpath(): Attribute
{
    return Attribute::get(new Realpath($this));
}

protected function stream(): Attribute
{
    return Attribute::get(new StreamableUrl($this));
}

protected function extension(): Attribute
{
    return Attribute::get(fn () => $this->type->extension());
}

protected function type(): Attribute
{
    return Attribute::get(fn () => FileType::from($this->mime));
}
```


### Takeways
 - You should use a read-only model when things get complex on the UI side.
 - Global scopes are not always bad.
 - These models allow for fine-tuning according to the use cases that they need to support.
 - This approach can also be used if a package does not allow you to override the "base model" that it uses. Simply create your own model that references the package's table and start solving problems.



## `WithoutRelations` for queue performance
Last but not least, the topic I'd like to talk about is the mysterious `WithoutRelations` attribute or the `withoutRelations` method. Avid and eagle-eyed source divers of Laravel packages may have already noticed some usage while browsing through the source code. In fact, it is indeed used in a [Livewire component by Laravel Jetstream](https://github.com/laravel/jetstream/blame/4.x/src/Http/Livewire/UpdateProfileInformationForm.php#L46). Though the reason why it's being used here is to prevent too much information leaking to the client-side, which is—even though completely valid—not the use case I'd like to talk about.

As you may already know, you should be using the `SerializesModels` trait if you'd like to enqueue a `Job` that houses Eloquent models. (Its purpose is briefly described [in the documentation](https://laravel.com/docs/10.x/queues#class-structure), so I'm not going to repeat that.) But there is a catch that a lot of developers are not aware of: `SerializesModels` also remembers which relationships were loaded at serialization-time and uses that information to reload all relationships when the models are deserialized. An example payload:

```json
{
    "user": {
        "class": "App\\Models\\User",
        "id": 10269,
        "relations": ['company', 'orders', 'likes'],
        "connection": "mysql",
        "collectionClass": null
    }
}
```

As you can see, the `relations` property contains three relationships. These will be eagerly loaded upon the deserialization of this `Job`. A relationship like `likes` and `orders` can potentially pull in hundreds or even thousands of records, hurting the `Job`s performance tremendously. Even worse, the `Job` from which I grabbed this snapshot didn't even need any of these relations to carry out its main task.


### Method option
An easy way to fix this problem is by—you guessed it—using the **`withoutRelations`** method before passing along your Eloquent models to the `Job`s constructor. An example:

```php
final class AccessIdentitySubscriber
{
    public function subscribe(Dispatcher $events): void
    {
        $events->listen(
            Registered::class, 
            $this->whenRegistered(...),
        );
    }

    private function whenRegistered(Registered $event): void
    {
        CreateProspect::dispatch($event->user->withoutRelations());
    }
}
```

This event subscriber is responsible for creating a new prospect in a disparate CRM system whenever a new user is registered in our application. Before dispatching `CreateProspect`, `withoutRelations` is called in order to make sure no useless relationships are serialized beyond this point, ensuring optimal performance. If we now inspect the serialized payload, we can see that the array has been emptied:

```json
{
    "user": {
        "class": "App\\Models\\User",
        "id": 10269,
        "relations": [],
        "connection": "mysql",
        "collectionClass": null
    }
}
```

Perfect.


### Attribute option
While preparing this blog post, I realized that a fellow Laravel developer [`contributed a brand new #[WithoutRelations] attribute`](https://github.com/laravel/framework/pull/48068) that automatically takes care of stripping away all model relations upon `Job` serialization:

```php
#[WithoutRelations]
final class CreateProspect implements ShouldQueue
{
    use Dispatchable;
    use InteractsWithQueue;
    use SerializesModels;

    public function __construct(private User $user) {}

    public function handle(Gateway $crm): void
    {
        // omitted for brevity
    }
}
```

This will definitely be my new default way of defining `Job`s. I also don't know about you, but I have had **zero** use cases where have I said to myself "Darn it, I should have left the relations alone". This behavior introduces more hidden bugs than anything (in my experience). Most of the time, [lazy loading](https://laravel.com/docs/10.x/eloquent-relationships#relationship-methods-vs-dynamic-properties) does the job just fine. Remember, there are no bad tools. There are only bad tools within a particular context. That's why I'm not a huge fan of the newish [`Model::preventLazyLoading`](https://laravel.com/docs/10.x/eloquent-relationships#preventing-lazy-loading) feature. Sorry, [namesake](https://twitter.com/taylorotwell/status/1395087054254526474).



## Wrap‑up
At this point my fingers are numb, but I think it was worth it. Curiosity makes you a better programmer, so get out of the tutorial hell and start experimenting. Trust me, the worst thing that can happen is you learning. And please... don't forget to read up on the [trade‑offs of Active Record](https://shawnmc.cool/2023-02-13_active-record-how-we-got-persistence-perfectly-wrong). The worst thing that can happen is—again—you learning.

[Join the discussion on X (formerly Twitter)](https://twitter.com/mabdullahsari/status/1702289877520261180)! I'd love to know what you thought about this blog post.

Thanks for reading!