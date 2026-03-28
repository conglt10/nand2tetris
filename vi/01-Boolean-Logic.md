# 1 Boolean Logic

>Những thứ đơn giản đến vậy, thế mà ta lại làm từ chúng nên một điều gì đó phức tạp đến mức gần như đánh bại chính mình.

—John Ashbery (1927–2017)

Mọi thiết bị số, dù là máy tính cá nhân, điện thoại di động hay bộ định tuyến mạng, đều dựa trên một tập hợp các chip được thiết kế để lưu trữ và xử lý thông tin nhị phân. Dù các chip này có hình dạng và kiểu thức khác nhau, tất cả chúng đều được tạo nên từ cùng những khối xây dựng cơ bản: các cổng Logic sơ cấp. Các cổng này có thể được hiện thực vật lý bằng nhiều công nghệ phần cứng khác nhau, nhưng hành vi Logic của chúng, hay mức trừu tượng của chúng, là nhất quán trên mọi cách triển khai.

Trong chương này, chúng ta sẽ bắt đầu với một cổng Logic nguyên thủy là Nand và từ đó xây dựng mọi cổng Logic khác mà ta cần. Cụ thể, chúng ta sẽ xây dựng các cổng Not, And, Or và Xor, cùng với hai cổng mang tên multiplexer và demultiplexer (chức năng của tất cả các cổng này sẽ được mô tả bên dưới). Vì máy tính mục tiêu của chúng ta được thiết kế để hoạt động trên các giá trị 16-bit, ta cũng sẽ xây dựng các phiên bản 16-bit của những cổng cơ bản, như Not16, And16, v.v. Kết quả sẽ là một bộ cổng Logic khá tiêu chuẩn, về sau sẽ được dùng để dựng nên các chip xử lý và bộ nhớ của máy tính. Việc đó sẽ được thực hiện lần lượt ở chương 2 và chương 3.

Chương này mở đầu bằng tập hợp tối thiểu các khái niệm lý thuyết và công cụ thực hành cần thiết để thiết kế và hiện thực các cổng Logic. Cụ thể, chúng ta giới thiệu đại số Boolean và các hàm Boolean, rồi chỉ ra cách các hàm Boolean có thể được hiện thực bởi các cổng Logic. Sau đó, chúng ta mô tả cách hiện thực các cổng Logic bằng một Hardware Description Language (HDL) và cách kiểm thử các thiết kế này bằng các bộ mô phỏng phần cứng. Phần dẫn nhập này sẽ tiếp tục phát huy tác dụng trong suốt phần I của cuốn sách, vì đại số Boolean và HDL sẽ xuất hiện trong mọi chương và dự án phần cứng sắp tới.

## 1.1 Đại số Boolean

Đại số Boolean thao tác trên các giá trị nhị phân hai trạng thái, thường được gắn nhãn là đúng/sai, 1/0, có/không, bật/tắt, v.v. Chúng ta sẽ dùng 1 và 0. Một hàm Boolean là hàm nhận đầu vào nhị phân và trả về đầu ra nhị phân. Vì phần cứng máy tính dựa trên việc biểu diễn và thao tác các giá trị nhị phân, các hàm Boolean giữ vai trò trung tâm trong việc đặc tả, phân tích và tối ưu hóa các kiến trúc phần cứng.

Toán tử Boolean: Hình 1.1 trình bày ba hàm Boolean thường dùng, còn được gọi là các toán tử Boolean. Các hàm này có tên là And, Or và Not, cũng được viết bằng ký hiệu , , và , hoặc  và ¬x, tương ứng. Hình 1.2 đưa ra định nghĩa của mọi hàm Boolean có thể được xác định trên hai biến, cùng với các tên gọi thông dụng của chúng. Các hàm này được xây dựng một cách có hệ thống bằng cách liệt kê mọi tổ hợp giá trị có thể của hai biến nhị phân. Mỗi toán tử có một tên quy ước nhằm mô tả ngữ nghĩa nền tảng của nó. Chẳng hạn, tên của toán tử Nand là dạng viết tắt của Not-And, xuất phát từ nhận xét rằng Nand (x, y) tương đương với Not (And (x, y)). Toán tử Xor, viết tắt của exclusive or, cho giá trị 1 khi chính xác một trong hai biến của nó bằng 1. Cổng Nor có tên bắt nguồn từ Not-Or. Tuy vậy, bản thân các tên cổng này không phải điều quá quan trọng.

![](../src/images/figure_1.1.png)

Hình 1.1    Ba hàm Boolean sơ cấp.

![](../src/images/figure_1.2.png)

Hình 1.2    Tất cả các hàm Boolean của hai biến nhị phân. Nói chung, số lượng hàm Boolean được tạo bởi n biến nhị phân (ở đây ) là  (một con số rất lớn).

Hình 1.2 gợi ra một câu hỏi: Điều gì khiến And, Or và Not trở nên thú vị hơn, hay được ưu ái hơn, so với bất kỳ tập con nào khác của các toán tử Boolean? Câu trả lời ngắn gọn là thật ra không có gì đặc biệt ở And, Or và Not cả. Câu trả lời sâu hơn là có nhiều tập con khác nhau của các toán tử Logic có thể được dùng để biểu diễn mọi hàm Boolean, và {And, Or, Not} là một trong những tập như vậy. Nếu bạn thấy nhận định này ấn tượng, hãy xét thêm điều này: mỗi một trong ba toán tử cơ bản ấy đều có thể được biểu diễn bằng một toán tử khác nữa là Nand. Điều đó mới thật sự ấn tượng. Hệ quả là mọi hàm Boolean đều có thể được hiện thực chỉ bằng các cổng Nand. Phụ lục 1, là phần đọc thêm không bắt buộc, đưa ra chứng minh cho khẳng định đáng chú ý này.

### Boolean Functions

