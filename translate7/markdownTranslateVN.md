# Danh sách các lỗi và hành vi sai trái hàng đầu của trình điều khiển mà chúng tôi gặp phải trong năm nay.
Chúng tôi đã dành rất nhiều thời gian để viết code dựa trên cơ sở dữ liệu, kho dữ liệu và các loại trình điều khiển của chúng trong năm nay. Giống như, rất rất nhiều. Thông qua quá trình đó, chúng tôi đã phát hiện ra một số… hành vi không mong muốn. Trong một số trường hợp, những hành vi đó đi ngược lại trực tiếp với tài liệu của công cụ. Trong những trường hợp khác, chúng chỉ đi ngược lại lẽ thường (trong phạm vi mà lẽ thường của chúng ta là phổ biến). Trong mọi trường hợp, chúng tôi cho rằng sẽ rất thú vị nếu biên soạn những nội dung đó và chia sẻ chúng, cho cả mục đích giải trí và cho hậu thế.

Trước khi chúng ta đi sâu vào, hãy lưu ý nhanh. Chúng tôi cảm thấy vô cùng may mắn khi được làm việc với nhiều công cụ như vậy và có thể dựa trên thành quả của những người khác để tiếp tục cung cấp những sản phẩm tốt hơn. Chúng tôi viết phần mềm để kiếm sống và chúng tôi là những người đầu tiên biết rằng những lỗi đi kèm với lãnh thổ - trừ khi bạn là siêu nhân hoặc bạn không viết bất kỳ số lượng code có ý nghĩa nào, lỗi là điều không thể tránh khỏi.

Mục tiêu của chúng tôi ở đây là không chế nhạo các dự án hoặc công ty cụ thể vì có chúng. Thay vào đó, chúng tôi cho rằng đó là kiến ​​thức đủ thú vị để chia sẻ. Bất cứ nơi nào và khi nào có thể, chúng tôi cố gắng hết sức để trở thành những công dân chính trực và báo cáo những lỗi đó với chủ sở hữu tương ứng của chúng. Bất chấp điều đó, để ngăn bài đăng này trở thành cơ hội “tên và sự xấu hổ”, chúng tôi nghĩ tốt nhất nên ẩn danh cơ sở dữ liệu và trình điều khiển ở đây. Nếu bạn làm việc tại một công ty csdl và muốn nhận phản hồi của chúng tôi về một trình điều khiển cụ thể hoặc mở một đường dây liên lạc, hãy gửi cho chúng tôi một ghi chú tại prequel.co!

Bây giờ, không cần chần chừ gì nữa, đây là bản hit hàng đầu của chúng tôi về “uhm, WTF?!” về cơ sở dữ liệu và hành vi của trình điều khiển trong năm.



## Một số trình điều khiển của bên thứ nhất không triển khai chức năng được quảng cáo
Trình điều khiển GoLang của bên thứ nhất dành cho cơ sở dữ liệu chính/kho dữ liệu không triển khai chức năng tệp SAO CHÉP, mặc dù đã nêu rõ trong tài liệu của mình rằng nó có chức năng đó. Đưa cái gì? Chúng tôi cũng không chắc chắn, nhưng từ kinh nghiệm trực tiếp, chúng tôi biết rằng nó khó để cập nhật tài liệu một cách hoàn hảo. Vì vậy, chúng tôi sẽ tha cho họ.

Để mở khóa đầy đủ chức năng (và giải quyết các vấn đề khác), chúng tôi đã bắt đầu sử dụng nhiều trình điều khiển dựa trên ODBC hơn. Điều này đưa chúng ta đến cú đánh tiếp theo.



