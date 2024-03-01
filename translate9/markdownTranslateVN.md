# Tránh AOP: Lập trình hướng mảng



## Giới thiệu
Mảng là một cách thuận tiện để tổ chức và truyền dữ liệu trong các ứng dụng **PHP**, nhưng chúng ta có nên làm điều đó không? Trong bài viết này, tôi sẽ chỉ ra những nhược điểm của việc lạm dụng việc sử dụng mảng kết hợp và một giải pháp thay thế đơn giản cho việc đó.



## Mảng kết hợp là gì
**Mảng kết hợp, hay còn gọi là Từ điển,** trong PHP là một loại mảng sử dụng các cặp khóa-giá trị để tổ chức và lưu trữ dữ liệu theo định dạng có cấu trúc. Các mảng này cho phép chúng tôi quản lý dữ liệu hiệu quả bằng các khóa thay vì các giá trị chỉ mục thông thường như 0,1,2, v.v., cho phép các phần tử trực quan hơn, dễ đọc hơn.

Mỗi khóa trong mảng kết hợp trong PHP là duy nhất và chỉ có thể tham chiếu đến một giá trị duy nhất. Các khóa này có thể được đặt tên bất cứ thứ gì bạn muốn, miễn là chúng là một chuỗi hoặc một số nguyên, cho phép một phương thức linh hoạt truy cập và thao tác dữ liệu.

Ví dụ: bạn có thể gán tên nhân viên làm khóa và thông tin liên quan của họ làm giá trị. Mảng kết hợp đơn giản hóa rất nhiều quá trình xử lý dữ liệu trong PHP.



## Ưu điểm của việc sử dụng mảng kết hợp
**Mảng kết hợp** trong PHP có một số ưu điểm. Trước hết, chúng có tính linh hoạt cao vì chúng cho phép sử dụng chuỗi để chỉ định chỉ mục thay vì chỉ giới hạn ở các giá trị số. Điều này làm cho mã dễ đọc và dễ truy cập hơn vì nhà phát triển có thể sử dụng tên hoặc mô tả có ý nghĩa làm khóa.

Do đó, chúng có thể đơn giản hóa việc tìm kiếm và quản lý dữ liệu vì bạn có thể truy cập các giá trị trực tiếp thông qua các khóa cụ thể, loại bỏ nhu cầu lặp lại các phần tử. Điều này giúp truy xuất dữ liệu nhanh hơn và hiệu quả hơn, đặc biệt khi xử lý các tập dữ liệu lớn.

Ngoài ra, PHP còn cung cấp rất nhiều **hàm dựng sẵn** có thể xử lý mảng kết hợp một cách hiệu quả, do đó đẩy nhanh quá trình sắp xếp, hợp nhất, chia và lọc mảng.



## AOP - Lập trình hướng mảng
Có thể bạn đã nghe nói về **OOP (Lập trình hướng đối tượng)**, nhưng còn **AOP (Lập trình hướng mảng)** thì sao? Đây không phải là một khái niệm lập trình "thực sự", nhưng nó khá phổ biến trong rất nhiều ứng dụng PHP. Tôi gọi **AOP** khi một cơ sở mã đang sử dụng **Mảng kết hợp** ở mọi nơi.

Chúng ta hãy xem một ví dụ trong đó việc sử dụng **Mảng kết hợp** có thể gây hại cho cơ sở mã của bạn khi nó bắt đầu phát triển hoặc khi nhóm làm việc trên nó bắt đầu phát triển.

Nếu bạn đã làm việc với bất kỳ khung công tác PHP nào thì có lẽ bạn đã quen với việc dữ liệu từ **Yêu cầu HTTP** thường được trả về dưới dạng một mảng khi chúng ta cần xác thực/sử dụng nó. Trong ví dụ bên dưới, bạn có thể thấy cách chúng tôi có thể lấy dữ liệu từ yêu cầu và chuyển nó đến lớp dịch vụ sẽ sử dụng dữ liệu yêu cầu để lấy danh sách sản phẩm và lọc dữ liệu đó bằng **Laravel**.

