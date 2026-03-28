# 3 Bộ nhớ

> Một kiểu trí nhớ chỉ hoạt động theo chiều ngược lại thì quả là một kiểu trí nhớ tồi.

—Lewis Carroll (1832–1898)

Hãy xét phép toán mức cao `x = x + 1`. Ở chương 2, chúng ta đã chỉ ra cách các cổng Logic có thể được dùng để biểu diễn số và tính các biểu thức số học đơn giản như `x + 1`. Giờ đây, chúng ta chuyển sang thảo luận cách các cổng Logic có thể được dùng để lưu trữ giá trị theo thời gian, cụ thể là cách một biến như `x` có thể được đặt để “chứa” một giá trị và duy trì giá trị đó cho tới khi ta đặt cho nó một giá trị khác. Để làm được điều này, chúng ta sẽ phát triển một họ chip bộ nhớ mới.

Cho tới thời điểm này, mọi chip mà chúng ta đã xây ở chương 1 và 2, với đỉnh cao là ALU, đều không phụ thuộc vào thời gian. Những chip như vậy đôi khi được gọi là combinational: chúng phản hồi với các tổ hợp đầu vào khác nhau mà không có độ trễ, ngoại trừ khoảng thời gian cần để các chip-part bên trong hoàn tất phép tính. Trong chương này, chúng ta sẽ giới thiệu và xây dựng các chip sequential. Khác với chip combinational, vốn không biết gì về thời gian, đầu ra của chip sequential không chỉ phụ thuộc vào đầu vào ở thời điểm hiện tại mà còn phụ thuộc vào các đầu vào và đầu ra đã được xử lý trước đó.

Không cần phải nói nhiều, các khái niệm hiện tại và trước đó luôn đi liền với khái niệm thời gian: hiện tại bạn nhớ điều đã được đưa vào bộ nhớ từ trước. Vì thế, trước khi bắt đầu nói về bộ nhớ, trước hết ta phải tìm ra cách dùng Logic để mô hình hóa sự trôi đi của thời gian. Điều này có thể được thực hiện bằng một clock tạo ra một chuỗi tín hiệu nhị phân liên tục mà ta gọi là tick và tock. Khoảng thời gian từ lúc bắt đầu một tick tới lúc kết thúc tock tiếp theo được gọi là một cycle, và các cycle này sẽ được dùng để điều tiết hoạt động của toàn bộ các chip bộ nhớ trong máy tính.

Sau phần giới thiệu ngắn gọn, theo góc nhìn người dùng, về các thiết bị bộ nhớ, chúng ta sẽ trình bày nghệ thuật Logic tuần tự, công cụ mà ta sẽ dùng để xây dựng các chip phụ thuộc thời gian. Sau đó, chúng ta sẽ bắt tay xây dựng các thanh ghi, thiết bị RAM và bộ đếm. Các thiết bị bộ nhớ này, cùng với các thiết bị số học đã xây ở chương 2, tạo thành toàn bộ các chip cần thiết để xây dựng một hệ thống máy tính đa dụng hoàn chỉnh, một thử thách mà chúng ta sẽ đảm nhận ở chương 5.

# 3.1 Thiết bị bộ nhớ

Các chương trình máy tính sử dụng biến, mảng và đối tượng, tức những trừu tượng duy trì dữ liệu theo thời gian. Các nền tảng phần cứng hỗ trợ khả năng này bằng cách cung cấp các thiết bị bộ nhớ biết cách duy trì trạng thái. Vì quá trình tiến hóa đã ban cho con người một hệ thống trí nhớ điện-hóa đáng kinh ngạc, ta thường mặc nhiên coi khả năng ghi nhớ theo thời gian là điều hiển nhiên. Tuy nhiên, khả năng này rất khó hiện thực trong Logic cổ điển, vốn không biết gì về thời gian lẫn trạng thái. Vì vậy, để bắt đầu, trước hết ta phải tìm ra cách mô hình hóa sự trôi đi của thời gian và trao cho các cổng Logic khả năng duy trì trạng thái cũng như phản ứng với sự thay đổi của thời gian.

Chúng ta sẽ tiếp cận thách thức này bằng cách giới thiệu một clock và một cổng Logic cơ bản, phụ thuộc thời gian, có thể lật qua lật lại giữa hai trạng thái ổn định: biểu diễn 0 và biểu diễn 1. Cổng này, gọi là data flip-flop (DFF), là khối xây dựng nền tảng mà từ đó mọi thiết bị bộ nhớ sẽ được dựng lên. Tuy vậy, bất chấp vai trò trung tâm của nó, DFF lại là một cổng rất kín đáo và ít được chú ý: khác với các thanh ghi, thiết bị RAM và bộ đếm, vốn giữ vai trò nổi bật trong kiến trúc máy tính, các DFF được dùng một cách ngầm định như những chip-part mức thấp nằm sâu bên trong các thiết bị bộ nhớ khác.

Vai trò nền tảng của DFF được thể hiện rõ trong hình 3.1, nơi nó đóng vai trò móng của hệ thứ bậc bộ nhớ mà chúng ta sắp xây dựng. Chúng ta sẽ chỉ ra cách DFF có thể được dùng để tạo ra các thanh ghi 1 bit, và cách ghép nối n thanh ghi như vậy để tạo thành một thanh ghi n bit. Tiếp theo, chúng ta sẽ xây dựng một thiết bị RAM chứa một số lượng tùy ý các thanh ghi kiểu này. Trong số những điều khác, chúng ta sẽ phát triển một cơ chế định địa chỉ, tức truy cập theo địa chỉ, để có thể truy cập trực tiếp và tức thời tới bất kỳ thanh ghi nào được chọn trong RAM.

![](../src/images/figure_3.1.png)

Hình 3.1    Hệ thứ bậc bộ nhớ được xây dựng trong chương này.

