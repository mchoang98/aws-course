# CHƯƠNG 8. AMAZON VPC BASICS
## Xây dựng thành phố riêng trong AWS

*KidZone StoryTech – Học công nghệ bằng những câu chuyện thú vị*

---

# 🌟 Câu chuyện mở đầu: Thành phố Internet của An và Bin

Một ngày nọ, An và Bin quyết định xây dựng một thành phố công nghệ trên AWS.

Thành phố của hai bạn có:

- Một cửa hàng trực tuyến để mọi người mua sắm.
- Một nhà kho chứa hàng hóa.
- Một văn phòng để nhân viên làm việc.
- Một phòng máy tính chứa dữ liệu quan trọng.
- Một cổng chính để khách hàng từ Internet có thể ghé thăm.

Ban đầu, An muốn đặt tất cả mọi thứ ở cùng một nơi và mở cửa cho mọi người đi vào.

Bin lập tức phản đối:

"Không được! Nếu ai cũng có thể vào phòng chứa dữ liệu thì nguy hiểm lắm!"

Cô Mây, người hướng dẫn hai bạn, mỉm cười:

"Đó chính là lý do chúng ta cần Amazon VPC. Trong AWS, chúng ta có thể tự xây dựng một mạng máy tính riêng, quyết định ai được vào, ai được ra và các máy tính được phép giao tiếp với nhau như thế nào."

An tò mò:

"Vậy chúng ta có thể xây dựng một thành phố riêng của mình ngay trên Internet sao?"

"Chính xác! Và hôm nay chúng ta sẽ học cách làm điều đó."

---

# 🎯 Mục tiêu của chương

Sau khi hoàn thành chương này, bạn sẽ hiểu:

1. Mạng máy tính là gì và các máy tính giao tiếp với nhau như thế nào?
2. Địa chỉ IP và CIDR là gì?
3. Amazon VPC là gì và tại sao cần sử dụng?
4. Subnet là gì? Public Subnet khác Private Subnet như thế nào?
5. Route Table điều khiển đường đi của dữ liệu ra sao?
6. Internet Gateway và NAT Gateway có nhiệm vụ gì?
7. Security Group và Network ACL bảo vệ hệ thống như thế nào?
8. VPC Endpoint giúp kết nối AWS Services ra sao?
9. VPC Peering dùng để làm gì?
10. VPC Flow Logs giúp theo dõi lưu lượng mạng như thế nào?
11. Bastion Host và Systems Manager Session Manager khác nhau ra sao?
12. Làm thế nào để thiết kế một VPC cho ứng dụng thực tế?

---

# PHẦN 1 – HIỂU VỀ MẠNG MÁY TÍNH

## 8.1. Mạng máy tính là gì?

Hãy tưởng tượng An và Bin đang ở hai ngôi nhà khác nhau.

An muốn gửi một bức thư cho Bin. Để bức thư đến đúng nơi, An cần biết:

- Bin đang ở đâu?
- Địa chỉ nhà của Bin là gì?
- Làm thế nào để gửi bức thư đến đó?

Máy tính cũng hoạt động tương tự.

Khi hai máy tính muốn trao đổi dữ liệu, chúng cần một hệ thống kết nối gọi là **Computer Network – Mạng máy tính**.

Ví dụ:

- Máy tính của bạn kết nối với máy chủ của Google.
- Điện thoại kết nối với máy chủ của Facebook.
- Một ứng dụng kết nối với cơ sở dữ liệu MySQL.
- Một máy chủ EC2 kết nối với một máy chủ EC2 khác.

Tất cả đều cần mạng máy tính.

### Mạng máy tính hoạt động như thế nào?

Hãy tưởng tượng:

| Trong đời sống | Trong mạng máy tính |
|---|---|
| Ngôi nhà | Máy tính hoặc Server |
| Địa chỉ nhà | Địa chỉ IP |
| Đường phố | Network |
| Cửa ra vào | Port |
| Quy tắc giao tiếp | Protocol |
| Người đưa thư | Network Router |

Khi An gửi dữ liệu đến Bin, máy tính sẽ sử dụng địa chỉ IP để xác định nơi cần gửi dữ liệu.

### Ví dụ thực tế

Bạn mở trình duyệt và truy cập:

`https://www.google.com`

Điều gì xảy ra?

1. Máy tính của bạn tìm địa chỉ IP của Google thông qua DNS.
2. Máy tính gửi yêu cầu đến địa chỉ IP đó.
3. Dữ liệu đi qua nhiều thiết bị mạng.
4. Máy chủ Google nhận yêu cầu.
5. Máy chủ trả kết quả về trình duyệt.

Đây chính là cách Internet giúp các máy tính giao tiếp với nhau.

**Ghi nhớ:**

Mạng máy tính là nền tảng để các dịch vụ AWS như EC2, RDS, S3 và Lambda có thể giao tiếp với nhau.

---

## 8.2. IP Address, Port và Protocol là gì?

Trước khi xây dựng mạng riêng, chúng ta cần hiểu ba khái niệm quan trọng.

### 1. IP Address – Địa chỉ IP

IP giống như địa chỉ nhà của một chiếc máy tính.

Ví dụ:

`192.168.1.10`

Địa chỉ này giúp các thiết bị xác định nơi gửi và nhận dữ liệu.

Có hai phiên bản IP phổ biến:

**IPv4:**

Ví dụ:

`192.168.1.10`

**IPv6:**

Ví dụ:

`2001:db8:1234:5678::1`

IPv4 sử dụng 32 bit, còn IPv6 sử dụng 128 bit.

IPv6 được tạo ra để cung cấp số lượng địa chỉ lớn hơn rất nhiều so với IPv4.

### 2. Port – Cổng giao tiếp

Nếu IP là địa chỉ của một ngôi nhà thì Port giống như các cửa khác nhau trong ngôi nhà đó.

Ví dụ:

| Port | Công dụng |
|---|---|
| 22 | SSH – Truy cập máy chủ Linux |
| 80 | HTTP – Website không mã hóa |
| 443 | HTTPS – Website mã hóa |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |

Ví dụ, máy chủ có địa chỉ:

`10.0.1.10`

Nếu muốn kết nối MySQL, chúng ta có thể truy cập:

`10.0.1.10:3306`

### 3. Protocol – Giao thức

Protocol là những quy tắc giúp các thiết bị hiểu nhau.

Một số giao thức phổ biến:

- TCP: Đảm bảo dữ liệu được truyền theo cơ chế kết nối đáng tin cậy.
- UDP: Truyền dữ liệu không cần thiết lập kết nối như TCP.
- HTTP: Giao thức truyền tải dữ liệu website.
- HTTPS: HTTP được bảo vệ bằng TLS.

### Ví dụ dễ hiểu

An muốn gửi một gói hàng cho Bin.

- IP: Địa chỉ nhà của Bin.
- Port: Cửa nhận hàng.
- Protocol: Quy định cách gửi và nhận hàng.

Ba yếu tố này giúp các máy tính giao tiếp chính xác với nhau.

---

## 8.3. Public Network và Private Network

Cô Mây đưa An và Bin đến thăm hai khu vực trong thành phố.

Khu vực đầu tiên là quảng trường công cộng.

Ai cũng có thể đến đây.

Khu vực thứ hai là khu nhà riêng của cư dân. Chỉ những người được phép mới có thể vào.

Đây là cách chúng ta hình dung hai loại mạng:

### Public Network – Mạng công cộng

Đây là mạng có khả năng giao tiếp với các hệ thống bên ngoài, chẳng hạn Internet.

Ví dụ:

- Website công khai.
- Máy chủ web phục vụ khách hàng.
- API mà người dùng Internet cần truy cập.

### Private Network – Mạng riêng

Đây là mạng được thiết kế để hạn chế khả năng truy cập từ bên ngoài.

Ví dụ:

- Máy chủ cơ sở dữ liệu.
- Máy chủ xử lý nghiệp vụ.
- Máy chủ lưu trữ thông tin nội bộ.

### So sánh

| Tiêu chí | Public Network | Private Network |
|---|---|---|
| Khả năng truy cập | Có thể kết nối Internet | Hạn chế truy cập từ Internet |
| Mục đích | Cung cấp dịch vụ công khai | Chạy hệ thống nội bộ |
| Ví dụ | Web Server | Database Server |
| Bảo mật | Cần kiểm soát truy cập chặt chẽ | Giảm khả năng bị truy cập trực tiếp từ bên ngoài |

**Một điều quan trọng:**

Public không có nghĩa là mọi người đều được phép truy cập.

Ngay cả khi một máy chủ nằm trong mạng công cộng, chúng ta vẫn cần cấu hình địa chỉ IP, Security Group và các quy tắc mạng phù hợp.