```php
// ProductController.php

final class ProductController extends Controller
{
    public function __construct(private ProductService $service)
		{
		}
	
    public function index(ProductsListRequest $request): JsonResponse
		{
		    return response()->json($this->service->productsList($request->validated()));
		}
}

// ProductService.php

final class ProductService
{
    public function productsList(array $filters): Collection
		{
		    $query = Product::query()
				    ->select(['id', 'name', 'price']);
			
		    if (isset($filters['search'])) {
				    $query->where('name', 'LIKE', "%{$filters['search']}%");
				}
			
		    if (isset($filters['min_price'])) {
				    $query->where('price', '>=', $filters['min_price']);
				}
			
	      if (isset($filters['max_price'])) {
				    $query->where('price', '<=', $filters['max_price']);
				}
		}
}
```

Thoạt nhìn, không có gì sai với đoạn mã trên và thực tế là sẽ không xảy ra nếu nhóm là một nhóm nhỏ, tuân theo quy trình làm việc tiêu chuẩn và cơ sở mã không lớn. Mặc dù vậy, **DX** không phải là cái tốt nhất, bởi vì nếu ai đó cần thay đổi logic của phương thức `productList`, nhà phát triển cũng cần phải kiểm tra lớp `ProductListRequest` và đảm bảo rằng lớp này được cập nhật với tất cả các thay đổi xảy ra với các bộ lọc được giao diện người dùng chuyển qua.

Vấn đề chính ở đây và ở tất cả những nơi chúng tôi sử dụng **Mảng kết hợp** đều giống nhau: bạn không có bất kỳ thông tin nào về dữ liệu mà mảng chứa. Nó có thể là bất cứ thứ gì và thậm chí tệ hơn, mỗi mục trong mảng có thể là thứ gì đó hoàn toàn khác nhau! Dữ liệu không có ngữ cảnh nào cả.

Hãy tưởng tượng bây giờ bạn đang làm việc trên một cơ sở mã có hơn 100 điểm cuối và với 10 nhà phát triển, tất cả đều sử dụng cùng một phương pháp như được trình bày ở trên. Điều này có thể trở nên thực sự LỘN XỘN. Tôi chỉ nói vậy vì tôi đã trải nghiệm điều đó trong một dự án mà tôi thực hiện. Việc bảo trì mã cũ và giới thiệu các nhà phát triển mới có thể trở thành một cơn ác mộng.

Ngoài ví dụ này, một cách lạm dụng khác của **Mảng kết hợp** là trên phương thức trả về như ví dụ dưới đây:

```php
public function getUserContactInfo(int $userId): array
{
    $user = User::query()->firstOrFail($userId);
	
	   // LOGIC HERE
	
    return [
		    'street' => 'Foo',
			  'number' => 1,
			  'zip' => '123456',
			  'phone' => '123456789',
		];
}
```

Giống như ví dụ đầu tiên, lúc đầu, phương pháp trên không có gì sai, nhưng hãy tưởng tượng rằng 1 năm sau có người vào đoạn mã trên và xóa `phone`, tùy thuộc vào cách thực hiện và quy mô nhóm, các nhà phát triển khác vẫn có thể cho rằng thuộc tính `phone` vẫn còn đó và gây ra sự cố hoặc lỗi. Các bài kiểm tra có thể bao gồm điều đó? Chắc chắn rồi, nhưng đó không phải là DX lý tưởng cho đội của bạn.



## Nhược điểm khác
Một điều khác cần ghi nhớ và đó là mối quan tâm hàng đầu khi xử lý **Mảng kết hợp** là việc sử dụng bộ nhớ. Chúng có thể khá tốn nhiều bộ nhớ, đặc biệt với các tập dữ liệu lớn, vì chúng lưu trữ thông tin bổ sung (khóa) ngoài các giá trị. Do đó, hiệu suất có thể bị ảnh hưởng xấu trong các ứng dụng không được mã tối ưu.

Ngoài ra, các mảng trong PHP không được gõ mạnh, điều đó có nghĩa là bạn có thể vô tình lưu trữ các loại giá trị khác nhau trong cùng một mảng, dẫn đến các lỗi tiềm ẩn hoặc việc sử dụng bộ nhớ không hiệu quả.



## Hiểu cơ sở mã của bạn
Đối với tôi, vấn đề chính với việc lạm dụng **Mảng kết hợp** là nó làm cho cơ sở mã của bạn **tối nghĩa**. Nó sẽ luôn yêu cầu công việc bổ sung từ bất kỳ ai đang làm việc với phần mã đó để hiểu loại dữ liệu mà mảng đang lưu giữ.