Tuy nhiên, trước khi bắt tay xây các chip này, chúng ta sẽ trình bày một phương pháp luận và các công cụ cho phép mô hình hóa sự trôi đi của thời gian và duy trì trạng thái theo thời gian.

# 3.2 Logic tuần tự

Mọi chip đã được bàn ở chương 1 và 2 đều dựa trên Logic cổ điển, vốn độc lập với thời gian. Để phát triển các thiết bị bộ nhớ, chúng ta cần mở rộng Logic cổng của mình để nó có thể phản hồi không chỉ với sự thay đổi của đầu vào mà còn với nhịp ticking của clock: ta nhớ nghĩa của từ dog ở thời điểm `t` vì ta đã nhớ nó ở thời điểm `t - 1`, và cứ thế lần ngược về thời điểm đầu tiên ta đưa nó vào trí nhớ. Để phát triển khả năng duy trì trạng thái theo thời gian này, ta phải mở rộng kiến trúc máy tính của mình bằng một chiều thời gian và xây dựng các công cụ xử lý thời gian bằng các hàm Boolean.

## 3.2.1 Thời gian là yếu tố quan trọng

Cho tới lúc này trong hành trình Nand to Tetris, chúng ta đã giả định rằng chip phản hồi với đầu vào của nó một cách tức thời: bạn đưa 7, 2 và lệnh “subtract” vào ALU, rồi... bụp, đầu ra ALU trở thành 5. Trong thực tế, đầu ra luôn có độ trễ, ít nhất vì hai lý do. Thứ nhất, đầu vào của chip không tự nhiên mà xuất hiện; thay vào đó, các tín hiệu biểu diễn chúng phải truyền từ đầu ra của các chip khác, và quá trình truyền này cần thời gian. Thứ hai, các phép tính do chip thực hiện cũng cần thời gian; chip càng có nhiều chip-part, Logic của nó càng phức tạp, thì càng mất nhiều thời gian hơn để đầu ra của chip xuất hiện hoàn chỉnh từ mạch điện bên trong.

Vì vậy, thời gian là một vấn đề bắt buộc phải được xử lý. Như thể hiện ở phần trên của hình 3.2, thời gian thường được hình dung như một mũi tên ẩn dụ tiến đều không ngừng về phía trước. Sự tiến triển này được xem là liên tục: giữa bất kỳ hai thời điểm nào cũng còn một thời điểm khác, và những thay đổi trong thế giới có thể nhỏ tới mức vô cùng vi. Khái niệm thời gian này, vốn phổ biến trong giới triết gia và vật lý gia, quá sâu và quá bí ẩn đối với các nhà khoa học máy tính. Vì thế, thay vì xem thời gian là một dòng chảy liên tục, chúng ta thích chia nó thành các khoảng có độ dài cố định, gọi là các cycle. Cách biểu diễn này là rời rạc, tạo ra cycle 1, cycle 2, cycle 3, v.v. Khác với mũi tên thời gian liên tục, vốn có độ hạt vô hạn, các cycle là những đơn vị nguyên tử và không thể chia nhỏ: các thay đổi trong thế giới chỉ diễn ra ở thời điểm chuyển tiếp giữa các cycle; còn bên trong một cycle, thế giới đứng yên.

![](../src/images/figure_3.2.png)

Hình 3.2    Biểu diễn thời gian rời rạc: Các thay đổi trạng thái (giá trị đầu vào và đầu ra) chỉ được quan sát tại các thời điểm chuyển tiếp giữa các cycle. Trong mỗi cycle, mọi thay đổi đều bị bỏ qua.

Dĩ nhiên, thế giới chưa bao giờ đứng yên. Tuy nhiên, khi xử lý thời gian theo kiểu rời rạc, chúng ta chủ động quyết định bỏ qua sự thay đổi liên tục. Ta chỉ cần biết trạng thái của thế giới ở cycle `n`, rồi ở cycle `n + 1`; còn trong suốt mỗi cycle, trạng thái được giả định là, nói sao nhỉ, ta không quan tâm. Trong việc xây dựng kiến trúc máy tính, quan điểm rời rạc về thời gian này phục vụ hai mục tiêu thiết kế quan trọng. Thứ nhất, nó có thể được dùng để trung hòa tính ngẫu nhiên gắn với các độ trễ truyền thông và độ trễ tính toán. Thứ hai, nó có thể được dùng để đồng bộ hoạt động của các chip khác nhau trên toàn hệ thống, như ta sẽ thấy về sau.

Để minh họa, hãy tập trung vào phần dưới của hình 3.2, nơi theo dõi cách một cổng Not, được dùng làm ví dụ, phản hồi với các đầu vào được chọn tùy ý. Khi ta cấp cho cổng giá trị 1, phải mất một lúc đầu ra của cổng mới ổn định ở 0. Tuy nhiên, vì độ dài cycle được thiết kế dài hơn độ trễ thời gian, nên khi ta tới cuối cycle, đầu ra của cổng đã ổn định ở 0. Vì ta chỉ thăm dò trạng thái của thế giới ở cuối cycle, ta không nhìn thấy các độ trễ trung gian; thay vào đó, dường như ta cấp cho cổng giá trị 0, rồi bụp, cổng phản hồi bằng 1. Nếu ta thực hiện cùng kiểu quan sát này ở cuối mỗi cycle, ta có thể tổng quát hóa rằng khi một cổng Not nhận đầu vào nhị phân `x`, nó sẽ xuất tức thời `Not(x)`.

