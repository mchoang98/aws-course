# PHẦN I --- NHẬP MÔN AWS: HIỂU ĐIỆN TOÁN ĐÁM MÂY

## CHƯƠNG 1. GETTING STARTED WITH AWS --- BẮT ĐẦU VỚI AWS

**Mục tiêu của chương**

Sau khi hoàn thành chương này, người học có thể:

-   Giải thích Internet, website và server hoạt động như thế nào.
-   Hiểu tại sao chúng ta cần Cloud Computing và AWS.
-   Phân biệt những mô hình điện toán đám mây cơ bản.
-   Hiểu Region, Availability Zone và Edge Location.
-   Biết cách bảo vệ tài khoản AWS và tránh phát sinh chi phí ngoài ý
    muốn.

**Phương pháp học:** Chúng ta sẽ cùng theo dõi câu chuyện của một bạn
nhỏ tên An, muốn tạo một website để khoe những mô hình LEGO mình đã lắp
ráp.

------------------------------------------------------------------------

## 1.1. Internet hoạt động như thế nào?

### Câu chuyện của An

Một ngày nọ, An đang chơi LEGO và nghĩ:

"Mình đã lắp được một tòa lâu đài rất đẹp. Mình muốn các bạn ở Hà Nội,
Đà Nẵng và cả nước ngoài đều nhìn thấy nó!"

An nhờ bố tạo một website đăng hình lâu đài.

Sau đó, An mở điện thoại, gõ địa chỉ website và thấy hình lâu đài xuất
hiện.

Nhưng có một điều kỳ lạ: Hình ảnh đó không nằm sẵn trong điện thoại của
An. Vậy nó đã đi từ đâu đến?

Câu trả lời chính là **Internet**.

### Internet giống như một hệ thống đường giao thông

Hãy tưởng tượng:

-   Mỗi ngôi nhà là một chiếc máy tính.
-   Mỗi con đường là một đường kết nối mạng.
-   Những chiếc xe chở hàng là các gói dữ liệu.
-   Các địa chỉ nhà giúp xe biết phải đi đâu.

Internet là một mạng lưới khổng lồ kết nối rất nhiều thiết bị trên thế
giới để chúng trao đổi dữ liệu với nhau.

Khi An mở một website, quá trình diễn ra như sau:

**Bước 1:** An nhập địa chỉ website vào trình duyệt.

Ví dụ: `www.example.com`

**Bước 2:** Máy tính cần tìm địa chỉ của máy chủ chứa website.

Hệ thống DNS đóng vai trò như một cuốn danh bạ, giúp tìm địa chỉ IP
tương ứng với tên miền.

**Bước 3:** Trình duyệt gửi yêu cầu đến máy chủ qua Internet.

Giống như An gửi một lá thư: "Cho mình xem trang chủ của website này!"

**Bước 4:** Máy chủ nhận yêu cầu và gửi dữ liệu website về.

**Bước 5:** Trình duyệt sử dụng dữ liệu nhận được để hiển thị trang web
trên màn hình.

Quá trình này thường chỉ mất một khoảng thời gian rất ngắn.

### Một điều quan trọng

Internet và website không hoàn toàn giống nhau.

**Internet** là hệ thống mạng kết nối các thiết bị. **Website** là một
trong những dịch vụ hoạt động nhờ Internet.

Giống như đường giao thông không phải là cửa hàng, nhưng chúng ta có thể
đi trên đường để đến cửa hàng.

**Ghi nhớ:** Internet là hệ thống kết nối các thiết bị, giúp chúng gửi
và nhận thông tin từ xa.

------------------------------------------------------------------------

## 1.2. Website được lưu trữ ở đâu?

An thắc mắc:

"Con có một website. Nhưng khi con tắt điện thoại, tại sao bạn bè vẫn
xem được website của con?"

Bố trả lời:

"Bởi vì website không nhất thiết được lưu trong điện thoại của con. Nó
có thể được lưu trên một máy tính khác luôn sẵn sàng phục vụ người truy
cập."

### Website giống như một cuốn sách

Hãy tưởng tượng một website là một cuốn sách có:

-   Chữ viết.
-   Hình ảnh.
-   Màu sắc.
-   Các nút để người dùng bấm vào.
-   Thông tin về người sử dụng.

Trong thế giới máy tính, website thường bao gồm các thành phần như:

**HTML:** Giống như bộ khung của ngôi nhà, xác định trang web có những
gì.

**CSS:** Giống như sơn tường và đồ trang trí, quyết định trang web trông
đẹp như thế nào.

**JavaScript:** Giống như các công tắc và máy móc, giúp trang web có thể
tương tác với người dùng.

**Database:** Giống như cuốn sổ ghi chép, dùng để lưu thông tin có cấu
trúc như tài khoản, sản phẩm hay đơn hàng.

Những thành phần này cần được lưu trữ trên máy tính hoặc các dịch vụ lưu
trữ.

Với website đơn giản, nhiều tệp có thể được lưu trên một máy chủ. Với
website lớn, hình ảnh, dữ liệu và chương trình có thể nằm trên nhiều máy
chủ hoặc dịch vụ khác nhau.

### Ví dụ thực tế

An tạo một website bán LEGO.

Khi bạn Minh mở website:

1.  Minh nhìn thấy danh sách LEGO.
2.  Minh bấm vào một sản phẩm.
3.  Website hiển thị giá và số lượng còn lại.
4.  Minh đặt mua sản phẩm.

Thông tin sản phẩm và đơn hàng không nhất thiết nằm trong máy tính của
Minh. Chúng thường được lưu và xử lý phía máy chủ.

**Ghi nhớ:** Website cần có nơi lưu trữ dữ liệu và, tùy loại website,
một hệ thống xử lý yêu cầu của người truy cập.

------------------------------------------------------------------------

## 1.3. Server là gì? Máy tính cá nhân khác server như thế nào?

### Server là người phục vụ

Hãy tưởng tượng An mở một quán nước.

An là người bán hàng. Khi khách đến và nói:

"Cho mình một ly nước cam!"

An nhận yêu cầu, chuẩn bị nước cam và đưa cho khách.

Trong máy tính cũng có một cơ chế tương tự.

-   Người khách là **Client**.
-   Người phục vụ là **Server**.
-   Lời gọi món là **Request**.
-   Ly nước được phục vụ là **Response**.

Server thường được gọi là **máy chủ**.

Đó có thể là một chiếc máy tính hoặc một chương trình có nhiệm vụ nhận
yêu cầu và cung cấp dữ liệu, chức năng hoặc dịch vụ cho thiết bị khác.

### Máy tính cá nhân và server khác nhau ra sao?

Máy tính cá nhân thường dùng để học tập, chơi game, lập trình hoặc làm
việc.

Server thường được thiết lập để phục vụ nhiều người dùng, có thể hoạt
động liên tục và được quản lý theo những yêu cầu nhất định về hiệu năng,
độ ổn định và bảo mật.

Ví dụ:

-   Laptop của An dùng để chơi Minecraft.
-   Một server Minecraft cho phép nhiều bạn cùng tham gia một thế giới.
-   Một web server phục vụ trang web cho nhiều người truy cập.

### Có thể biến laptop thành server không?

Có!

Một chiếc laptop bình thường hoàn toàn có thể chạy phần mềm web server.

