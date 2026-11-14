# CHƯƠNG 3 --- AMAZON EC2 BASICS

## Làm quen với máy chủ ảo trên AWS

**Định hướng:** AWS Certified Developer -- Associate (DVA-C02)\
**Trình độ:** Người mới bắt đầu\
**Cách học:** Giải thích bằng ví dụ đời thường → hiểu khái niệm → xem ví
dụ kỹ thuật → thực hành → ôn thi.

------------------------------------------------------------------------

## Mục tiêu chương học

Ở Chương 1, chúng ta đã biết Cloud Computing là gì. Ở Chương 2, chúng ta
đã học IAM để hiểu ai được phép làm gì trong AWS.

Bây giờ hãy tưởng tượng bạn muốn đưa website của mình lên Internet. Bạn
cần một chiếc máy tính luôn bật, có Internet, có CPU, RAM và ổ cứng để
chạy website.

Bạn có thể mua một máy chủ vật lý, đặt ở nhà hoặc thuê một máy chủ từ
nhà cung cấp dịch vụ. Nhưng AWS có một cách linh hoạt hơn: **thuê một
máy tính ảo trên đám mây**.

Đó chính là **Amazon EC2**.

Sau chương này, bạn sẽ hiểu:

-   EC2 là gì và dùng để làm gì.
-   Instance, AMI và Instance Type có ý nghĩa gì.
-   Cách chọn CPU, RAM và dung lượng lưu trữ.
-   Public IP, Private IP và Elastic IP khác nhau như thế nào.
-   Security Group giống chiếc cửa bảo vệ máy chủ ra sao.
-   SSH, Key Pair và EC2 Instance Connect dùng để kết nối máy chủ.
-   User Data giúp tự động cài đặt phần mềm khi khởi động.
-   IAM Role giúp ứng dụng trên EC2 truy cập dịch vụ AWS an toàn.
-   Các cách thuê EC2 và cách tránh phát sinh chi phí.
-   Cách tạo, kết nối, kiểm tra và xóa một EC2 Instance.

------------------------------------------------------------------------

# 3.1. Amazon EC2 là gì?

**EC2** là viết tắt của **Elastic Compute Cloud**.

Hiểu đơn giản:

> Amazon EC2 là dịch vụ cho phép bạn thuê máy tính ảo của Amazon để chạy
> chương trình, website, API hoặc các tác vụ của mình.

Ví dụ, bạn đang làm một website bán hàng:

-   Frontend là giao diện người dùng.
-   Backend là nơi xử lý đăng nhập, đơn hàng và thanh toán.
-   Database là nơi lưu thông tin khách hàng và sản phẩm.

Backend cần một máy tính để chạy liên tục. Thay vì mua máy chủ vật lý,
bạn có thể thuê một máy tính ảo trên AWS EC2.

Máy tính ảo đó có thể chạy Ubuntu, Amazon Linux, Windows Server hoặc các
hệ điều hành được AWS hỗ trợ.

### Ví dụ đời thường: Thuê một căn nhà

Hãy tưởng tượng bạn muốn mở một cửa hàng.

  Khi thuê nhà               Khi dùng EC2
  -------------------------- -----------------
  Căn nhà bạn thuê           EC2 Instance
  Chọn nhà nhỏ hay lớn       Chọn CPU và RAM
  Chọn nhà có sẵn nội thất   Chọn AMI
  Khóa cửa, bảo vệ           Security Group
  Chìa khóa nhà              Key Pair
  Địa chỉ nhà                IP Address
  Tiền thuê nhà              Chi phí EC2

Bạn không cần xây cả tòa nhà. Bạn chỉ thuê phần tài nguyên mình cần.

Tương tự, bạn không cần mua cả máy chủ vật lý. Bạn chỉ thuê lượng tài
nguyên phù hợp.

### EC2 có thể dùng để làm gì?

  Công việc               Ví dụ
  ----------------------- -------------------------------------
  Chạy website            Website bán hàng, blog
  Chạy Backend API        NestJS, Laravel, Flask, Spring Boot
  Chạy Database           MySQL, PostgreSQL tự quản lý
  Chạy tác vụ nền         Worker, cron job, xử lý hàng đợi
  Chạy phần mềm           Game server, công cụ nội bộ
  Môi trường thử nghiệm   Server staging, development

**Ghi nhớ:** EC2 không phải là một website hay một database. Nó là máy
tính ảo để bạn tự cài đặt và chạy những thứ đó.

------------------------------------------------------------------------

# 3.2. EC2 Instance là gì?

Khi tạo một máy chủ trên EC2, AWS gọi máy chủ đó là một **Instance**.

Ví dụ:

-   Bạn tạo một máy chủ Ubuntu để chạy NestJS.
-   AWS cấp cho máy chủ một mã định danh, ví dụ `i-0123456789abcdef0`.
-   Máy chủ đó chính là một EC2 Instance.

Một AWS Account có thể có nhiều EC2 Instance.

Ví dụ:

``` text
AWS Account
│
├── EC2 Instance 1: Web Server
│   └── Chạy website
│
├── EC2 Instance 2: API Server
│   └── Chạy NestJS
│
└── EC2 Instance 3: Worker Server
    └── Xử lý Bull Queue
```

Bạn có thể bật, tắt, khởi động lại hoặc xóa từng Instance tùy nhu cầu.

### Một Instance có những thành phần nào?

Khi tạo EC2, bạn thường phải lựa chọn:

1.  **AMI:** Máy tính sẽ cài hệ điều hành nào?
2.  **Instance Type:** Máy tính có bao nhiêu CPU và RAM?
3.  **Storage:** Máy tính có bao nhiêu dung lượng ổ cứng?
4.  **Key Pair:** Dùng chìa khóa nào để đăng nhập?
5.  **Network:** Máy tính nằm trong mạng nào?
6.  **Security Group:** Cho phép ai kết nối vào máy tính?
7.  **IAM Role:** Máy tính được phép sử dụng dịch vụ AWS nào?
8.  **User Data:** Có cần tự động chạy lệnh khi khởi động không?

Hãy nhớ 8 thành phần này. Chúng là những lựa chọn quan trọng khi tạo
EC2.

------------------------------------------------------------------------

# 3.3. AMI là gì?

**AMI (Amazon Machine Image)** là bản mẫu dùng để tạo máy chủ EC2.

Hãy tưởng tượng bạn mua một chiếc laptop mới. Trước khi sử dụng, bạn cần
cài hệ điều hành và một số phần mềm.

AMI giống như một chiếc USB cài đặt hệ điều hành đã được chuẩn bị sẵn.

Khi tạo EC2, bạn chọn AMI để AWS biết cần khởi động máy chủ với hệ điều
hành và cấu hình phần mềm nào.

### Ví dụ về AMI

  AMI                        Dùng để làm gì?
  -------------------------- ------------------------------------
  Amazon Linux               Hệ điều hành Linux do AWS cung cấp
  Ubuntu                     Hệ điều hành Linux phổ biến
  Windows Server             Chạy ứng dụng Windows
  Red Hat Enterprise Linux   Hệ điều hành Linux doanh nghiệp
  Custom AMI                 Bản mẫu do bạn tự tạo

### Custom AMI là gì?

Giả sử bạn thường xuyên tạo máy chủ để chạy ứng dụng NestJS.

Lần đầu tiên, bạn phải:

1.  Tạo EC2.
2.  Cài Node.js.
3.  Cài Nginx.
4.  Cài các công cụ cần thiết.
5.  Cấu hình môi trường.

Nếu lần sau phải làm lại tất cả, bạn sẽ mất thời gian.

Thay vào đó, bạn có thể tạo một AMI từ máy chủ đã cấu hình xong. Những
lần sau, bạn dùng AMI đó để tạo máy chủ mới.

