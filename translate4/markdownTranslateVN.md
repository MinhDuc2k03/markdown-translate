# Giải thích Xác thực - Mật khẩu, Phiên, Cookie, Mã thông báo, JWT, SSO, OAuth



## Xác thực không cần mật khẩu
Cho đến nay, chúng ta đã đề cập đến ba loại xác thực: xác thực cơ bản HTTP, xác thực cookie phiên và xác thực dựa trên mã thông báo. Tất cả đều yêu cầu mật khẩu. Tuy nhiên, có nhiều cách khác để chứng minh danh tính của bạn mà không cần mật khẩu.

Khi nói đến xác thực, có ba yếu tố cần xem xét:

 - Yếu tố về kiến thức: Thứ gì bạn biết, chẳng hạn như mật khẩu của bạn

 - Yếu tố về sở hữu: Thứ gì bạn sở hữu, chẳng hạn như thiết bị hoặc số điện thoại của bạn

 - Yếu tố về kế thừa: Điều gì độc đáo đối với bạn, chẳng hạn như đặc điểm sinh trắc học của bạn

Mật khẩu nằm trong mục “điều gì đó bạn biết”. One-Time Passwords (OTP) chứng minh rằng người dùng sở hữu điện thoại di động hoặc thiết bị. trong khi xác thực sinh trắc học chứng minh "thứ gì đó độc đáo dành cho bạn".



## Mật khẩu dùng một lần (OTP)
Mật khẩu dùng một lần (OTP) được sử dụng rộng rãi như là một phương pháp xác thực an toàn hơn. Không giống như mật khẩu tĩnh, có thể được sử dụng lại, OTPs có giá trị trong một thời gian giới hạn, thường là một vài phút. Đây có nghĩa là mặc dù nếu ai đó chặn OTP của bạn, họ sẽ không thể sử dụng nó để đăng nhập sau đó. Thêm vào đó, OTP yêu cầu “thứ gì đó bạn sở hữu” cũng như là “điều gì đó bạn biết” để đăng nhập vào. Nó có thể là số điện thoại hoặc địa chỉ email mà người dùng có quyền truy cập, khiến tin tặc hacker khó lấy cắp hơn.

Tuy nhiên, điều quan trọng cần lưu ý là sử dụng SMS làm phương thức gửi OTP có thể kém an toàn hơn các phương thức khác. Điều này là do tin nhắn SMS có thể bị tin tặc hacker chặn hoặc chuyển hướng, đặc biệt nếu số điện thoại của người dùng đã bị xâm phạm. Ở một số trường hợp, người tấn công đã có thể chiếm đoạt số điện thoại bằng cách thuyết phục nhà cung cấp dịch vụ di động chuyển số này sang thẻ SIM mới. một khi người tấn công có quyền kiểm soát số điện thoại, họ có thể chặn bất kỳ OTP gửi qua SMS. Vì lý do này, bạn nên sử dụng các phương thức gửi thay thế, chẳng hạn như email hoặc ứng dụng dành cho thiết bị di động, bất cứ khi nào có thể.

Dưới đây là cách OTP hoạt động chi tiết hơn:

Bước1: Người dùng muốn đăng nhập vào một trang web và được yêu cầu nhập tên người dùng, số điện thoại di động hoặc email.

Bước2: Máy chủ tạo một OTP có thời gian hết hạn.

Bước3: Máy chủ gửi OTP đó cho thiết bị của người dùng qua SMS hoặc email.

Bước4: Người dùng nhập OTP nhận được vào hộp đăng nhập.

Bước5-6: Máy chủ so sánh OTP được tạo với OTP mà người dùng đã nhập. Nếu chúng khớp nhau, đăng nhập được cấp.

![stsps](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fb27865df-e833-47c8-9340-cba5510a90a0_1600x1069.png)

Ngoài ra, khóa phần cứng hoặc phần mềm có thể được sử dụng để tạo OTP cho xác thực đa yếu tố (MFA). Ví dụ. 2FA của Google sử dụng khóa phần mềm tạo OTP mới cứ sau 30 giây. Khi đăng nhập vào, người dùng nhập mật khẩu và OTP hiện tại được hiển thị trên thiết bị của họ. Điều này bổ sung thêm một lớp bảo mật vì tin tặc sẽ cần quyền truy cập vào thiết bị của người dùng để đánh cắp OTP. Thông tin thêm về MFA sau.