Ví dụ, An viết một chương trình Python Flask và chạy nó trên laptop. Khi
được cấu hình mạng phù hợp, máy tính khác có thể gửi yêu cầu tới chương
trình đó.

Điều quan trọng cần nhớ là **server không nhất thiết phải là một chiếc
máy tính đặc biệt**.

Sự khác biệt chủ yếu nằm ở vai trò, cách cấu hình, phần mềm và yêu cầu
vận hành.

**Ghi nhớ:** Client là bên yêu cầu dịch vụ. Server là bên cung cấp dịch
vụ.

------------------------------------------------------------------------

## 1.4. Tại sao không dùng máy tính ở nhà để chạy website?

An nghĩ:

"Nếu laptop của con có thể trở thành server, tại sao con phải thuê máy
chủ của người khác?"

Bố đưa ra một câu hỏi:

"Nếu tối nay nhà mình mất điện, website sẽ thế nào?"

An trả lời:

"Máy tính tắt, vậy website cũng có thể ngừng hoạt động."

Đó chính là một vấn đề khi sử dụng máy tính ở nhà để cung cấp website
cho mọi người.

### Những vấn đề thường gặp

**1. Mất điện**

Máy tính tắt thì chương trình đang chạy cũng dừng lại.

**2. Mất Internet**

Dù máy tính còn bật, người bên ngoài vẫn có thể không truy cập được
website.

**3. Quá nhiều người truy cập**

Giống như một quán nước chỉ có một nhân viên nhưng phải phục vụ hàng
nghìn khách cùng lúc.

Nếu máy tính hoặc đường truyền không đủ mạnh, website sẽ chậm hoặc lỗi.

**4. Máy tính bị hỏng**

Ổ cứng hỏng, máy quá nóng hoặc các linh kiện gặp sự cố đều có thể khiến
website ngừng hoạt động.

**5. Bảo mật**

Khi đưa máy tính ở nhà ra Internet, chúng ta phải biết cách cấu hình
mạng, tường lửa và các biện pháp bảo vệ khác.

### Vậy có nên chạy server tại nhà?

Có thể, đặc biệt để học tập, thử nghiệm hoặc phục vụ nhu cầu cá nhân.

Tuy nhiên, khi website cần phục vụ người dùng liên tục, doanh nghiệp
thường lựa chọn các hệ thống máy chủ chuyên nghiệp hơn.

**Ghi nhớ:** Server tại nhà có thể hoạt động, nhưng việc duy trì điện,
mạng, bảo mật và độ ổn định sẽ do chúng ta tự quản lý.

------------------------------------------------------------------------

## 1.5. Data Center là gì?

Hãy tưởng tượng một thư viện rất lớn.

Thay vì chứa sách, thư viện đó chứa hàng nghìn máy tính.

Mỗi máy tính có thể thực hiện một công việc khác nhau:

-   Lưu hình ảnh.
-   Chạy website.
-   Lưu cơ sở dữ liệu.
-   Xử lý chương trình.
-   Phục vụ ứng dụng di động.

Tòa nhà hoặc cơ sở hạ tầng chuyên dụng chứa các hệ thống máy tính này
được gọi là **Data Center --- Trung tâm dữ liệu**.

### Một Data Center cần những gì?

Máy tính hoạt động liên tục sẽ tạo ra nhiệt và tiêu thụ điện.

Vì vậy, một Data Center thường cần:

**Hệ thống điện:** Cung cấp điện cho các thiết bị.

**Nguồn điện dự phòng:** Giúp giảm nguy cơ gián đoạn khi nguồn điện
chính gặp sự cố.

**Hệ thống làm mát:** Giữ nhiệt độ phù hợp để thiết bị hoạt động ổn
định.

**Hệ thống mạng:** Kết nối các máy chủ với nhau và với các mạng bên
ngoài.

**Hệ thống bảo mật:** Kiểm soát người được phép vào khu vực máy chủ và
bảo vệ các thiết bị.

**Hệ thống giám sát:** Theo dõi nhiệt độ, nguồn điện, mạng và tình trạng
hoạt động.

### Ví dụ dễ hiểu

Máy tính ở nhà giống như một chiếc xe đạp cá nhân.

Data Center giống như một bến xe được tổ chức chuyên nghiệp, có nhân
viên, hệ thống quản lý và cơ sở vật chất để phục vụ rất nhiều phương
tiện.

Không phải Data Center nào cũng giống nhau, nhưng mục tiêu chung là tạo
môi trường phù hợp cho hệ thống máy tính hoạt động.

**Ghi nhớ:** Data Center là nơi đặt và vận hành các hệ thống máy tính,
mạng và lưu trữ ở quy mô chuyên nghiệp.

------------------------------------------------------------------------

## 1.6. Cloud Computing là gì? Giải thích bằng ví dụ thuê nhà

### Một câu chuyện về thuê nhà

Giả sử An muốn mở một cửa hàng bán LEGO.

An có hai cách.

**Cách thứ nhất: Tự xây cửa hàng.**

An phải:

-   Mua đất.
-   Xây nhà.
-   Lắp điện nước.
-   Mua bàn ghế.
-   Sửa chữa khi có hư hỏng.

An có nhiều quyền kiểm soát, nhưng cũng phải tự lo nhiều công việc.

**Cách thứ hai: Thuê một cửa hàng.**

An chỉ cần tìm nơi phù hợp, trả tiền thuê và sử dụng.

Nếu cần mặt bằng lớn hơn, An có thể chuyển sang cửa hàng khác.

### Cloud Computing cũng có ý tưởng tương tự

Thay vì tự mua máy chủ và chuẩn bị Data Center, chúng ta có thể thuê tài
nguyên công nghệ từ một nhà cung cấp.

Những tài nguyên có thể thuê bao gồm:

-   Máy chủ để chạy chương trình.
-   Dung lượng lưu trữ.
-   Cơ sở dữ liệu.
-   Hệ thống mạng.
-   Các dịch vụ phục vụ xây dựng ứng dụng.

Chúng ta sử dụng tài nguyên thông qua mạng và trả tiền theo mô hình giá
của dịch vụ.

Đó là **Cloud Computing --- Điện toán đám mây**.

### Nhưng đám mây ở đâu?

Đây là câu hỏi rất hay.

Trong công nghệ, "Cloud" không có nghĩa là máy tính bay trên trời.

Những máy chủ đó vẫn là thiết bị thật, được đặt trong các Data Center
thật.

Chúng được gọi là "đám mây" vì người sử dụng không cần trực tiếp sở hữu
và vận hành toàn bộ cơ sở hạ tầng phía sau.

### Ví dụ

An thuê một máy chủ trên Cloud để chạy website bán LEGO.

Khi cửa hàng chỉ có vài khách, An sử dụng một máy chủ nhỏ.

Khi cửa hàng phát triển, An có thể thay đổi cấu hình hoặc bổ sung tài
nguyên nếu dịch vụ hỗ trợ.

Điều này giống như thuê thêm không gian khi cửa hàng đông khách.

### Ba lợi ích cần nhớ

**Nhanh:** Không cần chờ mua và lắp đặt máy chủ vật lý.

**Linh hoạt:** Có thể tăng hoặc giảm tài nguyên tùy nhu cầu và khả năng
của dịch vụ.

