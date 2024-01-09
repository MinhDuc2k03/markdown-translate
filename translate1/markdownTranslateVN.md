# Tổng quan
Hầu hết tất cả các ứng dụng Markdown hỗ trợ những cú pháp cơ bản nêu ra trong tài liệu thiết kế Markdown nguyên bản. Đây là những biến thể nhỏ và sự khác biệt giữa bộ xử lý Markdown — Những điều đó được ghi lại nội tuyến khi nào có thể.





# Tiêu đề
Để tạo tiêu đề, thêm ký hiệu số (`#`) trước một từ hoặc chữ. Số lượng ký hiệu số bạn sử dụng phải tương ứng với tiêu đề cấp độ. Ví dụ, đễ tạo ra một tiêu đề cấp độ ba (`<h3>`), dùng ba hiệu số (v.d., `### My Header`).

| Markdown             | HTML                          | Đầu ra                      |
| -------------------- | ----------------------------- | --------------------------- |
|# Tiêu đề cấp độ 1|`<h1>Tiêu đề cấp độ 1</h1>`|<h1>Tiêu đề cấp độ 1</h1>|
|# Tiêu đề cấp độ 2|`<h2>Tiêu đề cấp độ 2</h2>`|<h2>Tiêu đề cấp độ 2</h2>|
|# Tiêu đề cấp độ 3|`<h3>Tiêu đề cấp độ 3</h3>`|<h3>Tiêu đề cấp độ 3</h3>|
|# Tiêu đề cấp độ 4|`<h4>Tiêu đề cấp độ 4</h4>`|<h4>Tiêu đề cấp độ 4</h4>|
|# Tiêu đề cấp độ 5|`<h5>Tiêu đề cấp độ 5</h5>`|<h5>Tiêu đề cấp độ 5</h5>|
|# Tiêu đề cấp độ 6|`<h6>Tiêu đề cấp độ 6</h6>`|<h6>Tiêu đề cấp độ 6</h6>|



## Cú pháp thay thế
Ngoài ra, trên dòng ở dưới hàng chữ, thêm bất kỳ số lượng ký tự `==` cho tiêu đề cấp độ 1 hoặc ký tự `--` cho tiêu đề cấp độ 2.

| Markdown                              | HTML                          | Đầu ra                      |
| ------------------------------------- | ----------------------------- | --------------------------- |
|Tiêu đề cấp độ 1<br>===============|`<h1>Tiêu đề cấp độ 1</h1>`|<h1>Tiêu đề cấp độ 1</h1>|
|Tiêu đề cấp độ 2<br>---------------|`<h2>Tiêu đề cấp độ 2</h2>`|<h2>Tiêu đề cấp độ 2</h2>|



## Thực hành tốt nhất cho mở đầu
Các ứng dụng Markdown không thống nhất cách xử lý phần không có khoảng cách giữa kí hiệu số (`#`) và tên tiêu đề. Để tương thích, luôn để một khoảng cách giữa kí hiệu số và tên tiêu đề.

| ✅ Làm như thế này |❌ Không làm như thế này |
| ------------------- | ----------------------- |
|# Đây là tiêu đề |#Đây là tiêu đề      |

Bạn cũng nên để dòng trống sau và trước để tương thích.

| ✅ Làm như thế này |❌ Không làm như thế này |
| ------------------- | ----------------------- |
|Thử để một dòng trống sau khi...<br><br># Tiêu đề<br><br>...và sau tiêu đề.|Khi không có dòng trống, cái này có khi nhìn không đúng lắm.<br># Tiêu đề<br>Đừng làm như thế này!|





# Đoạn văn
Để tạo một đoạn văn, dùng một dòng trống để chia ra một hoặc nhiều dòng chữ.

| Markdown                         | HTML                     | Đầu ra                 |
| -------------------------------- | ------------------------ | ---------------------- |
|Tôi rất thích dùng Markdown.<br><br>Tôi nghĩ tôi sẽ dùng nó để định dạng tất cả những tài liệu của tôi kể từ bây giờ.|`<p>Tôi rất thích dùng.</p>`<br><br>`<p>Tôi nghĩ tôi sẽ dùng nó để định dạng tất cả những tài liệu của tôi kể từ bây giờ.</p>`|Tôi rất thích dùng.<br><br>Tôi nghĩ tôi sẽ dùng nó để định dạng tất cả những tài liệu của tôi kể từ bây giờ.|



