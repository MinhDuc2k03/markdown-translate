# Eloquent không chính thống

Eloquent là một công cụ sắc bén được nhiều người yêu thích. Nó cho phép bạn thực hiện các hoạt động cơ sở dữ liệu một cách dễ dàng trong khi vẫn duy trì API dễ sử dụng. Việc triển khai mẫu Active Record (AR), như Fowler đã mô tả trong PoEAA, đây là một trong những cách triển khai AR tốt nhất hiện nay.

Trong bài đăng blog này, tôi muốn điểm qua một số mẹo và thủ thuật mà tôi đã học được trong quá trình thử nghiệm các tùy chọn khác nhau. Ví dụ, bạn đã bao giờ cân nhắc việc chia sẻ sự háo hức của mình bằng cách này hay cách khác chưa? KHÔNG? Vậy thì tôi khá chắc chắn rằng bạn sẽ học được ít nhất một hoặc hai điều, vì vậy hãy đảm bảo bạn kiên trì cho đến hết!

Giống như mọi công cụ hiện có, Eloquent đều có những sự đánh đổi riêng. Là nhà phát triển có trách nhiệm, chúng ta phải luôn nhận thức được những thứ mà chúng ta đang phải đánh đổi. Nếu bạn muốn tìm hiểu thêm về AR và triết lý thiết kế của nó, tôi thực sự khuyên bạn nên xem [***bài viết của Shawn McCool***](https://shawnmc.cool/2023-02-13_active-record-how-we-got-persistence-perfectly-wrong).



## Phạm vi có thể chạm
Theo truyền thống, phạm vi truy vấn có thể sử dụng lại luôn được xác định trong chính mô hình đích bằng cách sử dụng cú pháp `scopeXXX`, macro hoặc lớp `Builder` chuyên dụng. Vấn đề với hai cách tiếp cận đầu tiên là cả hai đều dựa vào phép thuật thời gian chạy không rõ ràng khiến cho việc nhận được sự trợ giúp của IDE (gần như) là không thể. Tệ hơn nữa, việc xung đột tên có thể xảy ra trong trường hợp đăng ký macro. Tuy nhiên, có cách tiếp cận thứ tư—và theo ý kiến ​​của tôi—tốt hơn: phạm vi có thể chạm được. Phạm vi có thể chạm được là một trong những viên ngọc ẩn vô cùng có giá trị, nhưng đồng thời lại hoàn toàn không được công chúng biết đến vì [**nó không được ghi lại ở bất cứ đâu**](https://laravel.com/docs/10.x/eloquent#query-scopes).


### Giải thích bằng ví dụ
Hãy lấy phương thức Controller sau đây làm ví dụ:

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

Chúng ta có thể thấy rằng nó áp dụng một số điều kiện cho `Builder` và trả về kết quả nguyên trạng mà không có bất kỳ chuyển đổi nào. Mặc dù đây là một cách hoàn toàn hợp lệ để viết truy vấn, nhưng nó làm rò rỉ nội dung bên trong và mệnh đề Where không cho chúng ta biết bất cứ điều gì về ngôn ngữ miền. Có lẽ yêu cầu là: "Tạo một điểm cuối trả về các công ty mồ côi & chưa được xác minh lâu đời nhất". Mồ côi trong trường hợp này có nghĩa là một công ty đã bị bỏ rơi trong quá trình đăng ký và đó là khái niệm mà các chuyên gia tên miền của chúng tôi sử dụng. Vì vậy chúng ta có thể làm tốt hơn nhiều:

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

Điều này gần giống như yêu cầu vậy, phải không? Bây giờ, nếu chúng ta nhanh chóng xem xét một trong những phạm vi có thể chạm được sau:

```php
final readonly class Orphan
{
    public function __invoke(Builder $builder): void
    {
        $builder->whereNull('user_id');
    }
}
```

Là nó! Sự đơn giản này cho phép chúng ta soạn các truy vấn theo bất kỳ cách, hình dạng hoặc hình thức nào mà chúng ta muốn và không bị hạn chế sử dụng một đặc điểm cụ thể hoặc thứ gì đó làm ô nhiễm các mô hình Eloquent của chúng ta.

Bây giờ hãy tưởng tượng rằng có một yêu cầu mới yêu cầu một điểm cuối hoàn toàn mới liệt kê các thành viên mồ côi thuộc về một công ty nhất định. Tại thời điểm này, chúng ta có thể bắt đầu đổ mồ hôi vì không có điểm chung nào giữa `Công ty` và `Thành viên`, nhưng đừng lo lắng! Phạm vi có thể chạm đây để giải cứu! Hãy sử dụng lại phạm vi và gọi nó là một ngày:

```php
public function index(Request $request): Collection
{
    return Member::query()
        ->whereBelongsTo($request->company)
        ->tap(new Orphan())
        ->get();
}
```

**Đây** là sức mạnh của phạm vi có thể chạm được. Tôi nghĩ cũng nên đề cập rằng ví dụ này yêu cầu cả hai mô hình phải có khái niệm "mồ côi", là trạng thái bị bỏ rơi sau quá trình đăng ký được bắt đầu, như được báo hiệu bằng cột `user_id` có thể rỗng. Việc đăng ký hoàn tất khi người dùng được liên kết với tất cả các mô hình. Không cần phải nói, bạn không thể cứ đi và sử dụng bất kỳ phạm vi nào với bất kỳ mô hình nào. Nó phải được hỗ trợ bởi bảng cơ sở dữ liệu sao lưu.


### Ghi chú mẫu đặc tả
Bạn đã bao giờ nghe nói về [Mẫu đặc tả](https://www.martinfowler.com/apsupp/spec.pdf) và thử áp dụng nó [một cách giáo điều](https://laracasts.com/series/design-patterns-in-php/episodes/6)? Như bạn có thể biết, giáo điều là gốc rễ của mọi tội lỗi. Cách áp dụng các ràng buộc truy vấn này mang lại những điều tốt nhất trong nhiều thế giới.


### Bạn có phải là tác giả package không?
Phạm vi có thể chạm vào đặc biệt hữu ích cho các tác giả package muốn chia sẻ phạm vi có thể sử dụng lại cùng với package của họ. Hãy lấy [laravel-adjacency-list](https://github.com/staudenmeir/laravel-adjacency-list) làm ví dụ. [`scopeIsRoot`](https://github.com/staudenmeir/laravel-adjacency-list/blob/master/src/Eloquent/Traits/HasRecursiveRelationshipScopes.php#L94) có thể được tái cấu trúc như sau:

```php
final readonly class IsRoot
{
    public function __invoke(Builder $builder): void
    {
        $builder->whereNull('parent_id');
    }
}
```

Cách tiếp cận này cũng giải quyết được [vấn đề về xung đột giữa phương thức và tên phạm vi](https://github.com/staudenmeir/laravel-adjacency-list/pull/84#issuecomment-1037416147), chỉ cần tránh các phép thuật cổ xưa vẫn còn tồn tại có sẵn trong khuôn khổ từ những ngày đầu. Nói chung, việc sử dụng phạm vi có thể nhấn được mang lại kết quả tích cực cho 90% trường hợp sử dụng hiện có.



## Phạm vi toàn cầu không quá toàn cầu
Tôi biết rằng tiêu đề không có nhiều ý nghĩa khi đọc ngoài ngữ cảnh, nhưng xin vui lòng thông cảm. Thỉnh thoảng lại có một vài [bài đăng liên quan đến phạm vi toàn cầu xuất hiện](https://twitter.com/stef_r/status/1627692788673392641) trên dòng thời gian X (trước đây là Twitter) của tôi. Tâm lý chung luôn là "[phạm vi toàn cầu là xấu, phạm vi địa phương là tốt](https://twitter.com/djgeisi/status/1648643569027100672)". Lý do cho điều này là vì [tài liệu của Laravel về phạm vi toàn cầu](https://laravel.com/docs/10.x/eloquent#global-scopes) khiến họ không hài lòng lắm bằng cách làm cho nó trông giống như cách áp dụng được ghi lại trong tài liệu phạm vi toàn cầu là cách duy nhất, nhưng không có gì có thể xa hơn sự thật.

Cách đây một thời gian, tôi đang nghĩ về lời phàn nàn phổ biến này và rồi tôi chợt nhận ra: điều gì sẽ xảy ra nếu bạn coi thường quy ước này? Tôi đã xem xét API phạm vi toàn cầu và nhanh chóng nhận ra rằng trên thực tế không bắt buộc phải khai báo phạm vi trong phương thức vòng đời `booted` của mô hình Eloquent của bạn. Trên thực tế, nó không có hạn chế nào cả! Chúng có thể được áp dụng trong `ServiceProvider`, `Middleware`, `Job`, v.v. khả năng là vô tận. Tuy nhiên, cách sử dụng tốt nhất—theo ý kiến ​​của tôi—là khi kết hợp với phần mềm trung gian. Vì vậy, chúng ta hãy xem một ví dụ.


### Giải thích bằng ví dụ: giới hạn quốc gia
Hãy tưởng tượng rằng bạn đang làm việc trên một ứng dụng như [IMDb](https://imdb.com/) có trang web công cộng trực diện và bảng quản trị nội bộ. Một trong những yêu cầu có thể là một số bộ phim nhất định chỉ được hiển thị cho người dùng nếu quốc gia của người dùng có trong danh sách trắng nhất định, nếu không thì coi như bộ phim đó hoàn toàn không tồn tại. Tóm lại, bạn phải phân vùng dữ liệu dựa trên quốc gia xuất xứ. Tuy nhiên, hạn chế này chỉ nên áp dụng cho trang web công cộng, **không** cho bảng quản trị nội bộ. Một cách dễ dàng để thực hiện yêu cầu này là tận dụng *phạm vi toàn cầu không quá toàn cầu*.

Đầu tiên, hãy tạo phạm vi toàn cầu như bạn vẫn thường làm:

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

Tiếp theo, tạo một phần mềm HTTP middleware có trách nhiệm áp dụng phạm vi cho các mô hình có liên quan:

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

Lưu ý: *`Repository`* trong ví dụ này có thể là bất cứ thứ gì trả về quốc gia của người dùng, như [laravel-geo](https://github.com/dive-be/laravel-geo).

Cuối cùng, hãy mở tệp tuyến đường `web.php` của bạn và áp dụng middleware cho nhóm có liên quan:

```php
$router->group([
    'middleware' => ['web', RestrictByCountry::NAME],
], static function ($router) {
    $router->resource('movies', Site\MovieController::class);
    $router->resource('ratings', Site\RatingController::class);
    $router->resource('reviews', Site\ReviewController::class);
	
    // Các tuyến trang web công cộng trực diện...
});

$router->group([
    'middleware' => ['web', 'auth'],
    'prefix' => 'admin',
], static function ($router) {
    $router->resource('movies', Admin\MovieController::class);
    $router->resource('ratings', Admin\RatingController::class);
    $router->resource('reviews', Admin\ReviewController::class);
	
    // Các tuyến quản trị...
});
```

Hãy chú ý đến thực tế là middleware chỉ được áp dụng cho các tuyến trang web công cộng. Điều này có ý nghĩa sau:

 - Bất cứ khi nào người dùng truy cập bất kỳ tuyến trang web công cộng nào, nội dung sẽ tự động được lọc dựa trên quốc gia. Điều này có thể dẫn đến các trang 404 vô hại.
 - Bất cứ khi nào cần thêm các tuyến đường mới do yêu cầu tính năng mới, nhà phát triển không cần phải nhớ thực tế là mỗi mô hình phải được lọc dựa trên quốc gia của người dùng. Điều này đã được xử lý rồi và không có cách nào có thể vượt qua hạn chế này trừ khi được thực hiện có chủ ý.
 - Bất cứ khi nào các nhà phát triển sử dụng REPL như tinker, họ sẽ không bị bất ngờ vì một phạm vi toàn cầu khó chịu, ẩn giấu đang thay đổi các truy vấn. Hãy nhớ rằng, phạm vi toàn cầu của chúng tôi không mang tính toàn cầu.
 - Bất cứ khi nào quản trị viên truy cập bảng quản trị nội bộ, họ sẽ luôn nhìn thấy tất cả nội dung bất kể nguồn gốc của chúng. Đây chính xác là những gì chúng tôi muốn.

Nói một cách ngắn gọn, nếu chúng ta nắm bắt được bản chất toàn cầu của phạm vi toàn cầu trong khi suy nghĩ về vị trí chính xác và phạm vi ảnh hưởng của nó, chúng ta thực sự có thể tạo ra những trải nghiệm phát triển vui vẻ đồng thời loại bỏ những thất vọng tiềm ẩn trong tương lai. Nó không cần phải khó chịu!



## Thuộc tính ảo
Trong một dự án khá gần đây mà tôi thực hiện, tôi phải hiển thị một lượng lớn điểm đánh dấu trên bản đồ tương tác như [Google Maps](https://developers.google.com/maps), [Leaflet](https://leafletjs .com/) hoặc [Mapbox](https://www.mapbox.com/developers). Các bản đồ tương tác này chấp nhận danh sách các loại hình học theo [thông số GeoJSON](https://geojson.org/). Loại `Point`, chính xác là những gì tôi cần, phải cung cấp thuộc tính `tọa độ` với một bộ giá trị tương ứng là **(lat,lon)**. Vấn đề ở đây là `tọa độ` đại diện cho một giá trị tổng hợp, trong khi dữ liệu được làm phẳng dưới dạng `addresses:id,latitude,longitude` trong cơ sở dữ liệu. Bảng được thiết kế như vậy nhờ bảng quản trị được chọn: [Laravel Nova](https://nova.laravel.com/). Việc xử lý việc tạo bản ghi trong [Nova](https://nova.laravel.com/) sẽ dễ dàng hơn nhiều nếu bạn giữ cấu trúc càng phẳng càng tốt. Lẽ ra tôi có thể giải quyết vấn đề này một cách đơn giản trong [Tài nguyên Eloquent](https://laravel.com/docs/10.x/eloquent-resources) (còn gọi là máy biến áp), nhưng lập trình viên tò mò trong tôi đã nói với tôi rằng nên có để trở thành một cách tốt hơn. Nội tâm tôi chắc chắn đã đúng: có một cách tốt hơn nhờ—cái mà tôi gọi là—*Thuộc tính ảo*.


### Giải thích bằng ví dụ: `Coordinates`
Để giải quyết vấn đề hiện tại, trước tiên chúng ta cần tạo `ValueObject` đại diện cho địa chỉ `Coordinates`:

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

Tiếp theo, chúng ta nên xác định dàn diễn viên đối tượng `AsCoordines` của mình:

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

Cuối cùng, chúng ta nên gán nó làm diễn viên trong mô hình `Address` của mình:

```php
final class Address extends Model
{
    protected $casts = [
        'coordinates' => AsCoordinates::class,
    ];
}
```

Bây giờ, chúng ta có thể chỉ cần sử dụng nó trong Tài nguyên Eloquent của mình:

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

- `Coordinates` hiện chịu trách nhiệm hoàn toàn về khái niệm `Coordinates` (đại diện cho một điểm hai chiều trên trái đất).
 - Chúng ta không cần phải tự gọi `jsonSerialize()` vì giao diện đã được triển khai. Việc này sẽ được chúng tôi xử lý do Laravel gọi `json_encode` ở đâu đó trong ngăn xếp lệnh gọi.
 - Nếu có điều gì đó thay đổi về `Coordinates`, sẽ rất đơn giản để tìm ra khái niệm `Coordinates` đang được sử dụng ở đâu.

Đây là những gì chúng tôi đã kết thúc như mong đợi:

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


### Giải thích bằng một ví dụ khác: hiển thị dòng địa chỉ
Một cách hữu ích khác để sử dụng thuộc tính ảo là giúp bạn hiển thị mẫu. Thông thường, nếu bạn muốn hiển thị địa chỉ dưới dạng HTML, bạn phải làm như sau:

```php
<address>
  <span>{{ $address->line_one }}</span>
  @if($two = $address->line_two)<span>{{ $two }}</span>@endif
  @if($three = $address->line_three)<span>{{ $three }}</span>@endif
</address>
```

Như bạn có thể thấy, nó có thể thoát khỏi tầm tay rất nhanh. Mặc dù tôi nhận ra rằng đây là một ví dụ khá giả tạo, vì các địa chỉ thường được hiển thị khác nhau tùy theo quốc gia, nhưng nó giúp vẽ ra bức tranh rằng nó có thể trở thành một mớ hỗn độn khá nhanh. Điều gì sẽ xảy ra nếu chúng ta có thể làm điều gì đó như thế này:

```php
<address>
  @foreach($address->lines as $line)
  <span>{{ $line }}</span>
  @endforeach
</address>
```

Đẹp hơn nhiều, phải không? Mẫu của chúng tôi không còn quan tâm đến việc nó có thể đột ngột trở nên phức tạp như thế nào do các quy định khác nhau ở các quốc gia khác nhau. Nó làm những gì nó làm tốt nhất: kết xuất. Thuộc tính ảo chịu trách nhiệm cho việc này có thể trông như sau:

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

Ví dụ: nếu chúng tôi phải hoán đổi `line_two` và `line_third` cho Nam Cực, chúng tôi có thể thực hiện điều chỉnh trong `AsLines` và sẽ không phải điều chỉnh mẫu blade nào cả. Suy nghĩ sáng tạo có thể đơn giản hóa rất nhiều cách chúng ta có thể hiển thị giao diện người dùng và ngăn chúng ta tạo ra những giao diện người dùng quá thông minh thường bị phản đối và coi là phản mẫu.



## Đối tượng truy vấn thông thạo
Tôi đã đề cập đến các lớp `Builder` tùy chỉnh trong phần đầu tiên [phạm vi có thể mở rộng](https://muhammedsari.me/unorthodox-eloquent#tappable-scopes). Mặc dù chúng là bước tốt đầu tiên hướng tới các truy vấn dễ đọc và dễ bảo trì hơn, nhưng tôi nghĩ rằng chúng sẽ nhanh chóng thất bại khi cần thêm nhiều ràng buộc tùy chỉnh vào các lớp `Builder` tùy chỉnh. Họ chỉ có xu hướng trở thành một loại [đối tượng của Chúa](https://en.wikipedia.org/wiki/God_object) khác. Cũng có rất nhiều rắc rối để đưa IDE đến mức nó bắt đầu trợ giúp bạn với các đề xuất. Bạn cũng có thể tạo một `Repository` dành riêng cho các mô hình của mình, nhưng tôi rất không thích tùy chọn đó. Bạn thấy đấy, trong mắt tôi, `Repository` và `Eloquent` loại trừ lẫn nhau.—Trước khi bạn lấy cây chĩa ra, tôi biết rằng điều đó không hoàn toàn đúng. Tuy nhiên, nếu bạn biết lý do tại sao `ActiveRecord` tồn tại và tại sao `Repository` tồn tại thì bạn sẽ hiểu tôi đến từ đâu.—Bạn có thể đọc thêm về điều đó [tại đây](https://muhammedsari.me/repositories-and-their-true-purpose).

Một cách khác là việc sử dụng cái được gọi là `QueryObject`. Nó là một đối tượng chịu trách nhiệm về việc cấu thành và thực thi một loại truy vấn duy nhất. Mặc dù điều này không hoàn toàn tuân theo [định nghĩa của Martin Fowler trong PoEAA](https://www.martinfowler.com/eaaCatalog/queryObject.html), nhưng nó đủ gần và tôi nghĩ rằng việc mượn khái niệm này cho mục đích cụ thể này ổn. Nếu có đăng ký Laracasts, bạn có thể đã xem [bài học về chủ đề này](https://laracasts.com/series/whip-monstrous-code-into-shape/episodes/17). Mặc dù triết lý và cách suy nghĩ giống hệt nhau, tôi muốn giới thiệu một API thay thế dễ sử dụng hơn nhiều: các đối tượng truy vấn thông thạo.


### Giải thích bằng ví dụ: trung tâm thông báo
Hãy tưởng tượng rằng chúng ta có một SPA, được hỗ trợ bởi HTTP JSON API, có chuông thông báo ở trên cùng. Phần back-end hiển thị một điểm cuối mà chúng tôi có thể sử dụng để truy xuất các thông báo chưa đọc của người dùng đã đăng nhập. Phương thức `Controller` chịu trách nhiệm truy xuất các thông báo chưa đọc có thể trông như sau:

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

Mọi chuyện đều đơn giản cho đến khi có yêu cầu tính năng mới yêu cầu chúng tôi tạo một trang chuyên dụng để quản lý tất cả các thông báo: đã đọc, chưa đọc, loại thông báo, v.v. Để giúp cuộc sống của các nhà phát triển giao diện người dùng dễ dàng hơn, chúng tôi đã quyết định tạo một điểm cuối dành riêng cho mỗi loại chế độ xem. Một trong số chúng chịu trách nhiệm truy xuất thông báo đã đọc có thể trông như sau:

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

Những độc giả tinh mắt có thể đã nhận thấy rằng mọi thứ trong đoạn code này đều giống với đoạn code trước, ngoại trừ mệnh đề `whereNotNull` và sự háo hức của mối quan hệ `notifiable`. Bây giờ chúng ta phải lặp lại quá trình này cho các loại khác:

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

Tôi nghĩ bạn hiểu ý chính của nó. Việc này lặp đi lặp lại quá nhiều và cần phải làm gì đó để giải quyết vấn đề này. Nhập **đối tượng truy vấn thông thạo**. Đầu tiên, chúng ta sẽ tạo lớp truy vấn chịu trách nhiệm "nhận thông báo của tôi":

```php
final readonly class GetMyNotifications
{
}
```

Tiếp theo, chúng ta sẽ chuyển truy vấn cơ sở (các điều kiện cần được áp dụng mọi lúc) sang **`constructor`** của đối tượng sáng bóng, hoàn toàn mới của chúng ta:

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

Bây giờ, chúng ta cần khai thác khả năng sáng tác bằng cách sử dụng đặc điểm của [`ForwardsCalls`](https://github.com/laravel/framework/blob/10.x/src/Illuminate/Support/Traits/ForwardsCalls.php):

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

Quan sát:
 - `ForwardsCalls` cho phép chúng ta xử lý lớp như thể nó là một phần của "lớp cơ sở" `\Illuminate\Database\Eloquent\Builder` mặc dù không có sự kế thừa tại chỗ. Tôi yêu sáng tác.
 - Chú thích `@mixin` sẽ giúp IDE cung cấp cho chúng ta những gợi ý tự động hoàn thành hữu ích.
 - Bạn cũng có thể chọn thêm `Conditionable` để có API trôi chảy hơn nữa, nhưng điều đó không thực sự cần thiết ở đây do lựa chọn thiết kế của chúng tôi (điểm cuối riêng biệt cho mỗi loại chế độ xem).

Điều duy nhất còn lại bây giờ là các ràng buộc truy vấn tùy chỉnh, vì vậy hãy thêm chúng:

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

Tuyệt vời. Hiện tại, chúng ta có mọi thứ cần thiết để triển khai đúng cách tính năng "nhận thông báo của tôi" hay còn gọi là trung tâm thông báo. Vì vậy, với mọi thứ được khâu lại với nhau:

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

Hãy cấu trúc lại một trong những **`Controller`** trước đó và xem nó trông như thế nào:

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

Không biết bạn thế nào, nhưng đoạn code trang nhã này thật đẹp khi nhìn vào. Bạn có thể tiếp tục sử dụng tất cả các phương thức [`Illuminate\Database\Eloquent\Builder`](https://laravel.com/api/10.x/Illuminate/Database/Eloquent/Builder.html) thông thường đồng thời vẫn có khả năng để gọi các phương thức cụ thể, nâng cao dành riêng cho truy vấn riêng biệt này. Takeaways:
 - Các khái niệm chính được gói gọn đằng sau các giao diện có ý nghĩa.
 - Tuân thủ [SRP](https://en.wikipedia.org/wiki/Single-responsibility_principle)
 - Tìm kiếm framework và các công cụ của nó thay vì chống lại nó như sử dụng `Repository`
 - Dễ dàng tái sử dụng ở nhiều nơi
 - Dễ dàng kiểm tra


### Lưu ý khi sử dụng `Pipeline`
Có [rất nhiều hướng dẫn trên YouTube](https://www.youtube.com/watch?v=9E5IevdHPwA) cho biết cách chúng ta có thể sử dụng `Pipeline` để phân chia một cách hợp lý chuỗi hoạt động hoặc thực hiện một số thao tác lọc phức tạp. Một số độc giả có thể nghĩ rằng thật lãng phí thời gian khi xử lý `QueryObject` của riêng bạn. Tuy nhiên, vấn đề là tôi không tin rằng `Pipeline` và `QueryObject` loại trừ lẫn nhau. Chúng có thể bổ sung cho nhau và giúp đỡ nhau hoàn thành nhiệm vụ hiệu quả hơn. Thay vì gợi ý kiểu `Builder` trong các đường dẫn, chúng ta có thể gợi ý kiểu `QueryObject` tùy chỉnh của mình. Về cơ bản là xây dựng [laravel-query-builder](https://github.com/spatie/laravel-query-builder) của riêng chúng ta, nhưng với một API cụ thể hơn.

`Pipeline` có thể trông như sau:

```php
$orders = Pipeline::send(
    GetMyOrders::query($request->user())
)->through([
    Filter\Cancelled::class,
    Filter\Delayed::class,
    Filter\Shipped::class,
])->thenReturn()->get();
```

Một `Pipe` có thể trông như sau:

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

Không có gì sai khi kết hợp các khái niệm khác nhau để đạt được mục tiêu cuối cùng của bạn. Chỉ cần đảm bảo rằng nó có ý nghĩa đối với bối cảnh hiện tại của bạn và bạn không vô tình tạo ra sự phức tạp.



## Chia sẻ háo hức
Đây là một cách ngắn gọn nhưng không thể thiếu (ít nhất là đối với tôi). Tại một thời điểm nào đó, bạn có thể thấy mình tự hỏi làm thế nào bạn có thể chia sẻ các tải mong muốn, đặc biệt là những tải thực hiện một số tinh chỉnh bổ sung, tuy nhiên cuối cùng vẫn chỉ sao chép-dán code có liên quan. Mặc dù sao chép-dán là một lựa chọn hoàn toàn hợp lệ nhưng trên thực tế có nhiều cách tốt hơn để giải quyết vấn đề này. Việc lặp lại các loại tải háo hức này có thể nhanh chóng trở nên cồng kềnh do áp dụng các ràng buộc truy vấn bổ sung. Ví dụ: đây có thể là trường hợp khi sử dụng gói phantasmagorical của Spatie [laravel-medialibrary](https://github.com/spatie/laravel-medialibrary).

Hãy tưởng tượng rằng bạn có 10 mô hình khác nhau. Mỗi mô hình xác định nhiều `MediaCollections` riêng biệt và mỗi mô hình cũng xác định một `thumbnail` cho mục đích hiển thị trên mặt tiền cửa hàng. Vì nhiều lý do khác nhau, code `Controller`, v.v. không thể được chia sẻ (dù sao thì bạn cũng không nên chia sẻ). Gói này hoạt động với một mối quan hệ `media` lớn tải tất cả các đối tượng `Media` được đính kèm và sử dụng phép thuật `Collection` trong nền để phân vùng chúng. Việc háo hức tải toàn bộ mối quan hệ `media` có thể nhanh chóng trở thành một vấn đề trên trang chỉ mục liệt kê một mô hình có rất nhiều `MediaCollections`. Rốt cuộc, thứ duy nhất chúng ta cần trên trang chỉ mục là `thumbnail` của mô hình. Để giải quyết vấn đề này, chúng ta có thể áp dụng ràng buộc truy vấn như sau:

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

Mặc dù điều này giải quyết được vấn đề tìm nạp quá mức nhưng trông nó không đẹp mắt. Bây giờ lặp lại điều này thêm 9 lần nữa. Kinh quá! Trên thực tế, việc giải quyết vấn đề này rất đơn giản. Đầu tiên, hãy suy nghĩ về những gì bạn muốn tải háo hức. Hiểu rồi? `LoadThumbnail`. Sau đó, tạo một lớp đại diện cho ràng buộc này:

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

Bây giờ chỉ cần sử dụng nó:

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

Tuyệt vời phải không? Bạn cũng có thể nhận thấy `toArray` ở phía dưới. Điều đó sẽ rất hữu ích nếu bạn muốn xác định mỗi lần tải háo hức 1 mối quan hệ bằng các lệnh gọi `with((new LoadThumbnail)->toArray())` liên tiếp. Kỹ thuật này thực hiện quá đơn giản nên gần như không công bằng. Vui lòng không tìm nạp quá mức và thay vào đó hãy đảm bảo dữ liệu tối thiểu được trả về qua dây từ cơ sở dữ liệu. Lười biếng không phải là lý do!



## Trình truy cập có thể gọi được
Chúng ta đã nói về các kỹ thuật như [Thuộc tính ảo](https://muhammedsari.me/unorthodox-eloquent#phantom-properties). Nếu bạn chưa đọc phần đó, vui lòng đọc nó trước và quay lại phần này. Dù sao đi nữa, lỗ hổng lớn nhất với [Thuộc tính ảo](https://muhammedsari.me/unorthodox-eloquent#phantom-properties) là nó yêu cầu chúng ta xác định một `set` (inbound) cast ngay cả khi chúng ta không sử dụng nó, giống như ví dụ `$address->lines`; và nó cũng không có cơ chế tự động ghi nhớ kết quả tính toán. Thật đáng tiếc khi không có `CastsOutboundAttributes`, nhưng đó là nơi các trình truy cập có thể gọi được tỏa sáng. Những lợi ích chính là:
 - Ghi nhớ
 - Không có mô hình lộn xộn
 - Đơn vị có thể kiểm tra
 - Có thể kết hợp, giống như bất kỳ đối tượng nào khác

Một định nghĩa ví dụ (`Attribution::get` là có thật):

```php
final class File extends Model
{
    protected function stream(): Attribute
    {
        return Attribute::get(new StreamableUrl($this));
    }
}
```

Đó là tất cả những gì cần thiết để xác định một trình truy cập có thể gọi được. Hãy lưu ý đến đối số của hàm tạo, vì đó là mẫu lặp lại với các bộ truy cập có thể gọi được. Cần có quyền truy cập vào mô hình đang được sử dụng, nếu không, chúng tôi sẽ không thể thu thập thông tin theo ngữ cảnh cần thiết để thực hiện nhiệm vụ của mình. Trong ví dụ này, `StreamableUrl` chịu trách nhiệm—bạn đoán rồi đấy—tạo các URL có thể phát trực tuyến. Chúng ta lẽ ra có thể nội tuyến logic và sử dụng [cách `Closure` cổ điển](https://laravel.com/docs/10.x/eloquent-mutators#defining-an-accessor), nhưng điều đó sẽ bắt đầu lấp đầy mô hình của chúng ta khá là nhanh. Mô hình thực tế mà đoạn code này xuất phát, có mười bốn trình truy cập khác (!). Xem nhanh từ trình truy cập có thể gọi được cụ thể này:

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

Các chi tiết chính xác không quan trọng lắm, nhưng điều đáng chú ý là nó gói gọn logic một cách hợp lý để tạo các URL có thể phát trực tuyến được tối ưu hóa. Việc trả về đường dẫn `s3://` trực tiếp sẽ hiệu quả hơn nhiều khi truyền trực tuyến tệp từ [S3](https://aws.amazon.com/s3/).

Điểm chính là, hãy tưởng tượng nếu chúng ta đã xác định mã này bên trong một `Closure` truyền thống bên trong trình truy cập trên chính mô hình đó và cũng thực hiện điều gì đó tương tự cho mười ba trình truy cập khác. Mô hình của chúng ta sẽ nhanh chóng trở nên quá tải. Việc sử dụng các bộ truy cập có thể gọi được cũng cho phép chúng ta trích xuất những code đó và giữ cho các mô hình của chúng ta sạch sẽ và gọn gàng.



## Nhiều mô hình đọc cho cùng một bảng
Đây là một trong những khoảnh khắc hiếm hoi mà tôi thực sự đánh giá cao những hạn chế của [Laravel Nova](https://muhammedsari.me/nova.laravel.com), thường rất khó tùy chỉnh so với các giải pháp bảng quản trị khác. Nó cho phép tôi khám phá một trường hợp sử dụng mới cho các mô hình chỉ đọc.

Gần đây, có một yêu cầu về tính năng yêu cầu chúng tôi tạo một trình khám phá tệp chính thức để chia sẻ tệp với các bên thứ ba và khách hàng. Việc triển khai giải pháp quản lý tệp của riêng chúng tôi là điều không thể thực hiện được vì 1. đây là một vấn đề đã được giải quyết và 2. đây là một vấn đề phức tạp và có nhiều trường hợp. Chúng tôi quyết định sử dụng [laravel-medialibrary](https://github.com/spatie/laravel-medialibrary) (như bất kỳ người tỉnh táo nào khác sẽ làm, cảm ơn Spatie!), nhưng có một trở ngại lớn cần vượt qua. Chúng tôi phải tạo một giao diện thân thiện với UX ở Nova trong tài nguyên `Directory`, nơi chứa các `File` thuộc về `Directory` cụ thể đó và nó phải có thể sắp xếp được. Mặc dù mô hình `Media` mặc định hoạt động tốt nhưng nó không tương thích với thư viện sắp xếp phổ biến nhất của Nova (cũng từ Spatie). Chúng tôi phải đưa ra một giải pháp ban đầu. Đó là lúc tôi chợt nảy ra ý tưởng tạo một mô hình chỉ đọc cho bảng `media` và đưa lý thuyết vào thử nghiệm:

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

Mặc dù điều này có vẻ đầy hứa hẹn nhưng vẫn còn một trở ngại khác cần phải vượt qua. Mô hình này có thể được sử dụng để truy vấn **bất kỳ thứ gì** trong bảng `media`, điều này có thể dẫn đến mất dữ liệu không mong muốn. Tất nhiên, điều này không được chấp nhận vì mô hình này đại diện cụ thể cho một `File` mà thực tế là một đối tượng `Media` đáp ứng hai tiêu chí:

 - Nó phải thuộc model `Directory`
 - `collection_name` phải là `file`

Tôi đã quyết định tạo một phạm vi toàn cầu thực sự và đăng ký nó trong `ServiceProvider` để luôn thực thi các quy tắc này (một thời điểm hiếm hoi khác mà phạm vi toàn cầu thực sự có ý nghĩa):

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

Các mô hình không phù hợp với các tiêu chí này sẽ không được trả lại nữa và thay vào đó bắt đầu ném ra `ModelNotFoundException`. Đây chính xác là những gì chúng tôi muốn. Hoàn hảo, nhưng chúng ta chưa thể tuyên bố chiến thắng. Giao diện Nova yêu cầu một loạt thông tin mà đơn giản là không thể trích xuất được từ mô hình `Media` mặc định. Nhưng sau đó tôi lại chợt nhận ra: vì đây là model tùy chỉnh của chúng tôi nên tôi có thể làm bất cứ điều gì tôi muốn! Bây giờ tôi thậm chí có thể khai báo nó như một mối quan hệ trong mô hình `Directory`:

```php
public function files(): HasMany
{
    return $this->hasMany(File::class, 'model_id')->ordered();
}
```

Bạn có nhận thấy điều gì "kỳ lạ" không? Không? Hãy nhìn vào loại mối quan hệ. Nếu bạn biết cách hoạt động của `MediaLibrary`, bạn sẽ biết rằng bảng `media` thực sự sử dụng mối quan hệ `MorphMany`. Nhưng vì chúng ta đã xác định một `FileScope` toàn cục luôn tinh chỉnh các truy vấn trên `model_type`, nên chúng ta có thể chỉ cần sử dụng chính loại quan hệ `HasMany` và mọi thứ vẫn hoạt động. Đây là lúc tâm trí tôi như bị thổi bay. Việc gọi `$directory->files` bây giờ sẽ trả về một Bộ sưu tập các đối tượng `File` chứ không phải các đối tượng `Media`. Tóm lại, `File` hiện đã sở hữu mọi thứ cần thiết để phục vụ bối cảnh `Chia sẻ tệp`. Chúng tôi không cần thay đổi bất kỳ cấu hình nào hay thứ gì khác—không cần gì cả. Chỉ cần một số cách tiếp cận thông minh và mới lạ. Kết quả cuối cùng là sự xuất sắc thuần túy.

ví dụ. Tôi cũng có thể thêm một loạt [trình truy cập (có thể gọi được)](https://muhammedsari.me/unorthodox-eloquent#invokeable-accessors) để đáp ứng nhu cầu về giao diện người dùng:

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


### Đường đi
 - Bạn nên sử dụng mô hình chỉ đọc khi mọi thứ trở nên phức tạp ở phía giao diện người dùng.
 - Phạm vi toàn cầu không phải lúc nào cũng xấu.
 - Các mô hình này cho phép tinh chỉnh theo các trường hợp sử dụng mà chúng cần hỗ trợ.
 - Cách tiếp cận này cũng có thể được sử dụng nếu một gói không cho phép bạn ghi đè "mô hình cơ sở" mà nó sử dụng. Chỉ cần tạo mô hình của riêng bạn tham chiếu đến bảng của package và bắt đầu giải quyết vấn đề.



## `WithoutRelations` cho hiệu suất hàng đợi
Cuối cùng nhưng không kém phần quan trọng, chủ đề tôi muốn nói đến là thuộc tính `WithoutRelations` bí ẩn hoặc phương thức `withoutRelations`. Những người đam mê tìm hiểu nguồn các gói Laravel có thể đã nhận thấy một số cách sử dụng khi duyệt qua code nguồn. Trên thực tế, nó thực sự được sử dụng trong [thành phần Livewire của Laravel Jetstream](https://github.com/laravel/jetstream/blame/4.x/src/Http/Livewire/UpdateProfileInformationForm.php#L46). Mặc dù lý do tại sao nó được sử dụng ở đây là để ngăn chặn quá nhiều thông tin rò rỉ sang phía khách hàng, nhưng—mặc dù hoàn toàn hợp lệ—không phải là trường hợp sử dụng mà tôi muốn nói đến.

Như bạn có thể đã biết, bạn nên sử dụng đặc điểm `SerializesModels` nếu bạn muốn xếp hàng một `Job` chứa các mô hình Eloquent. (Mục đích của nó được mô tả ngắn gọn [trong tài liệu](https://laravel.com/docs/10.x/queues#class-structure), vì vậy tôi sẽ không lặp lại điều đó.) Nhưng có một nhược điểm là rất nhiều nhà phát triển không biết về: `SerializesModels` cũng ghi nhớ những mối quan hệ nào đã được tải vào thời điểm tuần tự hóa và sử dụng thông tin đó để tải lại tất cả các mối quan hệ khi các mô hình được giải tuần tự hóa. Một tải trọng ví dụ:

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

Như bạn có thể thấy, thuộc tính `relations` chứa ba mối quan hệ. Những thứ này sẽ được tải một cách háo hức khi quá trình khử tuần tự hóa `Job` này. Một mối quan hệ như `likes` và `orders` có thể tạo ra hàng trăm hoặc thậm chí hàng nghìn bản ghi, làm tổn hại nghiêm trọng đến hiệu suất của `Job`. Tệ hơn nữa, `Job` mà tôi lấy ảnh chụp nhanh này thậm chí còn không cần bất kỳ mối quan hệ nào trong số này để thực hiện nhiệm vụ chính của nó.


### Tùy chọn phương thức
Một cách dễ dàng để khắc phục vấn đề này là—bạn đoán rồi đấy—sử dụng phương thức **`withoutRelations`** trước khi chuyển các mô hình Eloquent của bạn tới hàm tạo `Job`. Ví dụ:

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

Thứ đăng ký sự kiện này chịu trách nhiệm tạo khách hàng tiềm năng mới trong hệ thống CRM khác nhau bất cứ khi nào người dùng mới đăng ký trong ứng dụng của chúng tôi. Trước khi gửi `CreatProspect`, `withoutRelations` được gọi để đảm bảo không có mối quan hệ vô dụng nào được tuần tự hóa ngoài thời điểm này, đảm bảo hiệu suất tối ưu. Nếu bây giờ chúng ta kiểm tra tải trọng được tuần tự hóa, chúng ta có thể thấy rằng mảng đã bị làm trống:

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

Hoàn hảo.


### Tùy chọn thuộc tính
Trong khi chuẩn bị bài đăng blog này, tôi nhận ra rằng một nhà phát triển Laravel [`đã đóng góp một thuộc tính #[WithoutRelations] hoàn toàn mới`](https://github.com/laravel/framework/pull/48068) tự động xử lý việc loại bỏ tất cả các mối quan hệ mô hình khi tuần tự hóa `Job`:

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

Đây chắc chắn sẽ là cách định nghĩa mặc định mới của tôi về `Job`s. Tôi cũng không biết bạn thế nào, nhưng tôi gặp **không** trường hợp sử dụng nào mà tôi đã tự nhủ "Chết tiệt, lẽ ra mình nên để yên các mối quan hệ". Hành vi này gây ra nhiều lỗi ẩn hơn bất cứ điều gì (theo kinh nghiệm của tôi). Trong hầu hết các trường hợp, [tải chậm](https://laravel.com/docs/10.x/eloquent-relationships#relationship-methods-vs-dynamic-properties) hoạt động tốt. Hãy nhớ rằng, không có công cụ xấu. Chỉ có những công cụ tồi trong một bối cảnh cụ thể. Đó là lý do tại sao tôi không phải là người hâm mộ lớn tính năng [`Model::preventLazyLoading`](https://laravel.com/docs/10.x/eloquent-relationships#preventing-lazy-loading). Xin lỗi, [namesake](https://twitter.com/taylorotwell/status/1395087054254526474).



## Kết luận
Lúc này các ngón tay của tôi tê cứng, nhưng tôi nghĩ điều đó đáng giá. Sự tò mò khiến bạn trở thành một lập trình viên giỏi hơn, vì vậy hãy thoát khỏi đống hướng dẫn và bắt đầu thử nghiệm. Tin tôi đi, điều tồi tệ nhất có thể xảy ra chính là việc bạn đang học. Và xin vui lòng... đừng quên đọc [sự đánh đổi của Active Record](https://shawnmc.cool/2023-02-13_active-record-how-we-got-persistence-perfectly-wrong ). Điều tồi tệ nhất có thể xảy ra là—một lần nữa—bạn đang học.

[Tham gia thảo luận trên X (trước đây là Twitter)](https://twitter.com/mabdullahsari/status/1702289877520261180)! Tôi rất muốn biết bạn nghĩ gì về bài đăng trên blog này.

Cảm ơn vì đã đọc!