**Không phải tự vận hành toàn bộ hạ tầng:** Nhà cung cấp đảm nhận nhiều
công việc về phần cứng và cơ sở hạ tầng.

Tuy nhiên, Cloud không đồng nghĩa với luôn rẻ hơn, luôn an toàn hơn hay
luôn không bị gián đoạn. Điều đó còn phụ thuộc vào cách sử dụng và thiết
kế hệ thống.

**Ghi nhớ:** Cloud Computing là cách sử dụng tài nguyên máy tính qua
mạng mà không cần tự sở hữu toàn bộ cơ sở hạ tầng.

------------------------------------------------------------------------

## 1.7. AWS là gì? Amazon xây dựng AWS để làm gì?

### AWS giống như một siêu thị cho thuê công nghệ

Hãy tưởng tượng có một siêu thị đặc biệt.

Trong siêu thị này, thay vì bán bánh kẹo, người ta cung cấp:

-   Máy chủ.
-   Nơi lưu trữ dữ liệu.
-   Cơ sở dữ liệu.
-   Hệ thống gửi thông báo.
-   Công cụ xử lý dữ liệu.
-   Nhiều dịch vụ công nghệ khác.

Người dùng có thể lựa chọn dịch vụ mình cần và sử dụng qua Internet.

Đó là cách dễ hiểu nhất để hình dung về AWS.

**AWS là viết tắt của Amazon Web Services**, nền tảng điện toán đám mây
do Amazon cung cấp.

### Tại sao Amazon tạo ra AWS?

Vào những năm đầu phát triển, các công ty muốn xây dựng website lớn
thường phải đầu tư nhiều vào máy chủ và hạ tầng.

Việc chuẩn bị, vận hành và mở rộng những hệ thống này rất tốn công sức.

AWS phát triển các dịch vụ giúp những tổ chức khác có thể sử dụng tài
nguyên công nghệ mà không phải tự xây dựng mọi thứ từ đầu.

Các dịch vụ AWS bắt đầu được cung cấp rộng rãi từ năm 2006.

### Một số dịch vụ nổi bật

**Amazon EC2:** Cung cấp máy chủ ảo để chạy chương trình.

Giống như thuê một chiếc máy tính trên Internet.

**Amazon S3:** Dùng để lưu trữ các đối tượng dữ liệu như hình ảnh, video
và tệp tin.

Giống như thuê một kho chứa đồ.

**Amazon RDS:** Hỗ trợ sử dụng và quản lý cơ sở dữ liệu quan hệ.

Giống như có một hệ thống tủ hồ sơ thông minh, trong đó nhà cung cấp lo
giúp nhiều công việc vận hành.

**AWS Lambda:** Chạy mã lập trình khi có sự kiện hoặc yêu cầu phù hợp mà
chúng ta không phải trực tiếp quản lý máy chủ chạy mã.

Giống như gọi một người đến làm một việc cụ thể khi cần.

### Ví dụ

Website bán LEGO của An có thể sử dụng:

-   EC2 để chạy backend.
-   S3 để lưu ảnh sản phẩm.
-   RDS để lưu sản phẩm và đơn hàng.

Đây chỉ là một phương án triển khai. Một website thực tế có thể dùng
những dịch vụ AWS khác.

**Ghi nhớ:** AWS là nhà cung cấp dịch vụ điện toán đám mây. EC2, S3, RDS
và Lambda là những dịch vụ nằm trong AWS.

------------------------------------------------------------------------

## 1.8. So sánh AWS, Azure và Google Cloud

An hỏi:

"Chỉ có Amazon mới cho thuê máy chủ sao?"

Câu trả lời là không.

Hãy tưởng tượng có ba siêu thị công nghệ rất lớn:

-   Amazon mở siêu thị AWS.
-   Microsoft mở siêu thị Azure.
-   Google mở siêu thị Google Cloud.

Cả ba đều cung cấp những dịch vụ điện toán đám mây.

### Các nền tảng giống nhau ở đâu?

Chúng đều hỗ trợ nhiều công việc như:

-   Thuê máy chủ.
-   Lưu trữ dữ liệu.
-   Sử dụng cơ sở dữ liệu.
-   Xây dựng ứng dụng.
-   Phân tích dữ liệu.
-   Triển khai những hệ thống có quy mô lớn.

### Chúng khác nhau ở đâu?

  Nền tảng       Nhà cung cấp   Ví dụ dịch vụ máy chủ
  -------------- -------------- ------------------------
  AWS            Amazon         Amazon EC2
  Azure          Microsoft      Azure Virtual Machines
  Google Cloud   Google         Compute Engine

Các nền tảng có cách đặt tên, giao diện, hệ sinh thái và chính sách giá
khác nhau.

Azure có nhiều giải pháp tích hợp với hệ sinh thái Microsoft.

Google Cloud cung cấp các công nghệ liên quan đến hạ tầng, dữ liệu và
trí tuệ nhân tạo của Google.

AWS có danh mục dịch vụ rộng phục vụ nhiều loại nhu cầu.

Không có nền tảng nào tốt nhất cho tất cả mọi trường hợp.

### Chọn nền tảng nào?

Giống như chọn trường học hoặc cửa hàng, chúng ta cần xem:

-   Mình muốn làm gì?
-   Công nghệ hiện tại có phù hợp không?
-   Chi phí như thế nào?
-   Đội ngũ có kinh nghiệm với nền tảng nào?
-   Dịch vụ cần dùng có được hỗ trợ không?

Trong giáo trình này, chúng ta tập trung vào AWS.

**Ghi nhớ:** AWS, Azure và Google Cloud là ba nền tảng điện toán đám mây
khác nhau, có nhiều khả năng tương tự nhau.

------------------------------------------------------------------------

## 1.9. IaaS, PaaS, SaaS là gì?

Đây là ba cách cung cấp dịch vụ công nghệ phổ biến.

Chúng ta tiếp tục dùng câu chuyện về cửa hàng LEGO của An.

### IaaS --- Thuê cơ sở hạ tầng

IaaS là viết tắt của **Infrastructure as a Service**.

Tưởng tượng An thuê một cửa hàng trống.

Chủ nhà cung cấp tòa nhà và cơ sở hạ tầng cơ bản, còn An phải tự sắp xếp
nhiều thứ bên trong.

Trong công nghệ, IaaS cung cấp các tài nguyên như máy chủ, mạng và lưu
trữ.

Ví dụ: **Amazon EC2**.

Khi thuê một máy chủ EC2 thông thường, người dùng cần quan tâm đến hệ
điều hành, phần mềm, bảo mật và ứng dụng chạy trên máy chủ.

### PaaS --- Thuê một nền tảng đã được chuẩn bị

PaaS là viết tắt của **Platform as a Service**.

Tưởng tượng An thuê một quầy bán hàng có sẵn nhiều tiện nghi.

An không phải tự chuẩn bị toàn bộ cơ sở hạ tầng và có thể tập trung hơn
vào việc bán hàng.

Trong công nghệ, PaaS giúp lập trình viên tập trung xây dựng và triển
khai ứng dụng, còn nền tảng đảm nhận nhiều công việc vận hành.

Ví dụ: **AWS Elastic Beanstalk** hỗ trợ triển khai và quản lý môi trường
chạy ứng dụng.