---

# PHẦN 2 – AMAZON VPC LÀ GÌ?

## 8.4. Amazon VPC – Thành phố riêng của bạn trên AWS

<details>
</details>

Cô Mây nói:

"An và Bin đã hiểu mạng máy tính là gì. Bây giờ chúng ta hãy tưởng tượng AWS cung cấp cho mỗi người một khu đất riêng để xây dựng thành phố công nghệ."

Khu đất đó chính là Amazon VPC.

**Amazon VPC (Virtual Private Cloud)** là dịch vụ cho phép bạn tạo một mạng máy tính riêng, được cô lập về mặt logic trong AWS.

Trong VPC, bạn có thể:

- Tạo các mạng con (Subnet).
- Phân chia địa chỉ IP.
- Xây dựng các tuyến đường mạng.
- Kết nối Internet.
- Kiểm soát quyền truy cập.
- Kết nối nhiều VPC với nhau.
- Kết nối các dịch vụ AWS.

### Hình dung VPC

Hãy tưởng tượng:

```text
                 AWS CLOUD
                     |
          +----------------------+
          |       MY VPC         |
          |                      |
          |  +----------------+  |
          |  | Public Subnet  |  |
          |  |                |  |
          |  |   Web Server   |  |
          |  +----------------+  |
          |                      |
          |  +----------------+  |
          |  | Private Subnet |  |
          |  |                |  |
          |  |  App Server    |  |
          |  |                |  |
          |  |  Database      |  |
          |  +----------------+  |
          |                      |
          +----------------------+
```

Đây là một mạng riêng do bạn quản lý.

### VPC có những đặc điểm gì?

**1. Logical Isolation – Cô lập về mặt logic**

VPC của bạn được tách biệt về mặt logic với các VPC khác.

**2. Tùy chỉnh mạng**

Bạn có thể tự quyết định dải địa chỉ IP, subnet và các quy tắc kết nối.

**3. Kiểm soát truy cập**

Bạn có thể quyết định máy chủ nào được phép giao tiếp với máy chủ nào.

**4. Phạm vi hoạt động**

Một VPC thuộc một AWS Region và có thể trải rộng trên nhiều Availability Zone trong Region đó.

Ví dụ:

Bạn tạo VPC tại Singapore Region (`ap-southeast-1`).

Bạn có thể tạo Subnet ở:

- Availability Zone A.
- Availability Zone B.
- Availability Zone C.

Điều này giúp ứng dụng có thể hoạt động ở nhiều khu vực hạ tầng khác nhau.

### Khi nào cần VPC?

Hầu hết các ứng dụng chạy trên AWS đều cần một mạng để các thành phần giao tiếp.

Ví dụ, một website thương mại điện tử có thể sử dụng:

- EC2 chạy Backend.
- RDS chạy MySQL.
- ElastiCache chạy Redis.
- Load Balancer tiếp nhận yêu cầu.

VPC giúp chúng kết nối với nhau một cách có kiểm soát.

**Ghi nhớ:**

VPC giống như một thành phố riêng. Bạn là người quyết định thành phố có những khu vực nào và các khu vực được phép giao tiếp với nhau ra sao.

---

## 8.5. CIDR – Cách chia địa chỉ IP

An hỏi:

"Nếu chúng ta xây một thành phố thì làm thế nào để biết thành phố có bao nhiêu địa chỉ nhà?"

Cô Mây trả lời:

"Chúng ta cần một bản đồ địa chỉ IP. Trong AWS, bản đồ đó thường được biểu diễn bằng CIDR."

### CIDR là gì?

CIDR là viết tắt của:

**Classless Inter-Domain Routing**

Đây là cách biểu diễn một dải địa chỉ IP.

Ví dụ:

`10.0.0.0/16`

Chúng ta có thể hiểu:

- `10.0.0.0`: Địa chỉ bắt đầu của dải mạng.
- `/16`: Số bit được sử dụng để xác định phần mạng.

Với IPv4, địa chỉ có 32 bit.

Nếu sử dụng `/16`, chúng ta còn 16 bit để biểu diễn các địa chỉ bên trong dải mạng.

Số lượng địa chỉ lý thuyết:

`2^(32 - 16) = 65.536 địa chỉ`

### Một số CIDR phổ biến

| CIDR | Tổng số địa chỉ IPv4 |
|---|---:|
| /16 | 65.536 |
| /20 | 4.096 |
| /24 | 256 |
| /25 | 128 |
| /26 | 64 |
| /28 | 16 |

### Ví dụ chia mạng

Giả sử An tạo VPC:

`10.0.0.0/16`

Sau đó An chia thành các khu vực:

```text
VPC: 10.0.0.0/16

    +---------------------------+
    |      10.0.0.0/16          |
    |                           |
    |  +---------------------+  |
    |  | 10.0.1.0/24         |  |
    |  +---------------------+  |
    |                           |
    |  +---------------------+  |
    |  | 10.0.2.0/24         |  |
    |  +---------------------+  |
    |                           |
    |  +---------------------+  |
    |  | 10.0.3.0/24         |  |
    |  +---------------------+  |
    +---------------------------+
```

Mỗi `/24` có 256 địa chỉ lý thuyết.

Tuy nhiên, AWS giữ lại 5 địa chỉ IPv4 trong mỗi subnet.

Vì vậy, subnet `/24` có thể sử dụng tối đa 251 địa chỉ cho tài nguyên thông thường.

### Những lưu ý khi chọn CIDR

1. Không nên chọn dải IP quá nhỏ nếu dự án có thể mở rộng.
2. Các subnet trong cùng VPC không được có dải CIDR chồng lấn.
3. Nếu dự định kết nối nhiều VPC, cần tránh các dải CIDR bị trùng nhau.
4. Cần tính toán trước khi kết nối VPC với mạng nội bộ của công ty.

### IPv4 và IPv6 trong VPC

AWS hỗ trợ:

- IPv4 only: Chỉ sử dụng IPv4.
- Dual-stack: Sử dụng cả IPv4 và IPv6.

IPv6 có không gian địa chỉ rất lớn.

Một điểm cần nhớ: Địa chỉ IPv6 Global Unicast có thể được định tuyến trên Internet. Vì vậy, không nên nghĩ rằng IPv6 tự động an toàn chỉ vì máy chủ nằm trong Private Subnet.

**Ghi nhớ:**

CIDR giống như cách chúng ta đánh số các căn nhà trong thành phố. Nếu chia mạng không hợp lý, sau này việc mở rộng và kết nối sẽ trở nên khó khăn.

---

## 8.6. Subnet – Chia thành phố thành nhiều khu vực

Một thành phố lớn thường có nhiều khu vực:

- Khu thương mại.
- Khu dân cư.
- Khu công nghiệp.
- Khu hành chính.

Trong VPC cũng vậy.

Chúng ta có thể chia một VPC thành nhiều Subnet.

**Subnet (Subnetwork)** là một dải địa chỉ IP nằm bên trong VPC.

Mỗi Subnet thuộc về một Availability Zone duy nhất.

### Ví dụ

VPC:

`10.0.0.0/16`

Chúng ta tạo ba Subnet:

| Subnet | CIDR | Availability Zone |
|---|---|---|
| Subnet A | 10.0.1.0/24 | ap-southeast-1a |
| Subnet B | 10.0.2.0/24 | ap-southeast-1b |
| Subnet C | 10.0.3.0/24 | ap-southeast-1c |

Mỗi Subnet nằm trong một Availability Zone.

### Tại sao cần nhiều Subnet?

Có ba lý do quan trọng:

**1. Phân chia hệ thống**

Bạn có thể đặt Web Server ở một Subnet và Database Server ở một Subnet khác.

**2. Bảo mật**

Bạn có thể thiết lập các quy tắc truy cập khác nhau cho từng Subnet.

**3. High Availability**

Bạn có thể triển khai hệ thống trên nhiều Availability Zone để giảm ảnh hưởng khi một AZ gặp sự cố.

### Hình dung

```text
             MY VPC
                |
       +--------+--------+
       |        |        |
      AZ-A     AZ-B     AZ-C
       |        |        |
     Subnet   Subnet   Subnet
       A        B        C
```

**Ghi nhớ:**

VPC giống như một thành phố. Subnet giống như những khu vực bên trong thành phố. Một Subnet không thể trải dài qua nhiều Availability Zone.

---

## 8.7. Public Subnet và Private Subnet

Đây là một trong những kiến thức quan trọng nhất của VPC.

Cô Mây hỏi:

"Nếu chúng ta có một cửa hàng và một kho hàng, em muốn khách hàng đi vào cả hai nơi không?"

An trả lời:

"Không ạ! Khách chỉ nên vào cửa hàng. Kho hàng phải được bảo vệ."

Đó chính là cách chúng ta sử dụng Public Subnet và Private Subnet.

### Public Subnet

Public Subnet là Subnet có Route Table chứa tuyến đường trực tiếp đến Internet Gateway.

Ví dụ:

```text
0.0.0.0/0 -> Internet Gateway
```

Các tài nguyên trong Public Subnet có thể giao tiếp với Internet nếu đáp ứng các điều kiện cần thiết như có địa chỉ IP phù hợp và quy tắc bảo mật cho phép.

Ví dụ:

- Public Load Balancer.
- Web Server cần truy cập trực tiếp từ Internet.
- NAT Gateway.

### Private Subnet

Private Subnet là Subnet không có tuyến đường trực tiếp đến Internet Gateway.

Ví dụ:

- Application Server.
- Database Server.
- Redis Server.
- Các dịch vụ xử lý nội bộ.

Private Subnet vẫn có thể kết nối Internet theo chiều đi ra nếu được cấu hình NAT Gateway hoặc cơ chế kết nối phù hợp.

### So sánh

| Tiêu chí | Public Subnet | Private Subnet |
|---|---|---|
| Route trực tiếp đến IGW | Có | Không |
| Truy cập Internet | Có thể | Không trực tiếp |
| Địa chỉ IP công cộng | Có thể sử dụng | Không cần để vận hành nội bộ |
| Mục đích | Tài nguyên công khai | Tài nguyên nội bộ |
| Ví dụ | Load Balancer | Database |

### Một hiểu lầm thường gặp

Bin hỏi:

"Nếu em đặt EC2 vào Public Subnet thì bất kỳ ai trên Internet cũng truy cập được đúng không?"

Cô Mây trả lời:

"Không hẳn!"

Để một EC2 sử dụng IPv4 giao tiếp trực tiếp với Internet thông qua Internet Gateway, cần có:

1. Subnet có tuyến đường đến Internet Gateway.
2. EC2 có Public IPv4 hoặc Elastic IP.
3. Security Group cho phép kết nối cần thiết.
4. Network ACL không chặn kết nối.
5. Ứng dụng đang lắng nghe trên Port tương ứng.

Thiếu một trong những điều kiện cần thiết, kết nối có thể không hoạt động.

**Ghi nhớ:**

Public Subnet không có nghĩa là mọi tài nguyên trong đó đều được công khai. Nó chỉ nói rằng Subnet có đường đi trực tiếp đến Internet Gateway.

---

# PHẦN 3 – ĐIỀU KHIỂN ĐƯỜNG ĐI CỦA DỮ LIỆU

## 8.8. Route Table – Bản đồ chỉ đường

Hãy tưởng tượng An đang lái xe trong thành phố.

An cần biết:

- Muốn đến trường thì đi đường nào?
- Muốn đến siêu thị thì rẽ ở đâu?
- Muốn ra khỏi thành phố thì đi qua cổng nào?

Máy tính cũng cần một bản đồ như vậy.

Trong AWS, chúng ta gọi nó là **Route Table**.

### Route Table là gì?

Route Table chứa những quy tắc xác định dữ liệu mạng sẽ được gửi đến đâu.

Mỗi quy tắc có hai thành phần chính:

- Destination: Đích đến của dữ liệu.
- Target: Nơi dữ liệu sẽ được gửi tiếp.

### Ví dụ

| Destination | Target |
|---|---|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | igw-123456 |

Giải thích:

**Dòng 1:**

`10.0.0.0/16 -> local`

Nếu dữ liệu muốn đi đến một địa chỉ nằm trong VPC, AWS sẽ định tuyến dữ liệu bên trong VPC.

**Dòng 2:**

`0.0.0.0/0 -> igw-123456`

Nếu dữ liệu muốn đi đến một địa chỉ IPv4 không được khớp bởi tuyến đường cụ thể hơn, nó sẽ được gửi đến Internet Gateway.

### Một số Target thường gặp

| Target | Công dụng |
|---|---|
| local | Kết nối nội bộ trong VPC |
| igw | Internet Gateway |
| nat | NAT Gateway |
| pcx | VPC Peering |
| vpce | VPC Endpoint |
| tgw | Transit Gateway |

### Main Route Table

Khi tạo VPC, AWS tự động tạo một Main Route Table.

Nếu một Subnet chưa được liên kết với Route Table khác, nó sẽ sử dụng Main Route Table của VPC.

Bạn cũng có thể tạo nhiều Route Table khác nhau để quản lý các Subnet theo nhu cầu.

### Ví dụ thực tế

Public Subnet:

```text
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

Private Subnet:

```text
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

Hai Subnet có đường đi khác nhau.

**Ghi nhớ:**

Route Table giống như bản đồ giao thông. Nó quyết định dữ liệu phải đi qua con đường nào để đến đúng nơi.

---

## 8.9. Internet Gateway (IGW) – Cổng kết nối Internet

Cô Mây chỉ vào cổng chính của thành phố.

"Đây là nơi giúp thành phố của chúng ta kết nối với thế giới bên ngoài."

Trong AWS, đó chính là Internet Gateway.

**Internet Gateway (IGW)** là thành phần giúp VPC giao tiếp với Internet.

### IGW hoạt động như thế nào?

Để EC2 có thể kết nối Internet thông qua IGW bằng IPv4, chúng ta cần:

1. Tạo Internet Gateway.
2. Attach Internet Gateway vào VPC.
3. Tạo Route Table có tuyến đường đến IGW.
4. Liên kết Route Table với Public Subnet.
5. Cấp Public IPv4 hoặc Elastic IP cho EC2.
6. Cấu hình Security Group cho phép kết nối phù hợp.

### Hình minh họa

```text
             INTERNET
                 |
                 |
        +----------------+
        | Internet       |
        | Gateway        |
        +----------------+
                 |
                 |
        +----------------+
        |      VPC       |
        |                |
        |  Public Subnet |
        |                |
        |   EC2 Server   |
        +----------------+
```

### IGW có phải NAT Gateway không?

Không!

Internet Gateway cho phép tài nguyên trong Public Subnet giao tiếp với Internet theo các quy tắc định tuyến và bảo mật.

NAT Gateway có nhiệm vụ khác: giúp tài nguyên trong Private Subnet chủ động kết nối ra bên ngoài mà không cần công khai địa chỉ IPv4 của chính tài nguyên đó.

### Lưu ý

- IGW phải được Attach vào VPC.
- Một VPC có thể sử dụng Internet Gateway để kết nối Internet.
- IGW hỗ trợ IPv4 và IPv6.
- Chỉ Attach IGW không có nghĩa là mọi EC2 trong VPC đều có thể truy cập Internet.

**Ghi nhớ:**

Internet Gateway giống như cổng chính kết nối thành phố với thế giới bên ngoài.

---

## 8.10. NAT Gateway – Cổng ra Internet dành cho mạng riêng

An hỏi:

"Nếu Database nằm trong Private Subnet thì làm sao nó tải được các bản cập nhật phần mềm từ Internet?"

Cô Mây trả lời:

"Chúng ta có thể sử dụng NAT Gateway."

### NAT là gì?

NAT là viết tắt của:

**Network Address Translation**

NAT giúp chuyển đổi địa chỉ IP nguồn khi dữ liệu đi qua một thiết bị trung gian.

Trong AWS, NAT Gateway là dịch vụ được quản lý giúp tài nguyên trong Private Subnet chủ động kết nối đến Internet.

### Ví dụ đời thường

Hãy tưởng tượng trong một công ty có nhiều nhân viên.

Nhân viên muốn gửi thư ra ngoài nhưng không muốn công khai địa chỉ cá nhân.

Họ gửi thư đến quầy tiếp nhận của công ty.

Quầy tiếp nhận sẽ thay mặt họ gửi thư ra ngoài.

Khi nhận được thư trả lời, quầy tiếp nhận chuyển lại cho đúng người.

NAT Gateway hoạt động theo ý tưởng tương tự.

### Kiến trúc NAT Gateway

```text
              INTERNET
                  |
                  |
          Internet Gateway
                  |
                  |
          +---------------+
          | Public Subnet |
          |               |
          |  NAT Gateway  |
          +---------------+
                  |
                  |
          +---------------+
          | Private       |
          | Subnet        |
          |               |
          | App Server    |
          +---------------+
```

### NAT Gateway hoạt động như thế nào?

1. EC2 trong Private Subnet muốn tải một gói phần mềm từ Internet.
2. EC2 gửi yêu cầu đến NAT Gateway.
3. NAT Gateway thay đổi địa chỉ nguồn IPv4.
4. Dữ liệu được chuyển qua Internet Gateway.
5. Máy chủ bên ngoài trả lời.
6. NAT Gateway chuyển phản hồi về EC2 ban đầu.