## SSO (Đăng nhập một lần)
Đăng nhập một lần (SSO) là phương thức xác thực người dùng cho phép chúng ta truy cập vào nhiều hệ thống hoặc ứng dụng bằng một bộ thông tin xác thực duy nhất. SSO hợp lý hóa quy trình đăng nhập, cung cấp trải nghiệm người dùng liền mạch trên nhiều nền tảng khác nhau.

Quá trình SSO chủ yếu dựa vào máy chủ Dịch vụ xác thực trung tâm (CAS). Dưới đây là bảng phân tích từng bước của quy trình SSO:

 - Khi chúng ta cố gắng đăng nhập vào một ứng dụng, như là Gmail, chúng ta được chuyển hướng đến máy chủ CAS.

 - Máy chủ CAS xác minh thông tin đăng nhập của chúng ta và tạo Ticket Granting Ticket (TGT). TGT này sau đó được lưu trữ trong Ticket Granting Cookie (TGC) trên trình duyệt của chúng ta, đại diện cho phiên toàn cầu của chúng ta.

 - CAS tạo Service Ticket (ST) cho lần truy cập Gmail của chúng ta và chuyển hướng chúng tôi quay lại Gmail bằng ST.

 - Gmail sử dụng ST để xác thực thông tin đăng nhập của chúng tôi với máy chủ CAS. Sau khi xác thực xong chúng ta có thể truy cập Gmail.

Khi chúng ta muốn truy cập một ứng dụng khác, như YouTube, quy trình này được đơn giản hóa:

- Vì chúng ta đã có TGC từ thông tin đăng nhập Gmail của mình nên CAS nhận ra trạng thái xác thực của chúng ta.
- CAS tạo ST mới để truy cập YouTube và chúng ta có thể sử dụng YouTube mà không cần nhập lại thông tin đăng nhập của mình.

Quá trình này giúp giảm nhu cầu ghi nhớ và nhập nhiều bộ thông tin xác thực cho các ứng dụng khác nhau.

![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fff52780c-e94e-4d80-a083-7c9cbead8b6f_1600x1473.png)

Có các giao thức khác nhau hỗ trợ SSO:

 - SAML (Security Assertion Markup Language) được sử dụng rộng rãi trong các ứng dụng doanh nghiệp. SAML truyền đạt dữ liệu xác thực và ủy quyền ở định dạng XML.

 - OIDC (OpenID Connect) phổ biến trong các ứng dụng tiêu dùng. OIDC xử lý xác thực thông qua JSON Web Tokens (JWT) và xây dựng trên OAuth 2.0 framework. Thông tin thêm về điều này trong phần tiếp theo.