Mọi hàm Boolean đều có thể được định nghĩa bằng hai cách biểu diễn thay thế. Thứ nhất, ta có thể định nghĩa hàm bằng bảng chân trị, như trong hình 1.3. Với mỗi bộ giá trị có thể có trong số 2n bộ giá trị của các biến  (ở đây ), bảng liệt kê giá trị của f . Ngoài cách định nghĩa theo dữ liệu này, ta cũng có thể định nghĩa các hàm Boolean bằng biểu thức Boolean, chẳng hạn  And Not (z)

![](../src/images/figure_1.3.png)

Hình 1.3    Bảng chân trị và định nghĩa hàm của một hàm Boolean (ví dụ).

Làm sao ta có thể kiểm chứng rằng một biểu thức Boolean cho trước là tương đương với một bảng chân trị cho trước? Hãy dùng hình 1.3 làm ví dụ. Bắt đầu từ dòng đầu tiên, ta tính f (0, 0, 0), tức là (0 Or 0) And Not (0). Biểu thức này cho kết quả 0, đúng bằng giá trị được liệt kê trong bảng chân trị. Tạm thời mọi thứ đều ổn. Một phép thử tương đương tương tự có thể áp dụng cho mọi dòng trong bảng, nhưng đó là một công việc khá tẻ nhạt. Thay vì dùng kỹ thuật chứng minh từ dưới lên đầy nặng nhọc này, ta có thể chứng minh tương đương theo hướng từ trên xuống bằng cách phân tích biểu thức Boolean (x Or y) And Not (z). Tập trung vào vế bên trái của toán tử And, ta thấy rằng toàn bộ biểu thức chỉ cho giá trị 1 khi ((x bằng 1) Or (y bằng 1)). Chuyển sang vế bên phải của toán tử And, ta thấy toàn bộ biểu thức chỉ cho giá trị 1 khi (z bằng 0). Ghép hai nhận xét này lại, ta kết luận rằng biểu thức chỉ cho giá trị 1 khi (((x bằng 1) Or (y bằng 1)) And (z bằng 0)). Mẫu 0 và 1 này chỉ xuất hiện ở các dòng 3, 5 và 7 của bảng chân trị, và quả thật đó cũng là những dòng duy nhất mà cột ngoài cùng bên phải của bảng chứa giá trị 1.

### Truth Tables and Boolean Expressions

Cho một hàm Boolean gồm n biến được biểu diễn bằng một biểu thức Boolean, ta luôn có thể dựng từ đó bảng chân trị của hàm. Ta chỉ việc tính giá trị của hàm cho mọi tập giá trị (mọi dòng) trong bảng. Cách dựng này tuy nặng nhọc nhưng hiển nhiên. Đồng thời, phép dựng ngược lại thì hoàn toàn không hiển nhiên: Cho biểu diễn bảng chân trị của một hàm Boolean, liệu ta luôn có thể tổng hợp từ đó một biểu thức Boolean cho hàm nền tảng hay không? Câu trả lời cho câu hỏi hấp dẫn này là có. Một chứng minh có thể được tìm thấy ở phụ lục 1.

Khi nói đến việc xây dựng máy tính, biểu diễn bằng bảng chân trị, biểu thức Boolean, và khả năng chuyển từ dạng này sang dạng kia đều cực kỳ quan trọng. Ví dụ, giả sử chúng ta được giao xây dựng phần cứng cho việc giải trình tự dữ liệu DNA và chuyên gia sinh học của lĩnh vực đó muốn mô tả Logic giải trình tự bằng một bảng chân trị. Nhiệm vụ của chúng ta là hiện thực Logic này trong phần cứng. Lấy dữ liệu bảng chân trị đã cho làm điểm xuất phát, ta có thể tổng hợp từ đó một biểu thức Boolean biểu diễn hàm nền tảng. Sau khi đơn giản hóa biểu thức bằng đại số Boolean, ta có thể tiến hành hiện thực nó bằng các cổng Logic, như ta sẽ làm ở phần sau của chương. Tóm lại, bảng chân trị thường là phương tiện thuận tiện để mô tả một số trạng thái của tự nhiên, còn biểu thức Boolean là một hình thức thuận tiện để hiện thực mô tả đó trong silicon. Khả năng chuyển từ một biểu diễn sang biểu diễn kia là một trong những thực hành quan trọng nhất của thiết kế phần cứng.

Nhân tiện, ta lưu ý rằng mặc dù biểu diễn bảng chân trị của một hàm Boolean là duy nhất, mỗi hàm Boolean có thể được biểu diễn bằng nhiều biểu thức Boolean khác nhau nhưng tương đương, và một số biểu thức sẽ ngắn hơn, dễ làm việc hơn. Ví dụ, biểu thức (Not (x And y) And (Not (x) Or y) And (Not (y) Or y)) tương đương với biểu thức Not (x). Ta thấy rằng khả năng đơn giản hóa một biểu thức Boolean là bước đầu tiên hướng tới tối ưu hóa phần cứng. Việc này được thực hiện bằng đại số Boolean và lẽ thường, như được minh họa ở phụ lục 1.

## 1.2 Logic Gates

Một cổng là thiết bị vật lý hiện thực một hàm Boolean đơn giản. Mặc dù ngày nay phần lớn máy tính số dùng điện để hiện thực các cổng và biểu diễn dữ liệu nhị phân, bất kỳ công nghệ thay thế nào cho phép chuyển mạch và dẫn truyền cũng đều có thể được sử dụng. Thật vậy, qua nhiều năm, đã có rất nhiều cách hiện thực phần cứng cho các hàm Boolean được tạo ra, bao gồm các cơ chế từ tính, quang học, sinh học, thủy lực, khí nén, dựa trên lượng tử, thậm chí cả dựa trên quân domino (nhiều cách trong số này được đề xuất như những màn trình diễn kiểu “xem tôi làm được gì”). Ngày nay, các cổng thường được hiện thực bằng transistor khắc trên silicon, rồi đóng gói thành chip. Trong Nand to Tetris, chúng ta dùng hai từ chip và gate thay thế cho nhau, tuy có xu hướng dùng từ gate cho những trường hợp đơn giản của chip.