Người dùng vẫn chịu trách nhiệm đối với mã nguồn và một số cấu hình của
mình.

### SaaS --- Sử dụng phần mềm hoàn chỉnh

SaaS là viết tắt của **Software as a Service**.

Tưởng tượng An sử dụng một dịch vụ đã được chuẩn bị đầy đủ.

An chỉ cần đăng nhập và sử dụng, không cần tự xây dựng phần mềm từ đầu.

Ví dụ quen thuộc:

-   Gmail.
-   Google Docs.
-   Microsoft 365.

Người dùng chủ yếu sử dụng các chức năng đã có, trong khi nhà cung cấp
quản lý phần lớn hệ thống phía sau.

### Bảng ghi nhớ

  Mô hình   Cách hiểu đơn giản     Người dùng làm gì?
  --------- ---------------------- ---------------------------------------
  IaaS      Thuê hạ tầng           Quản lý nhiều phần của hệ thống
  PaaS      Thuê nền tảng          Tập trung hơn vào phát triển ứng dụng
  SaaS      Dùng phần mềm có sẵn   Đăng nhập và sử dụng

**Mẹo nhớ:** IaaS cho bạn nhiều quyền kiểm soát. PaaS giúp bạn bớt việc
vận hành. SaaS cung cấp sản phẩm phần mềm sẵn để dùng.

------------------------------------------------------------------------

## 1.10. Public Cloud, Private Cloud và Hybrid Cloud

Hãy tưởng tượng ba cách tổ chức một sân chơi.

### Public Cloud --- Sân chơi do một công ty cung cấp dịch vụ

Một công ty xây dựng hệ thống hạ tầng rồi cung cấp cho nhiều khách hàng
sử dụng.

AWS là một ví dụ về nhà cung cấp Public Cloud.

Dù nhiều khách hàng sử dụng hạ tầng của cùng một nhà cung cấp, tài
nguyên và dữ liệu vẫn được kiểm soát, phân quyền và cách ly theo mô hình
dịch vụ.

**Public Cloud không có nghĩa là ai cũng được phép xem dữ liệu của
bạn.**

### Private Cloud --- Sân chơi dành riêng cho một tổ chức

Một công ty hoặc tổ chức có một môi trường Cloud dành riêng cho chính
mình.

Tổ chức đó kiểm soát cách cung cấp và sử dụng tài nguyên trong hệ thống.

Ví dụ, một doanh nghiệp lớn xây dựng nền tảng Cloud nội bộ để các phòng
ban sử dụng.

### Hybrid Cloud --- Kết hợp cả hai

Hãy tưởng tượng một trường học vừa có sân chơi riêng, vừa thuê thêm sân
chơi bên ngoài khi cần.

Hybrid Cloud cũng kết hợp hạ tầng riêng của tổ chức với Public Cloud.

Ví dụ:

Một công ty tiếp tục sử dụng hệ thống máy chủ nội bộ, đồng thời sử dụng
AWS cho một số ứng dụng và dịch vụ khác.

### Phân biệt nhanh

-   **Public Cloud:** Dùng dịch vụ Cloud từ nhà cung cấp bên ngoài.
-   **Private Cloud:** Môi trường Cloud được tổ chức riêng cho một đơn
    vị.
-   **Hybrid Cloud:** Kết hợp môi trường riêng với Public Cloud.

**Lưu ý:** Amazon VPC là môi trường mạng ảo được cách ly trong AWS;
không nên hiểu rằng cứ tạo một VPC là đã xây dựng một Private Cloud hoàn
chỉnh.

------------------------------------------------------------------------

## 1.11. AWS Global Infrastructure: Region, Availability Zone, Edge Location

### AWS đặt máy chủ ở đâu?

An ở Việt Nam.

Bạn của An sống tại Nhật Bản.

Một bạn khác sống tại Mỹ.

Nếu mọi người cùng truy cập website của An, việc đặt máy chủ ở đâu có
thể ảnh hưởng đến tốc độ kết nối và những yêu cầu khác của hệ thống.

AWS xây dựng cơ sở hạ tầng tại nhiều khu vực trên thế giới.

Hệ thống này được gọi là **AWS Global Infrastructure --- Cơ sở hạ tầng
toàn cầu của AWS**.

Để hiểu nó, chúng ta cần học ba khái niệm.

### 1. Region --- Khu vực địa lý

Hãy tưởng tượng AWS có nhiều khu vực hoạt động ở những nơi khác nhau
trên thế giới.

Mỗi khu vực lớn được gọi là một Region.

Ví dụ:

-   Singapore: `ap-southeast-1`
-   Tokyo: `ap-northeast-1`
-   Northern Virginia: `us-east-1`

Khi tạo một số loại tài nguyên AWS, chúng ta cần chọn Region.

Ví dụ, An muốn đặt máy chủ tại Singapore thì có thể chọn Region
Singapore.

### 2. Availability Zone --- Khu vực hạ tầng độc lập trong một Region

Hãy tưởng tượng trong một thành phố có nhiều trường học nằm ở những địa
điểm khác nhau.

Nếu một trường gặp sự cố, các trường còn lại vẫn có thể hoạt động.

Trong AWS, một Region có nhiều **Availability Zone**, thường viết tắt là
AZ.

Mỗi AZ gồm một hoặc nhiều Data Center riêng biệt, được thiết kế với hạ
tầng điện, mạng và các hệ thống hỗ trợ để giảm nguy cơ sự cố lan rộng.

Ví dụ trong Region Singapore có các AZ như:

-   `ap-southeast-1a`
-   `ap-southeast-1b`
-   `ap-southeast-1c`

Chúng được kết nối với nhau bằng hệ thống mạng tốc độ cao.

Nếu thiết kế một ứng dụng chạy trên nhiều AZ, chúng ta có thể giảm nguy
cơ toàn bộ ứng dụng ngừng hoạt động khi một AZ gặp sự cố.

**Quan trọng:** Chỉ chọn một Region có nhiều AZ không có nghĩa ứng dụng
tự động chạy trên nhiều AZ. Chúng ta phải thiết kế và triển khai phù
hợp.

### 3. Edge Location --- Điểm phục vụ gần người dùng

Hãy tưởng tượng An có một kho LEGO lớn ở Singapore.

Nếu khách ở rất xa đặt mua món hàng, việc vận chuyển có thể mất nhiều
thời gian.

Giờ An có thêm những điểm phân phối ở gần khách hàng, lưu sẵn một số món
hàng được yêu thích.

Khách có thể nhận những món đó nhanh hơn.

Edge Location cũng có ý tưởng tương tự.

Với các dịch vụ như Amazon CloudFront, AWS có thể lưu bản sao của những
nội dung phù hợp tại các điểm mạng gần người dùng hơn.

Ví dụ, hình ảnh website có thể được phục vụ từ vị trí gần khách truy cập
thay vì lần nào cũng phải lấy trực tiếp từ máy chủ gốc.

Không phải tất cả nội dung đều được lưu sẵn tại Edge Location.

### Hình dung tổng thể

**Region Singapore**

Bên trong có:

-   Availability Zone A
-   Availability Zone B
-   Availability Zone C

Các AZ chứa hạ tầng để chạy nhiều loại tài nguyên của AWS.