Những độc giả tinh ý hẳn đã nhận ra rằng để sơ đồ này hoạt động, độ dài của cycle phải lớn hơn mọi độ trễ thời gian cực đại có thể xảy ra trong hệ thống. Quả thật, độ dài cycle là một trong những tham số thiết kế quan trọng nhất của bất kỳ nền tảng phần cứng nào: khi thiết kế máy tính, kỹ sư phần cứng sẽ chọn một độ dài cycle thỏa mãn hai mục tiêu. Một mặt, cycle phải đủ dài để bao chứa và trung hòa mọi độ trễ thời gian có thể có; mặt khác, cycle càng ngắn thì máy tính càng nhanh: nếu mọi thứ chỉ diễn ra ở thời điểm chuyển cycle, thì hiển nhiên chu kỳ càng ngắn, mọi thứ càng diễn ra nhanh hơn. Tóm lại, độ dài cycle được chọn sao cho chỉ hơi dài hơn độ trễ thời gian cực đại của bất kỳ chip nào trong hệ thống. Nhờ tiến bộ vượt bậc trong công nghệ chuyển mạch, ngày nay chúng ta có thể tạo ra những cycle nhỏ tới mức một phần tỷ giây, đạt được tốc độ máy tính đáng kinh ngạc.

Thông thường, các cycle được hiện thực bằng một bộ dao động luân phiên liên tục giữa hai pha có nhãn 0−1, low-high hoặc ticktock, như thể hiện trong hình 3.2. Khoảng thời gian trôi qua từ lúc bắt đầu một tick tới lúc kết thúc tock kế tiếp được gọi là một cycle, và mỗi cycle được xem như mô hình của một đơn vị thời gian rời rạc. Pha clock hiện tại, tick hay tock, được biểu diễn bằng một tín hiệu nhị phân. Thông qua mạch điện phần cứng, cùng một tín hiệu master clock được phát đồng thời tới mọi chip bộ nhớ trong hệ thống. Trong mỗi chip như vậy, đầu vào clock được dẫn xuống các cổng DFF mức thấp hơn, nơi nó bảo đảm rằng chip chỉ cam kết sang trạng thái mới và xuất trạng thái đó ra vào cuối chu kỳ clock.

#### 3.2.2 Flip-Flop

Các chip bộ nhớ được thiết kế để “ghi nhớ”, hay lưu trữ, thông tin theo thời gian. Các thiết bị mức thấp hiện thực hóa phép trừu tượng lưu trữ này được gọi là cổng flip-flop, trong đó có một số biến thể khác nhau. Trong Nand to Tetris, chúng ta dùng một biến thể gọi là data flip-flop, hay DFF, với giao diện gồm một đầu vào dữ liệu 1 bit và một đầu ra dữ liệu 1 bit (xem phần trên của hình 3.3). Ngoài ra, DFF còn có một đầu vào clock nhận tín hiệu từ master clock. Khi kết hợp lại, đầu vào dữ liệu và đầu vào clock cho phép DFF hiện thực hành vi phụ thuộc thời gian đơn giản `out(t) = in(t - 1)`, trong đó `in` và `out` là các giá trị đầu vào và đầu ra của cổng, còn `t` là đơn vị thời gian hiện tại (từ giờ trở đi, ta sẽ dùng xen kẽ hai thuật ngữ “đơn vị thời gian” và “cycle”). Tạm thời, ta chưa cần bận tâm hành vi này được hiện thực cụ thể ra sao. Lúc này, chỉ cần quan sát rằng ở cuối mỗi đơn vị thời gian, DFF xuất ra giá trị đầu vào của đơn vị thời gian trước đó.

![](../src/images/figure_3.3.png)

Hình 3.3    Data flip-flop (trên) và ví dụ hành vi (dưới). Trong cycle đầu tiên, đầu vào trước đó là chưa biết, nên đầu ra của DFF không được xác định. Ở mỗi đơn vị thời gian tiếp theo, DFF xuất ra đầu vào của đơn vị thời gian liền trước. Theo quy ước vẽ sơ đồ cổng, đầu vào clock được đánh dấu bằng một tam giác nhỏ ở đáy biểu tượng cổng.

Giống như các cổng Nand, cổng DFF nằm rất sâu trong hệ thứ bậc phần cứng. Như thể hiện ở hình 3.1, mọi chip bộ nhớ trong máy tính, từ thanh ghi, đơn vị RAM cho tới bộ đếm, xét tới cùng đều dựa trên các cổng DFF. Tất cả các DFF này đều nối tới cùng một master clock, tạo thành một “hàng đồng ca” phân tán khổng lồ. Vào cuối mỗi chu kỳ clock, đầu ra của toàn bộ DFF trong máy tính sẽ cam kết sang đầu vào của chúng từ cycle trước. Ở mọi thời điểm khác, các DFF ở trạng thái latched, nghĩa là các thay đổi ở đầu vào không tạo ra tác động tức thời lên đầu ra. Thao tác dẫn truyền này tác động tới từng cổng DFF trong vô số DFF của hệ thống nhiều lần mỗi giây, tùy thuộc vào tần số clock của máy tính.

Các hiện thực phần cứng hiện thực hóa sự phụ thuộc thời gian này bằng một bus clock chuyên dụng, đưa tín hiệu master clock đồng thời tới mọi cổng DFF trong hệ thống. Các bộ mô phỏng phần cứng thì mô phỏng cùng hiệu ứng đó bằng phần mềm. Cụ thể, bộ mô phỏng phần cứng Nand to Tetris có một biểu tượng clock, cho phép người dùng đẩy clock tiến lên theo cách tương tác, cũng như các lệnh tick và tock có thể được dùng theo chương trình trong các script kiểm thử.

### 3.2.3 Logic kết hợp và Logic tuần tự

Mọi chip được phát triển ở chương 1 và 2, bắt đầu từ các cổng Logic cơ bản và kết thúc ở ALU, đều được thiết kế chỉ để phản hồi với những thay đổi xảy ra trong cycle clock hiện tại. Những chip như vậy được gọi là chip độc lập thời gian, hay combinational chip. Tên gọi sau ám chỉ việc các chip này chỉ phản hồi với những tổ hợp khác nhau của các giá trị đầu vào, trong khi hoàn toàn không quan tâm tới sự trôi đi của thời gian.