Điều quan trọng là Internet không thể tự ý khởi tạo một kết nối mới đến EC2 trong Private Subnet thông qua NAT Gateway.

### Public NAT Gateway

Một Public NAT Gateway thường được triển khai trong Public Subnet và sử dụng Elastic IP.

Route Table của Private Subnet có thể cấu hình:

```text
0.0.0.0/0 -> NAT Gateway
```

Route Table của Public Subnet chứa:

```text
0.0.0.0/0 -> Internet Gateway
```

### Khi nào cần NAT Gateway?

Ví dụ:

- Application Server cần tải thư viện.
- EC2 cần cập nhật hệ điều hành.
- Server cần gọi API bên ngoài.
- Máy chủ nội bộ cần kết nối dịch vụ Internet.

### NAT Gateway có miễn phí không?

Không nên nhầm NAT Gateway với Internet Gateway.

NAT Gateway có thể phát sinh chi phí theo thời gian sử dụng và lượng dữ liệu xử lý.

Trong môi trường Production, cần cân nhắc:

- NAT Gateway theo từng Availability Zone.
- Lượng dữ liệu đi qua NAT.
- VPC Endpoint để kết nối một số AWS Services mà không cần đi qua NAT.

**Ghi nhớ:**

Internet Gateway là cổng kết nối Internet của VPC.

NAT Gateway là thiết bị trung gian giúp các máy chủ trong Private Subnet chủ động kết nối ra ngoài.

---

# PHẦN 4 – BẢO VỆ MẠNG TRONG VPC

## 8.11. Security Group – Người bảo vệ từng máy chủ

An và Bin xây dựng được một thành phố rất đẹp.

Nhưng cô Mây nhắc nhở:

"Chúng ta không thể để bất kỳ ai muốn vào máy chủ nào cũng được."

Vì vậy, AWS cung cấp Security Group.

### Security Group là gì?

Security Group là một hệ thống tường lửa ảo dùng để kiểm soát lưu lượng mạng đi vào và đi ra khỏi tài nguyên được gắn với nó.

Ví dụ:

- EC2.
- RDS.
- Một số Network Interface.
- Load Balancer.

Security Group hoạt động ở cấp tài nguyên hoặc Network Interface, không phải ở cấp toàn bộ Subnet.

### Có hai loại quy tắc

**Inbound Rules**

Kiểm soát dữ liệu đi vào.

Ví dụ:

| Type | Port | Source |
|---|---:|---|
| HTTPS | 443 | 0.0.0.0/0 |
| SSH | 22 | IP của quản trị viên |
| MySQL | 3306 | Security Group của App |

**Outbound Rules**

Kiểm soát dữ liệu đi ra.

Ví dụ:

| Type | Port | Destination |
|---|---:|---|
| HTTPS | 443 | 0.0.0.0/0 |
| MySQL | 3306 | Security Group của Database |

### Ví dụ thực tế

Một hệ thống có:

- Web Server.
- Application Server.
- Database Server.

Chúng ta có thể tạo ba Security Group:

**SG-Web**

Cho phép người dùng Internet truy cập HTTPS trên Port 443.

**SG-App**

Chỉ cho phép Web Server kết nối đến Application Server trên Port 3000.

**SG-Database**

Chỉ cho phép Application Server kết nối MySQL trên Port 3306.

Như vậy, người dùng Internet không thể trực tiếp kết nối Database.

### Sơ đồ

```text
          INTERNET
              |
              |
          Port 443
              |
          +-------+
          |  WEB  |
          +-------+
              |
          Port 3000
              |
          +-------+
          |  APP  |
          +-------+
              |
          Port 3306
              |
          +-------+
          |  DB   |
          +-------+
```

### Một số đặc điểm quan trọng

1. Security Group chỉ có quy tắc Allow, không có quy tắc Deny trực tiếp.
2. Security Group có tính Stateful.
3. Có thể gắn nhiều Security Group vào một tài nguyên.
4. Các quy tắc của những Security Group được gắn vào tài nguyên sẽ được kết hợp để đánh giá quyền truy cập.

**Ghi nhớ:**

Security Group giống như người bảo vệ đứng trước từng căn phòng. Người bảo vệ quyết định ai được vào và dữ liệu nào được phép đi ra.

---

## 8.12. Network ACL – Người bảo vệ từng khu vực

Bin hỏi:

"Nếu Security Group đã bảo vệ từng máy chủ rồi thì tại sao chúng ta cần Network ACL?"

Cô Mây giải thích:

"Security Group bảo vệ từng tài nguyên. Network ACL giúp kiểm soát lưu lượng ở cấp Subnet."

### Network ACL là gì?

Network Access Control List (NACL) là một lớp bảo vệ mạng giúp kiểm soát lưu lượng Inbound và Outbound của Subnet.

### Ví dụ

Hãy tưởng tượng thành phố có một cổng kiểm soát chung.

Mọi người muốn đi vào một khu vực đều phải qua cổng này.

Đó là cách chúng ta hình dung Network ACL.

### NACL có hai loại quy tắc

- Inbound Rules: Quy tắc cho dữ liệu đi vào Subnet.
- Outbound Rules: Quy tắc cho dữ liệu đi ra Subnet.

Ví dụ:

| Rule Number | Type | Action |
|---:|---|---|
| 100 | TCP Port 443 | Allow |
| 110 | TCP Port 22 | Deny |
| * | All traffic | Deny |

NACL đánh giá các quy tắc theo thứ tự số tăng dần.

Khi một quy tắc khớp với lưu lượng đang xét, AWS áp dụng quy tắc đó và không tiếp tục kiểm tra các quy tắc phía sau.

### So sánh Security Group và NACL

| Tiêu chí | Security Group | Network ACL |
|---|---|---|
| Phạm vi | Tài nguyên / Network Interface | Subnet |
| Allow | Có | Có |
| Deny | Không có quy tắc Deny trực tiếp | Có |
| Stateful | Có | Không |
| Thứ tự đánh giá | Đánh giá tổng hợp các quy tắc | Theo số thứ tự |
| Mục đích | Bảo vệ tài nguyên | Bảo vệ cả khu vực mạng |

### Default NACL và Custom NACL

AWS tự động tạo Default NACL cho VPC.

Default NACL mặc định cho phép toàn bộ Inbound và Outbound traffic.

Nếu tạo Custom NACL mới, mặc định các quy tắc sẽ từ chối lưu lượng cho đến khi bạn thêm những quy tắc Allow phù hợp.

**Ghi nhớ:**

Security Group bảo vệ từng máy chủ.

Network ACL bảo vệ cả khu vực Subnet.

---

## 8.13. Stateful và Stateless là gì?

Đây là một kiến thức rất quan trọng khi học Security Group và Network ACL.

Cô Mây đưa ra một ví dụ.

An đi vào một tòa nhà và đăng ký tại quầy lễ tân.

Sau khi xác nhận danh tính, An được phép vào phòng họp.

Khi cuộc họp kết thúc, An đi ra ngoài mà không cần đăng ký lại từ đầu.

Đây là cách hình dung một hệ thống Stateful.

### Stateful – Có ghi nhớ trạng thái

Security Group là Stateful.

Điều này có nghĩa là khi một kết nối được cho phép, lưu lượng phản hồi tương ứng cũng được cho phép tự động.

Ví dụ:

1. EC2 gửi yêu cầu đến một máy chủ bên ngoài.
2. Security Group cho phép yêu cầu đi ra.
3. Máy chủ bên ngoài trả lời.
4. Security Group cho phép phản hồi của kết nối đó quay về.

Không cần tạo một quy tắc riêng chỉ để cho phép phản hồi của kết nối đã được chấp nhận.

### Stateless – Không ghi nhớ trạng thái

Network ACL là Stateless.

Nó không tự động ghi nhớ rằng một gói tin đã được cho phép trước đó.

Ví dụ:

1. Bạn tạo quy tắc cho phép kết nối HTTPS đi vào Subnet.
2. Máy chủ nhận yêu cầu.
3. Máy chủ gửi phản hồi ra ngoài.
4. NACL vẫn kiểm tra chiều Outbound theo những quy tắc riêng.

Nếu không có quy tắc cho phép phản hồi phù hợp, dữ liệu có thể bị chặn.

### So sánh

| Tiêu chí | Stateful | Stateless |
|---|---|---|
| Ghi nhớ kết nối | Có | Không |
| Phản hồi kết nối | Tự động được cho phép | Phải kiểm tra quy tắc riêng |
| AWS | Security Group | Network ACL |