``` text
EC2 ban đầu
    │
    ├── Cài Ubuntu
    ├── Cài Node.js
    ├── Cài Nginx
    └── Cấu hình ứng dụng
          │
          ▼
       Tạo AMI
          │
          ├── Tạo EC2 mới số 1
          ├── Tạo EC2 mới số 2
          └── Tạo EC2 mới số 3
```

**Ghi nhớ:**

-   AMI là bản mẫu.
-   EC2 Instance là máy tính được tạo từ bản mẫu đó.
-   Một AMI có thể được dùng để tạo nhiều Instance.
-   AMI phải có sẵn trong Region mà bạn muốn dùng, hoặc được copy sang
    Region đó.

------------------------------------------------------------------------

# 3.4. Instance Type --- Chọn cấu hình máy chủ

Đây là phần rất quan trọng.

Khi thuê máy tính, bạn cần chọn máy có cấu hình phù hợp.

Một máy tính có cấu hình mạnh sẽ xử lý được nhiều công việc hơn, nhưng
thường có chi phí cao hơn.

EC2 cũng tương tự.

### 3.4.1. Các thông số cơ bản

  Thành phần   Giải thích đơn giản
  ------------ ------------------------------------------
  vCPU         Số lõi xử lý ảo
  RAM          Bộ nhớ để chương trình hoạt động
  Storage      Không gian lưu trữ dữ liệu
  Network      Khả năng truyền và nhận dữ liệu qua mạng

Ví dụ:

``` text
EC2 Instance: t3.small

vCPU: 2
RAM: 2 GiB
```

Điều này có nghĩa máy chủ có 2 vCPU và 2 GiB RAM.

**Lưu ý:** vCPU là đơn vị CPU ảo mà AWS cấp cho Instance. Không nên hiểu
1 vCPU luôn tương đương chính xác một lõi CPU vật lý trên mọi loại máy
chủ.

### 3.4.2. Các nhóm Instance phổ biến

AWS chia Instance thành nhiều nhóm để phục vụ các công việc khác nhau.

  -----------------------------------------------------------------------
  Nhóm                    Ý nghĩa                 Ví dụ công việc
  ----------------------- ----------------------- -----------------------
  T -- Burstable          Máy chủ tiết kiệm, có   Website nhỏ, môi trường
                          khả năng tăng hiệu năng thử nghiệm
                          trong thời gian ngắn    

  M -- General Purpose    Cân bằng CPU và RAM     Backend, website thông
                                                  thường

  C -- Compute Optimized  Ưu tiên CPU             Xử lý dữ liệu, tính
                                                  toán

  R -- Memory Optimized   Ưu tiên RAM             Database, xử lý dữ liệu
                                                  trong bộ nhớ

  I, D -- Storage         Ưu tiên lưu trữ và tốc  Hệ thống cần I/O lưu
  Optimized               độ đọc ghi              trữ cao

  G, P -- Accelerated     Có bộ tăng tốc như GPU  Đồ họa, AI, machine
  Computing                                       learning
  -----------------------------------------------------------------------

Ví dụ đời thường:

-   Máy T giống chiếc xe tiết kiệm để đi lại hàng ngày.
-   Máy C giống xe thể thao, ưu tiên tốc độ.
-   Máy R giống một chiếc xe tải có khoang chứa lớn, ưu tiên khả năng
    mang nhiều đồ.
-   Máy G/P có thêm thiết bị chuyên dụng để làm những công việc đặc
    biệt.

### 3.4.3. Cách đọc tên Instance

Ví dụ:

``` text
t3.micro
m7i.large
c7i.large
r7i.xlarge
```

Hãy phân tích `m7i.large`:

  Thành phần   Ý nghĩa
  ------------ ---------------------------------
  `m`          Nhóm General Purpose
  `7`          Thế hệ thứ 7
  `i`          Biến thể sử dụng bộ xử lý Intel
  `large`      Kích thước Instance

Một số kích thước thường gặp:

``` text
nano
micro
small
medium
large
xlarge
2xlarge
4xlarge
8xlarge
```

Thông thường, kích thước càng lớn thì tài nguyên càng nhiều và giá càng
cao. Tuy nhiên, cần xem thông số cụ thể của từng Instance Type.

### 3.4.4. Nên chọn loại nào?

Không phải cứ chọn máy mạnh nhất là tốt nhất.

Ví dụ:

  Nhu cầu                  Hướng lựa chọn
  ------------------------ ----------------------------
  Học AWS                  Instance nhỏ, chi phí thấp
  Website cá nhân          T hoặc M
  Backend API vừa phải     T hoặc M tùy tải
  Xử lý CPU liên tục       C
  Database cần nhiều RAM   R
  Chạy AI cần GPU          G hoặc P

**Mẹo:** Hãy bắt đầu bằng cấu hình nhỏ, theo dõi CPU, RAM và hiệu năng
thực tế rồi nâng cấp khi cần.

------------------------------------------------------------------------

# 3.5. Storage --- Ổ cứng của EC2

Máy tính không thể chỉ có CPU và RAM. Nó còn cần ổ cứng để lưu hệ điều
hành, phần mềm và dữ liệu.

EC2 cũng cần Storage.

Khi tạo Instance, bạn thường thấy phần cấu hình ổ đĩa, ví dụ:

``` text
Root Volume
    ├── Ubuntu
    ├── Node.js
    ├── Nginx
    └── Application files
```

Một loại lưu trữ rất phổ biến là **Amazon EBS (Elastic Block Store)**.

Hãy tưởng tượng:

-   EC2 là chiếc máy tính.
-   EBS là ổ cứng được gắn vào chiếc máy tính đó.

EBS có thể lưu dữ liệu ngay cả khi bạn dừng EC2, miễn là Volume không bị
xóa.

### Phân biệt nhanh

  Thành phần       Vai trò
  ---------------- -----------------------------------------
  EC2              Máy tính ảo
  RAM              Bộ nhớ đang sử dụng
  EBS              Ổ đĩa lưu trữ bền vững
  Instance Store   Lưu trữ tạm thời gắn với máy chủ vật lý

Chúng ta sẽ học sâu hơn về EBS, Instance Store và các loại ổ đĩa trong
**Chương 4 -- Amazon EC2 Instance Storage**.

------------------------------------------------------------------------

# 3.6. Public IP và Private IP

Mỗi máy chủ cần có địa chỉ mạng để các thiết bị khác có thể tìm thấy nó.

Hãy tưởng tượng nhà bạn có:

-   Địa chỉ nhà để người bên ngoài tìm đến.
-   Số phòng bên trong để các thành viên trong nhà liên lạc.

EC2 cũng có hai loại địa chỉ IP quan trọng.

### 3.6.1. Private IP

Private IP là địa chỉ dùng để giao tiếp trong mạng riêng, ví dụ VPC của
AWS.

Ví dụ:

``` text
10.0.1.10
```

Máy chủ Backend có thể giao tiếp với máy chủ Database bằng Private IP mà
không cần Database phải có Public IP.

### 3.6.2. Public IP

Public IP là địa chỉ có thể được sử dụng để kết nối với máy chủ từ
Internet, nếu cấu hình mạng và Security Group cho phép.

Ví dụ:

``` text
54.123.45.67
```

Người dùng có thể truy cập website qua Public IP hoặc tên miền trỏ đến
máy chủ.

**Lưu ý:** Có Public IP không có nghĩa là chắc chắn kết nối được. Máy
chủ vẫn cần cấu hình mạng phù hợp và Security Group phải cho phép loại
kết nối đó.

### 3.6.3. Elastic IP

Đây là một loại Public IPv4 tĩnh.

Hãy tưởng tượng:

-   Public IP tự động giống địa chỉ tạm thời.
-   Elastic IP giống địa chỉ cố định mà bạn đăng ký để sử dụng lâu dài.

Ví dụ:

``` text
EC2
Public IP: 54.123.45.67
```

Nếu bạn Stop rồi Start Instance, Public IP tự động thường sẽ thay đổi.