Trái lại, những chip được thiết kế để phản hồi với các thay đổi đã xảy ra ở các đơn vị thời gian trước đó, và có thể cả trong đơn vị thời gian hiện tại, được gọi là sequential, hay clocked. Cổng tuần tự nền tảng nhất là DFF, và bất kỳ chip nào chứa nó, dù trực tiếp hay gián tiếp, cũng được xem là sequential. Hình 3.4 mô tả một cấu hình Logic tuần tự điển hình. Thành phần chính trong cấu hình này là một tập gồm một hoặc nhiều chip có chứa các chip-part DFF, trực tiếp hoặc gián tiếp. Như hình vẽ cho thấy, các chip tuần tự này cũng có thể tương tác với các chip kết hợp. Vòng hồi tiếp cho phép chip tuần tự phản hồi với đầu vào và đầu ra của đơn vị thời gian trước. Trong các chip kết hợp, nơi thời gian không được mô hình hóa cũng không được nhận biết, việc đưa vào các vòng hồi tiếp là có vấn đề: đầu ra của chip sẽ phụ thuộc vào đầu vào của nó, trong khi đầu vào đó lại phụ thuộc vào đầu ra, và như thế đầu ra sẽ phụ thuộc vào chính nó. Tuy nhiên, xin lưu ý rằng việc đưa đầu ra hồi tiếp trở lại đầu vào không gặp khó khăn gì miễn là vòng hồi tiếp đi qua một cổng DFF: DFF tạo ra một độ trễ thời gian cố hữu, nhờ đó đầu ra ở thời điểm `t` không phụ thuộc vào chính nó mà phụ thuộc vào đầu ra ở thời điểm `t - 1`.

![](../src/images/figure_3.4.png)

Hình 3.4    Thiết kế Logic tuần tự thường bao gồm các cổng DFF vừa nhận đầu vào từ, vừa nối tới, các chip kết hợp. Điều này trao cho chip tuần tự khả năng phản hồi với cả đầu vào và đầu ra hiện tại lẫn trước đó.

Sự phụ thuộc thời gian của các chip sequential có một tác dụng phụ quan trọng, đó là đồng bộ hóa toàn bộ kiến trúc máy tính. Để minh họa, giả sử ta yêu cầu ALU tính `x + y`, trong đó `x` là đầu ra của một thanh ghi gần, còn `y` là đầu ra của một thanh ghi RAM ở xa. Do các ràng buộc vật lý như khoảng cách, điện trở và nhiễu, các tín hiệu điện biểu diễn `x` và `y` nhiều khả năng sẽ tới ALU vào những thời điểm khác nhau. Tuy nhiên, vì là một chip combinational, ALU không nhạy với khái niệm thời gian; nó liên tục và vui vẻ cộng bất kỳ giá trị dữ liệu nào tình cờ nằm trên các đầu vào của nó. Vì vậy, sẽ cần một khoảng thời gian trước khi đầu ra của ALU ổn định về kết quả đúng của `x + y`. Cho tới lúc đó, ALU sẽ sinh ra dữ liệu rác.

Làm sao khắc phục khó khăn này? Nếu ta dùng biểu diễn thời gian rời rạc, thì đơn giản là ta không quan tâm. Tất cả những gì ta phải làm là bảo đảm, khi xây dựng clock của máy tính, rằng độ dài chu kỳ clock sẽ dài hơn một chút so với khoảng thời gian một bit cần để đi qua quãng đường dài nhất từ chip này tới chip khác, cộng với khoảng thời gian cần để hoàn tất phép tính nội bộ tốn thời gian nhất. Bằng cách đó, ta được bảo đảm rằng tới cuối chu kỳ clock, đầu ra của ALU sẽ hợp lệ. Tóm gọn lại, đây chính là mẹo biến một tập hợp các thành phần phần cứng độc lập thành một hệ thống được đồng bộ tốt. Chúng ta sẽ còn nói thêm về sự điều phối tổng thể này khi xây dựng kiến trúc máy tính ở chương 5.

### 3.3 Đặc tả

Giờ đây, chúng ta chuyển sang đặc tả các chip bộ nhớ thường dùng trong kiến trúc máy tính:

- data flip-flop (DFF)
- thanh ghi (dựa trên DFF)
- thiết bị RAM (dựa trên thanh ghi)
- bộ đếm (dựa trên thanh ghi)

Như thường lệ, chúng ta mô tả các chip này ở mức trừu tượng. Cụ thể, chúng ta tập trung vào giao diện của từng chip: đầu vào, đầu ra và chức năng. Việc các chip cung cấp chức năng đó như thế nào sẽ được bàn ở phần Implementation.

#### 3.3.1 Data Flip-Flop

Thiết bị sequential cơ bản nhất mà chúng ta sẽ dùng, tức thành phần nền tảng để xây dựng mọi chip bộ nhớ khác, là data flip-flop. Một cổng DFF có một đầu vào dữ liệu 1 bit, một đầu ra dữ liệu 1 bit, một đầu vào clock, và một hành vi phụ thuộc thời gian đơn giản: `out(t) = in(t - 1)`

Cách dùng: Nếu ta đặt một giá trị 1 bit vào đầu vào của DFF, trạng thái của DFF sẽ được đặt thành giá trị đó, và đầu ra của DFF sẽ phát nó ra ở đơn vị thời gian kế tiếp (xem hình 3.3). Thao tác khiêm tốn này sẽ tỏ ra cực kỳ hữu ích trong hiện thực các thanh ghi, nội dung được mô tả ngay sau đây.

#### 3.3.2 Thanh ghi

Chúng ta trình bày một thanh ghi 1 bit tên là Bit và một thanh ghi 16 bit tên là Register. Chip Bit được thiết kế để lưu trữ một bit thông tin, 0 hoặc 1, theo thời gian. Giao diện chip gồm một đầu vào `in` mang bit dữ liệu, một đầu vào `load` cho phép ghi vào thanh ghi, và một đầu ra `out` phát ra trạng thái hiện tại của thanh ghi. API của Bit và hành vi đầu vào/đầu ra của nó được mô tả ở hình 3.5.

