# Các yếu tố cần xem xét khi chọn Cơ sở dữ liệu
![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd05f286e-16e5-457c-aee3-cc6b960c796d_1600x755.png)



## Khả năng mở rộng
Khả năng mở rộng là khía cạnh quan trọng của bất kỳ cơ sở dữ liệu nào. Nó quyết định hệ thống có thể đáp ứng sự tăng trưởng tốt đến mức nào. Có hai phương pháp mở rộng chính: Dọc và Ngang. mở rộng quy mô theo chiều dọc tăng dung lượng của một máy chủ bằng cách thêm các tài nguyên như bộ nhớ hoặc CPU. Mặt khác, mở rộng quy mô theo chiều ngang liên quan đến việc thêm nhiều máy chủ vào hệ thống.

![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F0eab4887-7057-4552-9895-feabaeb4386e_1600x1164.png)

Cơ sở dữ liệu khác nhau xử lý việc mở rộng quy mô theo nhiều cách khác nhau. Ví dụ, cơ sở dữ liệu quan hệ có thể gặp khó khăn với việc mở rộng quy mô theo chiều ngang, trong khi cơ sở dữ liệu NoSQL thường vượt trội trong lĩnh vực này. Khi chọn cơ sở dữ liệu, bạn nên xem xét mức độ tăng trưởng dự kiến ​​của dự án và cơ sở dữ liệu và nó có thể xử lý việc mở rộng đó tốt như thế nào.

Để đánh giá khả năng mở rộng của cơ sở dữ liệu, trước ta phải hiểu về kiến ​​trúc và nguyên tắc thiết kế của nó. Ví dụ, cơ sở dữ liệu quan hệ lưu trữ dữ liệu trong các bảng có lược đồ được xác định trước và chúng có thể gặp khó khăn trong việc mở rộng quy mô theo chiều ngang do nhu cầu duy trì tính nhất quán trên nhiều máy chủ. Thử thách này có thể dẫn tới tắc nghẽn hiệu suất khi xử lý lượng lớn dữ liệu hoặc khối lượng công việc có lưu lượng truy cập cao.

Về mặt khác, cơ sở dữ liệu NoSQL được thiết kế có tính đến khả năng mở rộng. Nó sử dụng nhiều chiến lược khác nhau, chẳng hạn như phân mảnh và phân vùng, để phân phối dữ liệu trên nhiều máy chủ. Cách tiếp cận này cho phép mở rộng quy mô theo chiều ngang hiệu quả và có thể xử lý tốt hơn khối lượng dữ liệu và lưu lượng truy cập ngày càng tăng. Tuy nhiên, cơ sở dữ liệu NoSQL hy sinh một số mức độ nhất quán dữ liệu để đạt được khả năng mở rộng này.

Cơ sở dữ liệu NewSQL nhằm mục đích kết hợp những ưu điểm tốt nhất của hai bên bằng cách cung cấp khả năng mở rộng của cơ sở dữ liệu NoSQL và tính nhất quán trong giao dịch của cơ sở dữ liệu quan hệ. Các cơ sở dữ liệu này sử dụng các kiến ​​trúc và kỹ thuật tiên tiến để phân phối dữ liệu và duy trì tính nhất quán trên nhiều máy chủ. Nó cho phép mở rộng quy mô theo chiều ngang hiệu quả mà không ảnh hưởng đến tính nhất quán. Tuy nhiên, có những hạn chế cần xem xét. Cơ sở dữ liệu NewSQL có thể thiếu sự trưởng thành của các hệ thống truyền thống, dẫn đến nguồn lực và hỗ trợ của cộng đồng bị hạn chế. Sự phức tạp của chúng có thể tạo ra đường cong học tập dốc hơn cho các nhà phát triển, làm tăng thời gian và công sức cần thiết cho việc triển khai và bảo trì.

Cơ sở dữ liệu chuỗi thời gian, được thiết kế để xử lý dữ liệu dựa trên thời gian, cũng có thể mở rộng quy mô khi khối lượng dữ liệu tăng lên. Chúng sử dụng các kỹ thuật nén và lập chỉ mục chuyên dụng để lưu trữ và truy vấn khối lượng lớn dữ liệu theo chuỗi thời gian một cách hiệu quả, khiến chúng trở thành lựa chọn lý tưởng cho các ứng dụng tạo ra khối lượng lớn thông tin có dấu thời gian, chẳng hạn như IoT (Internet of Things) hoặc hệ thống giám sát.

Khi chọn cơ sở dữ liệu, bạn nên xem xét mức độ tăng trưởng dự kiến ​​của dự án và cơ sở dữ liệu có thể xử lý việc mở rộng đó tốt như thế nào. Đánh giá khả năng mở rộng quy mô theo chiều dọc hoặc chiều ngang của cơ sở dữ liệu và đánh giá hiệu suất của cơ sở dữ liệu khi khối lượng dữ liệu và lưu lượng truy cập ngày càng tăng.