Sự tồn tại của các công nghệ chuyển mạch thay thế, một mặt, và nhận xét rằng đại số Boolean có thể được dùng để trừu tượng hóa hành vi của các cổng Logic, mặt khác, là điều cực kỳ quan trọng. Về cơ bản, điều đó ngụ ý rằng các nhà khoa học máy tính không cần phải bận tâm tới các hiện vật vật lý như điện, mạch, công tắc, relay và nguồn điện. Thay vào đó, họ hài lòng với những khái niệm trừu tượng của đại số Boolean và Logic cổng, và ung dung tin rằng ai đó khác, tức các nhà vật lý và kỹ sư điện, sẽ tìm ra cách hiện thực chúng trong phần cứng. Do đó, các cổng nguyên thủy như những cổng trong hình 1.4 có thể được xem như các hộp đen hiện thực những phép toán Logic sơ cấp bằng cách này hay cách khác, và chúng ta không quan tâm cách làm cụ thể. Việc dùng đại số Boolean để phân tích hành vi trừu tượng của các cổng Logic đã được Claude Shannon trình bày vào năm 1937, dẫn tới điều đôi khi được mô tả là luận văn thạc sĩ quan trọng nhất trong khoa học máy tính.

![](../src/images/figure_1.4.png)

Hình 1.4    Các sơ đồ cổng chuẩn của ba cổng Logic sơ cấp.

### Primitive and Composite Gates

Vì mọi cổng Logic đều có cùng kiểu dữ liệu đầu vào và đầu ra (0 và 1), chúng có thể được kết hợp để tạo ra các cổng tổng hợp với độ phức tạp tùy ý. Chẳng hạn, giả sử ta được yêu cầu hiện thực hàm Boolean ba ngôi And (a, b, c), trả về 1 khi mọi đầu vào của nó đều bằng 1, và trả về 0 trong các trường hợp còn lại. Dùng đại số Boolean, trước hết ta có thể nhận thấy rằng  hoặc, dùng ký pháp tiền tố, And  (And (a, b), c). Tiếp theo, ta có thể dùng kết quả này để xây dựng cổng tổng hợp được mô tả ở hình 1.5.

![](../src/images/figure_1.5.png)

Hình 1.5    Cách hiện thực tổng hợp của cổng And ba ngôi. Đường viền nét đứt hình chữ nhật xác định ranh giới của giao diện cổng.

Ta thấy rằng bất kỳ cổng Logic nào cũng có thể được nhìn từ hai góc độ khác nhau: bên trong và bên ngoài. Phía bên phải của hình 1.5 cho thấy kiến trúc nội bộ, hay cách hiện thực, của cổng, trong khi phía bên trái cho thấy giao diện cổng, tức các chân vào và ra của nó cùng hành vi mà nó phơi bày ra thế giới bên ngoài. Góc nhìn bên trong chỉ liên quan tới người xây cổng, còn góc nhìn bên ngoài là mức chi tiết phù hợp cho những nhà thiết kế muốn dùng cổng như một thành phần trừu tượng có sẵn, không cần để ý tới cách hiện thực của nó.

Hãy xét một ví dụ thiết kế Logic khác: Xor. Theo định nghĩa, Xor (a, b) bằng 1 chính xác khi hoặc a bằng 1 và b bằng 0, hoặc a bằng 0 và b bằng 1. Nói cách khác,  Xor(a,b) = Or(And(Not (a), b)). Định nghĩa này được hiện thực trong thiết kế Logic ở hình 1.6.

![](../src/images/figure_1.6.png)

Hình 1.6    Giao diện cổng Xor (bên trái) và một cách hiện thực khả dĩ (bên phải).

Lưu ý rằng giao diện của bất kỳ cổng nào là duy nhất: chỉ có một cách để đặc tả nó, và việc này thường được làm bằng bảng chân trị, biểu thức Boolean hoặc mô tả bằng lời. Tuy nhiên, giao diện đó có thể được hiện thực theo nhiều cách khác nhau, và một số cách sẽ thanh nhã, hiệu quả hơn những cách khác. Ví dụ, cách hiện thực Xor trong hình 1.6 là một khả năng; vẫn còn những cách hiệu quả hơn để hiện thực Xor, dùng ít cổng Logic hơn và ít kết nối liên cổng hơn. Vì vậy, xét từ góc độ chức năng, yêu cầu nền tảng của thiết kế Logic là cách hiện thực cổng phải hiện thực đúng giao diện đã công bố của nó, bằng cách này hay cách khác. Còn xét từ góc độ hiệu quả, quy tắc chung là cố gắng dùng càng ít cổng càng tốt, vì ít cổng hơn đồng nghĩa với chi phí thấp hơn, tiêu thụ năng lượng ít hơn và tính toán nhanh hơn.

Tóm lại, nghệ thuật thiết kế Logic có thể được mô tả như sau: Cho một trừu tượng cổng (còn được gọi là đặc tả hoặc giao diện), hãy tìm một cách hiệu quả để hiện thực nó bằng những cổng khác đã được hiện thực trước đó.

### 1.3 Hardware Construction

Giờ đây chúng ta đã ở vị thế có thể bàn về cách các cổng thực sự được xây dựng. Hãy bắt đầu bằng một ví dụ cố ý ngây thơ. Giả sử chúng ta mở một xưởng chế tạo chip trong gara ở nhà. Hợp đồng đầu tiên là chế tạo một trăm cổng Xor. Dùng tiền đặt cọc của đơn hàng, ta mua một mỏ hàn, một cuộn dây đồng và ba thùng được dán nhãn “And gates”, “Or gates” và “Not gates”, mỗi thùng chứa nhiều bản sao giống hệt nhau của các cổng Logic sơ cấp tương ứng. Mỗi cổng như vậy được bọc trong một vỏ nhựa để lộ ra một số chân vào, chân ra, cùng một cổng cấp nguồn. Mục tiêu của chúng ta là hiện thực sơ đồ cổng trong hình 1.6 bằng phần cứng này.

