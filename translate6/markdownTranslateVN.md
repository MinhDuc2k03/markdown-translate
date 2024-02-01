# Tránh empty() trong PHP
Cấu trúc ngôn ngữ `empty()` có vẻ khá linh hoạt. Nó giống như một con dao quân đội Thụy Sĩ với hàng ngàn lưỡi dao, sẵn sàng tổn thương bạn nếu bạn cầm nhầm đầu. Hoặc là người biết tất cả các giao dịch nhưng không thành thạo một giao dịch nào. Trên hết, `empty()` là một người giao tiếp kém.

Tôi phát hiện ra `empty()` trong các dự án nguồn đóng có tuổi đời kém. Tôi cũng phát hiện ra `empty()` trong các dự án nguồn đóng hoàn toàn mới từ tuần trước. Và tôi gặp lại `empty()` trong các dự án nguồn mở với hàng triệu lượt tải xuống.

Vậy `empty()` có vấn đề gì khi có rất nhiều người sử dụng nó?



## Vấn đề
Nếu bạn tham khảo tài liệu về `empty()` thì bạn sẽ tìm thấy những điều sau:

>*"Xác định xem một biến có được coi là trống hay không. Một biến được coi là trống nếu nó không tồn tại hoặc nếu giá trị của nó bằng `false`. `empty()` không tạo ra cảnh báo nếu biến không tồn tại."*
>
>[**Hướng dẫn sử dụng PHP: empty()**](https://www.php.net/manual/en/function.empty.php)

Nếu bạn bỏ qua việc triển khai nội bộ và các vấn đề về hiệu suất có thể xảy ra thì việc sử dụng `empty()` cũng giống như sử dụng `isset()` và [**so sánh lỏng với** `false`](https://www.php.net/manual/en/language.types.boolean.php#language.types.boolean.casting).

```php
<?php

declare(strict_types=1);

-if (empty($value)) {
+if (!isset($value) || $value == false) {
     // ...
}
```

Khi bạn đã biết rằng một biến, một thuộc tính, một tham số hàm hoặc phương thức và một giá trị trả về của hàm hoặc phương thức đã được xác định, tại sao bạn lại sử dụng `isset()`?

Khi bạn đã biết rằng một biến, thuộc tính, tham số hàm hoặc phương thức và giá trị trả về của hàm hoặc phương thức giả định nhiều loại, tại sao bạn lại sử dụng so sánh lỏng và không xử lý riêng từng loại và giá trị được chấp nhận?

Khi bạn đã biết rằng một biến, thuộc tính, tham số của hàm hoặc phương thức và giá trị trả về của hàm hoặc phương thức đều giả định một loại duy nhất, tại sao bạn lại sử dụng so sánh lỏng thay vì so sánh nghiêm ngặt?

Sử dụng `empty()`, `isset()` hoặc so sánh lỏng là một việc làm thiếu sáng suốt. Đó có phải là cách bạn muốn làm việc?

Như đã đề cập, tôi thường tìm thấy `empty()` trong các cơ sở code cũ. Một số dự án này chạy trên các phiên bản PHP đã lỗi thời và hoàn toàn không biết về khai báo thuộc tính, tham số và kiểu trả về. Trong các dự án này, bạn thường không thể tìm thấy DocBlocks cho các thuộc tính, tham số hàm và phương thức hoặc kiểu trả về của hàm và phương thức.

Khi bạn bắt đầu bảo trì các dự án này và bắt đầu sửa lỗi, cập nhật các phiên bản PHP và cuối cùng là hiện đại hóa chúng, bạn sẽ gặp khó khăn trong việc tìm hiểu xem tác giả ban đầu dự định làm gì khi họ sử dụng `empty()`.

Các tác giả ban đầu có nhận thức được những điều kỳ quặc của `empty()` không? Có phải tác giả dự định một thuộc tính, hàm hoặc tham số phương thức sẽ chấp nhận tất cả các loại và giá trị không? Các tác giả có dự định trả về tất cả các loại và giá trị từ một hàm hoặc phương thức không? Liệu các tác giả, những người đã biến mất từ ​​lâu và ám ảnh các chủ dự án, có thực sự nghĩ đến việc sử dụng `empty()` không?

Tác giả ban đầu có thể là bạn. Người bảo trì cũng có thể là bạn, người tiếp tục thực hiện dự án sau nhiều năm. Có lẽ trong thời gian đó bạn đã bị đột quỵ hoặc tai nạn khiến khả năng nhận thức của bạn bị suy giảm. Bây giờ bạn gặp khó khăn trong việc hiểu ý định của tác giả ban đầu. Có lẽ tác giả và người bảo trì là những người hoàn toàn khác nhau. Có thể bạn hoàn toàn khỏe mạnh nhưng vẫn cố gắng hiểu ý định của tác giả ban đầu.

Trong khi bạn viết code cho máy tính xử lý, bạn - với tư cách là tác giả - cũng viết code cho người duy trì nó sau đó. Bằng cách lựa chọn cẩn thận ít nhiều các tính năng ngôn ngữ, bạn hướng dẫn máy tính nhưng cũng truyền đạt ý định của mình cho người bảo trì.

> *Bất kỳ kẻ ngốc nào cũng có thể viết code mà máy tính có thể hiểu được. lập trình viên giỏi viết code mà con người có thể hiểu được.*
>
> [**Martin Fowler, Tái cấu trúc**](https://martinfowler.com/books/refactoring.html)

Hãy xem xét tất cả các trường hợp khi `empty()` trả về giá trị đúng và khám phá các lựa chọn thay thế giúp truyền đạt nhận thức và ý định tình huống của bạn tốt hơn!



## Ứng viên
 - [**biến không xác định**](##biến-không-xác-định)
 - [**thuộc tính cá thể không xác định**](##thuộc-tính-cá-thể-không-xác-định)
 - [**thuộc tính tĩnh không xác định**](##thuộc-tính-tĩnh-không-xác-định)
 - [**thuộc tính cá thể không thể truy cập**](##thuộc-tính-cá-thể-không-thể-truy-cập)
 - [**thuộc tính tĩnh không thể truy cập**](##thuộc-tính-tĩnh-không-thể-truy-cập)
 - [**vô giá trị**](##vô-giá-trị)
 - [**mảng**](##mảng)
 - [**bool**](##boolean)
 - [**float**](##float)
 - [**int**](#int)
 - [**string**](##string)
 - [**SimpleXMLElement**](##simplexmlelement)



## Biến không xác định
`empty()` trả về `true` khi đối số là một biến không xác định.

```php
<?php

declare(strict_types=1);

var_dump(empty($value)); // (bool)true
```



## Thuộc tính cá thể không xác định
`empty()` trả về `true` khi đối số là thuộc tính phiên bản không xác định.

```php
<?php

declare(strict_types=1);

$object = new stdClass();

var_dump(empty($object->value)); // (bool)true
```

Là một tác dụng phụ, `empty()` cũng gọi phương thức ma thuật `__isset()` khi bạn tham chiếu một thuộc tính phiên bản không xác định của một đối tượng khai báo phương thức `__isset()`.

```php
<?php

declare(strict_types=1);

$object = new class() {
    public function __isset(string $name): bool
    {
        echo '👋';

        return true;
    }
};

var_dump(empty($object->value)); // 👋(bool)true
```



## Thuộc tính tĩnh không xác định
`empty()` trả về `true` khi đối số là thuộc tính `static` không xác định.

```php
<?php

declare(strict_types=1);

$object = new stdClass();

var_dump(empty($object::$value)); // (bool)true
```



## Thuộc tính cá thể không thể truy cập
`empty()` trả về `true` khi đối số là một thuộc tính phiên bản không thể truy cập được.

Là một tác dụng phụ, `empty()` gọi phương thức ma thuật `__isset()` khi nó tồn tại.

```php
<?php

declare(strict_types=1);

$object = new class() {
    private $value = 9000;
    protected $otherValue = 9001;

    public function __isset(string $name): bool
    {
        echo '👋';

        return true;
    }
};

var_dump(empty($object->value)); // 👋(bool)true
var_dump(empty($object->otherValue)); // 👋(bool)true
```



## Thuộc tính tĩnh không thể truy cập
`empty()` trả về `true` khi đối số là thuộc tính `static` không thể truy cập được.

```php
<?php

declare(strict_types=1);

$object = new class() {
    private static $value = 9000;
    protected static $otherValue = 9000;
};

var_dump(empty($object::$value)); // (bool)true
var_dump(empty($object::$otherValue)); // (bool)true
```



## Vô giá trị
`empty()` trả về `true` khi đối số là `null`.

```php
<?php

declare(strict_types=1);

$value = null;

var_dump(empty($value)); // (bool)true
```



## Mảng
`empty()` trả về `true` khi giá trị là một `mảng` trống.

```php
<?php

declare(strict_types=1);

$value = [];

var_dump(empty($value)); // (bool)true
```



## Boolean
`empty()` trả về `true` khi đối số là `bool` có giá trị `false`.

```php
<?php

declare(strict_types=1);

$value = false;

var_dump(empty($value)); // (bool)true
```



## Float
`empty()` trả về `true` khi một biến là `float` có giá trị `0,0` hoặc `-0,0`.

```php
<?php

declare(strict_types=1);

$value = 0.0;

var_dump(empty($value)); // (bool)true
```



## Int
`empty()` trả về `true` khi đối số là `int` có giá trị `0`.

```php
<?php

declare(strict_types=1);

$value = 0;

var_dump(empty($value)); // // (bool)true
```



## String
`empty()` trả về `true` khi đối số là `chuỗi` có giá trị `''`.

```php
<?php

declare(strict_types=1);

$value = '';

var_dump(empty($value)); // // (bool)true
```

`empty()` cũng trả về `true` khi đối số là `chuỗi` có giá trị `'0'`.

```php
<?php

declare(strict_types=1);

$value = '0';

var_dump(empty($value)); // // (bool)true
```



## SimpleXMLElement
`empty()` trả về `true` khi đối số là một thể hiện của `SimpleXMLElement` được xây dựng từ một chuỗi XML biểu thị một phần tử không có thuộc tính và phần tử con.

```php
<?php

declare(strict_types=1);

$value = new SimpleXMLElement('<foo></foo>');

var_dump(empty($value)); // (bool)true
```



## Kết luận
Không sử dụng `empty()`. Không viết code cho phép một biến, thuộc tính, tham số hoặc giá trị trả về giả định nhiều loại. Sử dụng so sánh loại an toàn.