**Mẹo ghi nhớ:**

- Security Group = Stateful.
- Network ACL = Stateless.

Đây là kiến thức thường gặp trong các câu hỏi về AWS Networking.

---

# PHẦN 5 – KẾT NỐI CÁC DỊCH VỤ AWS

## 8.14. VPC Endpoint – Cánh cửa kết nối riêng đến AWS Services

An hỏi:

"Nếu Application Server nằm trong Private Subnet và muốn truy cập Amazon S3 thì có bắt buộc phải đi qua Internet Gateway không?"

Cô Mây trả lời:

"Không! AWS có một cách kết nối riêng gọi là VPC Endpoint."

### VPC Endpoint là gì?

VPC Endpoint giúp tài nguyên trong VPC kết nối với các dịch vụ được hỗ trợ thông qua mạng riêng của AWS mà không cần sử dụng Internet Gateway hoặc NAT Gateway cho đường kết nối đó.

Ví dụ:

- EC2 trong Private Subnet truy cập Amazon S3.
- EC2 truy cập DynamoDB.
- Application Server kết nối với các dịch vụ AWS được hỗ trợ thông qua PrivateLink.

### Có hai loại Endpoint cơ bản cần nhớ

**1. Gateway Endpoint**

Hỗ trợ:

- Amazon S3.
- Amazon DynamoDB.

Gateway Endpoint sử dụng Route Table để định tuyến lưu lượng đến dịch vụ tương ứng.

Một ưu điểm đáng chú ý là Gateway Endpoint không tính thêm phí sử dụng Endpoint.

**2. Interface Endpoint**

Sử dụng AWS PrivateLink.

AWS tạo các Network Interface có địa chỉ IP riêng trong Subnet bạn lựa chọn.

Nhiều AWS Services hỗ trợ Interface Endpoint.

Loại này có thể phát sinh chi phí theo thời gian sử dụng và dữ liệu xử lý.

### Hình dung

Không có VPC Endpoint:

```text
Private EC2
     |
     v
 NAT Gateway
     |
     v
   Internet
     |
     v
     S3
```

Có Gateway Endpoint:

```text
Private EC2
     |
     v
VPC Endpoint
     |
     v
    S3
```

### Khi nào cần VPC Endpoint?

Ví dụ:

Một ứng dụng có EC2 chạy trong Private Subnet.

Ứng dụng cần đọc file từ S3.

Thay vì đưa lưu lượng qua NAT Gateway, bạn có thể tạo Gateway Endpoint cho S3.

Điều này giúp thiết lập kết nối phù hợp hơn và tránh phải sử dụng NAT Gateway cho luồng truy cập S3 đó.

**Lưu ý:**

VPC Endpoint không tự động cấp quyền truy cập dữ liệu.

Bạn vẫn cần kiểm tra IAM Policy, Bucket Policy, Endpoint Policy và các quy tắc mạng có liên quan.

**Ghi nhớ:**

VPC Endpoint giống như một cánh cửa riêng giúp thành phố kết nối trực tiếp với một số dịch vụ AWS mà không cần đi ra Internet.

---

## 8.15. VPC Peering – Kết nối hai thành phố riêng

Giả sử An có một thành phố tên là VPC-A.

Bin có một thành phố tên là VPC-B.

Hai thành phố đều có những máy chủ và dữ liệu riêng.

Một ngày nọ, An muốn Application Server trong VPC-A kết nối với Database Server trong VPC-B.

Làm thế nào?

Cô Mây giới thiệu VPC Peering.

### VPC Peering là gì?

VPC Peering là kết nối mạng giữa hai VPC, cho phép tài nguyên trong hai VPC giao tiếp với nhau bằng địa chỉ IP riêng.

### Ví dụ

```text
       VPC-A                         VPC-B
+------------------+          +------------------+
|                  |          |                  |
|   EC2 Server     |          |   Database       |
|                  |          |                  |
+------------------+          +------------------+
         |                              |
         +-----------+  +---------------+
                     |  |
                 VPC Peering
```

### Các bước thiết lập cơ bản

1. Tạo yêu cầu kết nối Peering từ VPC-A.
2. VPC-B chấp nhận yêu cầu.
3. Thêm Route Table cho VPC-A để đi đến dải IP của VPC-B.
4. Thêm Route Table cho VPC-B để đi đến dải IP của VPC-A.
5. Cấu hình Security Group và NACL nếu cần thiết.

### Một số giới hạn quan trọng

**1. CIDR không được chồng lấn**

Hai VPC không thể thiết lập Peering nếu các dải CIDR của chúng bị chồng lấn.

Ví dụ:

```text
VPC-A: 10.0.0.0/16
VPC-B: 10.0.0.0/16
```

Hai VPC sử dụng cùng dải mạng nên không thể thiết lập Peering theo cách thông thường.

**2. Không hỗ trợ Transitive Peering**

Đây là điều rất quan trọng.

Giả sử:

```text
VPC-A ----- VPC-B
   |
   |
 VPC-C
```

VPC-A đã kết nối với VPC-B và VPC-C.

Nhưng VPC-B không tự động giao tiếp được với VPC-C thông qua VPC-A.

Muốn B giao tiếp với C, cần tạo kết nối phù hợp giữa chúng hoặc sử dụng giải pháp kết nối trung tâm như Transit Gateway.

### Khi nào sử dụng VPC Peering?

- Kết nối hai hệ thống AWS độc lập.
- Kết nối ứng dụng với dịch vụ dùng chung.
- Kết nối VPC giữa hai AWS Account.
- Kết nối VPC giữa hai Region.

**Ghi nhớ:**

VPC Peering giống như xây một cây cầu riêng giữa hai thành phố.

---

## 8.16. VPC Flow Logs – Camera giám sát lưu lượng mạng

Một ngày nọ, An phát hiện Application Server không thể kết nối với Database.

An kiểm tra code nhưng không tìm thấy lỗi.

Bin kiểm tra Database nhưng cũng không có vấn đề.

Cô Mây hỏi:

"Vậy làm sao chúng ta biết dữ liệu mạng đang bị chặn ở đâu?"

Đó là lúc VPC Flow Logs phát huy tác dụng.

### VPC Flow Logs là gì?

VPC Flow Logs là tính năng ghi lại thông tin về lưu lượng IP đi đến và đi ra khỏi các Network Interface trong VPC.

Bạn có thể bật Flow Logs cho:

- VPC.
- Subnet.
- Network Interface.

### Flow Logs ghi lại những gì?

Ví dụ:

- Địa chỉ IP nguồn.
- Địa chỉ IP đích.
- Source Port.
- Destination Port.
- Protocol.
- Số lượng gói tin.
- Số lượng byte.
- Kết quả ACCEPT hoặc REJECT.

### Ví dụ

Giả sử Application Server muốn kết nối MySQL:

```text
Source IP:      10.0.11.10
Destination IP: 10.0.21.10
Destination Port: 3306
Protocol:       TCP
Action:         REJECT
```

Điều này cho thấy lưu lượng kết nối đã bị từ chối tại lớp kiểm soát mạng được ghi nhận.

Bạn có thể tiếp tục kiểm tra Security Group, NACL và các thành phần liên quan.

### Flow Logs lưu dữ liệu ở đâu?

AWS hỗ trợ các đích lưu trữ phổ biến:

- Amazon CloudWatch Logs.
- Amazon S3.
- Amazon Data Firehose.

### Flow Logs có phải Packet Capture không?

Không.

Flow Logs ghi lại thông tin về luồng lưu lượng mạng, không phải công cụ ghi toàn bộ nội dung của từng gói tin.

### Khi nào nên sử dụng?

- Kiểm tra vì sao EC2 không kết nối được Database.
- Phân tích lưu lượng mạng.
- Điều tra các kết nối bị từ chối.
- Hỗ trợ giám sát và bảo mật hệ thống.

**Ghi nhớ:**

VPC Flow Logs giống như camera ghi nhận hoạt động giao thông. Nó giúp chúng ta biết dữ liệu đi từ đâu đến đâu và có được cho phép hay không.

---

## 8.17. Bastion Host và Systems Manager Session Manager

Cô Mây hỏi:

"Nếu Database và Application Server nằm trong Private Subnet thì quản trị viên làm thế nào để truy cập chúng?"

An trả lời:

"Chúng ta cần một con đường quản trị riêng!"

Có hai khái niệm quan trọng.

### 1. Bastion Host – Máy chủ trung gian

Bastion Host là một máy chủ được sử dụng làm điểm trung gian để quản trị các máy chủ nằm trong mạng riêng.

Ví dụ:

```text
         ADMIN
            |
            | SSH
            v
     +-------------+
     |  Bastion    |
     |   Host      |
     +-------------+
            |
            | SSH
            v
     +-------------+
     | Private EC2 |
     +-------------+
```

Thay vì mở SSH trực tiếp đến mọi máy chủ, quản trị viên kết nối đến Bastion Host trước.

Sau đó, từ Bastion Host kết nối đến máy chủ nội bộ.

### 2. AWS Systems Manager Session Manager

AWS cung cấp một giải pháp quản trị hiện đại hơn là Session Manager.

Session Manager thuộc AWS Systems Manager.

Nó cho phép quản trị viên kết nối đến EC2 thông qua AWS Console hoặc AWS CLI mà không cần mở cổng SSH/RDP Inbound trên máy chủ.

### Session Manager hoạt động như thế nào?

```text
       ADMIN
          |
          |
     AWS Console
          |
          v
  Systems Manager
          |
          |
          v
     Private EC2
```

Để sử dụng, EC2 cần:

1. Được quản lý bởi Systems Manager.
2. Có SSM Agent hoạt động.
3. Có IAM Role với quyền cần thiết, thường sử dụng policy AmazonSSMManagedInstanceCore.
4. Có kết nối mạng đến các dịch vụ Systems Manager, thông qua NAT Gateway hoặc VPC Interface Endpoints phù hợp.
5. Người quản trị có IAM Permission để bắt đầu phiên làm việc.

### So sánh

| Tiêu chí | Bastion Host | Session Manager |
|---|---|---|
| Máy chủ trung gian | Có | Không cần |
| Mở SSH Inbound | Thường cần cho Bastion | Không cần |
| Quản lý bằng IAM | Có thể kết hợp | Tích hợp trực tiếp |
| Quản lý SSH Key | Thường cần | Không cần |
| Truy cập | SSH/RDP qua máy trung gian | AWS Console/CLI |

### Nên sử dụng cái nào?

Trong nhiều kiến trúc mới, Session Manager là lựa chọn đáng cân nhắc vì:

- Giảm nhu cầu mở cổng SSH.
- Không cần duy trì Bastion Host.
- Quản lý quyền thông qua IAM.
- Có thể ghi lại lịch sử phiên làm việc theo cấu hình phù hợp.

**Ghi nhớ:**

Bastion Host giống như một cổng kiểm soát trung gian.

Session Manager giống như một hệ thống quản trị từ xa được AWS quản lý.

---

# PHẦN 6 – THỰC HÀNH THIẾT KẾ VPC

## 8.18. Xây dựng mạng riêng cho một website thương mại điện tử

Bây giờ An và Bin đã hiểu những thành phần cơ bản.

Cô Mây giao cho hai bạn một thử thách:

"Hãy xây dựng một mạng AWS cho một website bán hàng trực tuyến."

Website cần:

- Người dùng truy cập website từ Internet.
- Application Server xử lý nghiệp vụ.
- Database lưu thông tin khách hàng và đơn hàng.
- Redis lưu dữ liệu cache.
- EC2 có thể tải các bản cập nhật cần thiết.
- Hệ thống có khả năng hoạt động trên nhiều Availability Zone.

### Bước 1: Thiết kế kiến trúc

Chúng ta sẽ sử dụng:

| Thành phần | AWS Service |
|---|---|
| Mạng riêng | VPC |
| Chia mạng | Subnet |
| Web entry point | Application Load Balancer |
| Backend | EC2 |
| Database | Amazon RDS |
| Cache | Amazon ElastiCache |
| Kết nối Internet | Internet Gateway |
| Kết nối Internet từ Private Subnet | NAT Gateway |
| Kết nối S3 riêng | VPC Endpoint |
| Bảo vệ máy chủ | Security Group |

### Bước 2: Chọn CIDR cho VPC

Chúng ta tạo VPC:

`10.20.0.0/16`

Sau đó chia thành các Subnet.

| Subnet | CIDR | AZ | Mục đích |
|---|---|---|---|
| Public-A | 10.20.1.0/24 | AZ-A | Load Balancer, NAT |
| Public-B | 10.20.2.0/24 | AZ-B | Load Balancer, NAT |
| App-A | 10.20.11.0/24 | AZ-A | Backend |
| App-B | 10.20.12.0/24 | AZ-B | Backend |
| DB-A | 10.20.21.0/24 | AZ-A | Database |
| DB-B | 10.20.22.0/24 | AZ-B | Database |

Tại sao cần hai Availability Zone?

Nếu một AZ gặp sự cố, chúng ta vẫn có thể duy trì tài nguyên ở AZ còn lại, tùy theo cách thiết kế và cấu hình High Availability của ứng dụng.

### Bước 3: Thiết kế kiến trúc mạng

```text
                       INTERNET
                           |
                           |
                    Internet Gateway
                           |
             +-------------+-------------+
             |                           |
         PUBLIC-A                    PUBLIC-B
             |                           |
       Load Balancer               Load Balancer
             |                           |
       NAT Gateway                 NAT Gateway
             |                           |
             |                           |
        APP-A                        APP-B
             |                           |
       Backend EC2                 Backend EC2
             |                           |
             +-------------+-------------+
                           |
                     PRIVATE DB
                           |
                     Amazon RDS
                           |
                     Redis Cache
```

Đây là mô hình minh họa đơn giản.

Trong kiến trúc thực tế:

- Load Balancer được triển khai ở các Public Subnet.
- Backend EC2 nằm trong Private App Subnet.
- Database và Redis nằm trong các Subnet riêng phù hợp.
- NAT Gateway được triển khai trong Public Subnet để hỗ trợ kết nối Internet đi ra từ App Subnet.
- Database không cần được mở trực tiếp ra Internet.

### Bước 4: Cấu hình Route Table

**Public Route Table**

```text
Destination       Target
--------------------------------
10.20.0.0/16      local
0.0.0.0/0         Internet Gateway
```

**Private App Route Table AZ-A**

```text
Destination       Target
--------------------------------
10.20.0.0/16      local
0.0.0.0/0         NAT Gateway AZ-A
```

**Private App Route Table AZ-B**

```text
Destination       Target
--------------------------------
10.20.0.0/16      local
0.0.0.0/0         NAT Gateway AZ-B
```

**Private DB Route Table**

```text
Destination       Target
--------------------------------
10.20.0.0/16      local
```

Database chỉ cần giao tiếp nội bộ theo kiến trúc này, không cần tuyến đường Internet mặc định.

### Bước 5: Cấu hình Security Group

**SG-LoadBalancer**

| Direction | Port | Source/Destination |
|---|---:|---|
| Inbound | 443 | 0.0.0.0/0 |
| Outbound | 3000 | SG-App |

**SG-App**

| Direction | Port | Source/Destination |
|---|---:|---|
| Inbound | 3000 | SG-LoadBalancer |
| Outbound | 3306 | SG-Database |

**SG-Database**

| Direction | Port | Source/Destination |
|---|---:|---|
| Inbound | 3306 | SG-App |

Như vậy:

- Người dùng Internet chỉ truy cập HTTPS thông qua Load Balancer.
- Backend chỉ nhận yêu cầu từ Load Balancer.
- Database chỉ nhận kết nối MySQL từ Backend.

Đây là cách phân chia quyền truy cập theo từng tầng ứng dụng.

### Bước 6: Thêm VPC Endpoint

Giả sử Backend cần đọc hình ảnh và tài liệu từ S3.

Chúng ta có thể tạo Gateway Endpoint cho S3.

Khi đó, EC2 trong Private Subnet có thể truy cập S3 thông qua đường kết nối phù hợp trong AWS mà không cần NAT Gateway cho lưu lượng S3 đó.

### Bước 7: Bảo vệ và giám sát

Cuối cùng, chúng ta có thể:

- Sử dụng Security Group để kiểm soát kết nối.
- Sử dụng Network ACL làm lớp bảo vệ bổ sung.
- Bật VPC Flow Logs.
- Sử dụng Systems Manager Session Manager để quản trị EC2.
- Kiểm tra Route Table và Resource Map.

### Bài học từ kiến trúc

Một ứng dụng tốt không chỉ cần code tốt.

Chúng ta còn cần:

- Mạng được thiết kế hợp lý.
- Các thành phần được phân chia rõ ràng.
- Quyền truy cập được giới hạn.
- Hệ thống có khả năng mở rộng.
- Có công cụ để kiểm tra và xử lý sự cố.

Đó chính là vai trò quan trọng của VPC.

---

# PHẦN 7 – THỰC HÀNH AWS CONSOLE VÀ CLI

## 8.19. Tạo VPC đầu tiên trên AWS Console

Bây giờ chúng ta thử tạo một VPC đơn giản.

### Bước 1: Mở VPC Console