Ta bắt đầu bằng cách lấy hai cổng And, hai cổng Not và một cổng Or, rồi gắn chúng lên một bảng theo bố cục của hình. Tiếp đó, ta nối các chip với nhau bằng cách đi dây giữa chúng và hàn hai đầu dây vào các chân vào/ra tương ứng.

Nếu làm đúng theo sơ đồ cổng, cuối cùng ta sẽ có ba đầu dây bị lộ ra ngoài. Khi đó, ta hàn một chân vào mỗi đầu dây, bọc kín toàn bộ thiết bị (trừ ba chân đó) trong một vỏ nhựa và dán nhãn “Xor”. Ta có thể lặp lại quy trình lắp ráp này nhiều lần. Đến cuối ngày, ta có thể cất toàn bộ các chip vừa chế tạo vào một thùng mới và dán nhãn “Xor gates”. Nếu sau này muốn dựng những chip khác, ta sẽ có thể dùng các cổng Xor này như những khối xây dựng hộp đen, đúng như cách trước đó ta đã dùng các cổng And, Or và Not.

Như hẳn bạn đã cảm nhận, cách tiếp cận kiểu gara đối với sản xuất chip còn rất nhiều điều đáng chê. Trước hết, không có gì bảo đảm rằng sơ đồ chip được cho là đúng. Dù ta có thể chứng minh tính đúng đắn ở những trường hợp đơn giản như Xor, điều đó là không thể với nhiều chip phức tạp trong thực tế. Vì vậy, ta đành chấp nhận kiểm thử thực nghiệm: dựng chip, nối nguồn điện, kích hoạt và tắt các chân đầu vào theo nhiều cấu hình khác nhau, rồi hy vọng rằng hành vi vào/ra của chip đáp ứng đặc tả mong muốn. Nếu chip không làm được điều đó, ta sẽ phải loay hoay chỉnh sửa cấu trúc vật lý của nó, một công việc khá lôi thôi. Hơn nữa, ngay cả khi cuối cùng ta nghĩ ra được một thiết kế đúng và hiệu quả, việc lặp đi lặp lại quá trình lắp ráp chip nhiều lần vẫn sẽ tốn thời gian và dễ sai sót. Chắc chắn phải có cách tốt hơn.

#### 1.3.1 Hardware Description Language

Ngày nay, các nhà thiết kế phần cứng không còn tự tay lắp ráp bất cứ thứ gì nữa. Thay vào đó, họ thiết kế kiến trúc chip bằng một hình thức mô tả có tên Hardware Description Language, hay HDL. Nhà thiết kế đặc tả Logic của chip bằng cách viết một chương trình HDL, sau đó chương trình này được đưa qua một loạt kiểm thử nghiêm ngặt. Các kiểm thử được tiến hành trên môi trường ảo, bằng mô phỏng máy tính: một công cụ phần mềm chuyên dụng gọi là bộ mô phỏng phần cứng nhận chương trình HDL làm đầu vào và tạo ra một biểu diễn phần mềm của Logic chip. Tiếp theo, nhà thiết kế có thể yêu cầu bộ mô phỏng kiểm thử chip ảo trên nhiều tập đầu vào khác nhau. Bộ mô phỏng tính toán các đầu ra của chip, rồi so sánh chúng với các đầu ra mong muốn, theo đúng yêu cầu của khách hàng đã đặt chế tạo chip.

Ngoài việc kiểm tra tính đúng đắn của chip, nhà thiết kế phần cứng thường còn quan tâm tới nhiều tham số khác như tốc độ tính toán, mức tiêu thụ năng lượng và tổng chi phí mà cách hiện thực chip đề xuất kéo theo. Tất cả các tham số này đều có thể được bộ mô phỏng phần cứng mô phỏng và định lượng, giúp nhà thiết kế tối ưu thiết kế cho đến khi chip mô phỏng đạt được mức chi phí/hiệu năng như mong muốn.

Vì vậy, bằng HDL, người ta có thể lập kế hoạch, gỡ lỗi và tối ưu toàn bộ một chip trước khi tốn dù chỉ một đồng cho sản xuất vật lý. Khi hiệu năng của chip mô phỏng làm hài lòng khách hàng đã đặt hàng, một phiên bản tối ưu của chương trình HDL có thể trở thành bản thiết kế dùng để dập ra hàng loạt bản sao vật lý của chip trên silicon. Bước cuối này trong quy trình thiết kế chip, từ một chương trình HDL đã được tối ưu tới sản xuất hàng loạt, thường được thuê ngoài cho các công ty chuyên chế tạo chip bằng robot, dùng công nghệ chuyển mạch này hay công nghệ khác.

Ví dụ: Xây dựng một cổng Xor: Phần còn lại của mục này sẽ đưa ra phần giới thiệu ngắn về HDL thông qua ví dụ cổng Xor; đặc tả HDL chi tiết có thể tìm thấy ở phụ lục 2.

Hãy nhìn vào phần góc dưới bên trái của hình 1.7. Một định nghĩa HDL của chip gồm một phần tiêu đề và một phần các thành phần. Phần tiêu đề đặc tả giao diện chip, liệt kê tên chip cùng tên các chân vào và chân ra của nó. Phần PARTS mô tả các thành phần con cấu thành kiến trúc chip. Mỗi thành phần được biểu diễn bằng một câu lệnh duy nhất, chỉ ra tên thành phần, tiếp theo là một biểu thức trong ngoặc đơn mô tả cách nó được nối với những thành phần khác trong thiết kế. Lưu ý rằng để viết được các câu lệnh như vậy, lập trình viên HDL phải có quyền truy cập vào giao diện của mọi thành phần con nền tảng: tên các chân vào và ra của chúng, cũng như chức năng dự định của chúng. Ví dụ, lập trình viên viết chương trình HDL trong hình 1.7 hẳn phải biết rằng các chân vào và ra của cổng Not có tên là in và out, còn của các cổng And và Or có tên là a, b và out. (API của mọi chip được dùng trong Nand to Tetris đều được liệt kê ở phụ lục 4).