![](../src/images/figure_3.5.png)

Hình 3.5    Thanh ghi 1 bit. Lưu trữ và phát ra một giá trị 1 bit cho tới khi được yêu cầu nạp một giá trị mới.

Hình 3.5 minh họa cách thanh ghi 1 bit hoạt động theo thời gian, phản hồi với các ví dụ tùy ý của đầu vào `in` và `load`. Lưu ý rằng bất kể giá trị đầu vào là gì, miễn là bit `load` không được kích hoạt, thanh ghi sẽ ở trạng thái latched, duy trì trạng thái hiện tại của nó.

Chip Register 16 bit hoạt động chính xác giống chip Bit, chỉ khác là nó được thiết kế để xử lý các giá trị 16 bit. Chi tiết được cho trong hình 3.6.

![](../src/images/figure_3.6.png)

Hình 3.6    Register 16 bit. Lưu trữ và phát ra một giá trị 16 bit cho tới khi được yêu cầu nạp một giá trị mới.

Cách dùng: Thanh ghi Bit và Register 16 bit được dùng giống hệt nhau. Để đọc trạng thái của thanh ghi, hãy thăm dò giá trị của `out`. Để đặt trạng thái của thanh ghi thành `v`, hãy đưa `v` vào đầu vào `in` và kích hoạt, tức đưa 1 vào, bit `load`. Việc này sẽ đặt trạng thái của thanh ghi thành `v`, và từ đơn vị thời gian kế tiếp trở đi, thanh ghi sẽ cam kết sang giá trị mới, còn đầu ra `out` của nó sẽ bắt đầu phát ra giá trị đó. Ta thấy rằng chip Register thực hiện đúng chức năng cổ điển của một thiết bị bộ nhớ: nó nhớ và phát ra giá trị cuối cùng đã được ghi vào nó, cho tới khi ta đặt cho nó một giá trị khác.

#### 3.3.3 Bộ nhớ truy cập ngẫu nhiên

Một đơn vị bộ nhớ truy cập trực tiếp, còn gọi là Random Access Memory, hay RAM, là một tập hợp gồm `n` chip Register. Bằng cách chỉ ra một địa chỉ cụ thể, một số trong khoảng từ 0 đến `n - 1`, mỗi thanh ghi trong RAM đều có thể được chọn và đưa vào cho các thao tác đọc/ghi. Điều quan trọng là thời gian truy cập tới bất kỳ thanh ghi bộ nhớ nào được chọn ngẫu nhiên đều là tức thời và không phụ thuộc vào địa chỉ của thanh ghi đó cũng như kích thước của RAM. Chính điều này làm cho thiết bị RAM hữu ích đến vậy: ngay cả khi chúng chứa hàng tỷ thanh ghi, ta vẫn có thể truy cập và thao tác trực tiếp từng thanh ghi được chọn trong cùng một khoảng thời gian truy cập tức thời. API của RAM được cho trong hình 3.7.

![](../src/images/figure_3.7.png)

Hình 3.7    Một chip RAM gồm `n` chip Register 16 bit có thể được chọn và thao tác riêng rẽ. Các địa chỉ thanh ghi từ 0 tới `n - 1` không phải là thành phần vật lý của chip. Thay vào đó, chúng được hiện thực bằng một cài đặt Logic cổng sẽ được bàn ở phần tiếp theo.

Cách dùng: Để đọc nội dung của thanh ghi số `m`, hãy đặt đầu vào `address` thành `m`. Hành động này sẽ chọn thanh ghi số `m`, và đầu ra của RAM sẽ phát ra giá trị của nó. Để ghi một giá trị mới `v` vào thanh ghi số `m`, hãy đặt đầu vào `address` thành `m`, đặt đầu vào `in` thành `v`, và kích hoạt bit `load`, tức đặt nó thành 1. Hành động này sẽ chọn thanh ghi số `m`, cho phép nó ghi dữ liệu, và đặt giá trị của nó thành `v`. Ở đơn vị thời gian tiếp theo, đầu ra của RAM sẽ bắt đầu phát ra `v`.

Kết quả ròng là thiết bị RAM hoạt động đúng như yêu cầu: một ngân hàng các thanh ghi định địa chỉ được, trong đó mỗi thanh ghi đều có thể được truy cập và thao tác một cách độc lập. Trong trường hợp thao tác đọc (`load == 0`), đầu ra của RAM sẽ phát ngay giá trị của thanh ghi được chọn. Trong trường hợp thao tác ghi (`load == 1`), thanh ghi bộ nhớ được chọn sẽ được đặt thành giá trị đầu vào, và đầu ra của RAM sẽ bắt đầu phát giá trị đó từ đơn vị thời gian kế tiếp.

Điều quan trọng là cài đặt RAM phải bảo đảm rằng thời gian truy cập tới bất kỳ thanh ghi nào trong RAM đều gần như tức thời. Nếu không như vậy, ta sẽ không thể nạp lệnh và thao tác biến trong một khoảng thời gian hợp lý, khiến máy tính chậm tới mức không thực tế. Phép màu của thời gian truy cập tức thời này sẽ sớm được hé lộ trong phần Implementation.

#### 3.3.4 Bộ đếm

Counter là một chip biết cách tăng giá trị của nó thêm 1 ở mỗi đơn vị thời gian. Khi xây dựng kiến trúc máy tính ở chương 5, chúng ta sẽ gọi chip này là Program Counter, hay PC, nên ở đây cũng sẽ dùng luôn tên đó.