Trong khi đó, các Edge Location thuộc mạng lưới phân phối toàn cầu có
thể giúp cung cấp một số nội dung đến người dùng nhanh hơn.

### Mẹo nhớ

**Region:** Khu vực lớn trên bản đồ thế giới.

**Availability Zone:** Một khu vực hạ tầng riêng biệt trong Region.

**Edge Location:** Một điểm mạng giúp phục vụ nội dung và các dịch vụ
phù hợp gần người dùng hơn.

------------------------------------------------------------------------

## 1.12. Tại sao AWS có nhiều Region?

An hỏi:

"Amazon chỉ cần xây dựng một Data Center thật lớn là đủ rồi, tại sao lại
phải xây dựng nhiều khu vực như vậy?"

Có ba lý do rất dễ hiểu.

### Lý do 1: Giảm khoảng cách tới người dùng

Hãy tưởng tượng An muốn gửi một món quà.

Gửi từ Việt Nam đến Singapore thường có hành trình ngắn hơn gửi từ Việt
Nam đến Mỹ.

Dữ liệu cũng cần di chuyển qua mạng.

Khoảng cách địa lý và đường truyền có thể ảnh hưởng đến thời gian phản
hồi.

Thời gian chờ trong quá trình truyền dữ liệu thường được gọi là
**Latency --- Độ trễ**.

Đặt máy chủ gần người dùng thường giúp giảm độ trễ, dù kết quả thực tế
còn phụ thuộc vào đường mạng và hệ thống.

### Lý do 2: Giảm tác động của sự cố lớn

Nếu mọi máy chủ đều tập trung tại một khu vực và nơi đó gặp sự cố nghiêm
trọng, rất nhiều dịch vụ có thể bị ảnh hưởng.

Việc triển khai ứng dụng ở nhiều địa điểm độc lập có thể giúp tăng khả
năng chống chịu.

Tuy nhiên, việc sử dụng nhiều Region cần được thiết kế chủ động và
thường làm tăng độ phức tạp, chi phí.

### Lý do 3: Đáp ứng yêu cầu về nơi lưu trữ dữ liệu

Một số tổ chức phải tuân thủ quy định về vị trí lưu trữ hoặc xử lý dữ
liệu.

Việc có nhiều Region giúp họ lựa chọn vị trí phù hợp hơn với nhu cầu và
quy định áp dụng.

### Ví dụ

Nếu An xây dựng website chủ yếu cho người dùng tại Việt Nam, Region
Singapore có thể là một lựa chọn để đánh giá.

Nhưng không nên kết luận Singapore luôn nhanh nhất hoặc rẻ nhất. Cần xem
xét loại dịch vụ, chi phí, độ trễ thực tế và yêu cầu về dữ liệu.

**Ghi nhớ:** AWS có nhiều Region để phục vụ người dùng ở những khu vực
khác nhau, tăng lựa chọn về khả năng chống chịu sự cố và vị trí dữ liệu.

------------------------------------------------------------------------

## 1.13. AWS Free Tier hoạt động như thế nào?

### AWS có miễn phí không?

An muốn thử AWS nhưng không muốn mất tiền.

Bố giải thích:

"AWS có những chương trình cho phép người mới sử dụng một số dịch vụ
miễn phí hoặc bằng khoản tín dụng được cấp. Nhưng không phải mọi thứ
trên AWS đều miễn phí."

Chương trình này được gọi là **AWS Free Tier**.

### Chính sách dành cho tài khoản mới

Theo chính sách AWS được kiểm tra vào tháng 11/2026, khách hàng mới đủ
điều kiện có thể được cấp:

**100 USD tín dụng ban đầu** khi đăng ký tài khoản.

**Tối đa 100 USD tín dụng bổ sung** khi hoàn thành các hoạt động đủ điều
kiện.

Như vậy, tổng số tín dụng có thể lên đến **200 USD**.

Tín dụng giống như một khoản tiền khuyến mại dùng để thanh toán cho các
dịch vụ AWS đủ điều kiện.

Đây không phải tiền mặt để rút về tài khoản ngân hàng.

### Free plan kéo dài bao lâu?

Với Free plan dành cho tài khoản mới đủ điều kiện:

-   Thời gian tối đa là 6 tháng.
-   Gói miễn phí kết thúc sớm hơn nếu sử dụng hết tín dụng.
-   Không tự động bị tính tiền sử dụng chỉ vì thời gian Free plan kết
    thúc.

Khi Free plan hết hạn, tài khoản và các dự án có thể bị tạm ngưng hoặc
đóng theo chính sách AWS. Người dùng cần nâng cấp lên Paid plan nếu muốn
tiếp tục sử dụng tài nguyên và khôi phục dữ liệu trong thời hạn cho
phép.

### Paid plan khác thế nào?

Paid plan cho phép sử dụng nhiều dịch vụ và tính năng hơn.

Nếu chi phí vượt quá tín dụng hoặc sử dụng dịch vụ không được tín dụng
chi trả, người dùng có thể phải thanh toán.

### Always Free là gì?

Một số dịch vụ AWS có hạn mức sử dụng miễn phí theo tháng, được gọi là
Always Free.

Ví dụ, một dịch vụ có thể cho phép một lượng yêu cầu hoặc dung lượng
nhất định mà không tính phí.

Nếu vượt hạn mức, chi phí có thể phát sinh tùy gói tài khoản và tín dụng
còn lại.

Không nên hiểu Always Free có nghĩa là dùng bao nhiêu cũng miễn phí.

### Câu chuyện dễ nhớ

An được một cửa hàng tặng phiếu mua hàng trị giá 100 USD.

An có thể dùng phiếu đó mua những món đồ đủ điều kiện.

Khi phiếu hết giá trị, An không thể mặc nhiên tiếp tục lấy đồ miễn phí.

AWS Free Tier cũng cần được hiểu theo cách cẩn thận như vậy.

**Ghi nhớ:** Free Tier có điều kiện, thời hạn và hạn mức. Luôn kiểm tra
chính sách hiện hành trước khi tạo tài nguyên.

------------------------------------------------------------------------

## 1.14. Tạo tài khoản AWS và bảo vệ tài khoản Root

### Root User là ai?

Hãy tưởng tượng An sở hữu một ngôi nhà.

Ngôi nhà có nhiều phòng, nhiều chiếc tủ và nhiều cánh cửa.

An có một chiếc chìa khóa tổng có thể mở gần như mọi nơi.

Trong AWS, tài khoản **Root User** cũng giống như chiếc chìa khóa tổng
đó.

Root User là danh tính có quyền quản lý cao nhất đối với tài khoản AWS,
bao gồm nhiều thao tác đặc biệt mà người dùng thông thường không thể
thực hiện.

### Tại sao Root User nguy hiểm?

Giả sử có người lấy được chìa khóa tổng.

Người đó có thể:

-   Truy cập các tài nguyên.
-   Thay đổi cấu hình.
-   Xóa dữ liệu.
-   Tạo tài nguyên gây phát sinh chi phí.
-   Thực hiện những thao tác quản lý quan trọng khác.

Vì vậy, chúng ta phải bảo vệ Root User đặc biệt cẩn thận.

### Ba nguyên tắc quan trọng

**1. Sử dụng mật khẩu mạnh và riêng biệt**

Không dùng lại mật khẩu của mạng xã hội hay website khác.