![](../src/images/figure_1.7.png)

Hình 1.7    Sơ đồ cổng và phần hiện thực HDL của hàm Boolean Xor  (And (a, Not (b)), And (Not (a), b)), được dùng làm ví dụ. Một script kiểm thử và một tệp đầu ra do phép kiểm thử sinh ra cũng được trình bày. Mô tả chi tiết về HDL và ngôn ngữ kiểm thử lần lượt được cho ở phụ lục 2 và 3.

Các kết nối liên thành phần được đặc tả bằng cách tạo và nối các chân nội bộ khi cần. Chẳng hạn, hãy xét phần dưới của sơ đồ cổng, nơi đầu ra của một cổng Not được dẫn vào đầu vào của một cổng And phía sau. Mã HDL mô tả kết nối này bằng cặp câu lệnh Not(…, out=nota) và And(a=nota, …). Câu lệnh đầu tiên tạo ra một chân nội bộ (kết nối đi ra) tên là nota và dẫn giá trị của chân out vào đó. Câu lệnh thứ hai dẫn giá trị của nota vào chân a của một cổng And. Có hai nhận xét cần nêu ở đây. Thứ nhất, các chân nội bộ được tạo “tự động” ngay lần đầu chúng xuất hiện trong chương trình HDL. Thứ hai, các chân có thể có fan-out không giới hạn. Ví dụ, trong hình 1.7, mỗi đầu vào đồng thời được đưa vào hai cổng. Trong sơ đồ cổng, các kết nối nhiều nhánh được mô tả bằng nét vẽ dạng chạc. Trong chương trình HDL, sự tồn tại của các nhánh được suy ra từ mã.

HDL mà chúng ta dùng trong Nand to Tetris có diện mạo và cảm giác tương tự các HDL công nghiệp nhưng đơn giản hơn nhiều. Cú pháp HDL của chúng ta phần lớn là tự giải thích và có thể học được bằng cách xem một vài ví dụ rồi tra cứu phụ lục 2 khi cần.

**Testing**

Đảm bảo chất lượng nghiêm ngặt đòi hỏi các chip phải được kiểm thử theo cách cụ thể, có thể lặp lại và được ghi chép đầy đủ. Với ý đó, các bộ mô phỏng phần cứng thường được thiết kế để chạy các script kiểm thử được viết bằng một ngôn ngữ kịch bản. Script kiểm thử ở hình 1.7 được viết bằng ngôn ngữ kịch bản mà bộ mô phỏng phần cứng Nand to Tetris hiểu được.

Hãy điểm qua nhanh script kiểm thử này. Hai dòng đầu yêu cầu bộ mô phỏng tải chương trình Xor.hdl và chuẩn bị in ra các giá trị của những biến được chọn. Tiếp theo, script liệt kê một loạt kịch bản kiểm thử. Trong mỗi kịch bản, script yêu cầu bộ mô phỏng gán các đầu vào của chip với những giá trị dữ liệu được chọn, tính đầu ra tương ứng và ghi kết quả kiểm thử vào một tệp đầu ra đã định. Với các cổng đơn giản như Xor, ta có thể viết một script kiểm thử đầy đủ liệt kê mọi giá trị đầu vào mà cổng có thể nhận. Trong trường hợp đó, tệp đầu ra thu được (phía bên phải hình 1.7) cung cấp một phép kiểm thử thực nghiệm hoàn chỉnh cho thấy chip hoạt động đúng. Sự xa xỉ của mức độ chắc chắn này sẽ không khả thi ở những chip phức tạp hơn, như ta sẽ thấy sau này.

Những độc giả dự định xây dựng máy tính Hack hẳn sẽ vui khi biết rằng mọi chip xuất hiện trong sách đều đi kèm các chương trình HDL khung và các script kiểm thử được cung cấp sẵn, có trong bộ phần mềm Nand to Tetris. Khác với HDL, vốn phải học để hoàn thiện các đặc tả chip, bạn không cần phải học ngôn ngữ kiểm thử của chúng tôi. Tuy vậy, bạn phải có khả năng đọc và hiểu các script kiểm thử được cung cấp. Ngôn ngữ kịch bản này được mô tả ở phụ lục 3, có thể được tra cứu khi cần biết.

#### 1.3.2 Hardware Simulation

Viết và gỡ lỗi các chương trình HDL tương tự như phát triển phần mềm thông thường. Điểm khác biệt chính là thay vì viết mã bằng một ngôn ngữ bậc cao, ta viết bằng HDL; và thay vì biên dịch rồi chạy mã, ta dùng bộ mô phỏng phần cứng để kiểm thử. Bộ mô phỏng phần cứng là một chương trình máy tính biết cách phân tích cú pháp và diễn giải mã HDL, biến nó thành một biểu diễn có thể thực thi, rồi kiểm thử nó theo các script kiểm thử được cung cấp. Trên thị trường có nhiều bộ mô phỏng phần cứng thương mại như vậy. Bộ phần mềm Nand to Tetris có kèm một bộ mô phỏng phần cứng đơn giản, cung cấp mọi công cụ cần thiết để xây dựng, kiểm thử và tích hợp toàn bộ các chip được trình bày trong sách, tiến tới việc dựng nên một máy tính đa dụng. Hình 1.8 minh họa một phiên làm việc mô phỏng chip điển hình.