Truy cập:

https://console.aws.amazon.com/vpc/

Chọn Region muốn sử dụng, ví dụ:

`Asia Pacific (Singapore) – ap-southeast-1`

### Bước 2: Tạo VPC

Chọn:

`Your VPCs → Create VPC`

Tạo một VPC với thông tin:

| Thuộc tính | Giá trị |
|---|---|
| Name | KidZone-VPC |
| IPv4 CIDR | 10.20.0.0/16 |
| IPv6 | Không bắt buộc cho bài thực hành đầu tiên |
| Tenancy | Default |

### Bước 3: Tạo Subnet

Tạo hai Subnet:

**Public Subnet**

```text
Name: KidZone-Public
CIDR: 10.20.1.0/24
```

**Private Subnet**

```text
Name: KidZone-Private
CIDR: 10.20.11.0/24
```

Chọn Availability Zone phù hợp.

### Bước 4: Tạo Internet Gateway

1. Chọn Internet Gateways.
2. Chọn Create Internet Gateway.
3. Đặt tên `KidZone-IGW`.
4. Attach vào `KidZone-VPC`.

### Bước 5: Tạo Public Route Table

1. Chọn Route Tables.
2. Tạo `KidZone-Public-RT`.
3. Chọn VPC tương ứng.
4. Thêm Route:

```text
Destination: 0.0.0.0/0
Target: Internet Gateway
```

5. Liên kết Route Table với Public Subnet.

### Bước 6: Kiểm tra Private Route Table

Private Subnet nên có tuyến đường nội bộ:

```text
10.20.0.0/16 -> local
```

Không thêm tuyến đường trực tiếp đến Internet Gateway.

Trong bài thực hành đầu tiên, chúng ta chưa cần tạo NAT Gateway để tránh phát sinh chi phí.

### Bước 7: Kiểm tra Resource Map

Trong VPC Console:

1. Chọn Your VPCs.
2. Chọn `KidZone-VPC`.
3. Mở tab Resource Map.

Bạn sẽ nhìn thấy các thành phần như:

- VPC.
- Subnet.
- Route Table.
- Internet Gateway.
- NAT Gateway nếu đã tạo.
- VPC Endpoint nếu đã tạo.

Đây là cách trực quan để kiểm tra kiến trúc mạng.

---

## 8.20. Kiểm tra VPC bằng AWS CLI

Nếu đã cài AWS CLI và cấu hình AWS Credentials, chúng ta có thể kiểm tra VPC trực tiếp từ Terminal.

### 1. Kiểm tra danh sách VPC

```bash
aws ec2 describe-vpcs \
  --region ap-southeast-1
```

### 2. Kiểm tra Subnet

```bash
aws ec2 describe-subnets \
  --region ap-southeast-1
```

### 3. Kiểm tra Route Table

```bash
aws ec2 describe-route-tables \
  --region ap-southeast-1
```

### 4. Kiểm tra Security Group

```bash
aws ec2 describe-security-groups \
  --region ap-southeast-1
```

### 5. Kiểm tra Internet Gateway

```bash
aws ec2 describe-internet-gateways \
  --region ap-southeast-1
```

### 6. Kiểm tra NAT Gateway

```bash
aws ec2 describe-nat-gateways \
  --region ap-southeast-1
```

Những lệnh trên chỉ đọc thông tin, không tạo hoặc xóa tài nguyên.

Nếu sử dụng AWS CLI Profile riêng, bạn có thể bổ sung:

```bash
--profile your-profile
```

---

## 8.21. Kiểm tra VPC bằng TypeScript AWS SDK

Là một lập trình viên, bạn cũng có thể sử dụng AWS SDK để quản lý và kiểm tra VPC bằng code.

Cài đặt thư viện:

```bash
npm install @aws-sdk/client-ec2
```

Tạo file `list-vpcs.ts`:

```typescript
import {
  EC2Client,
  DescribeVpcsCommand,
} from "@aws-sdk/client-ec2";

const client = new EC2Client({
  region: "ap-southeast-1",
});

async function listVpcs() {
  const result = await client.send(
    new DescribeVpcsCommand({})
  );

  for (const vpc of result.Vpcs ?? []) {
    console.log({
      vpcId: vpc.VpcId,
      cidr: vpc.CidrBlock,
      state: vpc.State,
    });
  }
}

listVpcs().catch(console.error);
```

Kết quả có thể tương tự:

```text
{
  vpcId: 'vpc-0123456789',
  cidr: '10.20.0.0/16',
  state: 'available'
}
```

### Điều cần lưu ý

AWS SDK sử dụng cơ chế Credential Provider Chain để tìm thông tin xác thực.

Không nên viết trực tiếp Access Key và Secret Key vào source code.

Tài khoản hoặc IAM Role sử dụng để chạy ví dụ cần có quyền phù hợp, chẳng hạn:

`ec2:DescribeVpcs`

Đây là một ví dụ nhỏ cho thấy bạn có thể quản lý hạ tầng AWS bằng TypeScript thay vì phải thao tác thủ công trên Console.

---

# PHẦN 8 – NHỮNG LỖI THƯỜNG GẶP KHI SỬ DỤNG VPC

## 8.22. Những sai lầm người mới thường mắc phải

### Lỗi 1: Tạo Internet Gateway nhưng EC2 vẫn không truy cập được Internet

Nguyên nhân có thể:

- Chưa Attach IGW vào VPC.
- Route Table chưa có tuyến đường đến IGW.
- EC2 không có Public IPv4.
- Security Group chặn kết nối.
- NACL chặn kết nối.

### Lỗi 2: Đặt Database trong Public Subnet

Đây là một thiết kế không nên sử dụng cho hệ thống thông thường.

Database nên được đặt trong Private Subnet và chỉ cho phép những ứng dụng cần thiết kết nối.

### Lỗi 3: Nhầm Security Group với NACL

Hãy nhớ:

- Security Group: Stateful.
- NACL: Stateless.

Nếu cấu hình sai NACL, dữ liệu phản hồi có thể bị chặn dù chiều Inbound đã được cho phép.

### Lỗi 4: CIDR bị trùng

Ví dụ:

```text
VPC-A: 10.0.0.0/16
VPC-B: 10.0.0.0/16
```

Hai VPC này không thể thiết lập VPC Peering theo cấu hình thông thường.

### Lỗi 5: Private Subnet không tải được phần mềm

Nguyên nhân:

Private Subnet chưa có NAT Gateway hoặc cơ chế kết nối phù hợp.

Nếu chỉ cần truy cập một số AWS Services như S3, bạn có thể cân nhắc VPC Endpoint.

### Lỗi 6: Mở SSH cho tất cả mọi người

Không nên cấu hình:

```text
Port: 22
Source: 0.0.0.0/0
```

trên máy chủ quản trị mà không có lý do và biện pháp bảo vệ phù hợp.

Nên giới hạn IP quản trị hoặc cân nhắc Session Manager.

### Lỗi 7: Quên chi phí NAT Gateway

NAT Gateway là dịch vụ có thể phát sinh chi phí ngay cả khi ứng dụng không có nhiều người dùng.

Cần kiểm tra chi phí trước khi tạo và xóa tài nguyên sau khi thực hành.

---

# PHẦN 9 – KIẾN THỨC CẦN NHỚ KHI THI AWS DVA-C02

## 8.23. Bảng tổng hợp các dịch vụ quan trọng

| Thành phần | Chức năng | Từ khóa ghi nhớ |
|---|---|---|
| VPC | Tạo mạng riêng | Private Network |
| Subnet | Chia mạng thành các khu vực | IP Range |
| CIDR | Biểu diễn dải IP | IP Address Range |
| Route Table | Điều khiển đường đi | Routing |
| Internet Gateway | Kết nối Internet | Internet Access |
| NAT Gateway | Cho Private Subnet kết nối ra ngoài | Outbound IPv4 |
| Security Group | Kiểm soát truy cập tài nguyên | Stateful |
| Network ACL | Kiểm soát truy cập Subnet | Stateless |
| VPC Endpoint | Kết nối AWS Services riêng tư | Private Connectivity |
| VPC Peering | Kết nối hai VPC | VPC-to-VPC |
| VPC Flow Logs | Ghi nhận lưu lượng mạng | Network Monitoring |
| Session Manager | Quản trị máy chủ từ xa | No Inbound SSH |

## 8.24. Những câu hỏi thường gặp trong kỳ thi

**Câu hỏi 1: EC2 nằm trong Private Subnet cần tải phần mềm từ Internet thì sử dụng gì?**

Đáp án: NAT Gateway (đối với IPv4).

**Câu hỏi 2: Dịch vụ nào giúp EC2 trong Private Subnet truy cập S3 mà không cần NAT Gateway?**