## Thực hành tốt nhất cho đoạn văn
Trừ khi [đoạn văn ở trong danh sách](https://www.markdownguide.org/basic-syntax/#paragraphs), đừng thụt lề đoặn văn bằng khoảng cách hoặc tabs.

**Ghi chú:** If you need to indent paragraphs in the output, see the section on how to [indent (tab)](https://www.markdownguide.org/hacks/#indent-tab).

| ✅ Làm như thế này |❌ Không làm như thế này |
| ------------------- | ----------------------- |
|Đừng thêm tabs hoặc khoảng cách trước đoạn văn.<br><br>Giữ cho các dòng căn lề trái như thế này.|    Thế này có thể dẫn đến vấn đề về định dạng.<br><br>  Đừng thêm tabs hoặc khoảng cách trước đoạn văn.|





# Ngắt dòng
Để tạo ngắt dòng hoặc dòng mới (`<br>`), kết thúc dòng với hai hoặc hơn khoảng cách, rồi đánh tiếp.

| Markdown                         | HTML                     | Đầu ra                 |
| -------------------------------- | ------------------------ | ---------------------- |
|Đây là dòng thứ nhất.<br>và đây là dòng thứ hai.|`<p>Đây là dòng thứ nhất.<br>`<br>`và đây là dòng thứ hai.</p>`|Đây là dòng thứ nhất.<br>và đây là dòng thứ hai.|



## Thực hành tốt nhất cho ngắt dòng
Bạn có thể dùng hai hoặc hơn khoảng cách (thường gọi là "dấu cách ở cuối") để ngắt dòng cho hầu hết mọi ứng dụng Markdown, nhưng nó gây tranh cãi. Nó khó để nhìn dấu cách ở trong một trình soạn thảo mã nguồn, và nhiều người accidentally hoặc cố tình thêm hai dấu cách từng chữ . Bởi vì lí do này, bạn có khi cần dùng thứ gì đó khác hơn là dấu cách ở cuối để ngắt dòng. Nếu ứng dụng Markdown của bạn [hỗ trợ HTML](https://www.markdownguide.org/basic-syntax/#html), bạn có thể dùng thẻ HTML `<br>`.

Để tương thích, dùng dấu cách hoặc thẻ HTML `<br>` ở cuối dòng.

Có hai lựa chọn khác tôi không khuyến cáo dùng. CommonMark và một số 
ngôn ngữ đánh dấu nhẹ cho bạn đánh dấu gạch chéo ngược (`\`) ở cuối dòng, nhưng không phải tất cả các ứng dụng Markdown hỗ trợ cái này, thế nên nó không phải là lựa chọn tuyệt vời từ một quan điểm tương thích. Và ít nhất một số ngôn ngữ đánh dấu nhẹ không yêu cầu cái gì ở cuối dòng — chỉ cần gõ xuống và nó sẽ tạo ngắt dòng.

| ✅ Làm như thế này |❌ Không làm như thế này |
| ------------------- | ----------------------- |
|Dòng đầu với hai dấu cách sau đó.<br>Và dòng tiếp theo.<br><br>Dòng đầu với thẻ HTML sau đó.`<br>`<br>Và dòng tiếp theo.|Dòng đầu với dấu gạch chéo ngược sau đó.`\`<br>Và dòng tiếp theo.<br><br>Dòng đầu với không có gì sau đó.<br>Và dòng tiếp theo.|





# Nhấn mạnh
Bạn có thể thêm nhấn mạnh bằng làm cho chữ in đậm hoặc in nghiêng.



## In đậm
Để in đậm chữ, thêm hai dấu hoa thị hoặc dấu gạch dưới trước và sau chữ hoặc cụm từ. Để in đậm một chữ giữa từ để nhấn mạnh, thêm hai dấu hoa thị không có dấu cách quanh các chữ.

| Markdown                     | HTML                                      | Đầu ra                     |
| ---------------------------- | ----------------------------------------- | -------------------------- |
| `Tôi chỉ yêu **chữ in đậm**.`| `Tôi chỉ yêu <strong>chữ in đậm</strong>.`| Tôi chỉ yêu **chữ in đậm**.|
| `Tôi chỉ yêu __chữ in đậm__.`| `Tôi chỉ yêu <strong>chữ in đậm</strong>.`| Tôi chỉ yêu __chữ in đậm__.|
| `Tình yêu **thật** táo bạo`  | `Tình yêu<strong>thật</strong>táo bạo`    | Tình yêu **thật** táo bạo  |



## Thực hành tốt nhất cho in đậm
Những ứng dụng Markdown không thống nhất về cách xử lý dấu gạch dưới ở giữa từ. 
Để tương thích, dùng dấu hoa thị để in hoa giữa từ để nhấn mạnh.

| ✅ Làm như thế này          |❌ Không làm như thế này    |
| --------------------------- | --------------------------- |
| `Tình yêu **thật** táo bạo` | `Tình yêu __thật__ táo bạo` |



## In nghiêng
Để in nghiêng chữ, thêm một dấu hoa thị hoặc dấu gạch dưới trước và sau chữ hoặc cụm từ. Để in đậm một chữ giữa từ để nhấn mạnh, thêm hai dấu hoa thị không có dấu cách quanh các chữ.

| Markdown                    | HTML                                     | Rendered Output           |
| --------------------------- | ---------------------------------------- | ------------------------- |
|`Chữ in nghiêng là *tiếng mèo kêu*.`|`Chữ in nghiêng là <em>tiếng mèo kêu</em>.`|Chữ in nghiêng là *tiếng mèo kêu*.|
|`Chữ in nghiêng là _tiếng mèo kêu_.`|`Chữ in nghiêng là <em>tiếng mèo kêu</em>.`|Chữ in nghiêng là _tiếng mèo kêu_.|
|`Con*mèo*kêu`|`Con<em>mèo</em>kêu`|Con*mèo*kêu|



## Thực hành tốt nhất cho in nghiêng
Những ứng dụng Markdown không thống nhất về cách xử lý dấu gạch dưới ở giữa từ. 
Để tương thích, dùng dấu hoa thị để in nghiêng giữa từ để nhấn mạnh.

| ✅ Làm như thế này |❌ Không làm như thế này |
| ------------------- | ----------------------- |
| `Con*mèo*kêu`       | `Con_mèo_kêu`           |



## In đậm và in nghiêng
để nhấn mạnh chữ bằng cả in đậm và in nghiêng, thêm ba dấu hoa thị hoặc dấu gạch dưới trước và sau chữ hoặc cụm từ. Để in đậm và in nghiêng một chữ giữa từ để nhấn mạnh, thêm ba dấu hoa thị không có dấu cách quanh các chữ.

| Markdown        | HTML                     | Đầu ra        |
| --------------------------- | ---------------------------------------- | ------------------------- |
|`This text is ***cực kì quan trọng***.`|`Dòng này<em><strong>cực kì quan trọng</strong></em>.`|Dòng này ***cực kì quan trọng***.|
|`Dòng này___cực kì quan trọng___.`|`Dòng này<em><strong>cực kì quan trọng</strong></em>.`|Dòng này ___cực kì quan trọng___.|
|`Dòng này__*cực kì quan trọng*__.`|`Dòng này<em><strong>cực kì quan trọng</strong></em>.`|Dòng này __*cực kì quan trọng*__.|
|`Dòng này**_cực kì quan trọng_**.`|`Dòng này<em><strong>cực kì quan trọng</strong></em>.`|Dòng này **_cực kì quan trọng_**.|
|`Đây là dòng chữ ***cực kì*** quan trọng.`|`Đây là dòng chữ<em><strong>cực kì</strong></em>quan trọng.`|Đây là dòng chữ ***cực kì*** quan trọng.|

 **Ghi chú:** Thứ tự của thẻ `em` và thẻ `strong` có thể đảo ngược dựa vào bộ xử lý Markdown ban đang dùng.



## Thực hành tốt nhất cho in đậm và in nghiêng
Những ứng dụng Markdown không thống nhất về cách xử lý dấu gạch dưới ở giữa từ. 
Để tương thích, dùng dấu hoa thị để in hoa và in nghiêng giữa từ để nhấn mạnh.

| ✅ Làm như thế này                        |❌ Không làm như thế này                  |
| ----------------------------------------- | ----------------------------------------- |
| `Đây là dòng chữ ***cực kì*** quan trọng.`| `Đây là dòng chữ ___cực kì___ quan trọng.`|





# Khối trích dẫn 
Để tạo một khối trích dẫn, thêm `>` trước đoạn.

    > Dorothy theo cô đi qua nhiều căn phòng xinh đẹp trong lâu đài của cô.

<br>Kết quả đầu ra trông như thế này:

> Dorothy theo cô đi qua nhiều căn phòng xinh đẹp trong lâu đài của cô.



## Những khối trích dẫns với nhiều đoạn
Những khối trích dẫn có thể bao gồm nhiều đoạn. Thêm `>` trên các dòng trống giữa các đoạn.

    > Dorothy theo cô đi qua nhiều căn phòng xinh đẹp trong lâu đài của cô.
    >
    > Mụ phù thủy bảo cô lau chùi những nồi niêu, quét sàn và để lửa đốt bằng củi.

<br>Kết quả đầu ra trông như thế này:

> Dorothy theo cô đi qua nhiều căn phòng xinh đẹp trong lâu đài của cô.
>
> Mụ phù thủy bảo cô lau chùi những nồi niêu, quét sàn và để lửa đốt bằng củi.



## Khối trích dẫn lồng nhau
Khối trích dẫn có thể lồng nhau. Thêm `>>` trước đoạn bạn muốn lồng.

    > Dorothy theo cô đi qua nhiều căn phòng xinh đẹp trong lâu đài của cô.
    >
    >> Mụ phù thủy bảo cô lau chùi những nồi niêu, quét sàn và để lửa đốt bằng củi.

<br>Kết quả đầu ra trông như thế này:

> Dorothy theo cô đi qua nhiều căn phòng xinh đẹp trong lâu đài của cô.
>
>> Mụ phù thủy bảo cô lau chùi những nồi niêu, quét sàn và để lửa đốt bằng củi.



## Những khối trích dẫn với các Yếu tố khác
Những khối trích dẫn có thể bao gồm phần tử được định dạng Markdown khác. Không phải tất cả các yếu tố có thể sử dụng được — bạn phải thử nghiệm để xem cái nào dùng được.

    > #### Kết quả hàng quý trông thật tuyệt vời!<br>
    >
    > - Doanh thu nằm ngoài bảng xếp hạng.
    > - Lợi nhuận cao hơn bao giờ hết.
    >
    >  *Mọi thứ* đang diễn ra theo **kế hoạch**.

<br>Kết quả đầu ra trông như thế này:

> #### Kết quả hàng quý trông thật tuyệt vời!
>
> - Doanh thu nằm ngoài bảng xếp hạng.
> - Lợi nhuận cao hơn bao giờ hết.
>
>  *Mọi thứ* đang diễn ra theo **kế hoạch**.



##  Thực hành tốt nhất cho những khối trích dẫn
Để tương thích, thêm dòng trống trước và sau những khối trích dẫn.

| ✅ Làm như thế này                      |❌ Không làm như thế này                |
| ---------------------------------------- | ---------------------------------------- |
| Thử thêm dòng trống sau...<br><br>> Đây là khối trích dẫn<br><br>...và sau khối trích dẫn.| Không có dòng trống, cái này có thể nhìn không ổn.<br>> Đây là khối trích dẫn<br>Đừng làm như thế này!|





# Danh sách
bạn có thể sắp xếp các mục thành danh sách đã hoặc chưa sắp đặc.



## Danh sách có thứ tự
Để tạo danh sách đã sắp đặc, thêm các mục có số theo sau là dấu chấm. Các số không nhất thiết phải theo thứ tự số, nhưng danh sach nên bắt đầu với số một.

| Markdown        | HTML                     | Đầu ra        |
| --------------------------- | ---------------------------------------- | ------------------------- |
|1. Mục đầu tiên<br>2. Mục thứ hai<br>3. Mục thứ ba<br>4. Mục thứ tư|`<ol>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba</li>`<br>`      <li>Mục thứ tư</li>`<br>`</ol>`|1. Mục đầu tiên<br>2. Mục thứ hai<br>3. Mục thứ ba<br>4. Mục thứ tư|
|1. Mục đầu tiên<br>1. Mục thứ hai<br>1. Mục thứ ba<br>1. Mục thứ tư|`<ol>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ol>`|1. Mục đầu tiên<br>2. Mục thứ hai<br>3. Mục thứ ba<br>4. Mục thứ tư|
|1. Mục đầu tiên<br>8. Mục thứ hai<br>3. Mục thứ ba<br>5. Mục thứ tư|`<ol>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ol>`|1. Mục đầu tiên<br>2. Mục thứ hai<br>3. Mục thứ ba<br>4. Mục thứ tư|
|1. Mục đầu tiên<br>2. Mục thứ hai<br>3. Mục thứ ba<br>    1. Mục thụt lề<br>    2. Mục thụt lề<br>4. Mục thứ tư|`<ol>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba`<br>`<ol>`<br>`   <li>Mục thụt lề</li>>`<br>`   <li>Mục thụt lề</li>`<br>`</ol>`<br>`</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ol>`|1. Mục đầu tiên<br>2. Mục thứ hai<br>3. Mục thứ ba<br>    1. Mục thụt lề<br>    2. Mục thụt lề<br>4. Mục thứ tư|



## Thực hành tốt nhất cho danh sách có thứ tự
CommonMark và một số ngôn ngữ đánh dấu nhẹ khác cho bạn dùng dấu ngoặc đơn (`)`) như dấu phân cách (v.d., `1) Mục đầu tiên`), nhưng không phải tất cả ứng dụng Markdown hỗ trợ cái này, vì vậy nó không phải là một lựa chọn tốt từ góc độ tương thích. Để tương thích, chỉ dùng dấu chấm.

| ✅ Làm như thế này                |❌ Không làm như thế này          |
| --------------------------------- | --------------------------------- |
| 1. Mục đầu tiên<br>2. Mục thứ hai | 1) Mục đầu tiên<br>2) Mục thứ hai |



## Danh sách không có thứ tự
Để tạo danh sách không có thứ tự, thêm dấu gạch ngang (`-`), dấu hoa thị (`*`), hoặc dấu cộng (`+`) trước các mục. Thụt lề một hoặc nhiều mục để tạo danh sách lồng nhau.

| Markdown        | HTML                     | Đầu ra        |
| --------------------------- | ---------------------------------------- | ------------------------- |
|- Mục đầu tiên<br>- Mục thứ hai<br>- Mục thứ ba<br>- Mục thứ tư|`<ul>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ul>`|- Mục đầu tiên<br>- Mục thứ hai<br>- Mục thứ ba<br>- Mục thứ tư|
|* Mục đầu tiên<br>* Mục thứ hai<br>* Mục thứ ba<br>* Mục thứ tư|`<ul>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ul>`|* Mục đầu tiên<br>* Mục thứ hai<br>* Mục thứ ba<br>* Mục thứ tư|
|+ Mục đầu tiên<br>+ Mục thứ hai<br>+ Mục thứ ba<br>+ Mục thứ tư|`<ul>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ul>`|+ Mục đầu tiên<br>+ Mục thứ hai<br>+ Mục thứ ba<br>+ Mục thứ tư|
|- Mục đầu tiên<br>- Mục thứ hai<br>- Mục thứ ba<br>    - Mục thụt lề<br>    - Mục thụt lề<br>- Mục thứ tư|`<ul>`<br>`   <li>Mục đầu tiên</li>`<br>`   <li>Mục thứ hai</li>`<br>`   <li>Mục thứ ba`<br>`<ul>`<br>`   <li>Mục thụt lề</li>>`<br>`   <li>Mục thụt lề</li>`<br>`</ul>`<br>`</li>`<br>`   <li>Mục thứ tư</li>`<br>`</ul>`|- Mục đầu tiên<br>- Mục thứ hai<br>- Mục thứ ba<br>    - Mục thụt lề<br>    - Mục thụt lề<br>- Mục thứ tư|



## Bắt đầu các mục trong danh sách không có thứ tự Với Số
Nếu bạn cần bắt đầu một mục danh sách không có thứ tự với một số theo sau là một dấu chấm, bạn có thể dùng dấu gạch chéo ngược (`\`) để [thoát khỏi](https://www.markdownguide.org/basic-syntax/#escaping-characters) dấu chấm.

| Markdown        | HTML                     | Đầu ra        |
| --------------------------- | ---------------------------------------- | ------------------------- |
|- 1968\. Một năm tuyệt vời!<br>- Tôi nghĩ năm 1969 là tốt thứ hai.|`<ul>`<br>`   <li>1968. Một năm tuyệt vời!</li>`<br>`   <li>Tôi nghĩ năm 1969 là tốt thứ hai.</li>`<br>`</ul>`|<ul>   <li>1968. Một năm tuyệt vời!</li>   <li>Tôi nghĩ năm 1969 là tốt thứ hai.</li></ul>|



## Thực hành tốt nhất cho danh sách không có thứ tự
Những ứng dụng Markdown không thống nhất về cách xử lý các dấu phân cách khác nhau trong cùng một danh sách. Để tương thích, đừng trộn lẫn và kết hợp các dấu phân cách trong cùng một danh sách — chọn một và chỉ dùng nó.

| ✅ Làm như thế này             |❌ Không làm như thế này         |
| ------------------------------- | ------------------------------- |
| - Mục đầu tiên<br>- Mục thứ hai<br>- Mục thứ ba<br>- Mục thứ tư| + Mục đầu tiên<br>* Mục thứ hai<br>- Mục thứ ba<br>+ Mục thứ tư|



## Thêm phần tử vào danh sách
Để thêm phần tử vào danh sách khi duy trì tính liên tục của nó, thụt lề phần tử vào bốn dấu cách hoặc một tab, như minh họa trong các ví dụ sau.

 **Mẹo:** Nếu mọi thứ không diễn ra như bạn mong đợi, kiểm tra xem bạn đã thụt lề các thành phần trong danh sách bốn dấu cách hay một tab chưa.


### Đoạn văn
    * Đây là mục danh sách đầu tiên.
    * Đây là mục danh sách thứ hai.

        Tôi cần thêm một đoạn bên dưới mục danh sách thứ hai.

    * Và đây là mục danh sách thứ ba.

Kết quả đầu ra trông như thế này:

* Đây là mục danh sách đầu tiên.
* Đây là mục danh sách thứ hai.

    Tôi cần thêm một đoạn bên dưới mục danh sách thứ hai.

* Và đây là mục danh sách thứ ba.


### Những khối trích dẫn
    * Đây là mục danh sách đầu tiên.
    * Đây là mục danh sách thứ hai.

        > Một khối trích dẫn would look great below the second list item.

    * Và đây là mục danh sách thứ ba.

Kết quả đầu ra trông như thế này:

* Đây là mục danh sách đầu tiên.
* Đây là mục danh sách thứ hai.

    > Một khối trích dẫn would look great below the second list item.

* Và đây là mục danh sách thứ ba.


### Khối mã
[Khối mã](https://www.markdownguide.org/basic-syntax/#code-blocks) thường được thụt vào bốn dấu cách hoặc một tab. Khi chúng nằm trong danh sách, thụt lề chúng thành tám dấu cách hoặc hai tab.

Để tạo khối mã, thụt lề mỗi dòng của khối ít nhất bốn dấu cách hoặc một tab.

    <html>
        <head>
        </head>
    </html>

Kết quả đầu ra trông như thế này:

    <html>
        <head>
        </head>
    </html>

 **Ghi chú:** Để tạo khối mã mà không cần thụt dòng, dùng [khối mã rào chắn](https://www.markdownguide.org/extended-syntax/#fenced-code-blocks).





# Quy tắc ngang
Để tạo quy tắc ngang, sử dụng ba dấu hoa thị trở lên (`***`), dấu gạch ngang (`---`), hoặc dấu gạch dưới (`___`) trên một dòng của nó.

    ***

    ---

    _________________

Kết quả của cả ba trông giống hệt nhau:



## Thực hành tốt nhất cho quy tắc ngang
Để tương thích, đặt dòng trống trước và sau quy tắc ngang.

| ✅ Làm như thế này                      |❌ Không làm như thế này                |
| ------------------------------- | ------------------------------- |
|Hãy thử đặt dòng trống trước...<br><br>---<br><br>...và sau quy tắc ngang.|Nếu không có dòng trống, đây sẽ là tiêu đề.<br>---<br>Đừng làm như thế này!|





# Liên kết
Để tạo liên kết, đặt liên kết trong ngoặc (v.d., [Duck Duck Go]) và sau đó theo ngay bằng một URL trong ngoặc đơn (v.d., (https://duckduckgo.com)).

    Công cụ tìm kiếm yêu thích của tôi là [Duck Duck Go](https://duckduckgo.com).

Kết quả đầu ra trông như thế này:

Công cụ tìm kiếm yêu thích của tôi là [Duck Duck Go](https://duckduckgo.com).

 **Ghi chú:** Để liên kết đến một phần tử trên cùng một trang, nhìn [liên kết đến tiêu đề ID](https://www.markdownguide.org/extended-syntax/#linking-to-heading-ids)
. Để tạo link khi mở ra trong một tab hoặc cửa sổ mới, xem phần trên [mục tiêu liên kết](https://www.markdownguide.org/extended-syntax/#linking-to-heading-ids](https://www.markdownguide.org/hacks/#link-targets)).



## Thêm tiêu đề
Bạn có thể tùy ý thêm tiêu đề cho liên kết. Nó sẽ xuất hiện dưới dạng chú giải khi người dùng di chuột qua liên kết. Để thêm tiêu đề, hãy đặt tiêu đề đó trong dấu ngoặc kép sau URL.

    Công cụ tìm kiếm yêu thích của tôi là [Duck Duck Go](https://duckduckgo.com "Công cụ tìm kiếm tốt nhất về quyền riêng tư").

Kết quả đầu ra trông như thế này:

Công cụ tìm kiếm yêu thích của tôi là [Duck Duck Go](https://duckduckgo.com "Công cụ tìm kiếm tốt nhất về quyền riêng tư").



## URL và địa chỉ Email
Để nhanh chóng biến một URL hoặc địa chỉ email thành một liên kết, đặt nó trong dấu ngoặc nhọn.

    <https://www.markdownguide.org>
    <fake@example.com>

Kết quả đầu ra trông như thế này:

<https://www.markdownguide.org><br>
<fake@example.com>



## Formatting Links
Để [nhấn mạnh](https://www.markdownguide.org/basic-syntax/#emphasis) liên kết, thêm dấu hoa thị trước và sau dấu ngoặc và dấu ngoặc đơn. Để biểu thị các liên kết như [lệnh](https://www.markdownguide.org/basic-syntax/#code), thêm dấu tích ngược trong ngoặc.

    Tôi thích ủng hộ **[EFF](https://eff.org)**.
    Đây là *[Hướng dẫn cách đánh dấu](https://www.markdownguide.org)*.
    Xem phần trên [`lệnh`](#code).

Kết quả đầu ra trông như thế này:

Tôi thích ủng hộ **[EFF](https://eff.org)**.
Đây là *[Hướng dẫn cách đánh dấu](https://www.markdownguide.org)*.
 Xem phần trên [`lệnh`](#code).



## Liên kết kiểu tham chiếu
Liên kết kiểu tham chiếu là một loại liên kết đặc biệt giúp URL dễ hiển thị và đọc hơn trong Markdown. Liên kết kiểu tham chiếu được xây dựng thành hai phần: phần bạn giữ nội dòng với văn bản của mình và phần bạn lưu trữ ở một nơi khác trong tệp để giữ cho văn bản dễ đọc.


### Định dạng phần đầu tiên của liên kết
Phần đầu tiên của liên kết kiểu tham chiếu được định dạng bằng hai bộ dấu ngoặc. Bộ dấu ngoặc đầu tiên bao quanh văn bản sẽ xuất hiện liên kết. Bộ dấu ngoặc thứ hai hiển thị nhãn được sử dụng để trỏ đến liên kết bạn đang lưu trữ ở nơi khác trong tài liệu của mình.

Mặc dù không bắt buộc, bạn có thể bao gồm dấu cách giữa bộ dấu ngoặc thứ nhất và thứ hai. Nhãn trong cặp dấu ngoặc thứ hai không phân biệt chữ hoa chữ thường và có thể bao gồm các chữ cái, số, dấu cách, hoặc dấu chấm câu.

Điều này có nghĩa là các định dạng ví dụ sau gần tương đương với phần đầu tiên của liên kết:

<ul>   <li>[hobbit-hole][1]</li>   <li>[hobbit-hole] [1]</li></ul>


### Định dạng phần thứ hai của liên kết
Phần thứ hai của liên kết kiểu tham chiếu được định dạng với các thuộc tính sau:

<ol>   <li>Nhãn, trong ngoặc đơn, ngay sau đó là dấu hai chấm và ít nhất một dấu cách (v.d., [nhãn]: ).</li>   <li>URL của liên kết mà bạn có thể tùy ý đặt trong dấu ngoặc nhọn.</li>   <li>Tiêu đề tùy chọn cho liên kết mà bạn có thể đặt trong dấu ngoặc kép, dấu nháy đơn hoặc dấu ngoặc đơn.</li></ol>

Điều này có nghĩa là các định dạng mẫu sau đây gần như tương đương với phần thứ hai của liên kết:

<ul>   <li><code>[1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle</code></li>   <li><code>[1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle "Hobbit lifestyles"</code></li>   <li><code>[1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle 'Hobbit lifestyles'</code></li>   <li><code>[1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle (Hobbit lifestyles)</code></li>   <li><code>[1]: <https://en.wikipedia.org/wiki/Hobbit#Lifestyle> "Hobbit lifestyles"</code></li>   <li><code>[1]: <https://en.wikipedia.org/wiki/Hobbit#Lifestyle> 'Hobbit lifestyles'</code></li>   <li><code>[1]: <https://en.wikipedia.org/wiki/Hobbit#Lifestyle> (Hobbit lifestyles)</code></li></ul>

Bạn có thể đặt phần thứ hai của liên kết này ở bất kỳ đâu trong tài liệu Markdown của mình. Một số người đặt chúng ngay sau đoạn văn mà chúng xuất hiện khi những người khác đặt chúng ở cuối tài liệu (như chú thích cuối trang hoặc chú thích cuối trang).


### Ví dụ về việc kết hợp các bộ phận lại với nhau
Giả sử bạn thêm một URL như [liên kết URL tiêu chuẩn](https://www.markdownguide.org/basic-syntax/#links) vào một đoạn văn và nó trông như thế này trong Markdown:

    Trong một hố trên mặt đất có một người Hobbit sống. Không phải là một hố bẩn thỉu, bẩn thỉu, ẩm ướt, đầy côn trùng và mùi hôi thối, cũng không phải là một hố cát khô cằn, trần trụi, không có gì để ngồi hoặc để ăn: đó là [hố-hobbit](https://en.wikipedia.org/wiki/Hobbit#Lifestyle "Hobbit lifestyles"),, và điều đó có nghĩa là an ủi.

Mặc dù nó có thể chỉ ra những thông tin bổ sung thú vị, cái URL được hiển thị thực sự không thêm nhiều vào văn bản thô ngoài việc làm cho nó khó đọc hơn. Để khắc phục điều này, thay vào đó bạn có thể định dạng URL như thế này:

    Trong một hố trên mặt đất có một người Hobbit sống. Không phải là một hố bẩn thỉu, bẩn thỉu, ẩm ướt, đầy côn trùng và mùi hôi thối, cũng không phải là một hố cát khô cằn, trần trụi, không có gì để ngồi hoặc để ăn: đó là [hố-hobbit][1],, và điều đó có nghĩa là an ủi.

    [1]: <https://en.wikipedia.org/wiki/Hobbit#Lifestyle> "Hobbit lifestyles"

Trong cả hai trường hợp trên, kết quả sẽ giống hệt nhau:

Trong một hố trên mặt đất có một người Hobbit sống. Không phải là một hố bẩn thỉu, bẩn thỉu, ẩm ướt, đầy côn trùng và mùi hôi thối, cũng không phải là một hố cát khô cằn, trần trụi, không có gì để ngồi hoặc để ăn: đó là [hố-hobbit](https://en.wikipedia.org/wiki/Hobbit#Lifestyle "Hobbit lifestyles"),, và điều đó có nghĩa là an ủi.

và HTML cho liên kết sẽ là:

    <a href="https://en.wikipedia.org/wiki/Hobbit#Lifestyle" title="Hobbit lifestyles">hố-hobbit</a>



## Thực hành tốt nhất cho liên kết
Các ứng dụng Markdown không đồng ý về cách xử lý khoảng trắng ở giữa URL. Để tương thích, cố gắng mã hóa URL bất kỳ khoảng trắng nào bằng %20. Ngoài ra, nếu ứng dụng Markdown của bạn [hỗ trợ HTML](https://www.markdownguide.org/basic-syntax/#html), bạn có thể dùng thẻ HTML.

| ✅ Làm như thế này              |❌ Không làm như thế này        |
| ------------------------------- | ------------------------------- |
|`[link](https://www.example.com/my%20great%20page)`<br><br>`<a href="https://www.example.com/my great page">link</a>`|`[link](https://www.example.com/my great page)`|

Dấu ngoặc đơn ở giữa URL cũng có thể có vấn đề. Để tương thích, cố gắng mã hóa URL dấu ngoặc đơn mở (() với %28 và dấu ngoặc đơn đóng ()) with %29. Ngoài ra, nếu ứng dụng Markdown của bạn [hỗ trợ HTML][supports HTML](https://www.markdownguide.org/basic-syntax/#html), bạn có thể dùng thẻ HTML.

| ✅ Làm như thế này              |❌ Không làm như thế này        |
| ------------------------------- | ------------------------------- |
|`[a novel](https://en.wikipedia.org/`<br>`wiki/The_Milagro_Beanfield_War_%28novel%29)`<br><br>`<a href="https://en.wikipedia.org/`<br>`wiki/The_Milagro_Beanfield_War_(novel)">a novel</a>`|`[a novel](https://en.wikipedia.org/wiki/The_Milagro_Beanfield_War_(novel))`|





# Ảnh
Để thêm ảnh, thêm dấu chấm than (`!`), theo sau là văn bản thay thế trong ngoặc, à đường dẫn hoặc URL tới nội dung hình ảnh trong ngoặc đơn. Bạn có thể tùy ý thêm tiêu đề trong dấu ngoặc kép sau đường dẫn hoặc URL.

    ![Dãy núi San Juan rất đẹp!](/assets/images/san-juan-mountains.jpg "Dãy núi San Juan")

Kết quả đầu ra trông như thế này:

![Dãy núi San Juan rất đẹp! (lol)](/assets/images/san-juan-mountains.jpg "Dãy núi San Juan")

**Ghi chú:** Để thay đổi kích thước ảnh, xem phần trên [kích cỡ ảnh](https://www.markdownguide.org/hacks/#image-size). Để thêm chú thích, xem phần trên [chú thích ảnh](https://www.markdownguide.org/hacks/#image-captions).



## Liên kết ảnh
Để thêm liên kết vào ảnh, đặt Markdown cho ảnh trong ngoặc, sau đó thêm liên kết trong ngoặc đơn.

    [![Một tảng đá cũ trên sa mạc](/assets/images/shiprock.jpg "Shiprock, New Mexico của Beau Rogers")](https://www.flickr.com/photos/beaurogers/31833779864/in/photolist-Qv3rFw-34mt9F-a9Cmfy-5Ha3Zi-9msKdv-o3hgjr-hWpUte-4WMsJ1-KUQ8N-deshUb-vssBD-6CQci6-8AFCiD-zsJWT-nNfsgB-dPDwZJ-bn9JGn-5HtSXY-6CUhAL-a4UTXB-ugPum-KUPSo-fBLNm-6CUmpy-4WMsc9-8a7D3T-83KJev-6CQ2bK-nNusHJ-a78rQH-nw3NvT-7aq2qf-8wwBso-3nNceh-ugSKP-4mh4kh-bbeeqH-a7biME-q3PtTf-brFpgb-cg38zw-bXMZc-nJPELD-f58Lmo-bXMYG-bz8AAi-bxNtNT-bXMYi-bXMY6-bXMYv)

Kết quả đầu ra trông như thế này:

[![Một tảng đá cũ trên sa mạc](/assets/images/shiprock.jpg "Shiprock, New Mexico của Beau Rogers")](https://www.flickr.com/photos/beaurogers/31833779864/in/photolist-Qv3rFw-34mt9F-a9Cmfy-5Ha3Zi-9msKdv-o3hgjr-hWpUte-4WMsJ1-KUQ8N-deshUb-vssBD-6CQci6-8AFCiD-zsJWT-nNfsgB-dPDwZJ-bn9JGn-5HtSXY-6CUhAL-a4UTXB-ugPum-KUPSo-fBLNm-6CUmpy-4WMsc9-8a7D3T-83KJev-6CQ2bK-nNusHJ-a78rQH-nw3NvT-7aq2qf-8wwBso-3nNceh-ugSKP-4mh4kh-bbeeqH-a7biME-q3PtTf-brFpgb-cg38zw-bXMZc-nJPELD-f58Lmo-bXMYG-bz8AAi-bxNtNT-bXMYi-bXMY6-bXMYv)





# Thoát kí tự
Để hiển thị một ký tự bằng chữ thường được sử dụng để định dạng văn bản trong tài liệu Markdown, thêm dấu gạch chéo ngược (`\`) trước kí tự.

    \* Nếu không có dấu gạch chéo ngược, đây là dấu đầu dòng trong danh sách không thứ tự.

Kết quả đầu ra trông như thế này:

\* Nếu không có dấu gạch chéo ngược, đây là dấu đầu dòng trong danh sách không thứ tự.



## Các kí tự bạn có thể thoát
Bạn có thể sử dụng dấu gạch chéo ngược để thoát khỏi các ký tự sau.

| Kí tự    | Tên                  |
| -------- | -------------------- |
| `\`      | dấu gạch chéo ngược  |
| `        | dấu tích ngược (xem thêm [thoát dấu tích ngược trong lệnh](https://www.markdownguide.org/basic-syntax/#escaping-backticks))|
|`*`       | dấu hoa thị          |
| `_`      | dấu gạch dưới        |
| `{ }`    | dấu ngoặc nhọn       |
| `[ ]`    | dấu ngoặc đơn        |
| `< >`    | dấu ngoặc nhọn       |
| `( )`    | dấu ngoặc            |
| `#`      | ký hiệu bảng Anh     |
|`+`       | dấu cộng             |
|`-`       | dấu trừ (gạch nối)   |
|`.`       | dấu chấm             |
|`!`       | dấu chấm than        |
| (&#124;) | biểu tượng ống (xem thêm [ống thoát trong bảng](https://www.markdownguide.org/extended-syntax/#escaping-pipe-characters-in-tables))|





# HTML
Nhiều ứng dụng Markdown cho phép bạn sử dụng thẻ HTML trong văn bản định dạng Markdown. Điều này sẽ hữu ích nếu bạn thích một số thẻ HTML nhất định hơn cú pháp Markdown. Ví dụ: một số người thấy việc sử dụng thẻ HTML cho ảnh dễ dàng hơn. Sử dụng HTML cũng hữu ích khi bạn cần thay đổi thuộc tính của một phần tử, như chỉ định  [màu của văn bản](https://www.markdownguide.org/hacks/#color) hoặc thay đổi chiều rộng của ảnh.

Để sử dụng HTML, đặt các thẻ vào văn bản của tệp có định dạng Markdown.

    **Từ** này được in đậm. <em>Từ</em> này được in nghiêng.

Kết quả đầu ra trông như thế này:

**Từ** này được in đậm. <em>Từ</em> này được in nghiêng.



## Thực hành tốt nhất cho HTML
Vì lý do bảo mật, không phải tất cả các ứng dụng Markdown đều hỗ trợ HTML trong tài liệu Markdown. Khi nghi ngờ, kiểm tra tài liệu của ứng dụng Markdown của bạn. Một số ứng dụng chỉ hỗ trợ một tập hợp con các thẻ HTML.

Sử dụng các dòng trống để phân tách các phần tử HTML cấp khối như `<div>`, `<table>`, `<pre>`, và `<p>` khỏi nội dung xung quanh. Cố gắng không thụt lề các thẻ bằng tab hoặc dấu cách — nó có thể can thiệp vào việc định dạng.

Bạn không thể sử dụng cú pháp Markdown bên trong các thẻ HTML cấp khối. Ví dụ, `<p>italic` và `**bold**</p>` 
sẽ không hoạt động.