Nếu sử dụng Elastic IP, địa chỉ đó có thể được giữ lại và gắn lại với
Instance hoặc Network Interface phù hợp.

### So sánh

  Loại IP             Đặc điểm
  ------------------- --------------------------------------------------------
  Private IP          Dùng trong mạng riêng
  Public IP tự động   Có thể thay đổi khi Stop/Start
  Elastic IP          Public IPv4 tĩnh, có thể giữ lại khi thay đổi Instance

**Cảnh báo chi phí:** AWS hiện tính phí cho Public IPv4, bao gồm địa chỉ
Public IPv4 gắn với Instance và Elastic IP. Vì vậy, không nên tạo địa
chỉ IP công cộng nếu ứng dụng không cần đến nó.

------------------------------------------------------------------------

# 3.7. Security Group --- Người bảo vệ máy chủ

Đây là một trong những khái niệm quan trọng nhất của EC2.

Hãy tưởng tượng EC2 là một căn nhà.

Nếu căn nhà có cửa nhưng không có khóa, bất kỳ ai cũng có thể thử đi
vào.

Security Group giống như người bảo vệ ở cửa nhà. Người bảo vệ quyết
định:

-   Ai được phép đi vào?
-   Được phép đi vào bằng cửa nào?
-   Máy chủ được phép kết nối ra ngoài ở đâu?

**Security Group là một Virtual Firewall (tường lửa ảo) kiểm soát lưu
lượng mạng đến và đi khỏi tài nguyên được gắn với nó.**

### 3.7.1. Inbound Rule là gì?

Inbound có nghĩa là lưu lượng đi vào máy chủ.

Ví dụ:

Bạn muốn người dùng truy cập website.

Trình duyệt gửi yêu cầu đến EC2 qua cổng HTTP 80.

Nếu Security Group không cho phép cổng 80, yêu cầu đó sẽ không được đi
vào EC2.

### 3.7.2. Outbound Rule là gì?

Outbound là lưu lượng đi ra khỏi máy chủ.

Ví dụ:

Backend trên EC2 cần gọi API bên thứ ba hoặc tải thư viện từ Internet.

Đây là lưu lượng Outbound.

### 3.7.3. Một số Port quan trọng

  ------------------------------------------------------------------------
                          Port Tên                   Dùng để làm gì?
  ---------------------------- --------------------- ---------------------
                            22 SSH                   Đăng nhập máy chủ
                                                     Linux

                            80 HTTP                  Truy cập website
                                                     không mã hóa

                           443 HTTPS                 Truy cập website có
                                                     mã hóa TLS

                          3389 RDP                   Đăng nhập máy chủ
                                                     Windows

                          3306 MySQL                 Kết nối MySQL

                          5432 PostgreSQL            Kết nối PostgreSQL

                          6379 Redis                 Kết nối Redis

                          3000 Custom                Thường dùng cho ứng
                                                     dụng Node.js trong
                                                     môi trường phát triển
  ------------------------------------------------------------------------

Port là số hiệu của cổng giao tiếp. Bạn có thể tưởng tượng mỗi Port là
một cửa riêng phục vụ một loại hoạt động.

### 3.7.4. Ví dụ cấu hình Security Group

Giả sử bạn có một EC2 chạy website.

Bạn muốn:

-   Quản trị viên đăng nhập bằng SSH.
-   Người dùng trên Internet truy cập website.
-   EC2 có thể gửi yêu cầu ra ngoài.

Một cấu hình ví dụ:

  Direction     Port Source/Destination   Mục đích
  ----------- ------ -------------------- ---------------------------
  Inbound         22 IP của bạn `/32`     Đăng nhập SSH
  Inbound         80 `0.0.0.0/0`          Người dùng truy cập HTTP
  Inbound        443 `0.0.0.0/0`          Người dùng truy cập HTTPS
  Outbound       All `0.0.0.0/0`          Cho phép đi ra ngoài

`0.0.0.0/0` có nghĩa là toàn bộ địa chỉ IPv4.

`/32` có nghĩa là chỉ một địa chỉ IPv4 cụ thể.

Ví dụ, nếu IP của bạn là `203.0.113.10`, thì `203.0.113.10/32` chỉ cho
phép đúng IP đó.

**Không nên mở SSH (port 22) cho toàn bộ Internet trong môi trường thực
tế.**

### 3.7.5. Security Group là Stateful

Đây là điểm rất hay xuất hiện trong đề thi AWS.

**Stateful** nghĩa là Security Group nhớ trạng thái kết nối.

Ví dụ:

1.  EC2 gửi yêu cầu đến một API bên ngoài.
2.  API trả kết quả về EC2.
3.  Security Group cho phép phản hồi quay về vì đó là phản hồi của kết
    nối đã được cho phép.

Bạn không cần tạo thêm một Inbound Rule riêng chỉ để cho phép phản hồi
của kết nối Outbound đó.

Tương tự, nếu người dùng gửi yêu cầu hợp lệ vào EC2, phản hồi của EC2
được phép đi ra mà không cần một Outbound Rule riêng cho gói phản hồi
đó.

### 3.7.6. Các đặc điểm cần nhớ

-   Security Group chỉ có Allow Rule, không có Deny Rule.
-   Security Group mới tạo không có Inbound Rule.
-   Security Group mới tạo thường có Outbound Rule cho phép toàn bộ lưu
    lượng đi ra.
-   Có thể gắn nhiều Security Group vào một tài nguyên.
-   Có thể dùng Security Group làm nguồn hoặc đích trong rule.
-   Thay đổi rule sẽ được áp dụng cho các tài nguyên đang sử dụng
    Security Group đó.

### 3.7.7. Security Group khác Network ACL như thế nào?

  Tiêu chí           Security Group                     Network ACL
  ------------------ ---------------------------------- -------------------------------
  Phạm vi            Cấp tài nguyên/Network Interface   Cấp Subnet
  Loại rule          Allow                              Allow và Deny
  Stateful           Có                                 Không
  Quy tắc phản hồi   Tự động cho phép                   Phải cấu hình chiều ngược lại
  Cách đánh giá      Tổng hợp các Allow Rule            Theo thứ tự rule

Hãy nhớ:

-   Security Group giống bảo vệ đứng ở cửa từng căn nhà.
-   Network ACL giống người bảo vệ ở cổng của cả khu dân cư.

------------------------------------------------------------------------

# 3.8. Key Pair --- Chìa khóa đăng nhập EC2

Bạn đã tạo một EC2. Bây giờ bạn muốn đăng nhập vào máy chủ đó.

Bạn cần chứng minh rằng mình có quyền truy cập.

AWS sử dụng **Key Pair** để hỗ trợ xác thực khi kết nối với Instance.

Một Key Pair gồm hai phần:

``` text
Key Pair
│
├── Public Key
│   └── Được lưu trên EC2
│
└── Private Key
    └── Được bạn giữ trên máy tính
```

Hãy tưởng tượng:

-   Public Key giống ổ khóa.
-   Private Key giống chìa khóa mở ổ khóa đó.

AWS lưu Public Key trên máy chủ. Bạn giữ Private Key.

Khi kết nối, máy chủ kiểm tra xem bạn có Private Key phù hợp hay không.

### 3.8.1. File Private Key

Khi tạo Key Pair, bạn có thể tải xuống file:

``` text
my-ec2-key.pem
```

File này rất quan trọng.

**Không gửi file Private Key cho người khác, không commit lên GitHub và
không đưa vào source code.**

AWS không lưu bản sao Private Key để bạn tải lại. Nếu mất file, bạn
không thể tải lại chính Private Key đó từ AWS.

### 3.8.2. SSH là gì?

SSH là viết tắt của Secure Shell.

Nói đơn giản, SSH cho phép bạn điều khiển máy chủ Linux từ xa bằng dòng
lệnh.

Ví dụ:

``` bash
ssh -i my-ec2-key.pem ubuntu@54.123.45.67
```

Ý nghĩa:

  Thành phần         Ý nghĩa
  ------------------ -----------------------------
  `ssh`              Bắt đầu kết nối SSH
  `-i`               Chỉ định file Private Key
  `my-ec2-key.pem`   File chìa khóa
  `ubuntu`           Tên người dùng trên máy chủ
  `54.123.45.67`     Public IP của EC2

Tên người dùng phụ thuộc vào AMI. Ví dụ:

-   Amazon Linux: thường là `ec2-user`.
-   Ubuntu: thường là `ubuntu`.

Không phải AMI nào cũng sử dụng cùng một username.

### 3.8.3. Kết nối từ Windows

Windows 10/11 có thể sử dụng OpenSSH trong PowerShell hoặc Windows
Terminal.

Ví dụ:

``` powershell
ssh -i "C:\keys\my-ec2-key.pem" ubuntu@54.123.45.67
```

Nếu kết nối thành công, bạn sẽ nhìn thấy terminal của máy chủ.

### 3.8.4. Những điều kiện cần có để SSH thành công

1.  EC2 đang ở trạng thái `running`.
2.  Có đường mạng để máy tính của bạn kết nối tới EC2.
3.  EC2 có địa chỉ IP phù hợp hoặc bạn có phương thức kết nối riêng.
4.  Security Group cho phép Inbound TCP port 22 từ IP của bạn.
5.  Bạn có đúng Private Key.
6.  Bạn sử dụng đúng username.

Thiếu một trong các điều kiện này, kết nối có thể thất bại.

------------------------------------------------------------------------

# 3.9. EC2 Instance Connect --- Kết nối mà không cần tự giữ Private Key để SSH

AWS cung cấp một cách khác để kết nối vào Linux EC2, đó là **EC2
Instance Connect**.

Hãy tưởng tượng:

-   SSH thông thường: bạn dùng chìa khóa riêng của mình.
-   EC2 Instance Connect: AWS hỗ trợ đưa một Public Key tạm thời lên máy
    chủ để bạn kết nối.

Khi sử dụng EC2 Instance Connect qua AWS Console hoặc AWS CLI:

1.  Bạn yêu cầu AWS kết nối đến Instance.
2.  AWS kiểm tra quyền IAM của bạn.
3.  AWS gửi Public Key tạm thời lên Instance.
4.  Public Key đó tồn tại trong thời gian ngắn, khoảng 60 giây.
5.  Bạn sử dụng kết nối SSH để đăng nhập.

### So sánh

  -----------------------------------------------------------------------
  SSH thông thường                    EC2 Instance Connect
  ----------------------------------- -----------------------------------
  Cần Private Key phù hợp             Có thể kết nối từ Console mà không
                                      cần tự quản lý Private Key

  Cần mở port 22 phù hợp              Vẫn cần đường mạng và cấu hình cho
                                      phép SSH

  Xác thực bằng Key Pair              Có kiểm tra quyền IAM để gửi Public
                                      Key

  Bạn quản lý Private Key             AWS hỗ trợ gửi Public Key tạm thời
  -----------------------------------------------------------------------

**Lưu ý:** EC2 Instance Connect không có nghĩa là bỏ qua mọi cấu hình
mạng. Instance vẫn cần hỗ trợ EC2 Instance Connect và có đường kết nối
phù hợp.

------------------------------------------------------------------------

# 3.10. User Data --- Tự động chạy lệnh khi khởi động

Đây là tính năng rất hữu ích khi tạo EC2.

Giả sử bạn vừa tạo một máy chủ Ubuntu hoặc Amazon Linux.

Bạn cần cài đặt:

-   Nginx.
-   Node.js.
-   Các công cụ cần thiết.
-   Một số cấu hình ban đầu.

Nếu phải SSH vào từng máy chủ rồi cài đặt thủ công, bạn sẽ mất thời
gian.

**User Data cho phép bạn gửi script để EC2 tự thực hiện các công việc
khởi tạo.**

Hãy tưởng tượng bạn thuê một căn nhà và yêu cầu người quản lý chuẩn bị
bàn ghế, điện nước trước khi bạn chuyển vào.

User Data giống như danh sách công việc được giao trước.

### 3.10.1. Ví dụ User Data cài Apache trên Amazon Linux 2023

``` bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello from Amazon EC2!</h1>" > /var/www/html/index.html
```

Giải thích:

  -----------------------------------------------------------------------
  Lệnh                                Ý nghĩa
  ----------------------------------- -----------------------------------
  `#!/bin/bash`                       Chạy script bằng Bash

  `dnf update -y`                     Cập nhật package

  `dnf install -y httpd`              Cài Apache HTTP Server

  `systemctl enable httpd`            Cho Apache tự khởi động khi hệ điều
                                      hành boot

  `systemctl start httpd`             Khởi động Apache ngay

  `echo ...`                          Tạo nội dung trang web
  -----------------------------------------------------------------------

Khi EC2 khởi động lần đầu, script được thực thi để chuẩn bị máy chủ.

### 3.10.2. User Data chạy khi nào?

Với cấu hình Linux phổ biến sử dụng cloud-init, User Data script mặc
định thường chỉ chạy trong lần khởi động đầu tiên.

Có thể cấu hình để chạy trong những lần khởi động tiếp theo, nhưng không
nên mặc định rằng script sẽ chạy lại mỗi lần Restart.

### 3.10.3. User Data có thể làm gì?

-   Cài đặt phần mềm.
-   Tạo thư mục.
-   Tạo file cấu hình.
-   Khởi động dịch vụ.
-   Tải ứng dụng.
-   Chuẩn bị môi trường chạy Backend.

### 3.10.4. Có nên lưu mật khẩu trong User Data không?

**Không nên.**

User Data có thể được truy cập từ bên trong Instance và không được thiết
kế để lưu bí mật.

Không đưa các thông tin như:

``` text
DATABASE_PASSWORD
AWS_SECRET_ACCESS_KEY
PRIVATE_API_KEY
```

vào User Data dưới dạng văn bản thuần.

Nếu ứng dụng cần bí mật, hãy sử dụng giải pháp quản lý bí mật phù hợp
như AWS Secrets Manager hoặc Systems Manager Parameter Store, kết hợp
IAM Role.

------------------------------------------------------------------------

# 3.11. IAM Role cho EC2 --- Cấp quyền cho máy chủ

Chúng ta đã học IAM ở Chương 2.

Bây giờ hãy áp dụng kiến thức đó.

Giả sử bạn có một ứng dụng chạy trên EC2. Ứng dụng cần đọc file từ
Amazon S3.

Câu hỏi:

Làm sao để ứng dụng trên EC2 có quyền đọc file từ S3?

Một cách không an toàn là tạo Access Key và Secret Key rồi ghi trực tiếp
vào source code.

Ví dụ không nên làm:

``` javascript
const s3 = new S3Client({
  credentials: {
    accessKeyId: "AKIAxxxxxxxx",
    secretAccessKey: "xxxxxxxx"
  }
});
```

Nếu source code bị lộ, người khác có thể sử dụng các thông tin này.

Cách tốt hơn là sử dụng **IAM Role gắn vào EC2**.

### 3.11.1. IAM Role hoạt động như thế nào?

Hãy tưởng tượng bạn thuê một nhân viên.

Bạn không cần đưa cho nhân viên chìa khóa của toàn bộ tòa nhà. Bạn chỉ
cấp thẻ ra vào những phòng mà nhân viên cần sử dụng.

IAM Role cũng tương tự.

Bạn tạo một Role và cấp quyền phù hợp.

Ví dụ:

``` text
EC2 Instance
    │
    │ Được gắn IAM Role
    ▼
IAM Role: EC2ReadS3Role
    │
    │ Policy: Chỉ cho phép đọc file
    ▼
Amazon S3
```

