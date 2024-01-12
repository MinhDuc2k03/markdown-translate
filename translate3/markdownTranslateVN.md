# TẬP34: Phiên, cookie, JWT, token, SSO, và OAuth



## Mã thông báo, Cookie, Phiên
Phiên, cookie, JWT, token, SSO, và OAuth 2.0 - chúng là gì?

![The San Juan Mountains are beautiful! (lol)](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Ff455a257-db74-4120-a736-b62d3c374422_1318x1536.jpeg)

Các thuật ngữ này đều liên quan tới quản lý danh tính người dùng. Khi bạn đăng nhập vào một trang web, bạn tuyên bố bạn là ai (nhận biết). Danh tính của bạn sẽ được xác minh (xác thực). Và bạn được cấp các quyền cần thiết (ủy quyền). Nhiều giải pháp đã đề xuất trong quá khứ và danh sách này vẫn tiếp tục tăng lên.

Từ đơn giản đến phức tạp, đây là hiểu biết của tôi về quản lý danh tính người dùng:

- WWW-Authenticate là phương pháp cơ bản nhất. Bạn được yêu cầu tên người dùng và mật khẩu từ trình duyệt. 
Do không thể kiểm soát vòng đời đăng nhập, nó được hiếm khi sử dụng ngày nay.

- Kiểm soát tốt hơn vòng đời đăng nhập là session-cookie. Máy chủ duy trì lưu trữ phiên, và trình duyệt giữ ID của phiên. Cookie thường chỉ hoạt động với các trình duyệt và nó không thân thiện với ứng dụng di động.

- Để giải quyết vấn đề về tương thích, mã thông báo có thể được sử dụng. Máy khách gửi mã thông báo cho máy chủ, và máy chủ xác nhận mã thông báo đấy. Nhược điểm là mã thông báo cần phải được mã hóa và giải mã, việc này có thể tốn thời gian

- JWT là một cách tiêu chuẩn để đại diện cho mã thông báo. Thông tin này có thể được xác minh và tin cậy vì nó được ký bằng chữ ký số. Vì JWT chứa chữ ký đó, mình không cần lưu thông tin phiên ở phía máy chủ.

- Bằng cách sử dụng SSO (đăng nhập một lần), bạn có thể đăng nhập chỉ một lần cho nhiều trang web. Nó sử dụng CAS (central authentication service) để duy trì thông tin chéo trang web

- Bằng cách sử dụng OAuth 2.0, bạn có thể ủy quyền cho một trang web để truy cập thông tin của bạn trên trang web khác



## CDN Là Gì? Nó Hoạt Động Như Thế Nào?
[![What Is A CDN? How Does It Work?](https://i.ytimg.com/vi/RI9np1LWzqw/maxresdefault.jpg)](https://www.youtube.com/watch?v=RI9np1LWzqw)



## Làm thế nào để tìm hiểu hệ thống thanh toán?
![How To Learn Payment?](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F1f8f2f27-1cac-4e19-a190-cec41a7e9d71_1351x1536.jpeg)



## Nền tảng bán vé trực tuyến
Tuần trước, Ticketmaster đã tạm dừng bán vé công cộng cho tour của Taylor Swift vì nhu cầu cực kỳ cao đối với hệ thống bán vé.

Nó là một vấn đề thú vị, nên chúng tôi đã thực hiện một số nghiên cứu về chủ đề này. Sơ đồ dưới đây cho thấy sự phát triển của hệ thống đặt vé tàu Trung Quốc trực tuyến.

![Tickets booking system](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F8456d0b2-5db7-4bb4-99b3-a7474a6d945f_1280x1616.jpeg)

Hệ thống đặt vé tàu Trung Quốc trực tuyến có những thách thức tương tự như hệ thống Ticketmaster:

1. Số lượt truy cập đồng thời rất cao trong giờ cao điểm.

2. QPS để kiểm tra số vé còn lại và đơn đặt hàng rất cao

3. Có rất nhiều bot

**Các giải pháp**

1. **Tách biệt các yêu cầu đọc và ghi.** Bởi vì những người dùng lo lắng liên tục làm mới trang web để kiểm tra xem còn vé không, hệ thống có thể chịu áp lực rất lớn.<br>
Để xử lý việc tính toán và truy vấn trong bộ nhớ, các thành phần vé còn lại đã được chuyển hoàn toàn sang GemFire. Nó có thể nhét vé tàu của cả nước vào bộ nhớ vào vài Gigabyte.<br>
Ngoài ra, thành phần truy vấn đơn hàng đã được chuyển sang GemFire ​​để giảm tải cho cơ sở dữ liệu đơn hàng. Hadoop được sử dụng để lưu trữ các đơn đặt hàng lịch sử.

2. **Tận dụng đám mây công cộng để có dung lượng linh hoạt.**

3. **Cấm bot.** Nó làm giảm lưu lượng truy cập tới 95%.

4. **tăng băng thông** của hệ thống.

5.  **Tăng tính khả dụng của hệ thống** bằng cách thiết lập thêm trung tâm dữ liệu ở các thành phố khác nhau.

6. **Thiết kế nhiều kế hoạch khẩn cấp.**

Lưu ý: các con số dựa trên ước tính back-of-the-envelope (không phải dữ liệu chính thức).



## Có giải pháp thay thế cho dịch vụ Twitter không?
Dịch vụ **mạng xã hội phi tập trung** là gì?

Sơ đồ dưới đây so sánh giữa Twitter và Mastodon.

![What is Decentralized Twitter](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Fa4a87ec5-ed9d-4132-a0e0-29c91e272e50_1280x2145.jpeg)

Người ta nói rằng nền tảng truyền thông xã hội mới Truth Social của Trump đang sử dụng Mastodon.

Mastodon chạy dịch vụ mạng xã hội **tự lưu trữ**. Nó miễn phí và **không có quảng cáo**. MAU (Monthly Active Users) của nó tăng từ 500k vào tháng 10 đến 1 triệu vào tháng 11, sau khi Elon Musk tiếp quản Twitter.

Không giống Twitter, máy chủ của họ thuộc về công ty Twitter, những máy chủ của Mastodon không thuộc về bất kỳ công ty nào. Mạng của nó bao gồm các máy chủ (phiên bản) từ các tổ chức khác nhau.

Khi người dùng đăng ký, họ phải chọn một máy chủ để bắt đầu. Vì các máy chủ đồng bộ hóa với nhau, người dùng vẫn có thể nhận được bản cập nhật từ các máy chủ khác.

Bởi vì mạng được điều hành bởi các tình nguyện viên, công ty chỉ có **một nhân viên** - người sáng lập là Eugen Rochko. Nó được điều hành bởi **gây quỹ cộng đồng** và hiện được 3500 người hỗ trợ.