![](../src/images/figure_1.8.png)

Hình 1.8    Ảnh chụp màn hình việc mô phỏng một chip Xor trong bộ mô phỏng phần cứng được cung cấp (các phiên bản khác của bộ mô phỏng này có thể có GUI hơi khác). Trạng thái của bộ mô phỏng được hiển thị ngay sau khi script kiểm thử chạy xong. Các giá trị chân tương ứng với bước mô phỏng cuối cùng  Không hiển thị trong ảnh này là một tệp so sánh liệt kê đầu ra kỳ vọng của phép mô phỏng do script kiểm thử cụ thể này đặc tả. Cũng như script kiểm thử, tệp so sánh thường do khách hàng muốn chế tạo chip cung cấp. Trong ví dụ cụ thể này, tệp đầu ra do phép mô phỏng sinh ra (phía dưới bên phải hình) giống hệt tệp so sánh đã được cung cấp.

### 1.4 Specification

Giờ đây chúng ta chuyển sang đặc tả một tập các cổng Logic sẽ cần cho việc xây dựng các chip của hệ thống máy tính. Những cổng này đều là các cổng thông dụng, mỗi cổng được thiết kế để thực hiện một phép toán Boolean phổ biến. Với mỗi cổng, ta sẽ tập trung vào giao diện cổng (cổng được kỳ vọng làm gì), còn các chi tiết hiện thực (xây dựng chức năng của cổng như thế nào) sẽ để lại cho một phần sau.

### 1.4.1 NAND

Điểm khởi đầu của kiến trúc máy tính của chúng ta là cổng Nand, từ đó mọi cổng và chip khác sẽ được xây dựng. Cổng Nand hiện thực hàm Boolean sau:

![](../src/images/figure_wo_caption_1.1.png)

![](../src/images/figure_wo_caption_1.2.png)

Xuyên suốt cuốn sách, các chip được đặc tả theo phong cách API như minh họa ở trên. Với mỗi chip, API chỉ rõ tên chip, tên các chân vào và chân ra, chức năng hay thao tác dự định của chip, và các ghi chú tùy chọn.

### 1.4.2 Basic Logic Gates

Các cổng Logic mà chúng ta trình bày ở đây thường được gọi là cơ bản, vì chúng đóng vai trò trong việc xây dựng các chip phức tạp hơn. Các cổng Not, And, Or và Xor hiện thực những toán tử Logic cổ điển, còn các cổng multiplexer và demultiplexer cung cấp phương tiện để điều khiển luồng thông tin.

Not: Còn được gọi là inverter, cổng này xuất ra giá trị đối ngược với giá trị đầu vào của nó. Đây là API:

![](../src/images/figure_wo_caption_1.3.png)

And: Trả về 1 khi cả hai đầu vào đều bằng 1, và 0 trong các trường hợp còn lại:

![](../src/images/figure_wo_caption_1.4.png)

Or: Trả về 1 khi ít nhất một đầu vào bằng 1, và 0 trong các trường hợp còn lại:

![](../src/images/figure_wo_caption_1.5.png)

Xor: Còn được gọi là exclusive or, cổng này trả về 1 khi chính xác một trong các đầu vào của nó bằng 1, và 0 trong các trường hợp còn lại:

![](../src/images/figure_wo_caption_1.6.png)

Multiplexer: Multiplexer là một cổng ba đầu vào (xem hình 1.9). Hai bit đầu vào tên là a và b được hiểu là các bit dữ liệu, còn bit đầu vào thứ ba tên là sel được hiểu là bit chọn. Multiplexer dùng sel để chọn và xuất ra giá trị của a hoặc b. Vì vậy, một cái tên hợp lý cho thiết bị này lẽ ra có thể là selector. Tên multiplexer được mượn từ các hệ thống truyền thông, nơi các phiên bản mở rộng của thiết bị này được dùng để tuần tự hóa (multiplexing) nhiều tín hiệu đầu vào qua một kênh truyền thông duy nhất.

![](../src/images/figure_1.9.png)

Hình 1.9    Multiplexer. Bảng ở góc trên bên phải là một phiên bản rút gọn của bảng chân trị.

Demultiplexer: Demultiplexer thực hiện chức năng ngược lại của multiplexer: nó nhận một giá trị đầu vào duy nhất và định tuyến giá trị đó tới một trong hai đầu ra có thể có, theo một bit chọn quyết định đầu ra đích. Đầu ra còn lại được đặt thành 0. Hình 1.10 đưa ra API.

![](../src/images/figure_1.10.png)

#### 1.4.3 Multi-Bit Versions of Basic Gates

Phần cứng máy tính thường được thiết kế để xử lý các giá trị nhiều bit, chẳng hạn tính hàm And theo từng bit trên hai đầu vào 16-bit cho trước. Mục này mô tả một số cổng Logic 16-bit sẽ cần đến để xây dựng nền tảng máy tính mục tiêu của chúng ta. Nhân tiện, ta lưu ý rằng kiến trúc Logic của các cổng n-bit này là như nhau, bất kể giá trị của n là bao nhiêu (ví dụ 16, 32 hay 64 bit). Các chương trình HDL xử lý giá trị nhiều bit tương tự như giá trị một bit, ngoại trừ việc các giá trị ấy có thể được đánh chỉ số để truy cập từng bit riêng lẻ. Ví dụ, nếu in và out biểu diễn các giá trị 16-bit, thì out đặt bit thứ 3 của out bằng giá trị của bit thứ 5 của in. Các bit được đánh số từ phải sang trái, trong đó bit ngoài cùng bên phải là bit thứ 0 và bit ngoài cùng bên trái là bit thứ 15 (trong thiết lập 16-bit).

Multi-bit Not: Cổng Not n-bit áp dụng phép toán Boolean Not lên từng bit trong đầu vào n-bit của nó:

![](../src/images/figure_wo_caption_1.7.png)