Vậy làm cách nào để khắc phục "sự cố" này? Điều đó khá đơn giản: bắt đầu sử dụng **Đối tượng truyền dữ liệu (DTO)**. DTO có thể là một lớp đơn giản được sử dụng để ánh xạ và nhập dữ liệu như ví dụ bên dưới:

```php
final readonly class ProductFilters(
    public ?string $search = null,
    public ?float $min_price = null,
    public ?float $max_price = null,
) {}
```

Với **DTO** này, chúng ta có thể cập nhật mã được hiển thị trước đó để sử dụng nó:

```php
final class ProductService
{
    public function productsList(ProductFilters $filters): Collection
		{
		    $query = Product::query()
				    ->select(['id', 'name', 'price']);
			
		    if (! blank($filters->search)) {
				    $query->where('name', 'LIKE', "%{$filters->search}%");
				}
			
		    if (! blank($filters->min_price)) {
				    $query->where('price', '>=', $filters->min_price);
				}
			
	      if (! blank($filters->max_price)) {
				    $query->where('price', '<=', $filters->max_price);
				}
		}
}
```

Bản thân mã hóa không thay đổi nhiều nhưng ngữ cảnh của dữ liệu trong mã hóa đó **HOÀN TOÀN KHÁC**. Bây giờ bạn có một đối tượng **ProductFilters** đó là **DTO** bao bọc các bộ lọc bạn cần và cung cấp ngữ cảnh cho chúng. Nếu bất kỳ ai cần cập nhật bất cứ điều gì theo phương pháp này: **khắc phục sự cố hoặc mở rộng sự cố bằng các bộ lọc mới**, thì đó là một nhiệm vụ khá đơn giản và dễ hiểu vì bạn có tất cả các bộ lọc có thể được liệt kê và bạn biết chính xác loại dữ liệu mà mỗi bộ lọc chứa .

Chúng ta có thể làm tương tự với ví dụ thứ hai bằng một **DTO** thực sự đơn giản khác:

```php
final readonly class UserContactInfo(
    public string $street,
	  public int $number,
	  public ?string $zip,
	  public ?string $phone,
) {}
```

Sau đó, chúng ta có thể cập nhật `getUsercontactInfo` để sử dụng nó:

```php
public function getUserContactInfo(int $userId): UserContactInfo
{
    $user = User::query()->firstOrFail($userId);
	
	   // LOGIC Ở ĐÂY
	
    return new UserContactInfo(
		    street: 'Foo',
			  number: 1,
			  zip: '123456',
			  phone: '123456789',
		);
}
```

Một lần nữa, bản thân mã hóa gần như giống nhau, nhưng giờ đây dữ liệu được gói trong **DTO** để cung cấp ngữ cảnh và cung cấp **DX** tốt hơn nhiều để nhóm làm việc!



## Phần kết luận
Thật vậy, PHP là một ngôn ngữ mẫu mực, nổi tiếng về tính linh hoạt và mạnh mẽ trong việc tạo ra các ứng dụng web phức tạp một cách tương đối dễ dàng. Mặc dù không thể bỏ qua tiện ích của **Mảng kết hợp** vì chúng cung cấp một cách linh hoạt để truy cập và thao tác dữ liệu, nhưng điều cần thiết là phải sử dụng chúng một cách có ý thức, hiểu được những hạn chế tiềm ẩn của chúng như tôi đã nêu bật một số trong bài viết này.

Tuy nhiên, mọi công cụ trong lập trình đều có vị trí và mục đích của nó. **Mảng kết hợp** cũng không ngoại lệ và các tính năng mạnh mẽ của chúng chắc chắn có thể khắc phục được những nhược điểm đã đề cập trong bối cảnh phù hợp. Xét cho cùng, việc lựa chọn sử dụng chúng phần lớn sẽ phụ thuộc vào các yêu cầu cụ thể của dự án hiện tại bên cạnh việc cân nhắc cẩn thận về sự đánh đổi. Việc sử dụng hợp lý các tính năng của ngôn ngữ sẽ luôn đóng vai trò quan trọng trong việc tạo ra các ứng dụng PHP hiệu quả và mạnh mẽ.

Tôi hy vọng rằng bạn thích bài viết này và nếu có, đừng quên chia sẻ bài viết này với bạn bè của bạn!!! Hẹn gặp lại sau!