Giao diện của chip PC của chúng ta giống hệt giao diện của một thanh ghi, ngoại trừ việc nó còn có thêm các bit điều khiển tên là `inc` và `reset`. Khi `inc == 1`, bộ đếm sẽ tăng trạng thái của nó ở mỗi chu kỳ clock, thực hiện phép toán `PC++`. Nếu muốn đặt lại bộ đếm về 0, ta kích hoạt bit `reset`; nếu muốn đặt bộ đếm thành giá trị `v`, ta đưa `v` vào đầu vào `in` và kích hoạt bit `load`, như ta vẫn làm với thanh ghi. Các chi tiết được cho trong hình 3.8.

![](../src/images/figure_3.8.png)

Cách dùng: Để đọc nội dung hiện tại của PC, hãy thăm dò chân `out`. Để reset PC, hãy kích hoạt bit `reset` và đặt các bit điều khiển còn lại về 0. Để PC tăng thêm 1 ở mỗi đơn vị thời gian cho tới khi có chỉ thị khác, hãy kích hoạt bit `inc` và đặt các bit điều khiển còn lại về 0. Để đặt PC thành giá trị `v`, hãy đặt đầu vào `in` thành `v`, kích hoạt bit `load`, và đặt các bit điều khiển còn lại về 0.

### 3.4 Implementation

Phần trước đã trình bày một họ trừu tượng chip bộ nhớ, tập trung vào giao diện và chức năng của chúng. Phần này tập trung vào cách các chip đó có thể được hiện thực bằng các chip đơn giản hơn đã được xây dựng từ trước. Như thường lệ, các hướng dẫn hiện thực của chúng tôi mang tính gợi ý có chủ đích; chúng tôi muốn đưa cho bạn vừa đủ gợi ý để bạn tự hoàn thành phần hiện thực bằng HDL và bộ mô phỏng phần cứng đi kèm.

#### 3.4.1 Data Flip-Flop

Một cổng DFF được thiết kế để có thể “flip-flop” giữa hai trạng thái ổn định, biểu diễn 0 và biểu diễn 1. Chức năng này có thể được hiện thực theo nhiều cách khác nhau, bao gồm cả những cách chỉ dùng các cổng Nand. Các cài đặt DFF dựa trên Nand rất thanh nhã nhưng cũng rất tinh vi, và không thể mô hình hóa trong bộ mô phỏng phần cứng của chúng ta vì chúng đòi hỏi các vòng hồi tiếp giữa các cổng combinational. Muốn trừu tượng hóa sự phức tạp đó, chúng ta sẽ coi DFF như một khối xây dựng nguyên thủy. Cụ thể, bộ mô phỏng phần cứng Nand to Tetris cung cấp sẵn một cài đặt built-in của DFF có thể được các chip khác sử dụng trực tiếp, và đó chính là điều chúng ta sẽ mô tả ngay sau đây.

#### 3.4.2 Thanh ghi

Các chip thanh ghi là thiết bị bộ nhớ: chúng được kỳ vọng sẽ ghi nhớ và phát ra trạng thái của mình theo thời gian. Điều này trông khá giống hành vi của DFF, vốn phát ra đầu vào của cycle trước. Nếu chỉ cần hồi tiếp đầu ra của DFF trở lại đầu vào của nó thì đây có thể là một điểm khởi đầu tốt để hiện thực thanh ghi Bit 1 bit. Lời giải này được thể hiện ở phía bên trái hình 3.9.

![](../src/images/figure_3.9.png)

Hình 3.9    Hiện thực Bit (thanh ghi 1 bit): lời giải không hợp lệ (trái) và lời giải đúng (phải).

Hóa ra cài đặt ở bên trái hình 3.9 là không hợp lệ, vì một số lý do liên quan với nhau. Thứ nhất, cài đặt này không lộ ra bit `load`, như giao diện thanh ghi yêu cầu. Thứ hai, không có cách nào để nói cho chip-part DFF biết khi nào nó phải lấy đầu vào từ dây `in` và khi nào phải lấy từ giá trị `out` hồi tiếp vào. Quả thật, các quy tắc lập trình HDL cấm việc cấp dữ liệu cho một chân từ nhiều hơn một nguồn.

Điểm tốt của thiết kế không hợp lệ này là nó dẫn ta tới cài đặt đúng, được thể hiện ở bên phải hình 3.9. Như sơ đồ chip cho thấy, một cách tự nhiên để giải quyết sự nhập nhằng đầu vào là đưa một multiplexer vào thiết kế. Bit `load` của toàn bộ chip thanh ghi khi đó có thể được dẫn tới bit chọn của multiplexer bên trong: nếu ta đặt bit này thành 1, multiplexer sẽ đưa giá trị `in` vào DFF; nếu đặt bit `load` thành 0, multiplexer sẽ đưa đầu ra trước đó của DFF vào. Cách này sẽ tạo ra hành vi “nếu `load` thì đặt thanh ghi thành giá trị mới, ngược lại thì giữ giá trị đã lưu trước đó”, đúng chính xác với cách ta muốn thanh ghi hoạt động.

Lưu ý rằng vòng hồi tiếp vừa mô tả không kéo theo các vấn đề đua dữ liệu dạng chu kỳ: vòng đó đi qua một cổng DFF, và DFF tạo ra một độ trễ thời gian. Thực ra, thiết kế Bit trong hình 3.9 là một trường hợp đặc biệt của thiết kế Logic tuần tự tổng quát trong hình 3.4.

Sau khi hoàn thành cài đặt thanh ghi Bit 1 bit, ta có thể chuyển sang xây dựng một thanh ghi `w` bit. Điều này có thể thực hiện bằng cách tạo một mảng gồm `w` chip Bit, xem hình 3.1. Tham số thiết kế cơ bản của một thanh ghi như vậy là `w`, số lượng bit mà nó phải giữ, chẳng hạn 16, 32 hoặc 64. Vì máy tính Hack sẽ dựa trên một nền tảng phần cứng 16 bit, chip Register của chúng ta sẽ dựa trên mười sáu chip-part Bit.