Cho các ứng dụng mới, OIDC được dùng nhiều hơn. Nó hỗ trợ nhiều loại máy khách khác nhau, bao gồm máy khách dựa trên web, thiết bị di động và [JavaScript](https://openid.net/developers/how-connect-works/).

SSO cung cấp phương thức xác thực an toàn và hợp lý, mang lại trải nghiệm người dùng liền mạch bằng cách chỉ yêu cầu một bộ thông tin xác thực cho nhiều ứng dụng. Cách tiếp cận này tăng cường bảo mật thông qua việc sử dụng mật khẩu mạnh, duy nhất và giảm rủi ro lừa đảo. Nó cũng giảm thiểu gánh nặng hành chính cho bộ phận CNTT.



## OAuth 2.0 và OpenID Connect (OIDC)
​Mặc dù OAuth 2.0 chủ yếu là một framework ủy quyền, nó có thể được sử dụng cùng với OpenID Connect (OIDC) cho mục đích xác thực. OIDC là lớp xác thực được xây dựng dựa trên OAuth 2.0, cho phép xác minh danh tính người dùng và cấp quyền truy cập có kiểm soát vào các tài nguyên được bảo vệ.

Khi sử dụng "Đăng nhập với Google" hoặc các tính năng tương tự, OAuth 2.0 và OIDC phối hợp với nhau để hợp lý hóa quy trình xác thực. OIDC cung cấp dữ liệu nhận dạng người dùng dưới dạng JSON Web Token (JWT) được tiêu chuẩn hóa. Web Token này chứa thông tin về người dùng được xác thực, cho phép ứng dụng của bên thứ ba tạo hồ sơ người dùng mà không yêu cầu quy trình đăng ký riêng.

Trong thiết lập này, [OAuth 2.0](https://oauth.net/2/) cung cấp "quyền truy cập được ủy quyền an toàn" bằng cách phát hành mã thông báo có thời hạn sử dụng ngắn hạn thay vì mật khẩu, cho phép các dịch vụ của bên thứ ba truy cập các tài nguyên được bảo vệ với sự cho phép của chủ sở hữu tài nguyên. Phương pháp này tăng cường bảo mật vì dịch vụ của bên thứ ba không xử lý hoặc lưu trữ trực tiếp mật khẩu của người dùng.

Sơ đồ dưới đây cho thấy cách hoạt động của giao thức trong trường hợp “Đăng nhập bằng Google”.

![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F75a1f3e9-7bad-410a-b059-66ccd6189f6b_1600x998.png)

Trong ví dụ “Đăng nhập bằng Google”, OAuth 2.0 xác định bốn vai trò:

1. Chủ sở hữu tài nguyên: Người dùng cuối, người kiểm soát quyền truy cập vào dữ liệu cá nhân của họ.

2. Máy chủ tài nguyên: Máy chủ Google lưu trữ hồ sơ người dùng dưới dạng tài nguyên được bảo vệ. Nó sử dụng mã thông báo truy cập để đáp ứng các yêu cầu tài nguyên được bảo vệ, đảm bảo rằng chỉ những dịch vụ được ủy quyền mới có thể truy cập dữ liệu.

3. Máy khách: Thiết bị (PC hoặc điện thoại di động) thay mặt chủ sở hữu tài nguyên đưa ra yêu cầu. Thiết bị này đại diện cho ứng dụng của bên thứ ba đang tìm kiếm quyền truy cập vào dữ liệu của người dùng.

4. Máy chủ ủy quyền: Máy chủ ủy quyền của Google phát hành mã thông báo cho khách, quản lý việc trao đổi mã thông báo an toàn giữa máy chủ tài nguyên và khách.

OAuth 2.0 cung cấp bốn loại cấp quyền để phù hợp với các tình huống khác nhau:

1. Cấp mã ủy quyền: Chế độ đầy đủ và linh hoạt nhất, phù hợp với hầu hết các loại ứng dụng. Thêm chi tiết dưới.

2. Cấp quyền ngầm: Được thiết kế cho các ứng dụng chỉ có giao diện người dùng, chẳng hạn như ứng dụng một trang hoặc ứng dụng dành cho thiết bị di động. Điều này không còn được khuyến khích nữa. Thêm chi tiết dưới .

3. Cấp thông tin xác thực mật khẩu của chủ sở hữu tài nguyên: Sử dụng khi người dùng tin tưởng cấp thông tin xác thực của họ cho ứng dụng của bên thứ ba, chẳng hạn như ứng dụng di động đáng tin cậy.

4. Cấp thông tin xác thực của khách hàng: Thích hợp cho các trường hợp không có giao diện người dùng, như công cụ dòng lệnh hoặc giao tiếp giữa máy chủ với máy chủ, trong đó không cần tương tác với chủ sở hữu tài nguyên.

Tiêu chuẩn này cung cấp nhiều chế độ để đáp ứng các tình huống và yêu cầu ứng dụng khác nhau, đảm bảo tính linh hoạt và khả năng thích ứng cho các tình huống khác nhau.

Việc cấp mã ủy quyền là một ví dụ đáng để xem xét. Thông số kỹ thuật cho ba loại cấp phép còn lại có sẵn trong [RFC-6749](https://www.rfc-editor.org/rfc/rfc6749).

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fce0b4f94-1fae-4d70-a71e-1f82ef93220c_1600x1257.png)