Multi-bit And: Cổng And n-bit áp dụng phép toán Boolean And lên từng cặp bit tương ứng trong hai đầu vào n-bit của nó:

![](../src/images/figure_wo_caption_1.8.png)

Multi-bit Or: Cổng Or n-bit áp dụng phép toán Boolean Or lên từng cặp bit tương ứng trong hai đầu vào n-bit của nó:

![](../src/images/figure_wo_caption_1.9.png)

Multi-bit multiplexer: Multiplexer n-bit hoạt động chính xác như multiplexer cơ bản, ngoại trừ việc các đầu vào và đầu ra của nó rộng n bit:

![](../src/images/figure_wo_caption_1.10.png)

#### 1.4.4 Multi-Way Versions of Basic Gates

Các cổng Logic hoạt động trên một hoặc hai đầu vào có thể được khái quát tự nhiên thành các biến thể nhiều ngả, hoạt động trên nhiều hơn hai đầu vào. Mục này mô tả một tập các cổng nhiều ngả sẽ được dùng tiếp theo trong nhiều chip của kiến trúc máy tính của chúng ta.

Multi-way Or: Cổng Or m-ngả xuất ra 1 khi ít nhất một trong m bit đầu vào của nó bằng 1, và 0 trong các trường hợp còn lại. Chúng ta sẽ cần biến thể 8-ngả của cổng này:

![](../src/images/figure_wo_caption_1.11.png)

Multi-way/Multi-bit multiplexer: Multiplexer m-ngả n-bit chọn một trong m đầu vào n-bit của nó và xuất giá trị đó ra đầu ra n-bit. Việc chọn được xác định bởi một tập gồm k bit chọn, trong đó  Đây là API của một multiplexer 4-ngả:

![](../src/images/figure_wo_caption_1.12.png)

Nền tảng máy tính mục tiêu của chúng ta cần hai biến thể của chip này: một multiplexer 4-ngả 16-bit và một multiplexer 8-ngả 16-bit:

![](../src/images/figure_wo_caption_1.13.png)

![](../src/images/figure_wo_caption_1.14.png)

Multi-way/Multi-bit demultiplexer: Demultiplexer m-ngả n-bit định tuyến đầu vào n-bit duy nhất của nó tới một trong m đầu ra n-bit. Các đầu ra còn lại được đặt thành 0. Việc chọn được xác định bởi một tập gồm k bit chọn, trong đó  Đây là API của một demultiplexer 4-ngả:

![](../src/images/figure_wo_caption_1.15.png)

### 1.5 Implementation

Phần trước đã mô tả các đặc tả, hay giao diện, của một họ các cổng Logic cơ bản. Sau khi đã mô tả cái gì, giờ ta chuyển sang thảo luận cách làm như thế nào. Cụ thể, ta sẽ tập trung vào hai cách tiếp cận tổng quát để hiện thực các cổng Logic: mô phỏng hành vi và hiện thực phần cứng. Cả hai cách tiếp cận này đều giữ vai trò quan trọng trong mọi dự án xây dựng phần cứng của chúng ta.

#### 1.5.1 Behavioral Simulation

Các mô tả chip được trình bày cho tới đây đều hoàn toàn trừu tượng. Sẽ rất tốt nếu ta có thể trực tiếp thử nghiệm các trừu tượng này trước khi bắt tay xây chúng bằng HDL. Nhưng làm sao có thể làm điều đó?

Thật ra, nếu điều duy nhất ta muốn là tương tác với hành vi của chip, thì không nhất thiết phải mất công xây chúng bằng HDL. Thay vào đó, ta có thể chọn một cách hiện thực đơn giản hơn nhiều, bằng lập trình thông thường. Chẳng hạn, ta có thể dùng một ngôn ngữ hướng đối tượng nào đó để tạo ra một tập lớp, mỗi lớp hiện thực một chip tổng quát. Ta có thể viết các hàm tạo lớp để tạo đối tượng chip và các phương thức eval để đánh giá Logic của chúng, đồng thời cho các lớp tương tác với nhau để các chip mức cao có thể được định nghĩa dựa trên các chip mức thấp hơn. Sau đó, ta có thể thêm một giao diện đồ họa đẹp mắt cho phép nhập những giá trị khác nhau vào đầu vào của chip, đánh giá Logic của chip và quan sát đầu ra. Kỹ thuật dựa trên phần mềm này, được gọi là mô phỏng hành vi, là một ý tưởng rất hợp lý. Nó cho phép thử nghiệm các giao diện chip trước khi bắt đầu quá trình xây dựng chúng bằng HDL vốn khá công phu.

Bộ mô phỏng phần cứng Nand to Tetris cung cấp chính xác dịch vụ như vậy. Ngoài việc mô phỏng hành vi của các chương trình HDL, vốn là mục đích chính của nó, bộ mô phỏng còn có sẵn các bản hiện thực bằng phần mềm của mọi chip được xây dựng trong các dự án phần cứng Nand to Tetris. Phiên bản built-in của mỗi chip được hiện thực như một mô-đun phần mềm thực thi, được gọi bởi một chương trình HDL khung cung cấp giao diện chip. Ví dụ, dưới đây là chương trình HDL hiện thực phiên bản built-in của chip Xor:

![](../src/images/figure_wo_caption_1.16.png)

Hãy so sánh điều này với chương trình HDL được liệt kê ở hình 1.7. Trước hết, lưu ý rằng các chip thông thường và các chip built-in có giao diện hoàn toàn giống nhau. Vì thế, chúng cung cấp đúng cùng một chức năng. Tuy nhiên, trong cách hiện thực built-in, phần PARTS được thay bằng câu lệnh duy nhất BUILTIN Xor. Câu lệnh này thông báo cho bộ mô phỏng rằng chip được hiện thực bởi Xor.class. Tệp lớp này, cũng như toàn bộ các tệp lớp Java hiện thực các chip built-in, đều nằm trong thư mục nand2tetris/tools/builtIn.