Thanh ghi Bit là chip duy nhất trong kiến trúc Hack dùng trực tiếp một cổng DFF; mọi thiết bị bộ nhớ cấp cao hơn trong máy tính đều dùng DFF một cách gián tiếp, nhờ sử dụng các chip Register được tạo từ các chip Bit. Lưu ý rằng việc đưa một cổng DFF vào thiết kế của bất kỳ chip nào, dù trực tiếp hay gián tiếp, sẽ biến chip đó, cũng như mọi chip cấp cao hơn dùng nó làm chip-part, thành chip phụ thuộc thời gian.

#### 3.4.3 RAM

Nền tảng phần cứng Hack cần một thiết bị RAM gồm 16K (16384) thanh ghi 16 bit, và đó chính là thứ chúng ta phải hiện thực. Chúng tôi đề xuất lộ trình hiện thực dần dần như sau:

![](../src/images/figure_wo_caption_3.1.png)

Tất cả các chip bộ nhớ này đều có cùng một API RAMn như trong hình 3.7. Mỗi chip RAM có `n` thanh ghi, và độ rộng của đầu vào `address` là `k` bit, với `n = 2^k`. Giờ đây, chúng ta mô tả cách các chip này có thể được hiện thực, bắt đầu với RAM8.

Một chip RAM8 có 8 thanh ghi, như thể hiện trong hình 3.7 với `n = 8`. Mỗi thanh ghi có thể được chọn bằng cách đặt đầu vào `address` 3 bit của RAM8 thành một giá trị từ 0 đến 7. Hành động đọc giá trị của một thanh ghi được chọn có thể mô tả như sau: với một địa chỉ nào đó, tức một giá trị từ 0 đến 7, làm thế nào ta có thể “chọn” thanh ghi số `address` và dẫn đầu ra của nó tới đầu ra của RAM8? Gợi ý: Ta có thể làm điều đó bằng một trong các chip combinational đã xây ở project 1. Đó là lý do việc đọc giá trị của một thanh ghi RAM được chọn diễn ra gần như tức thời, không phụ thuộc vào clock cũng không phụ thuộc vào số lượng thanh ghi trong RAM. Tương tự, hành động ghi một giá trị vào thanh ghi được chọn có thể mô tả như sau: với một giá trị `address`, một giá trị `load` bằng 1 và một giá trị `in` 16 bit, làm thế nào ta có thể đặt giá trị của thanh ghi số `address` thành `in`? Gợi ý: dữ liệu `in` 16 bit có thể được cấp đồng thời vào các đầu vào `in` của cả tám chip Register. Bằng cách dùng thêm một chip combinational khác đã phát triển trong project 1, cùng với các đầu vào `address` và `load`, bạn có thể bảo đảm rằng chỉ một thanh ghi sẽ chấp nhận giá trị `in` đi vào, còn bảy thanh ghi còn lại sẽ bỏ qua nó.

Xin lưu ý thêm rằng các thanh ghi RAM không được “đánh dấu địa chỉ” theo nghĩa vật lý nào cả. Thay vào đó, Logic vừa mô tả có khả năng, và cũng là đủ, để chọn từng thanh ghi riêng lẻ theo địa chỉ của chúng, và việc này được thực hiện nhờ dùng các chip kết hợp. Và đây là một quan sát cực kỳ quan trọng: vì Logic kết hợp độc lập với thời gian, nên thời gian truy cập tới bất kỳ thanh ghi riêng lẻ nào cũng sẽ gần như tức thời.

Sau khi đã hiện thực xong chip RAM8, chúng ta có thể chuyển sang hiện thực RAM64. Cài đặt này có thể dựa trên tám chip-part RAM8. Để chọn một thanh ghi cụ thể trong bộ nhớ RAM64, ta dùng một địa chỉ 6 bit, chẳng hạn `xxxyyy`. Ba bit `xxx` có thể được dùng để chọn một trong các chip RAM8, còn ba bit `yyy` có thể được dùng để chọn một trong các thanh ghi bên trong RAM8 đã chọn. Sơ đồ định địa chỉ phân cấp này có thể được hiện thực bằng Logic cổng. Ý tưởng hiện thực tương tự cũng có thể dẫn dắt việc xây dựng các chip RAM512, RAM4K và RAM16K còn lại.

Tóm lại, chúng ta lấy một tập hợp gồm số lượng không giới hạn các thanh ghi, rồi áp đặt lên nó một siêu cấu trúc combinational cho phép truy cập trực tiếp tới bất kỳ thanh ghi riêng lẻ nào. Hy vọng rằng vẻ đẹp của lời giải này không bị người đọc bỏ lỡ.

#### 3.4.4 Bộ đếm

Một bộ đếm là một thiết bị bộ nhớ có thể tăng giá trị của nó ở mỗi đơn vị thời gian. Ngoài ra, bộ đếm còn có thể được đặt về 0 hoặc một giá trị khác. Hai chức năng lưu trữ và đếm cơ bản của bộ đếm lần lượt có thể được hiện thực bằng một chip Register và chip incrementer đã xây ở project 2. Logic dùng để chọn giữa các chế độ `inc`, `load` và `reset` của bộ đếm có thể được hiện thực bằng một số multiplexer đã xây ở project 1.

### 3.5 Project

Mục tiêu: Xây dựng toàn bộ các chip được mô tả trong chương. Các khối xây dựng mà bạn có thể dùng là các cổng DFF nguyên thủy, các chip mà bạn sẽ xây chồng lên chúng, cùng các cổng đã xây ở các chương trước.

Tài nguyên: Công cụ duy nhất mà bạn cần cho project này là bộ mô phỏng phần cứng Nand to Tetris. Mọi chip phải được hiện thực bằng ngôn ngữ HDL được đặc tả trong phụ lục 2. Như thường lệ, với mỗi chip chúng tôi cung cấp một chương trình `.hdl` khung có thiếu phần cài đặt, một tệp script `.tst` chỉ cho bộ mô phỏng phần cứng cách kiểm thử nó, và một tệp so sánh `.cmp` định nghĩa kết quả kỳ vọng. Nhiệm vụ của bạn là hoàn tất các phần cài đặt còn thiếu trong các chương trình `.hdl` được cung cấp.