Đáp án: S3 Gateway VPC Endpoint.

**Câu hỏi 3: Security Group là Stateful hay Stateless?**

Đáp án: Stateful.

**Câu hỏi 4: Network ACL là Stateful hay Stateless?**

Đáp án: Stateless.

**Câu hỏi 5: Thành phần nào quyết định đường đi của dữ liệu trong VPC?**

Đáp án: Route Table.

**Câu hỏi 6: Một Subnet có thể trải dài qua nhiều Availability Zone không?**

Đáp án: Không.

**Câu hỏi 7: Muốn kết nối hai VPC bằng địa chỉ IP riêng thì sử dụng gì?**

Đáp án: VPC Peering hoặc một giải pháp kết nối mạng phù hợp khác.

**Câu hỏi 8: VPC Flow Logs dùng để làm gì?**

Đáp án: Ghi nhận thông tin lưu lượng IP nhằm giám sát và xử lý sự cố.

**Câu hỏi 9: Làm thế nào để quản trị EC2 mà không cần mở cổng SSH Inbound?**

Đáp án: AWS Systems Manager Session Manager.

**Câu hỏi 10: Hai VPC có CIDR bị chồng lấn có thể Peering không?**

Đáp án: Không.

---

# PHẦN 10 – KIỂM TRA KIẾN THỨC

## 🧠 10 câu hỏi trắc nghiệm

**Câu 1. Amazon VPC được sử dụng để làm gì?**

A. Tạo cơ sở dữ liệu.

B. Tạo mạng riêng trong AWS.

C. Lưu trữ hình ảnh.

D. Quản lý tài khoản người dùng.

**Câu 2. Thành phần nào giúp VPC kết nối với Internet?**

A. NAT Gateway.

B. Security Group.

C. Internet Gateway.

D. Network ACL.

**Câu 3. Thành phần nào giúp EC2 trong Private Subnet truy cập Internet thông qua IPv4 mà không cần Public IP riêng?**

A. Internet Gateway trực tiếp.

B. NAT Gateway.

C. Security Group.

D. Route Table.

**Câu 4. Security Group có đặc điểm nào?**

A. Stateless.

B. Stateful.

C. Chỉ bảo vệ Subnet.

D. Có quy tắc Deny trực tiếp.

**Câu 5. Network ACL có đặc điểm nào?**

A. Stateful.

B. Không có quy tắc Inbound.

C. Stateless.

D. Chỉ hoạt động với EC2.

**Câu 6. Thành phần nào quyết định đường đi của dữ liệu?**

A. Route Table.

B. IAM.

C. S3.

D. CloudWatch.

**Câu 7. Muốn kết nối EC2 trong Private Subnet với S3 mà không cần NAT Gateway, nên sử dụng gì?**

A. Bastion Host.

B. Internet Gateway.

C. Gateway VPC Endpoint.

D. VPC Peering.

**Câu 8. VPC Peering có hỗ trợ kết nối hai VPC có CIDR bị chồng lấn không?**

A. Có.

B. Không.

C. Chỉ khi cùng Region.

D. Chỉ khi cùng AWS Account.

**Câu 9. Dịch vụ nào giúp ghi lại thông tin về lưu lượng IP trong VPC?**

A. VPC Flow Logs.

B. CloudFront.

C. Route 53.

D. IAM.

**Câu 10. Dịch vụ nào cho phép quản trị EC2 mà không cần mở SSH Inbound?**

A. NAT Gateway.

B. Session Manager.

C. Internet Gateway.

D. VPC Peering.

---

## ✅ Đáp án và giải thích

| Câu | Đáp án | Giải thích |
|---|---|---|
| 1 | B | VPC tạo mạng riêng trong AWS |
| 2 | C | Internet Gateway kết nối VPC với Internet |
| 3 | B | NAT Gateway cho phép kết nối Internet đi ra |
| 4 | B | Security Group là Stateful |
| 5 | C | Network ACL là Stateless |
| 6 | A | Route Table quyết định đường đi |
| 7 | C | Gateway Endpoint hỗ trợ S3 và DynamoDB |
| 8 | B | CIDR bị chồng lấn không thể Peering |
| 9 | A | Flow Logs ghi nhận lưu lượng IP |
| 10 | B | Session Manager cho phép quản trị không cần SSH Inbound |

---

# 🎮 THỬ THÁCH KIDZONE – THIẾT KẾ THÀNH PHỐ AWS

Bây giờ đến lượt bạn trở thành một kỹ sư AWS!

Hãy tưởng tượng bạn đang xây dựng một website bán hàng có:

- 1 Load Balancer.
- 2 Backend Server.
- 1 Database.
- 1 Redis Cache.
- 1 S3 Bucket.

Hãy trả lời các câu hỏi sau:

**Thử thách 1:** Bạn sẽ tạo bao nhiêu Subnet?

**Thử thách 2:** Thành phần nào nên nằm trong Public Subnet?

**Thử thách 3:** Backend Server nên nằm ở Public hay Private Subnet?

**Thử thách 4:** Database có nên cho phép người dùng Internet kết nối trực tiếp không?

**Thử thách 5:** Backend Server cần truy cập S3. Bạn sẽ sử dụng NAT Gateway hay VPC Endpoint?

**Thử thách 6:** Làm thế nào để quản trị Backend Server mà không cần mở SSH công khai?

### Gợi ý

Một kiến trúc tốt có thể sử dụng:

- Public Subnet: Load Balancer và NAT Gateway.
- Private App Subnet: Backend Server.
- Private DB Subnet: Database và Redis.
- VPC Endpoint: Kết nối S3.
- Session Manager: Quản trị máy chủ.
- Security Group: Giới hạn quyền truy cập giữa các thành phần.

Bạn có thể tự vẽ sơ đồ và giải thích đường đi của dữ liệu.

Đó chính là cách một kỹ sư AWS bắt đầu thiết kế hệ thống thực tế!

---

# 🎓 TỔNG KẾT CHƯƠNG 8

Hôm nay An và Bin đã học được cách xây dựng một thành phố công nghệ riêng trong AWS.

Chúng ta đã khám phá:

1. Mạng máy tính giúp các thiết bị giao tiếp với nhau.
2. IP Address là địa chỉ của thiết bị.
3. CIDR giúp xác định dải địa chỉ IP.
4. VPC là mạng riêng trong AWS.
5. Subnet giúp chia VPC thành các khu vực.
6. Public Subnet có đường định tuyến trực tiếp đến Internet Gateway.
7. Private Subnet không có đường định tuyến trực tiếp đến Internet Gateway.
8. Route Table quyết định đường đi của dữ liệu.
9. Internet Gateway giúp VPC kết nối Internet.
10. NAT Gateway hỗ trợ kết nối Internet đi ra từ Private Subnet.
11. Security Group và Network ACL giúp bảo vệ mạng.
12. VPC Endpoint giúp kết nối riêng đến các dịch vụ AWS được hỗ trợ.
13. VPC Peering giúp kết nối các VPC.
14. VPC Flow Logs giúp theo dõi lưu lượng mạng.
15. Session Manager giúp quản trị máy chủ an toàn hơn.

## ⭐ 5 điều quan trọng nhất cần nhớ

Nếu chỉ được nhớ 5 điều sau khi học chương này, hãy nhớ:

- **VPC:** Thành phố riêng của bạn trong AWS.
- **Subnet:** Các khu vực trong thành phố.
- **Route Table:** Bản đồ giao thông.
- **Internet Gateway / NAT Gateway:** Các cổng kết nối ra bên ngoài.
- **Security Group / Network ACL:** Những lớp bảo vệ của thành phố.

Cô Mây kết thúc bài học:

"Một thành phố tốt không chỉ cần nhiều ngôi nhà đẹp. Nó còn cần đường giao thông hợp lý, cổng kiểm soát an toàn và những quy tắc rõ ràng."

An và Bin đã hiểu.

Và họ biết rằng, muốn xây dựng một ứng dụng AWS tốt, trước tiên phải biết cách xây dựng một mạng máy tính tốt!

---

## 📚 TÀI LIỆU THAM KHẢO

Tài liệu chính thức từ Amazon Web Services:

1. [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/)
2. [VPC Basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-subnet-basics.html)
3. [Subnet CIDR Blocks](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html)
4. [Configure Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
5. [Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)
6. [NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat.html)
7. [Security Groups and Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/infrastructure-security.html)
8. [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/)
9. [VPC Peering](https://docs.aws.amazon.com/vpc/latest/peering/)
10. [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
11. [Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

---

**KidZone Technology – Cứ tò mò, cứ khám phá, công nghệ sẽ không còn khó hiểu!**