Nhân tiện, ta lưu ý rằng việc hiện thực các cổng Logic bằng lập trình bậc cao không khó, và đó cũng là một ưu điểm khác của mô phỏng hành vi: nó rẻ và nhanh. Dĩ nhiên, đến một lúc nào đó, các kỹ sư phần cứng vẫn phải làm việc thật, tức hiện thực các chip không phải như hiện vật phần mềm mà như các chương trình HDL có thể được chuyển thành silicon. Đó chính là điều chúng ta sẽ làm tiếp theo.

#### 1.5.2 Hardware Implementation

Mục này đưa ra các hướng dẫn về cách hiện thực mười lăm cổng Logic được mô tả trong chương. Theo tinh thần chung của cuốn sách, các hướng dẫn hiện thực của chúng tôi cố ý được viết ngắn gọn. Chúng tôi chỉ đưa ra vừa đủ gợi ý để bạn bắt đầu, và để lại cho bạn niềm vui tự khám phá phần còn lại của các cách hiện thực cổng.

Nand: Vì chúng ta quyết định xây dựng phần cứng của mình dựa trên các cổng Nand sơ cấp, ta xem Nand như một cổng nguyên thủy có chức năng được cung cấp từ bên ngoài. Bộ mô phỏng phần cứng đi kèm đã có sẵn bản hiện thực built-in của Nand, nên không cần phải tự hiện thực nó.

Not: Có thể được hiện thực bằng một cổng Nand duy nhất. Gợi ý: Hãy xem bảng chân trị của Nand và tự hỏi các đầu vào của cổng Nand phải được bố trí ra sao để một tín hiệu đầu vào duy nhất, 0, khiến cổng Nand xuất ra 1, còn một tín hiệu đầu vào duy nhất, 1, khiến nó xuất ra 0.

And: Có thể được hiện thực từ hai cổng đã bàn ở trên.

Or / Xor: Hàm Boolean Or có thể được định nghĩa bằng các hàm Boolean And và Not. Hàm Boolean Xor có thể được định nghĩa bằng And, Not và Or.

Multiplexer / Demultiplexer: Có thể được hiện thực bằng những cổng đã xây trước đó.

Multi-bit Not / And / Or gates: Giả sử bạn đã xây xong các phiên bản cơ bản của những cổng này, việc hiện thực các phiên bản n-ngôi của chúng chỉ là bài toán sắp xếp các mảng gồm n cổng cơ bản và để mỗi cổng hoạt động độc lập trên các đầu vào một bit của nó. Mã HDL thu được sẽ hơi nhàm chán và lặp lại (dùng sao chép-dán), nhưng nó sẽ phát huy tác dụng khi các cổng nhiều bit này được dùng để xây các chip phức tạp hơn ở phần sau của sách.

Multi-bit multiplexer: Việc hiện thực một multiplexer n-ngôi là bài toán đưa cùng một bit chọn vào mọi một trong n multiplexer nhị phân. Một lần nữa, đây là công việc xây dựng hơi tẻ nhạt nhưng cho ra một chip rất hữu ích.

Multi-way gates: Gợi ý hiện thực: Hãy nghĩ tới các nhánh rẽ.

#### 1.5.3 Built-In Chips

Như đã chỉ ra khi bàn về mô phỏng hành vi, bộ mô phỏng phần cứng của chúng ta cung cấp các bản hiện thực built-in dựa trên phần mềm cho hầu hết các chip được mô tả trong sách. Trong Nand to Tetris, chip built-in nổi tiếng nhất dĩ nhiên là Nand: mỗi khi bạn dùng một chip-part Nand trong chương trình HDL, bộ mô phỏng phần cứng sẽ gọi bản hiện thực built-in tools/builtIn/Nand.hdl. Quy ước này là trường hợp đặc biệt của một chiến lược gọi chip tổng quát hơn: mỗi khi bộ mô phỏng phần cứng gặp một chip-part, giả sử tên là Xxx, trong một chương trình HDL, nó sẽ tìm tệp Xxx.hdl trong thư mục hiện tại; nếu tìm thấy tệp, bộ mô phỏng sẽ đánh giá mã HDL nền tảng của nó. Nếu không tìm thấy tệp, bộ mô phỏng sẽ tìm tiếp trong thư mục tools/builtIn. Nếu tìm thấy ở đó, bộ mô phỏng sẽ thực thi bản hiện thực built-in của chip; nếu không, bộ mô phỏng sẽ phát ra thông báo lỗi và chấm dứt mô phỏng.

Quy ước này rất hữu ích. Ví dụ, giả sử bạn đã bắt đầu hiện thực chương trình Mux.hdl nhưng vì lý do nào đó vẫn chưa hoàn thành. Đây có thể là một trở ngại khó chịu, vì về lý thuyết bạn không thể tiếp tục xây các chip có dùng Mux như một chip-part. May mắn thay, và thực ra là chủ ý trong thiết kế, đây chính là lúc các chip built-in phát huy tác dụng cứu nguy. Tất cả những gì bạn cần làm là đổi tên phần hiện thực còn dang dở đó thành Mux1.hdl chẳng hạn. Mỗi khi bộ mô phỏng phần cứng được gọi để mô phỏng chức năng của một chip-part Mux, nó sẽ không tìm thấy tệp Mux.hdl trong thư mục hiện tại. Điều này sẽ khiến mô phỏng hành vi được kích hoạt, buộc bộ mô phỏng dùng phiên bản Mux built-in thay thế. Chính xác là điều ta muốn. Ở giai đoạn sau, bạn có thể muốn quay lại Mux1.hdl và tiếp tục công việc hiện thực của mình. Khi đó, bạn chỉ việc khôi phục tên gốc Mux.hdl của nó và tiếp tục từ nơi đã dừng lại.