Cam kết: Khi được nạp vào bộ mô phỏng phần cứng, thiết kế chip của bạn, tức chương trình `.hdl` đã chỉnh sửa, khi được kiểm thử bằng tệp `.tst` đi kèm, phải tạo ra các đầu ra được liệt kê trong tệp `.cmp` tương ứng. Nếu không đúng như vậy, bộ mô phỏng sẽ cho bạn biết.

Mẹo: Cổng data flip-flop (DFF) được xem là nguyên thủy; vì vậy không cần phải xây nó. Khi bộ mô phỏng bắt gặp một chip-part DFF trong một chương trình HDL, nó sẽ tự động gọi cài đặt `tools/builtIn/DFF.hdl`.

Cấu trúc thư mục của project này: Khi xây các chip RAM từ các chip-part RAM mức thấp hơn, chúng tôi khuyên bạn nên dùng các phiên bản built-in của các chip-part đó. Nếu không, bộ mô phỏng sẽ phải đệ quy sinh ra rất nhiều đối tượng phần mềm cư trú trong bộ nhớ, một đối tượng cho mỗi chip-part trong vô số chip-part cấu thành nên một đơn vị RAM điển hình. Điều này có thể khiến bộ mô phỏng chạy chậm hoặc, tệ hơn, dùng cạn bộ nhớ của máy chủ đang chạy bộ mô phỏng.

Để tránh vấn đề này, chúng tôi đã chia các chip RAM được xây trong project này vào hai thư mục con. Các chương trình `RAM8.hdl` và `RAM64.hdl` được đặt trong `projects/03/a`, còn các chip RAM cấp cao hơn khác được đặt trong `projects/03/b`. Việc phân chia này chỉ nhằm một mục đích: khi đánh giá các chip RAM nằm trong thư mục `b`, bộ mô phỏng sẽ buộc phải dùng các cài đặt built-in của các chip-part RAM64, vì `RAM64.hdl` không tồn tại trong thư mục `b`.

Các bước: Chúng tôi khuyên nên tiến hành theo thứ tự sau:

1. Bộ mô phỏng phần cứng cần cho project này có sẵn trong `nand2tetris/tools`.
2. Hãy tham khảo phụ lục 2 và Hardware Simulator Tutorial khi cần.
3. Hãy xây dựng và mô phỏng toàn bộ các chip được đặc tả trong thư mục `projects/03`.

Một phiên bản web của project 3 có tại www.nand2tetris.org.

### 3.6 Góc nhìn

Nền móng của mọi hệ thống bộ nhớ được mô tả trong chương này là flip-flop, thứ mà chúng ta đã đối xử một cách trừu tượng như một cổng nguyên thủy built-in. Cách tiếp cận thông thường là xây dựng flip-flop từ các cổng combinational cơ bản, chẳng hạn cổng Nand, được nối với nhau trong các vòng hồi tiếp. Cấu trúc tiêu chuẩn bắt đầu bằng việc xây một flip-flop không clock, có tính hai ổn định, tức có thể được đặt vào một trong hai trạng thái, lưu 0 hoặc lưu 1. Sau đó, một flip-flop có clock được tạo ra bằng cách móc nối hai flip-flop không clock như vậy, trong đó cái thứ nhất được đặt khi clock tick và cái thứ hai được đặt khi clock tock. Thiết kế master-slave này trao cho flip-flop tổng thể khả năng đồng bộ hóa theo clock đúng như mong muốn.

Những cài đặt flip-flop như vậy vừa thanh nhã vừa tinh vi. Trong cuốn sách này, chúng ta đã chọn trừu tượng hóa các cân nhắc mức thấp đó bằng cách xem flip-flop là một cổng nguyên thủy. Những độc giả muốn khám phá cấu trúc bên trong của cổng flip-flop có thể tìm thấy các mô tả chi tiết trong hầu hết các giáo trình thiết kế Logic và kiến trúc máy tính.

Một lý do để không đào sâu vào những điều huyền bí của flip-flop là vì mức thấp nhất của các thiết bị bộ nhớ dùng trong máy tính hiện đại không nhất thiết được xây từ các cổng flip-flop. Thay vào đó, các chip bộ nhớ hiện đại được tối ưu rất cẩn thận, khai thác các tính chất vật lý độc đáo của công nghệ lưu trữ nền bên dưới. Ngày nay, có nhiều công nghệ thay thế như vậy sẵn có cho các nhà thiết kế máy tính; như thường lệ, việc dùng công nghệ nào là một bài toán đánh đổi giữa chi phí và hiệu năng. Tương tự, phương pháp leo thang đệ quy mà chúng ta dùng để xây các chip RAM là thanh nhã nhưng không nhất thiết hiệu quả. Những cài đặt hiệu quả hơn hoàn toàn có thể có.

Bỏ qua các cân nhắc vật lý đó, toàn bộ các cấu trúc chip được mô tả trong chương này, từ thanh ghi, bộ đếm cho tới các chip RAM, đều là chuẩn mực, và các biến thể của chúng có thể được tìm thấy trong mọi hệ thống máy tính.

Ở chương 5, chúng ta sẽ dùng các chip thanh ghi đã xây trong chương này, cùng với ALU đã xây ở chương 2, để xây dựng một Central Processing Unit. CPU sau đó sẽ được mở rộng thêm bằng một thiết bị RAM, dẫn tới một kiến trúc máy tính đa dụng có khả năng thực thi các chương trình được viết bằng ngôn ngữ máy. Ngôn ngữ máy này sẽ được bàn ở chương tiếp theo.