## Hiệu suất
Hiệu suất là một yếu tố thiết yếu khác trong việc lựa chọn cơ sở dữ liệu. Nó tác động trực tiếp đến trải nghiệm của người dùng. Bạn nên cần xem xét hiệu quả truy vấn và sự cân bằng giữa hiệu suất đọc và hiệu suất ghi. Một số cơ sở dữ liệu có thể được tối ưu hóa cho khối lượng công việc đọc nhiều, trong khi những cơ sở dữ liệu khác có thể ưu tiên hiệu suất ghi. Hiểu các yêu cầu hiệu suất cụ thể của dự án sẽ giúp bạn xác định loại cơ sở dữ liệu phù hợp nhất.

Để đánh giá hiệu suất của cơ sở dữ liệu, chúng ta nên bắt đầu bằng việc kiểm tra hiệu quả truy vấn của nó. Cơ sở dữ liệu quan hệ thường cung cấp khả năng truy vấn hiệu quả nhờ lược đồ có cấu trúc và hỗ trợ SQL. Hiệu suất của chúng thường được tối ưu hóa cho các truy vấn phức tạp liên quan đến phép nối và tập hợp. Tuy nhiên, khi khối lượng dữ liệu và độ phức tạp tăng lên, hiệu suất truy vấn có thể giảm, đặc biệt khi xử lý các tập dữ liệu lớn.

Về mặt khác, cơ sở dữ liệu NoSQL có thể cung cấp tốc độ ghi nhanh hơn nhờ mô hình dữ liệu đơn giản và lược đồ linh hoạt hơn. Lợi thế về hiệu suất này có thể đặc biệt có lợi trong các tình huống mà dữ liệu liên tục được tạo và cập nhật, chẳng hạn như các ứng dụng phát trực tuyến hoặc phân tích thời gian thực. Tuy nhiên, cơ sở dữ liệu NoSQL có thể không hiệu quả bằng khi xử lý các truy vấn hoặc tập hợp phức tạp vì chúng thiếu mức hỗ trợ cho SQL và các lược đồ có cấu trúc.

Cơ sở dữ liệu NewSQL nhằm mục đích cung cấp cả khả năng truy vấn hiệu quả và hiệu suất ghi cao bằng cách kết hợp các điểm mạnh của cơ sở dữ liệu quan hệ và cơ sở dữ liệu NoSQL. Nó thường sử dụng các kỹ thuật đổi mới, chẳng hạn như xử lý truy vấn phân tán và lập chỉ mục nâng cao để mang lại khả năng ghi và truy vấn hiệu suất cao. Do đó, cơ sở dữ liệu NewSQL có thể là lựa chọn tốt cho các ứng dụng yêu cầu cả truy vấn phức tạp và hiệu suất ghi cao.

Cơ sở dữ liệu chuỗi thời gian được thiết kế để xử lý dữ liệu dựa trên thời gian và hiệu suất của chúng được tối ưu hóa cho trường hợp sử dụng cụ thể này. Nó sử dụng các kỹ thuật nén và lập chỉ mục chuyên dụng để lưu trữ và truy vấn khối lượng lớn dữ liệu theo chuỗi thời gian một cách hiệu quả. Việc tập trung vào dữ liệu dựa trên thời gian này cho phép cơ sở dữ liệu chuỗi thời gian mang lại hiệu suất cao cho các ứng dụng tạo ra lượng lớn thông tin có dấu thời gian, chẳng hạn như IoT hoặc hệ thống giám sát.

Khi chọn cơ sở dữ liệu, điều quan trọng là phải hiểu các yêu cầu về hiệu suất của dự án. Điều này sẽ ảnh hưởng trực tiếp tới trải nghiệm của người dùng. Xem xét sự cân bằng giữa hiệu suất đọc và ghi cũng như hiệu quả xử lý truy vấn. Bằng cách đánh giá cẩn thận các đặc tính hiệu suất của các loại cơ sở dữ liệu khác nhau trong bối cảnh nhu cầu của dự án, ta có thể chọn cơ sở dữ liệu mang lại trải nghiệm người dùng tối ưu và hỗ trợ sự thành công của ứng dụng.



# Tính nhất quán của dữ liệu
Tính nhất quán của dữ liệu đảm bảo rằng thông tin trong cơ sở dữ liệu vẫn chính xác và cập nhật. Để đạt được tính nhất quán, cơ sở dữ liệu thường dựa vào các thuộc tính ACID (Atomicity, Consistency, Isolation, and Durability (Tính nguyên tử, Tính nhất quán, Cách ly và Độ bền)) và định lý CAP (Consistency, Availability, and Partition tolerance (Tính nhất quán, Tính sẵn có và Dung sai phân vùng)). Các cơ sở dữ liệu khác nhau ưu tiên những khía cạnh này một cách khác nhau, dẫn đến mức độ nhất quán khác nhau.