**2. Bật MFA**

MFA là viết tắt của Multi-Factor Authentication, nghĩa là xác thực bằng
nhiều yếu tố.

Ví dụ, đăng nhập không chỉ cần mật khẩu mà còn cần xác nhận qua ứng dụng
trên điện thoại hoặc khóa bảo mật.

MFA giống như việc mở cửa phải có cả chìa khóa và một bước xác nhận bổ
sung.

**3. Không sử dụng Root User cho công việc hằng ngày**

Khi cần quản lý AWS thường xuyên, nên sử dụng danh tính riêng với quyền
phù hợp.

AWS khuyến nghị sử dụng giải pháp danh tính như IAM Identity Center và
thông tin đăng nhập tạm thời khi phù hợp.

Cũng không nên tạo Access Key dài hạn cho Root User.

### Một nguyên tắc bảo mật nữa

Mỗi người chỉ nên được cấp những quyền cần thiết để làm công việc của
họ.

Ví dụ, bạn Minh chỉ cần xem hình ảnh LEGO thì không cần quyền xóa toàn
bộ website.

Nguyên tắc này được gọi là **Least Privilege --- Quyền tối thiểu**.

**Ghi nhớ:** Root User là chiếc chìa khóa tổng. Bật MFA, giữ an toàn và
hạn chế sử dụng nó.

------------------------------------------------------------------------

## 1.15. Làm quen với AWS Management Console

### AWS Management Console là gì?

Hãy tưởng tượng An bước vào một siêu thị công nghệ.

Trong siêu thị có rất nhiều khu vực:

-   Khu cho thuê máy tính.
-   Khu lưu trữ.
-   Khu cơ sở dữ liệu.
-   Khu quản lý tài khoản.
-   Khu kiểm tra hóa đơn.

AWS Management Console giống như bảng điều khiển giúp An tìm và quản lý
các dịch vụ đó bằng trình duyệt.

Địa chỉ đăng nhập:

https://console.aws.amazon.com/

### Những khu vực cần làm quen

**1. Search --- Thanh tìm kiếm**

Gõ tên dịch vụ mình cần.

Ví dụ:

-   EC2
-   S3
-   IAM
-   Lambda
-   RDS

AWS sẽ hiển thị các dịch vụ và kết quả phù hợp.

**2. Region selector --- Chọn Region**

Cho biết hoặc cho phép thay đổi Region đang sử dụng đối với các dịch vụ
hỗ trợ Region.

Ví dụ, khi đang chọn Singapore, danh sách EC2 thông thường sẽ hiển thị
các tài nguyên ở Singapore.

Nếu chuyển sang Tokyo, danh sách đó có thể khác.

Điều này không có nghĩa các tài nguyên ở Singapore đã biến mất.

**3. Billing and Cost Management**

Nơi xem thông tin chi phí và quản lý các công cụ theo dõi ngân sách.

**4. IAM**

Dịch vụ hỗ trợ quản lý danh tính và quyền truy cập tài nguyên AWS.

### Bài thực hành nhỏ

Hãy đăng nhập bằng tài khoản phù hợp rồi:

1.  Tìm dịch vụ EC2.
2.  Xem Region đang được lựa chọn.
3.  Tìm dịch vụ S3.
4.  Tìm Billing and Cost Management.
5.  Quay về trang chủ AWS Console.

Chúng ta chưa cần tạo máy chủ.

**Ghi nhớ:** AWS Management Console là giao diện để tìm, tạo, cấu hình,
theo dõi và quản lý dịch vụ AWS.

------------------------------------------------------------------------

## 1.16. AWS Billing, Budgets và cảnh báo chi phí

### Điều gì xảy ra nếu quên tắt máy chủ?

Hãy tưởng tượng An thuê một phòng học theo thời gian sử dụng.

Nếu hợp đồng tính tiền theo thời gian và An để phòng được thuê lâu hơn
dự kiến, chi phí có thể tăng.

Một số dịch vụ AWS cũng được tính tiền theo thời gian chạy, dung lượng,
số yêu cầu hoặc những yếu tố khác.

Ví dụ, khi học EC2, An có thể tạo một máy chủ rồi quên xóa các tài
nguyên liên quan.

Nếu tài nguyên có tính phí, chi phí có thể tiếp tục phát sinh theo quy
tắc của dịch vụ.

Vì vậy, AWS có các công cụ hỗ trợ kiểm soát chi phí.

### Billing là gì?

Billing là phần liên quan đến hóa đơn và chi phí sử dụng.

Bạn có thể xem:

-   Chi phí đã được ghi nhận.
-   Dịch vụ đang tạo ra chi phí.
-   Tín dụng còn lại khi được áp dụng.
-   Thông tin thanh toán.

### AWS Budgets là gì?

Budgets giống như việc đặt mục tiêu chi tiêu trong một tháng.

Ví dụ:

An nói với bố:

"Con muốn chi tối đa 5 USD mỗi tháng để học AWS."

An có thể tạo một Cost Budget với ngân sách 5 USD mỗi tháng và thiết lập
cảnh báo khi chi phí đạt những ngưỡng nhất định.

Ví dụ:

-   Khi chi phí đạt 4 USD, gửi email cảnh báo.
-   Khi chi phí đạt 5 USD, gửi email cảnh báo tiếp.

Đây là ví dụ về **cảnh báo ngân sách**.

### Zero Spend Budget

AWS có mẫu ngân sách mang tên Zero Spend Budget.

Mẫu này giúp gửi thông báo khi phát sinh chi phí vượt những điều kiện
miễn phí mà ngân sách theo dõi.

Đây là lựa chọn hữu ích để bắt đầu kiểm soát chi phí trong môi trường
học tập.

### Cảnh báo có tự động tắt máy chủ không?

**Không.**

Việc nhận email cảnh báo không có nghĩa AWS sẽ tự động dừng tài nguyên.

Ngoài ra, dữ liệu chi phí và thông báo có thể bị trễ.

Do đó, tuyệt đối không xem một Cost Budget thông thường là công tắc chặn
chi phí.

Một số trải nghiệm tài khoản AWS mới có tính năng Spend Limit riêng với
điều kiện sử dụng cụ thể. Tính năng này khác với cảnh báo Budget.

### Quy tắc an toàn

-   Kiểm tra chi phí thường xuyên.
-   Theo dõi tín dụng còn lại.
-   Tìm hiểu giá trước khi tạo tài nguyên.
-   Xóa tài nguyên thử nghiệm khi không cần nữa.
-   Không chia sẻ mật khẩu và thông tin truy cập tài khoản.
-   Không cho rằng tắt một máy chủ sẽ tự động xóa mọi tài nguyên liên
    quan.

**Ghi nhớ:** Billing cho biết chi phí. Budgets giúp theo dõi và cảnh
báo. Cảnh báo ngân sách không tự động ngăn toàn bộ chi phí phát sinh.

------------------------------------------------------------------------

## 1.17. Shared Responsibility Model --- Ai chịu trách nhiệm bảo mật?

### Câu chuyện thuê nhà

An thuê một căn hộ trong tòa nhà lớn.

Một ngày nọ, An hỏi:

"Nếu nhà bị mất trộm thì ai chịu trách nhiệm?"

Bố giải thích:

"Chủ tòa nhà cần bảo vệ cơ sở vật chất, nhưng con cũng phải khóa cửa và
giữ gìn tài sản của mình."

AWS sử dụng một nguyên tắc tương tự.

Nguyên tắc này được gọi là **Shared Responsibility Model --- Mô hình
chia sẻ trách nhiệm**.

### AWS chịu trách nhiệm gì?

AWS chịu trách nhiệm bảo mật cơ sở hạ tầng Cloud do AWS vận hành.

Ví dụ:

-   Bảo vệ Data Center.
-   Quản lý phần cứng của AWS.
-   Bảo vệ hệ thống hạ tầng mạng thuộc AWS.
-   Quản lý những lớp hạ tầng mà AWS chịu trách nhiệm cung cấp.

Đây được gọi là **Security of the Cloud**.

### Người dùng chịu trách nhiệm gì?

Khách hàng cần bảo vệ những phần hệ thống thuộc trách nhiệm của mình.

Ví dụ:

-   Bảo vệ tài khoản đăng nhập.
-   Phân quyền truy cập.
-   Bảo vệ dữ liệu.
-   Cấu hình bảo mật phù hợp.
-   Cập nhật và bảo vệ phần mềm mà mình quản lý.

Đây được gọi là **Security in the Cloud**.

### Ví dụ với Amazon EC2

An thuê một máy chủ EC2 để chạy website.

AWS bảo vệ hạ tầng vật lý và các lớp hạ tầng do AWS quản lý.

An phải quan tâm đến:

-   Cập nhật hệ điều hành của máy chủ.
-   Bảo mật mã nguồn ứng dụng.
-   Cấu hình Security Group.
-   Quyền truy cập và thông tin đăng nhập.
-   Bảo vệ dữ liệu và sao lưu khi cần.

Nếu An mở quyền truy cập không an toàn hoặc làm lộ mật khẩu, AWS không
tự động chịu trách nhiệm thay cho mọi sai sót cấu hình của An.

### Với các dịch vụ quản lý nhiều hơn thì sao?

Khi sử dụng những dịch vụ được AWS quản lý ở mức cao hơn, như Amazon S3
hoặc AWS Lambda, AWS đảm nhận thêm các lớp hạ tầng và vận hành tương
ứng.

Tuy vậy, khách hàng vẫn phải bảo vệ dữ liệu, quản lý quyền truy cập và
cấu hình những phần thuộc trách nhiệm của mình.

**Ghi nhớ:** AWS bảo vệ cơ sở hạ tầng mà AWS quản lý. Người dùng bảo vệ
dữ liệu, tài khoản và những cấu hình thuộc trách nhiệm của mình. Ranh
giới cụ thể phụ thuộc vào dịch vụ.

------------------------------------------------------------------------

## 1.18. Thực hành: Tạo tài khoản AWS an toàn và thiết lập cảnh báo chi phí

### Mục tiêu

Hoàn thành bài thực hành này, người học sẽ:

1.  Có tài khoản AWS hoặc được cấp quyền sử dụng tài khoản học tập.
2.  Biết cách truy cập AWS Console.
3.  Biết kiểm tra loại gói tài khoản.
4.  Bảo vệ Root User bằng MFA.
5.  Thiết lập cảnh báo ngân sách.
6.  Tìm được các dịch vụ cơ bản mà chưa cần tạo máy chủ.

**Lưu ý cho học sinh nhỏ tuổi:** Các bước đăng ký, cung cấp thông tin
thanh toán, xác minh danh tính và quản lý tài khoản phải do phụ huynh
hoặc giáo viên đủ điều kiện thực hiện. Học sinh có thể học bằng tài
khoản thực hành với quyền hạn được giới hạn.

### Bước 1. Chuẩn bị

Cần có:

-   Email có thể truy cập được.
-   Số điện thoại để xác minh nếu AWS yêu cầu.
-   Phương thức thanh toán hợp lệ nếu quy trình đăng ký yêu cầu.
-   Thiết bị xác thực MFA.
-   Một người quản lý tài khoản có trách nhiệm.

Nếu người học đã có tài khoản AWS, không cần tạo thêm tài khoản mới chỉ
để hoàn thành bài học.

### Bước 2. Truy cập trang đăng ký

Mở:

https://aws.amazon.com/free/

Chọn tạo tài khoản AWS.

Làm theo quy trình đăng ký được hiển thị, bao gồm xác minh thông tin cần
thiết.

### Bước 3. Kiểm tra gói tài khoản

Khi đăng ký, đọc kỹ sự khác biệt giữa Free plan và Paid plan.

Với mục đích học tập cơ bản và đủ điều kiện, ưu tiên Free plan để hạn
chế rủi ro chi phí.

Kiểm tra quyền lợi Free Tier thực tế của tài khoản trước khi sử dụng
dịch vụ.

### Bước 4. Bảo vệ Root User

Sau khi tài khoản được tạo thành công:

1.  Mở phần quản lý bảo mật dành cho Root User.
2.  Kiểm tra thiết lập MFA.
3.  Đăng ký phương thức MFA phù hợp.
4.  Bảo quản an toàn phương thức xác thực và phương án khôi phục.
5.  Tránh sử dụng Root User cho công việc thường ngày.

Khi cần sử dụng thường xuyên, người quản lý nên thiết lập phương thức
truy cập riêng theo hướng dẫn danh tính và phân quyền của AWS.

### Bước 5. Tìm trang quản lý chi phí

Trong AWS Console, tìm:

**Billing and Cost Management**

Kiểm tra:

-   Loại gói đang sử dụng.
-   Tín dụng còn lại nếu tài khoản có tín dụng.
-   Chi phí được ghi nhận.
-   Các thông tin Free Tier liên quan.

Một tài khoản mới có thể chưa hiển thị dữ liệu chi phí đầy đủ ngay lập
tức.

### Bước 6. Tạo cảnh báo ngân sách

Truy cập:

**Billing and Cost Management → Budgets → Create budget**

Nếu giao diện tài khoản hỗ trợ:

1.  Chọn mẫu **Zero Spend Budget**.
2.  Đặt tên như `AWS-Learning-Zero-Spend`.
3.  Nhập email nhận cảnh báo.
4.  Kiểm tra các thông tin.
5.  Tạo ngân sách.

Tiếp theo, nếu muốn theo dõi một ngân sách hàng tháng, có thể tạo thêm
Monthly Cost Budget với mức tiền phù hợp.

Ví dụ cho mục đích học tập: 5 USD/tháng và nhận cảnh báo khi đạt 80%
ngân sách.

Nhắc lại: Đây là mục tiêu theo dõi, không phải giới hạn chi tiêu cứng.

### Bước 7. Khám phá dịch vụ

Từ AWS Console, sử dụng thanh tìm kiếm để mở:

**Amazon EC2:** Quan sát giao diện quản lý máy chủ.

**Amazon S3:** Quan sát giao diện lưu trữ.

**IAM:** Tìm hiểu các khu vực quản lý quyền.

**AWS Lambda:** Quan sát giao diện quản lý chức năng chạy mã.

Chỉ xem giao diện, chưa tạo tài nguyên tính phí.

### Bước 8. Kiểm tra kết quả

Hãy tự trả lời:

-   Mình có biết đang sử dụng gói Free hay Paid không?
-   Root User đã được bảo vệ bằng MFA chưa?
-   Mình biết kiểm tra chi phí ở đâu chưa?
-   Email cảnh báo ngân sách đã được cấu hình chưa?
-   Mình biết AWS Region hiện được chọn ở đâu chưa?
-   Mình có vô tình tạo tài nguyên nào chưa?

Nếu tất cả đã được kiểm tra, bài thực hành hoàn thành.

------------------------------------------------------------------------

# TỔNG KẾT CHƯƠNG 1

## 10 điều quan trọng nhất cần nhớ

  Khái niệm         Hãy nhớ như thế này
  ----------------- ----------------------------------------------------------
  Internet          Hệ thống đường kết nối các thiết bị
  Website           Những trang thông tin và chức năng được cung cấp qua Web
  Server            Máy tính hoặc chương trình phục vụ yêu cầu
  Data Center       Nơi đặt và vận hành hạ tầng máy tính
  Cloud Computing   Sử dụng tài nguyên máy tính qua mạng
  AWS               Nền tảng cho thuê và cung cấp dịch vụ công nghệ
  EC2               Máy chủ ảo
  S3                Kho lưu trữ dữ liệu dạng đối tượng
  Region và AZ      Khu vực địa lý và các vùng hạ tầng độc lập bên trong
  IAM và MFA        Công cụ kiểm soát quyền và tăng cường bảo mật đăng nhập

## Câu chuyện tổng kết

Hãy quay lại với An.

An muốn tạo một website để khoe bộ LEGO của mình.

Đầu tiên, An cần hiểu website hoạt động trên Internet.

Sau đó, An biết rằng website phải có nơi lưu trữ và có thể cần máy chủ
để xử lý yêu cầu.

Thay vì tự mua và vận hành máy chủ, An có thể sử dụng Cloud Computing.

An chọn tìm hiểu AWS, nơi có những dịch vụ như EC2, S3 và RDS.

An biết rằng mình có thể lựa chọn Region phù hợp và sử dụng nhiều AZ khi
cần thiết kế hệ thống có khả năng chống chịu sự cố tốt hơn.

Cuối cùng, An học cách bảo vệ tài khoản và theo dõi chi phí trước khi
bắt đầu xây dựng website.

Đó chính là nền tảng của việc học AWS.

------------------------------------------------------------------------

# BÀI TẬP CUỐI CHƯƠNG

## Phần A --- Câu hỏi trắc nghiệm

**Câu 1. Internet là gì?**

A. Một chiếc máy tính rất lớn.

B. Một hệ thống kết nối nhiều thiết bị.

C. Một loại trò chơi.

**Câu 2. Server dùng để làm gì?**

A. Nhận và xử lý những yêu cầu phù hợp.

B. Chỉ để chơi game.

C. Chỉ để vẽ tranh.

**Câu 3. Cloud Computing có nghĩa là gì?**

A. Đặt máy tính lên đám mây trên trời.

B. Sử dụng tài nguyên công nghệ qua mạng.

C. Chỉ sử dụng máy tính khi trời mưa.

**Câu 4. AWS là dịch vụ của công ty nào?**

A. Microsoft.

B. Google.

C. Amazon.

**Câu 5. Dịch vụ nào của AWS cung cấp máy chủ ảo?**

A. Amazon EC2.

B. Amazon S3.

C. Amazon Route 53.

**Câu 6. Region là gì?**

A. Một khu vực địa lý trong hạ tầng AWS.

B. Một loại mật khẩu.

C. Một chương trình máy tính.

**Câu 7. MFA giúp làm gì?**

A. Tăng tốc độ Internet.

B. Bổ sung lớp xác thực khi đăng nhập.

C. Tự động làm bài tập.

**Câu 8. Khi tạo AWS Budget, điều gì chắc chắn đúng?**

A. AWS tự động xóa tất cả tài nguyên khi vượt ngân sách.

B. AWS không bao giờ có chi phí.

C. AWS có thể theo dõi và cảnh báo theo ngân sách được cấu hình.

**Đáp án:** 1B --- 2A --- 3B --- 4C --- 5A --- 6A --- 7B --- 8C.

## Phần B --- Bài tập tình huống

**Tình huống 1**

An có một website đang chạy trên laptop ở nhà.

Một ngày nọ, nhà An mất điện và các bạn không thể truy cập website nữa.

Câu hỏi:

-   Vì sao website ngừng hoạt động?
-   Sử dụng một dịch vụ Cloud có thể giải quyết được vấn đề gì?
-   Sử dụng Cloud có đảm bảo website không bao giờ bị lỗi không?

**Tình huống 2**

An đã tạo tài khoản AWS và muốn cho bạn Minh cùng sử dụng.

Minh chỉ cần xem thông tin một số tài nguyên.

Câu hỏi:

-   An có nên chia sẻ mật khẩu Root User không?
-   Minh có cần quyền quản trị toàn bộ tài khoản không?
-   Nên dùng nguyên tắc bảo mật nào để cấp quyền cho Minh?

## Phần C --- Hoạt động trên lớp

**Trò chơi: Em là Internet**

Giáo viên chuẩn bị bốn vai:

-   Một học sinh đóng vai trình duyệt.
-   Một học sinh đóng vai hệ thống DNS.
-   Một học sinh đóng vai máy chủ.
-   Một học sinh đóng vai người dùng.

Người dùng yêu cầu mở một website.

Trình duyệt nhờ DNS tìm địa chỉ máy chủ.

Sau đó, trình duyệt gửi lời yêu cầu đến máy chủ.

Máy chủ trả lại tờ giấy có hình ảnh website.

Trình duyệt đưa hình ảnh cho người dùng.

Qua hoạt động này, học sinh có thể tự đóng vai để hình dung cách một
website được truy cập qua Internet.

------------------------------------------------------------------------

# KẾT THÚC CHƯƠNG 1

Sau chương này, điều quan trọng nhất không phải là ghi nhớ thật nhiều
thuật ngữ tiếng Anh.

Điều quan trọng là hiểu:

**Một ứng dụng cần có máy tính để chạy. Internet giúp các máy tính kết
nối với nhau. Cloud cho phép chúng ta sử dụng tài nguyên máy tính của
nhà cung cấp. AWS là một trong những nền tảng cung cấp các tài nguyên và
dịch vụ đó.**

Khi đã hiểu điều này, việc học các dịch vụ AWS ở những chương tiếp theo
sẽ dễ dàng hơn rất nhiều.

**Chương tiếp theo: AWS Identity and Access Management (IAM) --- Học
cách quản lý người dùng, quyền truy cập và bảo vệ tài nguyên AWS.**

------------------------------------------------------------------------

## Tài liệu AWS chính thức tham khảo

-   [AWS --- What Is Cloud
    Computing?](https://aws.amazon.com/what-is/cloud-computing/)
-   [AWS --- Regions and Availability
    Zones](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions-availability-zones.html)
-   [AWS --- Free Tier](https://aws.amazon.com/free/)
-   [AWS --- Root User Best
    Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/root-user-best-practices.html)
-   [AWS --- Budget
    Templates](https://docs.aws.amazon.com/cost-management/latest/userguide/budget-templates.html)
-   [AWS --- Shared Responsibility
    Model](https://aws.amazon.com/compliance/shared-responsibility-model/)