Ứng dụng trên EC2 có thể lấy thông tin xác thực tạm thời từ cơ chế AWS
cung cấp và sử dụng chúng để gọi dịch vụ AWS.

Bạn không cần tự tạo và phân phối Access Key dài hạn cho từng máy chủ.

### 3.11.2. Ví dụ thực tế

Giả sử có Bucket:

``` text
ezleague-files
```

Ứng dụng trên EC2 chỉ cần đọc file từ Bucket này.

Bạn có thể tạo IAM Policy chỉ cho phép:

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::ezleague-files/*"
    }
  ]
}
```

Sau đó:

1.  Tạo IAM Role dành cho EC2.
2.  Chọn EC2 làm Trusted Entity.
3.  Gắn Policy phù hợp vào Role.
4.  Gắn Role vào EC2.
5.  Ứng dụng trên EC2 sử dụng AWS SDK để gọi S3.

### 3.11.3. IAM User và IAM Role cho EC2 khác nhau thế nào?

  -----------------------------------------------------------------------
  IAM User                            IAM Role gắn vào EC2
  ----------------------------------- -----------------------------------
  Đại diện cho người hoặc danh tính   Cấp quyền cho dịch vụ hoặc ứng dụng
  lâu dài                             

  Có thể sử dụng Access Key dài hạn   Sử dụng thông tin xác thực tạm thời

  Cần quản lý và xoay vòng Access Key AWS quản lý việc cấp thông tin xác
  nếu dùng                            thực tạm thời

  Không nên nhúng Access Key vào      Không cần nhúng Access Key vào
  source code                         source code
  -----------------------------------------------------------------------

**Ghi nhớ để thi:** Nếu đề bài hỏi cách an toàn để ứng dụng chạy trên
EC2 truy cập S3, hãy nghĩ đến IAM Role thay vì lưu Access Key và Secret
Key trên máy chủ.

------------------------------------------------------------------------

# 3.12. EC2 Instance Lifecycle --- Vòng đời của máy chủ

Một máy chủ EC2 không phải lúc nào cũng chạy.

Bạn có thể tạo, bật, tắt, khởi động lại hoặc xóa nó.

Có một số trạng thái quan trọng:

  Trạng thái        Ý nghĩa
  ----------------- -------------------------------------
  `pending`         AWS đang chuẩn bị khởi động máy chủ
  `running`         Máy chủ đang chạy
  `stopping`        Máy chủ đang được tắt
  `stopped`         Máy chủ đã tắt
  `shutting-down`   Máy chủ đang được xóa
  `terminated`      Máy chủ đã bị xóa

### 3.12.1. Reboot là gì?

Reboot giống như khởi động lại máy tính.

Ví dụ:

``` text
Running
   │
   ▼
Reboot
   │
   ▼
Running
```

Instance vẫn tồn tại.

Với thao tác Reboot thông thường, Public IP và Private IP của Instance
được giữ nguyên.

### 3.12.2. Stop là gì?

Stop giống như tắt máy tính.

``` text
Running
   │
   ▼
Stop
   │
   ▼
Stopped
```

Instance chưa bị xóa. Bạn có thể Start lại sau đó.

Với Instance sử dụng EBS làm Root Volume:

-   Bạn không còn trả phí sử dụng compute của Instance khi nó ở trạng
    thái Stopped.
-   Bạn vẫn phải trả phí lưu trữ EBS.
-   Public IPv4 tự động thường sẽ thay đổi khi Start lại.
-   Private IP thường được giữ nguyên.
-   Dữ liệu trên Instance Store bị mất khi Stop.

### 3.12.3. Terminate là gì?

Terminate giống như trả lại và xóa chiếc máy tính thuê.

``` text
Running
   │
   ▼
Terminate
   │
   ▼
Terminated
```

Sau khi Terminate, bạn không thể Start lại chính Instance đó.

Với cấu hình thông thường, Root EBS Volume sẽ bị xóa khi Terminate. Tuy
nhiên, điều này phụ thuộc cấu hình Delete on Termination.

**Trước khi Terminate, hãy kiểm tra dữ liệu và các Volume liên quan.**

### 3.12.4. Phân biệt Stop, Reboot và Terminate

  Hành động   Ý nghĩa               Có thể chạy lại Instance?
  ----------- --------------------- ---------------------------
  Reboot      Khởi động lại         Có
  Stop        Tắt máy               Có
  Start       Bật lại máy đã Stop   Có
  Terminate   Xóa Instance          Không

Đây là bảng rất đáng nhớ khi học EC2.

------------------------------------------------------------------------

# 3.13. EC2 Purchasing Options --- Các cách thuê máy chủ

Không phải lúc nào bạn cũng thuê máy chủ theo cùng một cách.

AWS cung cấp nhiều lựa chọn thanh toán khác nhau.

Hãy tưởng tượng bạn cần thuê xe:

-   Thuê theo giờ khi cần.
-   Thuê dài hạn để được giảm giá.
-   Thuê xe đang nhàn rỗi với giá rẻ hơn nhưng có thể bị thu hồi.
-   Thuê riêng một chiếc xe chỉ dành cho bạn.

EC2 cũng có những lựa chọn tương tự.

### 3.13.1. On-Demand Instances

Đây là hình thức đơn giản nhất.

Bạn tạo máy chủ khi cần và trả tiền cho thời gian sử dụng mà không phải
cam kết dài hạn.

Ví dụ:

Bạn cần một máy chủ để thử nghiệm trong vài ngày.

Bạn chọn On-Demand, sử dụng rồi Stop hoặc Terminate khi không cần nữa.

**Phù hợp với:**

-   Người mới học AWS.
-   Môi trường thử nghiệm.
-   Ứng dụng có tải không ổn định.
-   Công việc ngắn hạn.

### 3.13.2. Reserved Instances

Bạn cam kết sử dụng một cấu hình EC2 trong thời hạn thường là 1 năm hoặc
3 năm để nhận mức giá ưu đãi.

Ví dụ:

Bạn có một hệ thống cần chạy ổn định trong thời gian dài.

Bạn dự đoán nhu cầu sử dụng khá chắc chắn và muốn tối ưu chi phí.

**Lưu ý:** Reserved Instance chủ yếu là hình thức cam kết sử dụng để
nhận ưu đãi giá. Không nên nhầm nó với việc luôn có một máy chủ vật lý
được dành riêng cho bạn. Zonal Reserved Instance có thể cung cấp thêm
khả năng đặt trước dung lượng tại Availability Zone.

### 3.13.3. Savings Plans

Savings Plans cũng là cách giảm chi phí khi bạn cam kết một mức sử dụng
tính theo USD mỗi giờ trong thời hạn thường là 1 năm hoặc 3 năm.

Điểm khác biệt quan trọng:

-   Reserved Instances gắn với các điều kiện cấu hình Instance nhất
    định.
-   Savings Plans linh hoạt hơn tùy loại Savings Plan và phạm vi áp
    dụng.

### 3.13.4. Spot Instances

Đây là một hình thức thuê máy chủ tận dụng tài nguyên AWS đang chưa được
sử dụng.

Giá có thể rẻ hơn đáng kể so với On-Demand.

Nhưng có một nhược điểm:

**AWS có thể thu hồi Spot Instance khi cần lấy lại tài nguyên.**

Ví dụ:

Bạn chạy một chương trình xử lý dữ liệu mất 10 giờ. Chương trình có thể
lưu tiến độ và tiếp tục chạy sau khi bị gián đoạn.

Spot có thể là lựa chọn phù hợp.

Nhưng nếu bạn đang chạy một giao dịch thanh toán quan trọng và không thể
bị gián đoạn, Spot có thể không phải lựa chọn thích hợp.

### 3.13.5. Dedicated Hosts

Bạn thuê một máy chủ vật lý dành riêng cho mình.

Phù hợp với những nhu cầu đặc biệt như:

-   Yêu cầu tuân thủ.
-   Cần kiểm soát máy chủ vật lý.
-   Có giấy phép phần mềm phụ thuộc vào số socket hoặc core.

### 3.13.6. Dedicated Instances

Instance chạy trên phần cứng dành riêng cho một khách hàng, nhưng không
cung cấp mức kiểm soát máy chủ vật lý như Dedicated Hosts.

### 3.13.7. Capacity Reservations

Bạn đặt trước dung lượng EC2 trong một Availability Zone cụ thể để đảm
bảo có khả năng khởi chạy Instance khi cần.

Đây là khái niệm về **đảm bảo dung lượng**, không đơn thuần là một hình
thức giảm giá.

### Bảng tổng hợp

  -----------------------------------------------------------------------
  Hình thức               Đặc điểm chính          Khi nào nên dùng?
  ----------------------- ----------------------- -----------------------
  On-Demand               Trả theo mức sử dụng,   Thử nghiệm, tải thay
                          không cam kết dài hạn   đổi

  Reserved Instances      Cam kết cấu hình để     Nhu cầu ổn định
                          được giảm giá           

  Savings Plans           Cam kết mức chi tiêu sử Nhu cầu ổn định nhưng
                          dụng để được giảm giá   cần linh hoạt

  Spot                    Dùng tài nguyên dư      Batch, xử lý nền có thể
                          thừa, có thể bị gián    chạy lại
                          đoạn                    

  Dedicated Hosts         Thuê máy chủ vật lý     Tuân thủ, giấy phép
                          riêng                   phần mềm

  Dedicated Instances     Chạy trên phần cứng     Nhu cầu cách ly phần
                          dành riêng              cứng

  Capacity Reservations   Đặt trước dung lượng ở  Cần chắc chắn có tài
                          AZ                      nguyên để chạy
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 3.14. Quy trình tạo EC2 Instance trên AWS Console

Bây giờ chúng ta sẽ thực hành tạo máy chủ đầu tiên.

Mục tiêu:

-   Tạo một EC2 chạy Amazon Linux 2023.
-   Chọn cấu hình nhỏ để học.
-   Tạo Key Pair.
-   Cấu hình Security Group.
-   Cài Apache bằng User Data.
-   Kết nối và xem website.

**Lưu ý về chi phí:** Giá EC2, EBS và Public IPv4 phụ thuộc Region, loại
Instance và chính sách tài khoản. Không mặc định rằng mọi Instance nhỏ
đều miễn phí. Hãy kiểm tra giá trong AWS Pricing và Billing trước khi
tạo.

### Bước 1. Mở EC2

Truy cập:

https://console.aws.amazon.com/ec2/

Chọn Region bạn muốn sử dụng.

Ví dụ:

``` text
Asia Pacific (Singapore)
ap-southeast-1
```

Region phải thống nhất với nơi bạn muốn triển khai tài nguyên.

### Bước 2. Chọn Launch Instance

Trong EC2 Dashboard:

1.  Chọn Instances.
2.  Chọn Launch instances.
3.  Nhập tên máy chủ.

Ví dụ:

``` text
Name: aws-learning-ec2-01
```

### Bước 3. Chọn AMI

Chọn:

``` text
Amazon Linux 2023
```

Đây là hệ điều hành Linux do AWS cung cấp.

### Bước 4. Chọn Instance Type

Chọn một Instance nhỏ phù hợp để thực hành.

Ví dụ:

``` text
t3.micro
```

Đây chỉ là cấu hình minh họa. Hãy kiểm tra giá và khả năng hỗ trợ trong
Region trước khi tạo.

### Bước 5. Tạo Key Pair

Chọn Create new key pair.

Ví dụ:

``` text
Name: aws-learning-key
Key pair type: RSA
Private key format: .pem
```

Tải file `.pem` về máy tính.

Lưu ở nơi an toàn, ví dụ:

``` text
C:\AWS\keys\aws-learning-key.pem
```

Không tải file này lên GitHub.

### Bước 6. Cấu hình Network và Security Group

Trong Network Settings:

-   Chọn VPC phù hợp.
-   Chọn Subnet phù hợp.
-   Nếu muốn kết nối trực tiếp từ Internet, cần cấu hình Public IP và
    đường mạng phù hợp.

Tạo Security Group:

``` text
Name: aws-learning-ec2-sg
```

Thêm Inbound Rules:

  Type     Port Source
  ------ ------ ---------------
  SSH        22 My IP
  HTTP       80 Anywhere IPv4

Ở đây:

-   SSH chỉ mở cho IP của bạn.
-   HTTP mở cho người dùng truy cập website.

Không mở SSH cho toàn bộ Internet nếu không cần thiết.

### Bước 7. Cấu hình Storage

Chọn dung lượng EBS phù hợp cho bài thực hành.

Ví dụ:

``` text
Root Volume: 8 GiB
```

Dung lượng thực tế có thể thay đổi theo AMI và nhu cầu sử dụng.

### Bước 8. Nhập User Data

Mở Advanced details, tìm phần User data.

Dán script:

``` bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello! My first AWS EC2 server.</h1>" > /var/www/html/index.html
```

### Bước 9. Launch Instance

Kiểm tra lại các lựa chọn rồi chọn Launch instance.

Chờ Instance chuyển sang trạng thái:

``` text
running
```

### Bước 10. Kiểm tra địa chỉ IP

Chọn Instance vừa tạo.

Tìm:

``` text
Public IPv4 address
Private IPv4 address
```

Ví dụ:

``` text
Public IPv4: 54.123.45.67
Private IPv4: 10.0.1.10
```

Địa chỉ thực tế của bạn sẽ khác.

### Bước 11. Truy cập website

Mở trình duyệt:

``` text
http://PUBLIC_IP
```

Nếu cấu hình đúng, bạn sẽ nhìn thấy:

``` text
Hello! My first AWS EC2 server.
```

Chúc mừng! Bạn vừa tạo máy chủ ảo đầu tiên trên AWS.

------------------------------------------------------------------------

# 3.15. Kết nối EC2 bằng SSH từ Windows

Nếu muốn quản trị máy chủ, bạn có thể dùng SSH.

Mở PowerShell:

``` powershell
ssh -i "C:\AWS\keys\aws-learning-key.pem" ec2-user@PUBLIC_IP
```

Nếu dùng Amazon Linux, username thường là `ec2-user`.

Nếu dùng Ubuntu, username thường là `ubuntu`.

Sau khi kết nối thành công, thử chạy:

``` bash
whoami
```

Kiểm tra hệ điều hành:

``` bash
cat /etc/os-release
```

Kiểm tra dung lượng:

``` bash
df -h
```

Kiểm tra RAM:

``` bash
free -h
```

Kiểm tra CPU:

``` bash
nproc
```

Bạn đang điều khiển một máy tính khác thông qua Internet.

------------------------------------------------------------------------

# 3.16. Một số lệnh AWS CLI quản lý EC2

Nếu đã cài AWS CLI và cấu hình quyền truy cập, bạn có thể quản lý EC2
bằng dòng lệnh.

### Liệt kê các Instance

``` bash
aws ec2 describe-instances \
  --query "Reservations[].Instances[].[InstanceId,State.Name,InstanceType,PublicIpAddress,PrivateIpAddress]" \
  --output table
```

### Khởi động Instance

``` bash
aws ec2 start-instances \
  --instance-ids i-0123456789abcdef0
```

### Tắt Instance

``` bash
aws ec2 stop-instances \
  --instance-ids i-0123456789abcdef0
```

### Khởi động lại Instance

``` bash
aws ec2 reboot-instances \
  --instance-ids i-0123456789abcdef0
```

### Xóa Instance

``` bash
aws ec2 terminate-instances \
  --instance-ids i-0123456789abcdef0
```

**Cảnh báo:** `terminate-instances` là lệnh xóa Instance. Hãy kiểm tra
đúng Instance ID trước khi thực hiện.

------------------------------------------------------------------------

# 3.17. Những lỗi thường gặp khi sử dụng EC2

### Lỗi 1: SSH Connection timed out

Ví dụ:

``` text
ssh: connect to host ... port 22: Connection timed out
```

Nguyên nhân thường gặp:

-   Security Group chưa mở port 22.
-   Source IP trong Security Group không đúng.
-   Máy chủ không có Public IP.
-   Subnet hoặc Route Table chưa có đường ra Internet.
-   Máy tính của bạn bị chặn kết nối bởi mạng nội bộ hoặc Firewall.

Cách kiểm tra:

1.  Instance có đang `running` không?
2.  Public IP có tồn tại không?
3.  Security Group có cho phép port 22 không?
4.  Source IP có đúng IP hiện tại của bạn không?
5.  Subnet có đường mạng phù hợp không?

### Lỗi 2: Permission denied (publickey)

Ví dụ:

``` text
Permission denied (publickey).
```

Nguyên nhân thường gặp:

-   Dùng sai Private Key.
-   Dùng sai username.
-   Key Pair không tương ứng với Public Key trên máy chủ.
-   File key có quyền truy cập không phù hợp.

Cách xử lý:

-   Kiểm tra lại file `.pem`.
-   Kiểm tra username theo AMI.
-   Đảm bảo dùng đúng Key Pair khi tạo Instance.

### Lỗi 3: Trình duyệt không truy cập được website

Ví dụ:

``` text
This site can't be reached
```

Kiểm tra:

1.  Apache/Nginx đã chạy chưa?
2.  Security Group có mở port 80 hoặc 443 chưa?
3.  Instance có Public IP và đường mạng phù hợp không?
4.  Ứng dụng có lắng nghe đúng port không?

Trên máy chủ có thể kiểm tra:

``` bash
sudo systemctl status httpd
```

Hoặc nếu dùng Nginx:

``` bash
sudo systemctl status nginx
```

### Lỗi 4: EC2 chạy nhưng ứng dụng không hoạt động

Có thể do:

-   User Data không chạy thành công.
-   Phần mềm chưa cài được.
-   Service chưa khởi động.
-   Cấu hình ứng dụng sai.
-   Thiếu biến môi trường.

Đừng chỉ nhìn trạng thái EC2 là `running` rồi kết luận website đã hoạt
động. EC2 chạy không có nghĩa ứng dụng bên trong cũng chạy.

### Lỗi 5: Stop EC2 rồi Start lại nhưng Public IP thay đổi

Đây là hành vi bình thường nếu bạn sử dụng Public IP tự động.

Nếu cần một Public IPv4 cố định, hãy tìm hiểu Elastic IP hoặc các giải
pháp mạng phù hợp.

### Lỗi 6: Đã Stop EC2 nhưng vẫn bị tính phí

Stop chỉ dừng chi phí compute của Instance.

Bạn vẫn có thể bị tính phí cho:

-   EBS Volume.
-   Snapshot.
-   Elastic IP/Public IPv4 theo chính sách giá hiện hành.
-   Các tài nguyên AWS khác.

Hãy kiểm tra Billing để biết tài nguyên nào đang phát sinh chi phí.

------------------------------------------------------------------------

# 3.18. Các nguyên tắc bảo mật khi sử dụng EC2

1.  Không mở SSH port 22 cho toàn bộ Internet nếu không cần thiết.
2.  Chỉ mở những Port mà ứng dụng thực sự sử dụng.
3.  Không lưu Access Key và Secret Key trong source code.
4.  Sử dụng IAM Role cho ứng dụng chạy trên EC2.
5.  Không chia sẻ file Private Key.
6.  Không lưu mật khẩu hoặc dữ liệu bí mật trong User Data.
7.  Cập nhật hệ điều hành và phần mềm thường xuyên.
8.  Chỉ cấp quyền IAM cần thiết theo nguyên tắc Least Privilege.
9.  Kiểm tra EBS Volume, Public IPv4 và các tài nguyên không còn sử
    dụng.
10. Kiểm tra Billing sau khi thực hành.

------------------------------------------------------------------------

# 3.19. Kiến thức trọng tâm cần nhớ khi thi DVA-C02

Đây là những điểm bạn nên đặc biệt chú ý khi làm câu hỏi về EC2.

  -----------------------------------------------------------------------
  Câu hỏi trong đề thi                Hướng suy nghĩ
  ----------------------------------- -----------------------------------
  Muốn tạo máy chủ ảo trên AWS?       Amazon EC2

  Muốn tạo Instance từ một bản mẫu hệ AMI
  điều hành?                          

  Muốn chọn CPU và RAM?               Instance Type

  Muốn kiểm soát kết nối đến EC2?     Security Group

  Muốn SSH vào Linux EC2?             Key Pair và TCP port 22

  Muốn kết nối qua AWS Console mà     EC2 Instance Connect, nếu đáp ứng
  không tự quản lý Private Key?       điều kiện

  Muốn ứng dụng trên EC2 đọc file từ  IAM Role
  S3 an toàn?                         

  Muốn tự động cài phần mềm khi khởi  User Data
  động Instance?                      

  Muốn máy chủ không bị gián đoạn và  Xem On-Demand
  cần cấu hình thanh toán linh hoạt?  

  Muốn tận dụng tài nguyên dư thừa,   Spot Instance
  chấp nhận bị gián đoạn?             

  Muốn giảm giá bằng cam kết sử dụng? Savings Plans hoặc Reserved
                                      Instances

  Muốn đặt trước dung lượng ở một     Capacity Reservations
  Availability Zone?                  

  Muốn giữ Public IPv4 cố định?       Elastic IP

  Muốn tắt tạm thời để không trả tiền Stop, nhưng vẫn có thể trả phí lưu
  compute?                            trữ và tài nguyên khác

  Muốn xóa Instance vĩnh viễn?        Terminate
  -----------------------------------------------------------------------

### Những bẫy thường gặp

**Bẫy 1: Security Group là Stateless?**

Sai. Security Group là Stateful.

**Bẫy 2: Security Group có Deny Rule?**

Sai. Security Group chỉ có Allow Rule.

**Bẫy 3: EC2 Role và IAM User giống nhau?**

Không giống nhau. Role được gắn vào EC2 để ứng dụng nhận thông tin xác
thực tạm thời.

**Bẫy 4: Stop và Terminate giống nhau?**

Không giống nhau. Stop chỉ tắt máy; Terminate xóa Instance.

**Bẫy 5: User Data luôn chạy mỗi lần Restart?**

Sai. Với cấu hình Linux phổ biến, User Data mặc định thường chỉ chạy lần
khởi động đầu tiên.

**Bẫy 6: Spot Instance phù hợp với mọi ứng dụng?**

Sai. Spot có thể bị gián đoạn, vì vậy ứng dụng phải có khả năng chịu
hoặc phục hồi sau gián đoạn.

------------------------------------------------------------------------

# 3.20. Câu hỏi ôn tập

Hãy tự trả lời trước khi xem đáp án.

### Câu 1

Dịch vụ AWS nào cho phép bạn thuê máy chủ ảo?

A. Amazon S3\
B. Amazon EC2\
C. Amazon Route 53\
D. Amazon CloudFront

### Câu 2

Bạn muốn tạo 10 máy chủ có cùng hệ điều hành và cấu hình phần mềm ban
đầu. Bạn nên sử dụng gì?

A. Security Group\
B. Elastic IP\
C. AMI\
D. IAM User

### Câu 3

Bạn muốn chọn máy chủ có nhiều RAM hơn. Bạn cần thay đổi gì?

A. Security Group\
B. Instance Type\
C. Key Pair\
D. Public IP

### Câu 4

Security Group có đặc điểm nào?

A. Stateless\
B. Có Allow và Deny Rule\
C. Stateful\
D. Chỉ hoạt động với port 80

### Câu 5

Bạn cần SSH vào Linux EC2. Port mặc định nào thường được sử dụng?

A. 21\
B. 22\
C. 80\
D. 443

### Câu 6

Ứng dụng trên EC2 cần đọc dữ liệu từ S3. Giải pháp an toàn được khuyến
nghị là gì?

A. Ghi Access Key vào source code\
B. Dùng IAM Role gắn với EC2\
C. Mở toàn bộ Port\
D. Tạo Elastic IP

### Câu 7

Bạn muốn tự động cài Apache khi tạo EC2. Bạn nên dùng gì?

A. User Data\
B. Elastic IP\
C. Key Pair\
D. Security Group

### Câu 8

Bạn Stop một EC2 dùng EBS Root Volume. Điều gì thường xảy ra?

A. Instance bị xóa vĩnh viễn\
B. Có thể Start lại Instance\
C. EBS Volume luôn bị xóa\
D. Không thể truy cập AWS Account nữa

### Câu 9

Bạn muốn thuê tài nguyên EC2 dư thừa với giá thấp hơn và chấp nhận khả
năng bị gián đoạn. Nên chọn gì?

A. On-Demand\
B. Spot\
C. Dedicated Host\
D. Capacity Reservation

### Câu 10

Bạn muốn giữ một Public IPv4 tĩnh khi Stop rồi Start EC2. Nên tìm hiểu
dịch vụ nào?

A. Private IP\
B. Security Group\
C. Elastic IP\
D. AMI

### Câu 11

Bạn muốn đặt trước dung lượng EC2 trong một Availability Zone cụ thể.
Nên dùng gì?

A. Capacity Reservation\
B. User Data\
C. Key Pair\
D. IAM User

### Câu 12

Bạn đã Stop EC2 nhưng vẫn thấy chi phí phát sinh. Nguyên nhân nào có thể
xảy ra?

A. AWS vẫn tính tiền CPU của Instance đã Stop\
B. EBS Volume và một số tài nguyên khác vẫn phát sinh chi phí\
C. Stop thực chất là Terminate\
D. IAM Role đang chạy CPU

------------------------------------------------------------------------

## Đáp án và giải thích

  ------------------------------------------------------------------------
                           Câu Đáp án                Giải thích
  ---------------------------- --------------------- ---------------------
                             1 B                     EC2 là dịch vụ máy
                                                     chủ ảo

                             2 C                     AMI là bản mẫu để tạo
                                                     Instance

                             3 B                     Instance Type quyết
                                                     định cấu hình CPU,
                                                     RAM và tài nguyên

                             4 C                     Security Group là
                                                     Stateful

                             5 B                     SSH mặc định dùng TCP
                                                     port 22

                             6 B                     IAM Role cấp quyền an
                                                     toàn cho ứng dụng
                                                     trên EC2

                             7 A                     User Data chạy script
                                                     khởi tạo

                             8 B                     Instance dùng EBS
                                                     Root Volume có thể
                                                     Stop rồi Start

                             9 B                     Spot dùng tài nguyên
                                                     dư thừa và có thể bị
                                                     gián đoạn

                            10 C                     Elastic IP là Public
                                                     IPv4 tĩnh

                            11 A                     Capacity Reservation
                                                     đặt trước dung lượng
                                                     trong AZ

                            12 B                     EBS, địa chỉ IP và
                                                     các tài nguyên khác
                                                     có thể vẫn bị tính
                                                     phí
  ------------------------------------------------------------------------

------------------------------------------------------------------------

# 3.21. Bài thực hành cuối chương

## Bài tập: Tạo một Web Server đơn giản trên AWS

### Yêu cầu

Tạo một EC2 có thể chạy một website HTML đơn giản.

### Công việc cần thực hiện

-   [ ] Chọn Region.
-   [ ] Chọn AMI Amazon Linux 2023.
-   [ ] Chọn Instance Type phù hợp với chi phí học tập.
-   [ ] Tạo Key Pair.
-   [ ] Tạo Security Group.
-   [ ] Chỉ mở SSH từ IP của bạn.
-   [ ] Mở HTTP port 80 để truy cập website.
-   [ ] Cấu hình User Data để tự cài Apache.
-   [ ] Launch Instance.
-   [ ] SSH vào EC2.
-   [ ] Kiểm tra trạng thái Apache.
-   [ ] Truy cập website bằng Public IP.
-   [ ] Stop Instance và quan sát trạng thái.
-   [ ] Start lại Instance và kiểm tra Public IP.
-   [ ] Terminate Instance sau khi hoàn thành nếu không còn nhu cầu.

### Câu hỏi tự kiểm tra

1.  Nếu không có Security Group cho phép port 80, người dùng có thể truy
    cập website không?
2.  Nếu bạn Stop rồi Start EC2, Public IP tự động có thể thay đổi không?
3.  Nếu ứng dụng cần đọc file từ S3, bạn nên cấp quyền bằng cách nào?
4.  Nếu muốn tạo một máy chủ mới có cấu hình phần mềm giống máy chủ hiện
    tại, bạn có thể dùng gì?
5.  Nếu Terminate EC2, bạn cần kiểm tra điều gì để tránh mất dữ liệu?
6.  Vì sao không nên mở SSH cho toàn bộ Internet?
7.  Vì sao Stop EC2 không có nghĩa là mọi chi phí đều bằng 0?

------------------------------------------------------------------------

# 3.22. Tóm tắt chương

Nếu chỉ được nhớ 10 điều sau chương này, hãy nhớ:

1.  **EC2** là máy chủ ảo trên AWS.
2.  **Instance** là một máy chủ EC2 cụ thể.
3.  **AMI** là bản mẫu để tạo máy chủ.
4.  **Instance Type** quyết định cấu hình tài nguyên.
5.  **Security Group** là tường lửa ảo kiểm soát kết nối.
6.  **Key Pair** hỗ trợ xác thực khi kết nối đến máy chủ.
7.  **User Data** tự động thực hiện công việc khởi tạo.
8.  **IAM Role** cấp quyền AWS cho ứng dụng trên EC2.
9.  **Stop** là tắt máy, còn **Terminate** là xóa máy.
10. **On-Demand, Spot, Savings Plans, Reserved Instances** là các lựa
    chọn quan trọng để sử dụng và tối ưu chi phí EC2.

## Bức tranh tổng thể

``` text
                    INTERNET
                       │
                       ▼
                Security Group
                       │
                       ▼
                 EC2 Instance
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
         AMI       Instance Type   EBS
      (Hệ điều     (CPU, RAM)    (Ổ cứng)
       hành)
          │
          ▼
       User Data
    (Tự động cài đặt)

          EC2 Instance
                │
                ▼
            IAM Role
                │
        ┌───────┴────────┐
        ▼                ▼
       S3              Other AWS
                       Services
```

------------------------------------------------------------------------

# Tài liệu tham khảo chính thức

1.  [What is Amazon
    EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)
2.  [Launch an Amazon EC2
    instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html)
3.  [Amazon EC2 instance
    types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)
4.  [Amazon EC2 security
    groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html)
5.  [Amazon EC2 key
    pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
6.  [Connect to your EC2
    instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect.html)
7.  [EC2 Instance
    Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-methods.html)
8.  [Run commands with User
    Data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)
9.  [IAM roles for Amazon
    EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
10. [EC2 instance
    lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html)
11. [EC2 purchasing
    options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html)
12. [Elastic IP
    addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)

------------------------------------------------------------------------

**Kết thúc Chương 3 -- Amazon EC2 Basics**

**Chương tiếp theo: Chương 4 -- Amazon EC2 Instance Storage**

Chúng ta sẽ tìm hiểu sâu hơn về EBS, EBS Volume, Snapshot, Instance
Store, EFS và cách lưu trữ dữ liệu trên AWS.