Để đánh giá cách tiếp cận của cơ sở dữ liệu đối với tính nhất quán của dữ liệu, chúng ta nên bắt đầu bằng cách kiểm tra sự tuân thủ của nó với các thuộc tính ACID. Cơ sở dữ liệu quan hệ thường nhấn mạnh tính nhất quán mạnh mẽ. Nó đảm bảo rằng mọi giao dịch đều duy trì tính toàn vẹn của dữ liệu. Họ đạt được điều này bằng cách triển khai các thuộc tính ACID, trong đó chỉ định rằng các giao dịch là nguyên tử, nhất quán, biệt lập và bền vững.

Định lý CAP phát biểu rằng một hệ thống cơ sở dữ liệu phân tán chỉ có thể đạt được hai trong số ba thuộc tính: tính nhất quán, tính khả dụng và dung sai phân vùng. Định lý này nêu bật sự đánh đổi mà cơ sở dữ liệu phải thực hiện khi đạt được tính nhất quán và nó có thể là một công cụ hữu ích để hiểu mô hình nhất quán của các loại cơ sở dữ liệu khác nhau.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbaea3943-ac0f-4a2a-a52a-40411c088acd_1418x972.png)

Mặc dù định lý CAP được nhiều người biết đến nhưng một mô hình tư duy tốt hơn để làm theo khi đánh giá cơ sở dữ liệu là định lý PACELC(Partition, Availability, Consistency). Định lý PACELC nêu rõ rằng nếu một hệ thống có khả năng chịu phân vùng thì nó phải chọn giữa Tính khả dụng và Tính nhất quán trong quá trình phân vùng mạng cũng như giữa Độ trễ và Tính nhất quán khi mạng hoạt động bình thường. Định lý này nêu bật sự đánh đổi mà cơ sở dữ liệu phải thực hiện khi đạt được tính nhất quán và có thể là một công cụ hữu ích để hiểu mô hình nhất quán của các loại cơ sở dữ liệu khác nhau.

Cơ sở dữ liệu NoSQL thường hướng tới tính nhất quán cuối cùng. Các bản cập nhật dữ liệu cuối cùng sẽ lan truyền trên tất cả các điểm giao trong hệ thống, nhưng chúng không hiển thị ngay lập tức. Cách tiếp cận này cho phép tính sẵn sàng cao hơn và hiệu suất tốt hơn trong các hệ thống phân tán, nhưng nó có thể dẫn đến sự không nhất quán tạm thời giữa các điểm giao.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F5ebe2481-4e5e-4c80-9a8d-a9caf8255a45_1600x1361.png)

Khi chọn cơ sở dữ liệu, hãy xem xét tầm quan trọng của tính nhất quán trong dự án và nó có thể tác động như thế nào đến trải nghiệm người dùng. Đối với một số ứng dụng, chẳng hạn như giao dịch tài chính, tính nhất quán cao là điều cần thiết để đảm bảo tính toàn vẹn dữ liệu và tránh sai sót. Ngược lại, đối với các ứng dụng khác, như nguồn cấp dữ liệu trên mạng xã hội hoặc chỉ mục tìm kiếm, tính nhất quán cuối cùng là đủ vì sự không nhất quán tạm thời ít có khả năng tác động tiêu cực đến trải nghiệm người dùng.

Điều quan trọng là phải hiểu sự cân bằng giữa tính nhất quán, tính khả dụng và dung sai phân vùng khi chọn cơ sở dữ liệu. Bằng cách xem xét cẩn thận các yêu cầu về tính nhất quán của dự án và đánh giá mô hình nhất quán của các loại cơ sở dữ liệu khác nhau, chúng ta có thể chọn cơ sở dữ liệu đáp ứng nhu cầu đồng thời cung cấp trải nghiệm người dùng tốt nhất có thể.



## Mô hình dữ liệu
Mô hình dữ liệu của cơ sở dữ liệu là một yếu tố quan trọng khác cần xem xét khi chọn cơ sở dữ liệu. Nó xác định cách dữ liệu được cấu trúc, lưu trữ và truy vấn. Các yếu tố như tính linh hoạt của lược đồ và hỗ trợ cho các mối quan hệ dữ liệu phức tạp cần được tính đến khi đánh giá mô hình dữ liệu của cơ sở dữ liệu.

Cơ sở dữ liệu quan hệ sử dụng một lược đồ cố định. Nó thực thi một cấu trúc nhất quán trên tất cả các bản ghi. Lược đồ này có thể có lợi cho việc đảm bảo tính toàn vẹn dữ liệu. Nó ngăn việc chèn dữ liệu không phù hợp với cấu trúc đã chỉ định. Tuy nhiên, nó cũng có thể là một hạn chế khi xử lý dữ liệu đa dạng hoặc thay đổi nhanh chóng, vì việc thay đổi lược đồ có thể tốn thời gian và có thể cần thời gian ngừng hoạt động.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Faabc4b05-4235-4667-a2cf-39b46cb43979_1208x1088.png)