## Một số trình điều khiển ODBC rò rỉ bộ nhớ
Giao diện ODBC được thiết kế để sử dụng với ngôn ngữ lập trình C. Nói cách khác, về bản chất, trình điều khiển ODBC phải chạy một số code C. Hóa ra, các chương trình C có thể rò rỉ bộ nhớ và trình điều khiển ODBC cũng không ngoại lệ. Một số bị rò rỉ một chút sau mỗi cuộc gọi, trong khi một số khác có các cuộc gọi hoặc kiểu sử dụng cụ thể khiến chúng bị rò rỉ nhiều bộ nhớ.
‍
![](https://assets-global.website-files.com/632e1440a7cdd10ccb606ffd/639746750094d913d8525cda_njk52zgJIYaLV9i7ErCL1TiEJvRgwdVsWPz_IGK1u1i5jcoTg5rlflSf2MitQvQ6psXBpbrrJe9uO0DW31u5EE9sEl1C0WqI4OymUgehc_-vPZ_Uk87eFMLbAzH2UEjIfcd17m3nfKjZfDKRhGr3kR8xoErWcnowjh5QfL_m61q0hbSR_4CT21IhiSvE0Q.png)

Đây là biểu đồ thú vị về mức sử dụng bộ nhớ của nhóm của chúng tôi trong khoảng thời gian vài ngày khi chúng tôi điều tra rò rỉ bộ nhớ trong môi trường nhà phát triển của mình. Rất may, chúng tôi đã bắt được nó trước khi nó được đưa vào sản xuất. Nguyên nhân cốt lõi là một kiểu cuộc gọi nhất định trong trình điều khiển ODBC.



## Một số trình điều khiển bị rò rỉ thông tin xác thực
Một số trình điều khiển ghi lại thông tin xác thực nếu có lỗi trong truy vấn chứa thông tin xác thực như khóa API hoặc khóa bí mật AWS. Những công dân khác có uy tín hơn sẽ thay thế các trường thông tin xác thực đã biết bằng chuỗi `REDACTED` an toàn khi đăng nhập vào stdout hoặc stderr.

‍

## Một số trình điều khiển loại bỏ các lỗi chính và thay vào đó chỉ trả về một tập kết quả trống
Chúng tôi đã học được điều này bằng cách khó khăn khi cố gắng đưa ra một giải pháp tích hợp mới. Code của chúng tôi có vẻ ổn và hoạt động trên các tập dữ liệu nhỏ. Nhưng khi chúng tôi cố chạy nó trên các tập dữ liệu lớn hơn, nó sẽ hoạt động như thể không có dữ liệu trong bảng csdl và trả về một tập kết quả trống.

Chúng tôi phát hiện ra rằng một số trình điều khiển có thời gian chờ truy vấn tiềm ẩn. Và đôi khi, khi hết thời gian chờ nó, trình điều khiển sẽ không đưa ra hoặc trả về bất kỳ loại ngoại lệ hoặc lỗi nào. Thay vào đó, chúng chỉ trả về một tập kết quả trống.

‍

## Một số trình điều khiển không đồng ý về cách thể hiện các kỷ nguyên
Không có gì bí mật khi thời gian, múi giờ và các loại đối tượng thường gây nhầm lẫn cho các nhà phát triển (minh họa A). Điều có lẽ ít được biết đến hơn là ngay cả các kỷ nguyên (biểu thị giây Unix của dấu thời gian), mặc dù chúng hấp dẫn như một biểu diễn không có múi giờ, nhưng cũng có thể khó làm việc.

Hóa ra, các cơ sở dữ liệu thường không đồng ý về việc chính xác một kỷ nguyên sẽ trông như thế nào. Một số tuân theo định nghĩa nghiêm ngặt, coi chúng một cách rõ ràng là số giây trôi qua kể từ nửa đêm UTC ngày 1 tháng 1 năm 1970. Trong những trường hợp đó, chúng được lưu trữ và biểu thị dưới dạng int32. Tuy nhiên, những người khác chọn bao gồm độ chính xác đến mili giây hoặc nano giây. Chúng được biểu thị dưới dạng int64 hoặc dưới dạng các loại số float khác nhau, với toàn bộ số là giây và phân số là độ chính xác được thêm vào.

Điều này có thể gây ra tất cả các loại vấn đề về tính toàn vẹn dữ liệu khi di chuyển dữ liệu. Trong những trường hợp tốt nhất, độ chính xác không bị mất đi một cách có ý nghĩa. Trong những trường hợp nguy hiểm hơn, tải hoạt động nhưng đưa ra các vùng đồng bằng giữa các dấu thời gian mà chỉ có thể nắm bắt được bằng cách phân biệt cẩn thận dữ liệu được tải.

Trong các trường hợp khác, các trình điều khiển khác nhau cho cùng một kho có thể xử lý chúng một cách khác nhau, một trình điều khiển coi chúng là int và trình điều khiển khác là float. Chúng tôi gặp phải vấn đề này khi chuyển từ trình điều khiển gốc sang trình điều khiển ODBC cho một nhà kho nổi tiếng. Tất cả điều này để nói: chỉ vì bạn di chuyển dấu thời gian của mình theo các kỷ nguyên không có nghĩa là bạn đã thoát khỏi khó khăn khi nói đến độ phức tạp trong việc xử lý thời gian.



## Một số nhà cung cấp cơ sở dữ liệu không cẩn thận trong việc phá vỡ các thay đổi
Một kho dữ liệu nổi tiếng đã cập nhật định dạng chuỗi kết nối mà trình điều khiển của nó mong đợi, dẫn đến lỗi kết nối khó hiểu. Thay đổi phiên bản tương ứng (Semver) biểu thị thay đổi “MINOR”, mặc dù trên thực tế đây không phải là thay đổi tương thích ngược. Qua đó, chúng tôi biết được 1, rằng không phải tất cả các nhà cung cấp đều coi trình điều khiển của họ là sản phẩm hạng nhất và 2, luôn kiểm tra mức độ hỗ trợ mà nhà cung cấp cung cấp cho trình điều khiển của họ trước khi cam kết hỗ trợ cơ sở dữ liệu hoặc kho dữ liệu nhất định.

‍

## Một số trình điều khiển có quan điểm cố định về chuỗi… như thực sự cố chấp
Một số trình điều khiển tin rằng các chuỗi phải là UTF-8, những người khác tin rằng tất cả các chuỗi chỉ là byte và sẽ luôn coi chúng là mảng byte. Ngoài ra, một số trình điều khiển có những ngoại lệ kỳ lạ về các ký tự mà trình điều khiển của họ có thể hỗ trợ (hầu hết trong số đó không thực sự được ghi lại).

‍

## Một số kho dữ liệu thực sự không nhất quán về loại
Chúng tôi có rất nhiều điều để nói về chủ đề này và đây xứng đáng là bài đăng trên blog của riêng nó. Nhưng nhìn chung, các kho dữ liệu rất khác nhau về cách chúng xử lý các loại một cách nhẹ nhàng. Một số sẽ vui vẻ tải một phiên bản của DECIMAL(38,9) vào cột DECIMAL(38,18), vì chúng không thực thi các giới hạn về kích thước, độ chính xác hoặc tỷ lệ đối với các loại số của chúng. Những người khác sẽ lớn tiếng phàn nàn và gọi bạn là đồ ngốc vì đã nghĩ đến việc làm một việc như vậy.

Một số thậm chí không có hỗ trợ đầy đủ trong trình điều khiển của riêng họ. Hầu hết trong số họ không có sự hỗ trợ tốt khi nói đến các cột JSON và xử lý nó theo mọi cách có ý nghĩa.

‍

## Một số cơ sở dữ liệu không tuân theo các tiêu chuẩn chung
Gần đây, chúng tôi đã gặp lỗi khi cố tải các tệp Parquet do DuckDB tạo vào Databricks. Điều buồn cười là các tệp tương tự có thể tải tốt trong các kho dữ liệu khác, chẳng hạn như Snowflake hoặc BigQuery. Lỗi cụ thể là như sau:
‍
> Chỉ nên đặt một trong số num_children và loại trong SchemaElement.

Khi tìm hiểu sâu hơn, chúng tôi nhận ra đây là sản phẩm phụ của hai thứ:

 - Databricks triển khai thông số Parquet theo đúng nghĩa đen hơn các thông số khác, ít nhất là khi nói đến siêu dữ liệu. Điều đó không hoàn toàn đáng ngạc nhiên vì Parquet có sự hỗ trợ sâu sắc trong hệ sinh thái Spark.
 - DuckDB đặt không chính xác cả hai trường siêu dữ liệu khi tạo tệp Parquet.

Chúng tôi đã gửi báo cáo lỗi với dự án DuckDB OSS để cho họ biết và họ đã sửa nó chỉ trong 48 giờ. Ngay từ đầu, chúng tôi là những người hâm mộ lớn của dự án DuckDB, nhưng điều này đã vượt xa những gì chúng tôi mong đợi. Khen ngợi cho đội.