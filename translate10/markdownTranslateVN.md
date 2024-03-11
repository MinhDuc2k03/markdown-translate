# Chiến lược bộ nhớ đệm trong Laravel
Tôi nghĩ bộ nhớ đệm là một trong những con dao hai lưỡi trong phát triển ứng dụng PHP.

Một mặt, chiến lược bộ nhớ đệm có thể mang lại hiệu suất đáng kể. Mặt khác, những lợi ích đó có thể phải trả giá bằng sự phức tạp ngày càng tăng trong cơ sở code hoặc cơ sở hạ tầng. Ngoài ra, luôn có mối đe dọa rằng bộ nhớ đệm sẽ cung cấp dữ liệu lỗi thời.

Khi chúng ta nghĩ về rủi ro so với phần thưởng khi nói đến bộ nhớ đệm, tôi khuyên bạn nên đưa ra những lựa chọn cân nhắc để đảm bảo thu được lợi nhuận thành công. Các lựa chọn bộ nhớ đệm phù hợp có liên quan đến mọi thứ liên quan đến cơ sở hạ tầng, bộ kỹ năng của bạn và chức năng của ứng dụng của bạn.

Để tìm hiểu thêm về cách đưa ra quyết định lưu trữ thành công cho ứng dụng của bạn, hãy cùng tìm hiểu về nhiều lớp bộ nhớ đệm có sẵn trong hệ sinh thái Laravel.



## Chiến lược tổng thể cho bộ nhớ đệm
Các ứng dụng vô cùng đa dạng về các vấn đề mà chúng giải quyết, do đó không có lựa chọn lý tưởng duy nhất cho mọi ứng dụng. Thay vào đó, cần có sự hiểu biết vững chắc về logic kinh doanh của ứng dụng và các công nghệ mà nó sử dụng để quyết định điều gì phù hợp cho từng ứng dụng riêng lẻ.

Tôi tuân theo một số nguyên lý khi nghĩ về việc triển khai/hiệu quả bộ nhớ đệm.



## Đầu tiên hãy tối ưu hóa code
Bộ nhớ đệm luôn gây ra chi phí và rủi ro. Sự phức tạp của code là một chi phí và rủi ro là có khả năng cung cấp thông tin không chính xác cho người dùng. Trong nhiều trường hợp, giải pháp tốt hơn là tối ưu hóa ứng dụng để giảm mức sử dụng tài nguyên hoặc thời gian tính toán để chúng ta không phải dựa vào bộ nhớ đệm.

Vì vậy, trong phần còn lại của bài viết này, hãy giả sử ứng dụng của tôi đã được tối ưu hóa hợp lý.



## Thứ hai, chọn (các) lớp bộ đệm của bạn
Điều tôi yêu thích ở các lớp bộ đệm là vì không có công nghệ hoặc mẫu nào có thể giải quyết được tất cả các vấn đề về tốc độ của tôi nên tôi chỉ có thể chọn những lớp dễ triển khai và kiểm tra. Tôi có thể triển khai ngay bây giờ và bổ sung thêm sau khi thấy hợp lý.

Dưới đây là danh sách rộng rãi và chắc chắn là chưa đầy đủ các lớp bộ nhớ đệm có sẵn cho hầu hết các ứng dụng Laravel.


### Bộ nhớ đệm HTML DNS và Webhost
Bộ nhớ đệm cấp độ DNS và webhost là những hoạt động diễn ra hoàn toàn trước các ứng dụng Laravel của chúng tôi; thông thường, toàn bộ yêu cầu được lưu trữ dựa trên URL yêu cầu và tiêu đề. Bộ nhớ đệm này có thể giảm đáng kể chi phí hiển thị HTML của trang, bao gồm mọi tra cứu cần thiết để hiển thị HTML đó.

Kiểu bộ đệm này hoạt động tốt đối với các trang không yêu cầu tùy chỉnh dựa trên dữ liệu người dùng hoặc có thể thêm tùy chỉnh đó sau khi thực tế (ví dụ: thông qua JavaScript). Hiện có nhiều dịch vụ có thể quản lý việc này và chúng có nhiều hành vi khác nhau mà bạn có thể khai thác. Một số ví dụ cơ bản:

- [Bộ đệm Cloudflare](https://developers.cloudflare.com/cache/)
 - [Amazon Cloudfront](https://docs.aws.amazon.com/cloudfront/index.html)
 - Varnish (có sẵn trên nhiều máy chủ hoặc thông qua gói như [Spatie’s Laravel Varnish](https://github.com/spatie/laravel-varnish))

Các dịch vụ này vượt trội trong việc cung cấp nội dung tĩnh (tương tự như CDN) và cũng tiết kiệm rất nhiều tải cho máy chủ khi chúng nắm bắt yêu cầu HTTP đến và trả về phản hồi mà không bao giờ yêu cầu được gửi đến (các) máy chủ web của tôi. Ít yêu cầu hơn đến máy chủ có nghĩa là chu kỳ tải/phản hồi thấp hơn và nhanh hơn.

Tôi có thể điều chỉnh các bộ đệm này và sự tương tác của chúng với ứng dụng của tôi đến từng chi tiết cực kỳ tốt. Nếu bạn muốn có ví dụ, hãy xem [bài đăng này](https://www.troyhunt.com/to-infinity-and-beyond-with-cloudflare-cache-reserve/), trong đó "Have I Been Pwned Operator Troy Hunt" nói về cách họ sử dụng [Dự trữ bộ nhớ đệm Cloudflare](https://developers.cloudflare.com/cache/advanced-configuration/cache-reserve/).


#### Hãy cẩn thận
 - Bộ nhớ đệm phải được xóa khi nội dung có thể đã thay đổi
 - Không thể xử lý vấn đề này ở cấp ứng dụng/code; nó sẽ yêu cầu bạn tương tác với cấu hình máy chủ/DNS
 - Kiểu bộ đệm này không hỗ trợ các tuyến cần phiên hoặc xử lý việc gửi biểu mẫu
 - Bạn có thể cần phải cẩn thận hơn khi tinh chỉnh tiêu đề HTTP theo cách mà hầu hết các trang web thường không yêu cầu



### Bộ nhớ đệm HTML của Laravel
Cấp độ tiếp theo bên dưới cấp độ này là lưu vào bộ đệm trong ứng dụng Laravel của bạn ở cấp độ gần nhất với người dùng: HTML. Tương tự như bộ nhớ đệm máy chủ và DNS, cái này lưu trữ toàn bộ HTML và cho phép bạn tránh các cuộc gọi cơ sở dữ liệu cũng như các cuộc gọi chuyên sâu khác về máy chủ trong nhiều trường hợp.

Tuy nhiên, loại bộ nhớ đệm này cho phép bạn kiểm soát nhiều hơn những gì được lưu vào bộ nhớ đệm và những gì không được lưu vào bộ nhớ đệm. Ví dụ: tôi có thể chọn lưu các tuyến vào bộ đệm dựa trên dữ liệu phiên hoặc yêu cầu. Tôi có thể lưu trữ HTML trang tùy chỉnh cho mỗi người dùng trên mỗi tuyến hoặc theo các tham số GET được cung cấp.

Với loại bộ đệm này, tôi sẽ có nhiều quyền kiểm soát hơn và ít cần phải tương tác với máy chủ hơn, tuy nhiên, mức tăng sẽ không đáng kể.

Hai gói chính thuộc loại này là [Bộ nhớ đệm trang của Joseph Silber](https://github.com/JosephSilber/page-cache) và [ResponseCache của Spatie](https://github.com/spatie/laravel-responsecache).


#### Hãy cẩn thận
 - Tất cả các yêu cầu sẽ đến máy chủ ứng dụng, trừ khi bạn sử dụng bộ đệm Nginx tùy chỉnh trong plugin bộ đệm trang của Joseph Silber
 - Hầu hết loại công cụ bộ nhớ đệm này sẽ không cho phép bạn sử dụng dữ liệu phiên trong các trang
 - cần đảm bảo xóa bộ đệm HTML tĩnh bất cứ khi nào nội dung của các trang đó cập nhật



### Bộ nhớ đệm tích hợp của Laravel
Có một số cơ chế bộ nhớ đệm sẵn có của Laravel siêu hiệu quả và thân thiện với phiên với rất ít độ phức tạp. Tài liệu Laravel khá chuyên sâu, vì vậy hãy nhấp vào các liên kết nếu bạn muốn tìm hiểu thêm:

 - Bộ nhớ đệm cấu hình môi trường
 - Bộ nhớ đệm tuyến đường
 - Xem bộ nhớ đệm



### Bộ nhớ đệm của Laravel
Công cụ [Bộ nhớ đệm của Laravel](https://laravel.com/docs/cache) cung cấp quyền truy cập vào nhiều dịch vụ bộ đệm khác nhau với một API nhất quán. Lớp bộ đệm này cho phép bạn lưu trữ hầu hết mọi giá trị vào bộ đệm, được lưu trữ tại một vị trí được xác định bằng khóa chuỗi—ví dụ: bộ đệm “42” tại khóa “câu-trả-lời-cho-cuộc-sống-vũ-trụ-và-mọi-thứ”. Bộ đệm của Laravel cũng cung cấp nhiều phương pháp tiện lợi để ghi nhớ, hết hạn, phá hủy, khóa và điều tiết bộ đệm.

Thật hoàn hảo khi tôi có một giá trị cụ thể tốn nhiều chi phí để tính toán và tôi biết tất cả các trường hợp mà giá trị đó sẽ bị phá vỡ, một thuật ngữ có nghĩa là “giá trị được lưu trong bộ nhớ đệm sẽ bị xóa để ứng dụng biết tính toán lại giá trị được lưu trong bộ nhớ đệm”.

Hãy xem một ví dụ. Nếu tôi đã lưu vào bộ nhớ đệm tổng số người dùng trong hệ thống của mình thì bộ nhớ đệm này phải bị xóa bất cứ khi nào ứng dụng thêm hoặc xóa một người dùng. Hoặc giả sử tôi đang lưu vào bộ nhớ đệm một phép tính của ngày trong tuần qua có doanh thu bán hàng cao nhất; Tôi sẽ cần phải phá bộ nhớ đệm đó vào lúc nửa đêm cuối tuần.

Một thủ thuật mà tôi thích sử dụng là xây dựng các đối tượng trợ giúp—chỉ là các đối tượng PHP (POPO) cũ đơn giản—giúp tôi quản lý việc tạo và truy xuất tên khóa của mình cũng như các quy tắc xung quanh việc chặn.


#### Hãy cẩn thận
 - Thường rất hẹp trong phạm vi nội dung được lưu vào bộ nhớ đệm; lưu trữ các bit dữ liệu lớn tương tự yêu cầu code hóa bổ sung
 - Yêu cầu kiến ​​thức kinh doanh về thời điểm thiết lập dữ liệu và phá hủy nó


### Ghi nhớ
Ghi nhớ là bộ đệm chỉ tồn tại trong khoảng thời gian của một yêu cầu. Các giá trị đang được lưu vào bộ đệm ở cấp lớp hoặc đôi khi ở cấp yêu cầu nhưng chúng không được lưu trữ trên máy chủ; chúng chỉ được lưu giữ trong bộ nhớ cho yêu cầu hiện tại rồi bị loại bỏ. Do đó, việc ghi nhớ sẽ nhanh chóng thiết lập một khái niệm hoạt động và có rủi ro rất thấp vì giá trị này không thể được sử dụng cho bất kỳ yêu cầu nào khác.


#### Ghi nhớ trong lớp PHP
Việc ghi nhớ một phép tính tốn nhiều tài nguyên trong một đối tượng lớp PHP rất đơn giản:
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

Cái hay của phong cách này là nó chỉ là một mẫu code. Miễn là tôi đang sử dụng cùng một đối tượng `MyPhpClass` được khởi tạo, tôi có thể gọi `$object->getResourceHeavyValue()` bao nhiêu lần tùy thích trong chu trình yêu cầu này và nó sẽ không thực hiện lại phép tính đó nữa.


#### Hãy cẩn thận
 - Chỉ hoạt động trong chu kỳ yêu cầu hiện tại đối với đối tượng hiện tại
 - Khi xảy ra thay đổi ảnh hưởng đến kết quả tính toán của đối tượng đó, đối tượng phải được khởi tạo lại HOẶC giá trị đó phải được bỏ đặt


#### Ghi nhớ trong toàn bộ ứng dụng
Kiểu ghi nhớ này cũng chỉ hoạt động cho chu kỳ yêu cầu hiện tại NHƯNG ở quy mô toàn ứng dụng. [Once](https://github.com/spatie/once) là một gói chắc chắn cho việc này, được viết bởi Taylor Otwell và được Spatie phát hành với sự cho phép của anh ấy.

Bạn có thể gọi `once()` và chuyển nó thành một bao đóng, và bất cứ điều gì xảy ra trong bao đóng đó, nó sẽ chỉ được chạy một lần trong yêu cầu đó bất kể đoạn code đó được gọi bao nhiêu lần.

Bất kể bạn triển khai nó như thế nào, việc ghi nhớ là hoàn hảo cho lần thử đầu tiên vào bộ nhớ đệm trong một ứng dụng khi tôi chưa sẵn sàng bắt đầu đào sâu vào bộ nhớ đệm cường độ cao hơn.


### Bộ nhớ đệm truy vấn cơ sở dữ liệu
Các truy vấn tới cơ sở dữ liệu thường là nguyên nhân lớn nhất khi nói đến thời gian tải trang trong các ứng dụng web. Tôi muốn nhắc lại rằng điều quan trọng là phải tối ưu hóa các truy vấn của bạn trước tiên, nhưng nếu bạn thực sự muốn lưu trữ các truy vấn cơ sở dữ liệu của mình vào bộ nhớ đệm thì bạn có các tùy chọn.

Cơ chế lưu vào bộ nhớ đệm phía cơ sở dữ liệu này có thể tồn tại trong giao diện phần mềm cơ sở dữ liệu (tức là ở phía trước mặt tiền `DB` và `Model`) hoặc ở phía trước cơ sở dữ liệu.

Mẫu phổ biến nhất để lưu các truy vấn cơ sở dữ liệu vào bộ đệm là sử dụng bộ đệm của Laravel, như chúng ta đã nói, để gói các lệnh gọi mô hình hoặc DB đắt tiền và lưu vào bộ nhớ đệm các kết quả.
```php
$states = Cache::remember('states', $seconds = 3600, function () {
    return State::all();
});
```

Ngoài ra còn có các dịch vụ bộ nhớ đệm truy vấn cơ sở dữ liệu xử lý bộ nhớ đệm cho bạn ngay trước cơ sở dữ liệu; [PlanetScale Boost](https://planetscale.com/docs/concepts/query-caching-with-planetscale-boost) cung cấp dịch vụ bộ đệm truy vấn cơ sở dữ liệu trả phí, hãy xem chúng để biết thêm chi tiết và chi tiết triển khai dành riêng cho Laravel!



## Thứ ba, hãy nhớ xóa bộ nhớ đệm của bạn
Nếu tôi đưa bộ nhớ đệm vào ứng dụng của mình, tôi cần đảm bảo rằng mọi thứ được lưu trong bộ nhớ đệm sẽ bị xóa bộ nhớ đệm (“bị hỏng”) bất cứ khi nào dữ liệu trong đó bị cũ—hoặc tôi đặt thời gian hết hạn hợp lý trên bộ nhớ đệm. Nếu chúng tôi không chặn truy xuất bộ đệm, bản trình bày công khai của ứng dụng của chúng tôi sẽ lỗi thời mỗi khi dữ liệu trong ứng dụng thay đổi.

**Dưới đây là hình ảnh về tác động của việc chặn truy xuất bộ nhớ đệm kém:**

>Email sẽ được gửi không phù hợp. Lời chúc mừng giả sẽ được đưa ra. Người dùng sẽ nhập hai lần giao dịch vì đồ họa không cập nhật sau khi họ nhập giao dịch lần đầu tiên. Người dùng sẽ thanh toán hai lần hoặc trả sai số tiền. Mọi người sẽ nhận được đơn đăng ký của bạn miễn phí.

Và cách các lỗi này hiển thị sẽ khác nhau đối với mỗi ứng dụng, điều đó có nghĩa là việc tìm kiếm trên Internet các giải pháp cho các lỗi của bạn sẽ là một thách thức.

Trong khi đó, những loại vấn đề này sẽ khiến người dùng khó chịu hơn nhiều so với việc trang tải chậm.

Sau khi lỗi cuối cùng được phát hiện, nhóm dịch vụ khách hàng sẽ cần trợ giúp để gỡ lỗi này (thường là nhất thời và phụ thuộc vào thời gian).

**Bất cứ khi nào tôi thêm bộ nhớ đệm, tôi đều dành thời gian để hoàn thành tất cả các tác vụ sau:**

1. Thêm bộ nhớ đệm
2. Ghi lại lớp bộ đệm: cách thiết lập nó trong môi trường cục bộ, dàn dựng và sản xuất cũng như cách nó hoạt động dự kiến
3. Thêm tính năng chặn bộ nhớ đệm và/hoặc hết hạn bộ nhớ đệm. Thêm thử nghiệm khẳng định việc phá sản xảy ra.

Việc chặn truy xuất bộ nhớ đệm rất quan trọng bất cứ khi nào tôi xác định khóa tiêu chuẩn hóa để lưu trữ. Hãy xem xét một phần dữ liệu như `sales-this-week`. Nếu tôi đang theo dõi doanh số bán hàng của người dùng trong tuần này, tôi có thể lưu trữ giá trị bộ đệm này bằng khóa `users.[$user->id].sale-this-week`. Điều đó giúp việc phá vỡ chỉ người dùng đó hoặc chỉ doanh số bán hàng của họ trong tuần này hoặc tất cả dữ liệu liên quan đến người dùng trở nên dễ dàng hơn.



### Một giải pháp chặn bộ nhớ đệm: Xóa bộ nhớ đệm
Loại bỏ bộ đệm là một cơ chế cấp trình điều khiển bộ đệm để loại bỏ các cặp khóa/giá trị cũ hoặc cũ. Điều này giữ cho kích thước bộ đệm tổng thể nhỏ hơn, dẫn đến sử dụng ít bộ nhớ hơn và thời gian phản hồi bộ đệm nhanh hơn.

Một chiến lược đặt tên bộ nhớ đệm mà tôi thích sử dụng chỉ có thể thực hiện được bằng cách xóa bộ nhớ đệm, vì vậy tôi rất hâm mộ. Về cơ bản, nếu tôi có thể biết liệu bộ đệm có bị vô hiệu hóa hay không (được đánh dấu là đã lỗi thời) hoàn toàn dựa trên dữ liệu trong mô hình Eloquent, thì tôi có thể sử dụng giá trị `update_at` của mô hình đó để tự động bỏ qua các phiên bản trước đây.

Tôi có thể tận dụng việc xóa bộ nhớ đệm trong chiến lược này bằng cách xây dựng các khóa của mình để bao gồm dấu thời gian, như `users.[$user->id].[$user->updated_at].sale-this-week`. Tôi sẽ đảm bảo rằng bất cứ khi nào việc bán hàng cho người dùng diễn ra, tôi `$user->touch()`, `User` chịu trách nhiệm bán hàng, sẽ cập nhật dấu thời gian `update_at` của mình. Lần tiếp theo khi tôi cố gắng lấy doanh số bán hàng của người dùng đó, việc tra cứu `doanh số tuần này` sẽ không tìm thấy kết quả khớp và sẽ tính toán và lưu vào bộ đệm giá trị mới cho `doanh số tuần này`.

Vì tôi không cố ý hủy các cặp khóa/giá trị cũ cho hoạt động bán hàng của người dùng cũ nên tôi cần đảm bảo rằng các hồ sơ bán hàng bị loại bỏ để giữ cho bộ nhớ đệm của tôi hoạt động nhanh chóng và bộ nhớ máy chủ của tôi luôn trống—đó là lý do tại sao về cơ bản, phương pháp cụ thể này yêu cầu tôi sử dụng xóa bộ nhớ đệm nếu tôi muốn nó có hiệu quả.

- [Cơ chế trục xuất của Redis](https://redis.io/docs/reference/eviction/)
 - [Cơ chế trục xuất của Memcached](https://www.dragonflydb.io/faq/what-happens-when-memcached-is-full)

Nếu tôi không muốn định cấu hình trình điều khiển bộ đệm của mình để xóa bộ đệm, trong bộ lập lịch của Laravel, tôi có thể chạy `php artisan cache:clear` cho trình điều khiển hoặc các thẻ mà tôi muốn xóa với tần suất hợp lý.

*Cảm ơn [Tony](https://github.com/tonysm) đã nhắc tôi rằng vẫn tồn tại chính sách xóa bộ nhớ đệm.*



## Tách biệt bộ đệm dữ liệu vĩnh viễn và bộ nhớ đệm dữ liệu có thể thay đổi được
Yêu cầu về vòng đời dữ liệu có thể khuyến khích tôi sử dụng các cơ chế bộ đệm khác nhau.

Hãy xem xét một ứng dụng sử dụng dịch vụ bên ngoài để lấy tọa độ GPS của các địa chỉ được nhập vào hệ thống. Khi không thể ánh xạ một vị trí địa chỉ, ứng dụng sẽ lưu chuỗi địa chỉ đó vào `danh sách bỏ qua`. Nó sẽ không thực hiện lại lệnh gọi API cho địa chỉ đó tới dịch vụ bên ngoài đó.

Một giải pháp hiệu quả hơn là lưu trữ dữ liệu hợp lệ được trả về từ dịch vụ GPS đó. Rất hiếm khi một địa chỉ thay đổi vị trí GPS của nó. Lần tới khi tôi cố gắng tra cứu địa chỉ đó, tôi có thể sử dụng giá trị được lưu trong bộ nhớ đệm cục bộ của mình! Bằng cách đó, tôi có thể tiết kiệm cả thời gian của yêu cầu HTTP và chi phí sử dụng dịch vụ bên ngoài đó.

Trong cả hai ví dụ trên, tôi không muốn dữ liệu này bị hủy khi tôi gõ `php artisan cache:clear`. Điều này cho thấy rằng tôi muốn có bộ đệm dữ liệu lâu dài hơn cho các cặp khóa/giá trị này. Có thể phù hợp nếu sử dụng trình điều khiển bộ nhớ đệm khác mà tôi chưa bao giờ xóa hoặc bộ nhớ đệm liên tục trên một máy chủ riêng biệt. Nếu vòng đời của dữ liệu này có thể vượt quá độ dài vòng đời của máy chủ ứng dụng này thì việc lưu trữ dữ liệu đó trong kho dữ liệu cố định như cơ sở dữ liệu của ứng dụng có thể hợp lý nhất.



# "Cache kết luận"
Tôi hy vọng điều này đã cung cấp một bản tóm tắt ban đầu về phạm vi rộng và các trường hợp sử dụng tốt nhất cho các cơ chế bộ đệm khác nhau trong phát triển ứng dụng Laravel. Tôi khuyên bạn nên lưu vào bộ nhớ đệm trong các lớp ứng dụng của mình, nơi nó mang lại hiệu suất tăng đáng kể và mức độ phức tạp khi triển khai là hợp lý.

Trong một bài đăng sau, tôi sẽ chia sẻ một số mẫu code hóa giúp việc sử dụng Ghi nhớ và `Cache` trở nên thân thiện hơn.