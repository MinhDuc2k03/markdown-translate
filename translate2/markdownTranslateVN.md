# README 101




## Nó là gì?
[README](https://en.wikipedia.org/wiki/README) là một tập tin văn bản giới thiệu và giải thích dự án. Nó bao gồm thông tin thường được yêu cầu để hiểu dự án nói về cái gì.



## Tại sao tôi cần làm nó?
Nó là một cách dễ dàng để trả lời những câu hỏi mà khán giả của bạn sẽ thường hỏi về cách cài đặt và sử dụng dự án của bạn và cả cách hợp tác với bạn.



## Ai nên làm nó?
Bất cứ ai đang làm một dự án lập trình, đặc biệt nếu mà bạn muốn mọi người khác dùng hoặc đóng góp.



## Khi nào nên làm nó?
Chắc chắn trước khi bạn giới thiệu dự án cho người khác hoặc công khai nó. Bạn có thể muốn tập thói quen biến nó thành [tập tiên bạn tạo đầu tiên](https://tom.preston-werner.com/2010/08/23/readme-driven-development.html) trong một dự án mới.



## Nên để nó ở đâu?
Trong phần cấp cao nhất của dự án. Đây là nơi mà người mới tham gia dự án của bạn sẽ bắt đầu. Dịch vụ lưu trữ lệnh như là [GitHub](https://github.com/), [Bitbucket](https://bitbucket.org/), hoặc [GitLab](https://about.gitlab.com/) cũng sẽ tìm tệp README của bạn và hiển thị nó cùng với danh sách tập tin và các thư mục của dự án của bạn.



## Nên làm nó như thế nào?
Mặc dù READMEs có thể được viết trong bất cứ định dạng tập tin văn bản nào, định dạng tập tin văn bản phổ biến nhất được sử dụng hiện nay là [Markdown](https://en.wikipedia.org/wiki/Markdown). Nó cho phép bạn thêm một số định dạng nhẹ. Bạn có thể học thêm về nó tại [CommonMark website](https://commonmark.org/), trang này cũng có một [trang hướng dẫn tham khảo](https://commonmark.org/help/) hữu ích và một [trang hướng dẫn tương tác](https://commonmark.org/help/tutorial/).

Một số định dạng khác mà bạn có thể thấy là [văn bản thô](https://en.wikipedia.org/wiki/Text_file), [reStructuredText](https://en.wikipedia.org/wiki/ReStructuredText) (thường dùng ở những dự án [Python](https://www.python.org/)), và [Textile](https://en.wikipedia.org/wiki/Textile_(markup_language)).

Bạn có thể dùng bất cứ trình soạn thảo văn bản nào. Hiện có plugin cho chúng (v.d. [Atom](https://github.com/atom/markdown-preview), [Emacs](https://github.com/jrblevin/markdown-mode), [Sublime Text](https://github.com/revolunet/sublimetext-markdown-preview), [Vim](https://github.com/suan/vim-instant-markdown), và [Visual Studio Code](https://code.visualstudio.com/docs/languages/markdown#_markdown-preview)) nó cho phép bạn xem trước Markdown trong khi chỉnh sửa nó.

Bạn cũng có thể sử dụng trình soạn thảo Markdown chuyên dụng như [Typora](https://typora.io/) hoặc trực tuyến như [StackEdit](https://stackedit.io/app) hoặc [Dillinger](http://dillinger.io/). Bạn còn có thể dùng mẫu chỉnh sửa bên dưới.





# Những đề xuất cho một README tốt
Mọi dự án đều một khác nhau, vì vậy xem xét phần nào trong này áp dụng cho phần của bạn. Các phần được sử dụng trong mẫu là gợi ý cho hầu hết mọi dự án mở. Cũng nên nhớ rằng khi mà README có thể quá dài và chi tiết, quá dài tốt hơn là quá ngắn. Nếu bạn nghĩ README quả bạn quá dài, bạn need cân nhắc sử dụng [một dạng tài liệu khác](https://www.makeareadme.com/#more-documentation) thay vì cắt bỏ thông tin.



## Tên
Chọn tên tự giải thích cho dự án.



## Miêu tả
Cho mọi người biết dự án của bạn có thể đặc biệt làm cái gì. Cung cấp bối cảnh hoặc thêm đường link tới bất kỳ tài liệu tham khảo nào mà khách có thể chưa quen với. Một danh sách tiểu mục về **Đặc trưng** hoặc **Ảnh nền** cũng có thể được cho vào. Nếu có lựa chọn thay thế cho dự án của bạn, đây là một nơi tốt để 
liệt kê các yếu tố khác biệt.



## Huy hiệu
Ở trong một số READMEs, bạn có thể thấy một số ảnh truyền tải siêu dữ liệu, chẳng hạn như liệu tất cả các bài kiểm tra của dự án có vượt qua hay không. Bạn có thể dùng [Shields](http://shields.io/) để thêm một số chúng vào README. Nhiều dịch vụ còn có hướng dẫn cách thêm huy hiệu.



## Ảnh
Dựa vào thứ mà bạn đang làm, nó có thể là một ý tưởng tốt để bao gồm ảnh chụp màn hình hoặc thậm chí
là video (bạn sẽ thường thấy GIFs nhiều hơn là video). Công cụ như [ttygif](https://github.com/icholy/ttygif) cũng có thể giúp, nhưng thử [Asciinema](https://asciinema.org/) cho phương pháp phức tạp hơn.



## Cài đặt
Trong một hệ sinh thái cụ thể, nó có thể có một cách chung để cài đặt mọi thứ, như là [Yarn](https://yarnpkg.com/), [NuGet](https://www.nuget.org/), hoặc [Homebrew](https://brew.sh/). Tuy nhiên, cân nhắc khả năng người đọc README của bạn là người mới và muốn được hướng dẫn thêm. Liệt kê các bước cụ thể giúp loại bỏ sự mơ hồ và thu hút người sử dụng dự án của bạn nhanh nhất có thể. Nếu nó chỉ chạy trong một ngữ cảnh cụ thể như phiên bản ngôn ngữ lập trình cụ thể hoặc hệ điều hành hoặc các phần phụ thuộc phải được cài đặt thủ công, cũng thêm phần tiểu mục **Yêu cầu**.



## Cách sử dụng
Tự do sử dụng ví dụ, và hiển thị kết quả mong đợi nếu có thể. Nó hữu ích to have nội tuyến ví dụ nhỏ nhất về cách sử dụng mà bạn có thể chứng minh, đồng thời cung cấp liên kết đến các ví dụ phức tạp hơn nếu chúng quá dài để cho vào README.



## Hỗ trợ
Nói cho mọi người nơi nào mà họ có thể chọn để giúp. Nó có thể là trình theo dõi vấn đề bất kỳ, phòng trò chuyện, địa chỉ email, vân vân.



## Lộ trình
Nếu bạn có những ý tưởng để phát hành về sau, nó là ý tưởng tốt để liệt kê chúng trong phần README.



## Đóng góp
State if you are open to contributions and what your requirements are for accepting them.

For people who want to make changes to your project, it's helpful to have some documentation on how to get started. Perhaps there is a script that they should run or some environment variables that they need to set. Make these steps explicit. These instructions could also be useful to your future self.

You can also document commands to [lint the code](https://stackoverflow.com/questions/8503559/what-is-linting) or [run tests](https://en.wikipedia.org/wiki/Test_automation). These steps help to ensure high code quality and reduce the likelihood that the changes inadvertently break something. Having instructions for running tests is especially helpful if it requires external setup, such as starting a [Selenium](http://www.seleniumhq.org/) server for testing in a browser.



## Tác giả và lời cảm ơn
Show your appreciation to those who have contributed to the project.



## Bằng phép
For open source projects, say how it is [licensed](https://www.makeareadme.com/#license-1).



## Trạng thái dự án
If you have run out of energy or time for your project, put a note at the top of the README saying that development has slowed down or stopped completely. Someone may choose to fork your project or volunteer to step in as a maintainer or owner, allowing your project to keep going. You can also make an explicit request for maintainers.