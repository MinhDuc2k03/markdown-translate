# Caching Strategies In Laravel
I think of caching as one of those double-edged swords in PHP application development.

On one hand, caching strategies can offer considerable gains in performance. On the other hand, those gains may be at the cost of increased complexity within the codebase or infrastructure. Plus, there is always the threat that caching is providing out-of-date data.

When we think about the risk versus reward when it comes to caching, I recommend making considerate choices to ensure a successful payoff. The right caching choices have everything to do with your infrastructure, your skill set, and what your application does.

To learn more about making successful caching decisions for your application, let’s walk through the wide range of caching layers available in the Laravel ecosystem.



# An overall strategy for caching
Applications are incredibly diverse in the problems they solve, so there is no singular ideal option for every application. Instead, it takes a solid understanding of the application’s business logic and the technologies it uses to decide what is right for each individual application.

I follow a few tenets when thinking about cache implementations/effectiveness.



## First, optimize the code
Caching always introduces cost and risk. The complexity of the code is a cost, and the risk is that of potentially providing incorrect information to users. In many cases, then, the better solution is to optimize the application to reduce resource use or computation time so that we don’t have to rely on caching.

So, for the rest of this article, let’s assume my application is already reasonably optimized.



## Second, choose your caching layer(s)
The thing I love about caching layers is that since no technology or pattern will solve all of my speed issues, I can pick and choose only the layers that are easy to implement and test. I can implement one now and add more later when it makes sense.

Below is a broad and undoubtedly incomplete list of caching layers available to most Laravel applications.


### DNS and Webhost HTML Caching
DNS and webhost level caching are those which happen completely in front of our Laravel applications; usually, the full request is cached based on the request URL and headers. This caching can significantly reduce the cost of rendering page HTML, including any lookups that were required to render that HTML.

This style of cache works well for pages that don’t require customization based on user data or can have that customization added after the fact (for example, via JavaScript). Many services exist that can manage this, and they have a wide variety of behaviors that you can tap into. Some primary examples:

 - [Cloudflare Cache](https://developers.cloudflare.com/cache/)
 - [Amazon Cloudfront](https://docs.aws.amazon.com/cloudfront/index.html)
 - Varnish (available with many server hosts or through a package like [Spatie’s Laravel Varnish](https://github.com/spatie/laravel-varnish))

These services excel at serving static content (similar to CDNs), and also save a lot of server load as they capture the incoming HTTP request and return a response without the request ever making it to my web server(s). Fewer requests to the server mean a lower load and snappier request/response cycles.

I can tune these caches and their interaction with my application to an incredibly fine detail. If you want an example, check out [this post](https://www.troyhunt.com/to-infinity-and-beyond-with-cloudflare-cache-reserve/) where Have I Been Pwned Operator Troy Hunt talks about how they use [Cloudflare Cache Reserve](https://developers.cloudflare.com/cache/advanced-configuration/cache-reserve/).


#### Caveats
 - Caches must be cleared when the content might have changed
 - This can’t be handled at the app/code level; it’ll require you to interact with server/DNS configuration
 - This style of cache does not support routes that need sessions, or that process form submissions
 - You may need to take some extra care fine tuning your HTTP headers in a way most sites usually don’t require



### Laravel HTML Caching
The next level below this is to cache in your Laravel app at the closest-to-the-user level: the HTML. Similar to server and DNS caching, this caches the entire HTML, and allows you to avoid database calls and other server-intensive calls in many cases.

However, this type of cache gives you a lot more control of what is and isn’t cached. For example, I could choose to cache routes based on session or request data. I could cache customized page HTML per user per route, or per provided GET parameters.

With this type of cache I’ll get more control, and have less need to interact with the server, however, the gains won’t be as significant.

The two main packages of this type are [Joseph Silber’s Page Cache](https://github.com/JosephSilber/page-cache) and [Spatie’s ResponseCache](https://github.com/spatie/laravel-responsecache).


#### Caveats
 - All requests will hit the application servers, unless you use the custom Nginx cache in Joseph Silber’s page cache plugin
 - Most of this type of caching tools won’t allow you to use session data in the pages
 - need to make sure to bust the static HTML cache whenever the content of those pages updates



### Laravel’s Built-in Caching
There are a handful of out-of-the-box Laravel caching mechanisms that are super effective and session-friendly with very little added complexity. The Laravel Docs are pretty in-depth, so follow the links, if you’d like to learn more:

 - Environment Configuration Caching
 - Route Caching
 - View Caching



### Laravel Cache
[Laravel’s Cache](https://laravel.com/docs/cache) tooling provides access to various cache services with a consistent API. This caching layer allows you to cache most any value, stored at a location defined by a string key—for example, cache “42” at the key “the-answer-to-life-the-universe-and-everything”. Laravel’s cache also offers many convenience methods for remembering, expiring, busting, locking, and throttling caches.

It’s perfect when I have a specific value that’s expensive to compute, and I know all the circumstances under which it should be busted, a term that means “the cached value is removed, so the application knows to recalculate the cached value.”

Let’s look at an example. If I’ve cached the total count of users in my system, this cache must be busted any time the app adds or deletes a user. Or, say, I’m caching a computation of the day of the past week that had the highest sales revenue; I’ll need to bust that cache at the end of the week at midnight.

One trick I like to use is to build helper objects—just simple plain old PHP objects (POPOs)—that help me manage the creation and retrieval of my key names, and also rules around busting.


#### Caveats
 - Usually very narrow in the scope of what’s being cached; caching large similar bits of data requires additional coding
 - Requires business knowledge of when to set data and bust it


### Memoization
Memoization is a cache that only lasts for the duration of a single request. The values are being cached either at a class level or sometimes at the request level, but they’re not stored on the server; they’re only held in memory for the current request, then discarded. Because of this, memoization is speedy to set up a working concept, and it’s very low risk because the value can’t be used for any other request.


#### Memoization in a PHP class
Memoizing a resource-heavy calculation in a PHP class object is very simple:
```php
class MyPhpClass
{
    protected $memoizedVariable;
 
    public function getResourceHeavyValue()
    {
        if ($this->memoizedVariable !== null) {
            return $this->memoizedVariable;
        }
 
        $this->memoizedVariable = $this->doingSomeHeavyCalculation();
 
        return $this->memoizedVariable;
    }
}
```

The beauty of this style is that it’s just a code pattern. As long as I’m using the same instantiated `MyPhpClass` object, I can call `$object->getResourceHeavyValue()` as many times as I like in this request cycle, and it won’t do a bit of that calculation again.


#### Caveats
 - Only works during the current request cycle for the current object
 - When a change that should affect that object’s calculated output occurs, the object must be re-instantiated OR that value must be unset


#### Memoization in the whole application
This kind of memoization also only functions for the current request cycle BUT at an application-wide scale. [Once](https://github.com/spatie/once) is a solid package for this, written by Taylor Otwell and released by Spatie with his permission.

You can call `once()` and pass it a closure, and whatever happens in that closure, it’ll only be run once in that request regardless of how many times that chunk of code is called.

Regardless of how you implement it, memoization is perfect for a first attempt at caching in an application when I’m not ready to start digging into more intense caching.


### Database Query Caching
Queries to the database are often the biggest offenders when it comes to page load time in web applications. I want to reiterate that it’s important to optimize your queries first, but if you do hit the point where you want to cache your database queries, you’ve got options.

This database-side caching mechanism can exist either in the database software interface (i.e., in front of the `DB` and `Model` facades) or in front of the database.

The most common pattern for caching database queries is to use Laravel’s cache, as we already talked about, to wrap expensive DB or model calls and cache the results.
```php
$states = Cache::remember('states', $seconds = 3600, function () {
    return State::all();
});
```

There are also database query caching services that handle the caching for you directly in front of the database; [PlanetScale Boost](https://planetscale.com/docs/concepts/query-caching-with-planetscale-boost) offers a paid database query cache service, check them out for further details and Laravel-specific implementation details!



## Third, remember to bust your caches
If I introduce caching to my application, I need to make sure that anything cached will have the cache wiped (“busted”) whenever the data it contains has been made stale—or that I set reasonable expiration times on the cache. If we don’t have cache busting, our application’s public presentation will go out of date every time the data in the application changes.

**Here’s a picture of the effects of poor cache busting:**

>Emails will be sent inappropriately. False congratulations will be offered. Users will double-import transactions because a graphic didn’t update after they entered a transaction the first time. Users will pay twice or pay the wrong amount. Everyone will get your application for free.

And the way these bugs will show themselves will be unique to each application, which means searching the Internet for solutions to your bugs will be challenging.

Meanwhile, these sorts of issues will frustrate users far more than a slow loading page.

Once the bug is finally discovered, the customer service team will need help to debug this (often transient and time-dependent) bug.

**Whenever I’m adding caching, I make time to complete all of these tasks:**

1. Add the caching
2. Document the caching layer: how to set it up in local, staging, and production environments, and how it is expected to perform
3. Add cache-busting and/or cache expiration. Add testing asserting the busting happens.

Cache busting is important any time I’m defining a standardized key for storage. Let’s consider a piece of data like `sales-this-week`. If I was tracking a user’s sales this week, I could store this cache value with a key `users.[$user->id].sales-this-week`. That makes it easier to bust just that user, or just their sales this week, or all user-related data.


### One cache-busting solution: Cache eviction
Cache eviction is a cache-driver level mechanism that removes stale or aged key/value pairs. This keeps the overall cache size smaller, resulting in less memory use and snappier cache response times.

One cache naming strategy I love to use is only possible with cache eviction, so I’m a big fan. Basically, if I can tell if a cache should be invalidated (marked as out of date) purely based on the data in an Eloquent model, I can use that model’s `updated_at` value to automatically ignore past versions.

I can leverage cache eviction in this strategy by building my keys to include a timestamp, like `users.[$user->id].[$user->updated_at].sales-this-week`. I’ll make sure that whenever a user sale happens, I `$user->touch()` the `User` responsible for the sale, which will update its `updated_at` timestamp. The next time I try to get that user’s sales, the `sales-this-week` lookup will not find a match, and will calculate and cache the new value for `sales-this-week`.

Since I’m not intentionally destroying the stale key/value pairs for old user sales, I need to make sure that sale records are evicted to keep my cache snappy and my server’s memory free—which is why this particular method basically requires me to use cache eviction if I want it to be effective.

 - [Redis’s Eviction mechanisms](https://redis.io/docs/reference/eviction/)
 - [Memcached’s Eviction mechanisms](https://www.dragonflydb.io/faq/what-happens-when-memcached-is-full)

If I don’t want to configure my cache driver for cache eviction, in Laravel’s scheduler I could run `php artisan cache:clear` for the driver or tags I’d like to clear at a reasonable frequency.

*Thanks [Tony](https://github.com/tonysm) for reminding me that cache eviction policies exist.*



## Separate permanent and churnable data caches
Requirements of data lifecycles may encourage me to use different cache mechanisms.

Consider an application that uses an external service to get the GPS coordinates of addresses entered into the system. When an address location can’t be mapped, the application caches that address string into an `ignore-list`. It won’t execute an API call for that address to that external service again.

A more efficient solution is caching the valid data returned from that GPS service. It’s rare for an address to change its GPS location. Next time I try to look up that same address, I can use my local cached value! That way, I can save both the time of the HTTP request and the cost of utilizing that external service.

In both examples above, I don’t want this data destroyed when I type `php artisan cache:clear`. This indicates that I want a more permanent data cache for these key/value pairs. It might be appropriate to use a different cache driver that I never clear, or a persistent cache on a separate server. If this data’s lifecycle might exceed the length of this application server’s lifecycle, storing that data in a permanent data store like the application’s database could make the most sense.



# Cacheclusion
I hope this has provided an initial summary of the breadth and best use cases for various caching mechanisms in Laravel application development. I recommend caching in layers of your application where it offers a significant performance boost and where the complexity of implementation is reasonable.

In a future post, I’ll share some coding patterns that make using Memoization and `Cache` more friendly to use.