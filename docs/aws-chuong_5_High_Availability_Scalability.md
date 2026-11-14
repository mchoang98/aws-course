# CHƯƠNG 5. HIGH AVAILABILITY & SCALABILITY
## Tính sẵn sàng cao và khả năng mở rộng trên AWS

> **Mục tiêu của chương:** Hiểu cách xây dựng một hệ thống website có thể phục vụ nhiều người dùng, tự tăng hoặc giảm số lượng máy chủ khi cần, và vẫn hoạt động khi một máy chủ hoặc một khu vực gặp sự cố.

---

## 0. Hãy tưởng tượng bạn đang quản lý một cửa hàng

Bạn có một cửa hàng bán đồ chơi.

- Ngày thường chỉ có 10 khách đến mua hàng.
- Cuối tuần có 100 khách.
- Ngày khai trương có thể có 1.000 khách cùng đến.
- Nếu nhân viên duy nhất bị ốm, cửa hàng phải đóng cửa.
- Nếu cửa hàng quá đông mà chỉ có một quầy thanh toán, khách sẽ phải xếp hàng rất lâu.

Bạn sẽ giải quyết thế nào?

1. Thuê thêm nhân viên khi cửa hàng đông.
2. Có nhiều quầy thanh toán để khách không phải chờ quá lâu.
3. Nếu một nhân viên nghỉ, người khác có thể thay thế.
4. Khi cửa hàng vắng khách, giảm bớt nhân viên để tiết kiệm chi phí.

Một hệ thống website cũng gặp những vấn đề tương tự.

| Cửa hàng ngoài đời | Hệ thống trên AWS |
|---|---|
| Nhân viên | EC2 Instance |
| Khách hàng | Người truy cập website |
| Quầy điều phối khách | Load Balancer |
| Thuê thêm nhân viên | Scale Out |
| Giảm nhân viên khi vắng khách | Scale In |
| Có nhân viên dự phòng | Redundancy |
| Cửa hàng vẫn hoạt động khi một nhân viên nghỉ | High Availability |

AWS cung cấp các dịch vụ giúp chúng ta giải quyết những vấn đề này.

---

# PHẦN I. NHỮNG KHÁI NIỆM QUAN TRỌNG

## 1. Scalability là gì? – Khả năng mở rộng

**Scalability** có nghĩa là hệ thống có khả năng xử lý nhiều công việc hơn khi số lượng người dùng tăng lên.

Ví dụ:

Ban đầu website của bạn chỉ có 100 người truy cập mỗi ngày. Một máy chủ EC2 có thể xử lý tốt.

Sau đó website nổi tiếng hơn và có 10.000 người truy cập cùng lúc. Máy chủ cũ có thể bị quá tải.

Hệ thống cần được mở rộng để đáp ứng lượng truy cập mới.

```text
Ban đầu:

100 người dùng
      |
      v
  1 EC2
      |
      v
  Website


Khi website phát triển:

10.000 người dùng
      |
      v
Nhiều EC2 hơn
      |
      v
  Website
```

Điểm quan trọng:

- Scalability không có nghĩa là website chắc chắn không bao giờ bị lỗi.
- Scalability tập trung vào khả năng đáp ứng khi khối lượng công việc tăng.
- Có thể mở rộng bằng cách nâng cấp một máy chủ hoặc thêm nhiều máy chủ.

## 2. Elasticity là gì? – Khả năng co giãn

**Elasticity** là khả năng tự động tăng hoặc giảm tài nguyên dựa trên nhu cầu thực tế.

Hãy tưởng tượng:

- Buổi sáng có 100 khách: cửa hàng cần 2 nhân viên.
- Buổi trưa có 1.000 khách: cửa hàng cần 10 nhân viên.
- Buổi tối chỉ còn 50 khách: cửa hàng chỉ cần 1 nhân viên.

Nếu có thể tự động điều chỉnh số nhân viên theo lượng khách, đó là cách hoạt động tương tự Elasticity.

Trên AWS:

```text
Lượng truy cập tăng
        |
        v
AWS nhận thấy hệ thống cần thêm năng lực
        |
        v
Tạo thêm EC2 Instance
        |
        v
Website xử lý được nhiều người hơn


Lượng truy cập giảm
        |
        v
AWS nhận thấy hệ thống không cần nhiều máy chủ
        |
        v
Giảm bớt EC2 Instance
        |
        v
Tiết kiệm chi phí
```

**Scalability và Elasticity khác nhau thế nào?**

| Tiêu chí | Scalability | Elasticity |
|---|---|---|
| Ý nghĩa | Có khả năng mở rộng để xử lý tải lớn hơn | Tự co giãn tài nguyên theo nhu cầu |
| Điều chỉnh | Có thể thủ công hoặc tự động | Thường nhấn mạnh việc tự động điều chỉnh |
| Ví dụ | Nâng cấp máy chủ từ 2 CPU lên 8 CPU | Tự thêm máy chủ khi lượng truy cập tăng và giảm khi lượng truy cập giảm |

**Cách nhớ:**

- Scalability: Hệ thống có thể lớn hơn.
- Elasticity: Hệ thống tự lớn lên hoặc nhỏ lại theo nhu cầu.

## 3. High Availability là gì? – Tính sẵn sàng cao

**High Availability (HA)** có nghĩa là thiết kế hệ thống để dịch vụ vẫn hoạt động khi một hoặc một số thành phần gặp sự cố.

Ví dụ:

Bạn có một cửa hàng chỉ có một cửa ra vào. Nếu cửa đó bị hỏng, khách không thể vào cửa hàng.

Nếu cửa hàng có thêm cửa thứ hai, khách vẫn có thể đi vào khi cửa thứ nhất gặp vấn đề.

Trong hệ thống:

- Chỉ có một EC2: EC2 hỏng thì website có thể ngừng hoạt động.
- Có hai EC2 ở hai Availability Zone khác nhau: nếu một máy chủ hoặc một AZ gặp sự cố, máy chủ còn lại vẫn có thể phục vụ người dùng.

```text
Thiết kế chưa có High Availability:

Người dùng
    |
    v
  EC2 duy nhất
    |
    v
 Website

Nếu EC2 hỏng -> Website có thể ngừng hoạt động.


Thiết kế có High Availability:

                 +--> EC2 A (AZ A) --+
Người dùng --> ELB                  +--> Website
                 +--> EC2 B (AZ B) --+

Nếu EC2 A gặp sự cố, ELB có thể chuyển yêu cầu
đến EC2 B nếu EC2 B vẫn khỏe mạnh.
```

HA không có nghĩa là hệ thống không bao giờ gặp lỗi. Nó có nghĩa là hệ thống được thiết kế để giảm thời gian ngừng hoạt động và có thể tiếp tục phục vụ khi một thành phần bị lỗi.

## 4. Fault Tolerance là gì? – Khả năng chịu lỗi

**Fault Tolerance** là khả năng tiếp tục hoạt động ngay cả khi một thành phần bị hỏng, với mức gián đoạn rất thấp hoặc không đáng kể tùy thiết kế.

Ví dụ:

- Một chiếc xe có một động cơ. Động cơ hỏng thì xe dừng.
- Một hệ thống có nhiều thành phần dự phòng được thiết kế để tiếp tục chạy khi một thành phần hỏng.

Phân biệt đơn giản:

| Khái niệm | Ý nghĩa dễ hiểu |
|---|---|
| High Availability | Giảm thời gian hệ thống bị ngừng hoạt động |
| Fault Tolerance | Tiếp tục hoạt động khi có lỗi xảy ra |
| Scalability | Có thể tăng khả năng xử lý |
| Elasticity | Tự tăng hoặc giảm tài nguyên theo nhu cầu |

Fault Tolerance thường yêu cầu thiết kế dự phòng kỹ hơn và có thể tốn kém hơn. Không phải website nào cũng cần mức chịu lỗi cao nhất.

## 5. Redundancy là gì? – Dự phòng

**Redundancy** nghĩa là có thêm thành phần dự phòng để thay thế khi thành phần chính gặp sự cố.

Ví dụ:

Bạn có hai chiếc đèn trong phòng. Nếu một chiếc bị hỏng, chiếc còn lại vẫn chiếu sáng.

Trong AWS, chúng ta có thể dự phòng:

- Nhiều EC2 Instance.
- Nhiều Availability Zone.
- Nhiều đường kết nối.
- Các bản sao dữ liệu.
- Các thành phần mạng.

Tuy nhiên, chỉ tạo nhiều EC2 trong cùng một máy chủ vật lý hoặc cùng một điểm lỗi không đảm bảo hệ thống có thể chịu được sự cố của cả khu vực đó.

Vì vậy, cần hiểu khái niệm Availability Zone.

---

# PHẦN II. MỞ RỘNG MÁY CHỦ

## 6. Vertical Scaling là gì? – Mở rộng theo chiều dọc

Vertical Scaling còn được gọi là **Scale Up**.

Nghĩa là nâng cấp máy chủ hiện tại để nó mạnh hơn.

Ví dụ:

Ban đầu bạn thuê một máy chủ EC2 có:

- 2 vCPU.
- 4 GB RAM.

Khi website có nhiều người dùng hơn, bạn nâng cấp lên:

- 8 vCPU.
- 32 GB RAM.

```text
Trước khi nâng cấp:

+----------------------+
|       EC2            |
|      2 vCPU          |
|      4 GB RAM        |
+----------------------+


Sau khi nâng cấp:

+----------------------+
|       EC2            |
|      8 vCPU          |
|      32 GB RAM       |
+----------------------+
```

### Ưu điểm

- Dễ hình dung và thường dễ triển khai.
- Không cần quản lý nhiều máy chủ ứng dụng.
- Có thể phù hợp với ứng dụng cũ chưa hỗ trợ chạy nhiều bản sao.

### Nhược điểm

- Có giới hạn về cấu hình phần cứng.
- Máy chủ càng mạnh thường càng tốn chi phí.
- Có thể cần dừng hoặc khởi động lại EC2 để thay đổi loại Instance, tùy thao tác và cấu hình.
- Nếu chỉ có một máy chủ, máy chủ đó vẫn là điểm lỗi duy nhất.

**Ví dụ AWS:** Thay đổi EC2 Instance Type từ `t3.small` sang một loại có cấu hình CPU và RAM cao hơn.

## 7. Horizontal Scaling là gì? – Mở rộng theo chiều ngang

Horizontal Scaling còn được gọi là **Scale Out**.

Thay vì làm một máy chủ mạnh hơn, chúng ta tạo thêm nhiều máy chủ.

Ví dụ:

Ban đầu:

```text
100 người dùng
      |
      v
    EC2 A
```

Khi có nhiều người dùng:

```text
1.000 người dùng
      |
      v
    EC2 A
    EC2 B
    EC2 C
```

Các máy chủ cùng xử lý công việc.

### Ưu điểm

- Có thể phục vụ lượng truy cập lớn hơn.
- Có thể tăng số lượng máy chủ khi cần.
- Có thể kết hợp nhiều Availability Zone để tăng tính sẵn sàng.
- Khi một máy chủ hỏng, các máy chủ còn lại vẫn có thể tiếp tục phục vụ.

### Nhược điểm

- Cần có cách phân phối yêu cầu đến nhiều máy chủ.
- Ứng dụng cần được thiết kế phù hợp để chạy trên nhiều Instance.
- Cần xử lý dữ liệu dùng chung, phiên đăng nhập và trạng thái người dùng.
- Cần theo dõi và quản lý nhiều tài nguyên hơn.

**Ví dụ AWS:** Tạo nhiều EC2 Instance và sử dụng Elastic Load Balancing để phân phối yêu cầu.

## 8. So sánh Vertical Scaling và Horizontal Scaling

| Tiêu chí | Vertical Scaling | Horizontal Scaling |
|---|---|---|
| Cách làm | Nâng cấp một máy chủ | Thêm nhiều máy chủ |
| Tên thường gặp | Scale Up / Scale Down | Scale Out / Scale In |
| Ví dụ | 2 vCPU thành 8 vCPU | 1 EC2 thành 4 EC2 |
| Giới hạn | Phụ thuộc cấu hình tối đa của máy | Phụ thuộc thiết kế và giới hạn dịch vụ |
| Dự phòng | Không tự tạo thêm máy chủ dự phòng | Có thể kết hợp nhiều máy chủ |
| Phù hợp | Ứng dụng cần máy chủ mạnh hơn | Website có lượng truy cập thay đổi lớn |

**Mẹo nhớ:**

- Scale Up: Làm một người khỏe hơn.
- Scale Out: Thuê thêm nhiều người.

---

# PHẦN III. AVAILABILITY ZONE VÀ THIẾT KẾ DỰ PHÒNG

## 9. Availability Zone là gì?

Trong AWS, một **Region** là một khu vực địa lý lớn, ví dụ Singapore hoặc Tokyo.

Bên trong một Region có nhiều **Availability Zone (AZ)**.

Mỗi AZ là một khu vực hạ tầng độc lập trong cùng Region, có các thành phần điện, mạng và hạ tầng riêng. Các AZ được kết nối với nhau bằng mạng có độ trễ thấp.

Hãy tưởng tượng:

- Region là một thành phố.
- Availability Zone là các khu vực có trung tâm dữ liệu riêng trong thành phố đó.

```text
AWS Region: Singapore
|
+-- Availability Zone A
|   +-- Data Center
|   +-- EC2
|
+-- Availability Zone B
|   +-- Data Center
|   +-- EC2
|
+-- Availability Zone C
    +-- Data Center
    +-- EC2
```

Nếu một AZ gặp sự cố, các AZ khác trong cùng Region có thể vẫn hoạt động.

Lưu ý:

- AZ không phải là một EC2.
- Một AZ có thể bao gồm một hoặc nhiều trung tâm dữ liệu.
- Các AZ được thiết kế để giảm khả năng cùng gặp một sự cố.
- Không nên đặt tất cả máy chủ quan trọng vào một AZ nếu mục tiêu là chịu được sự cố của AZ đó.

## 10. Multi-AZ là gì?

**Multi-AZ** nghĩa là triển khai tài nguyên ở nhiều Availability Zone.

Ví dụ, website có hai EC2:

- EC2 A nằm ở AZ A.
- EC2 B nằm ở AZ B.

```text
             Người dùng
                 |
                 v
          Load Balancer
             /      \
            /        \
           v          v
        EC2 A        EC2 B
        AZ A         AZ B
```

Nếu AZ A gặp sự cố:

```text
             Người dùng
                 |
                 v
          Load Balancer
                 |
                 v
              EC2 B
              AZ B
```

Website vẫn có thể hoạt động, miễn là EC2 B và các thành phần phụ thuộc vẫn khỏe mạnh, đồng thời còn đủ năng lực xử lý.

**Multi-AZ thường được sử dụng để tăng High Availability.**

## 11. Multi-AZ và Multi-Region khác nhau thế nào?

| Tiêu chí | Multi-AZ | Multi-Region |
|---|---|---|
| Phạm vi | Nhiều AZ trong một Region | Nhiều Region |
| Mục tiêu thường gặp | Chịu lỗi ở cấp AZ | Chịu lỗi ở cấp Region |
| Độ phức tạp | Thường thấp hơn | Thường cao hơn |
| Chi phí | Có thể tăng do cần tài nguyên dự phòng | Có thể tăng đáng kể do triển khai ở nhiều Region |
| Ví dụ | Singapore AZ A và AZ B | Singapore và Tokyo |

Ví dụ:

- Nếu một trung tâm dữ liệu hoặc AZ gặp sự cố, Multi-AZ có thể giúp hệ thống tiếp tục hoạt động.
- Nếu cả một Region gặp sự cố nghiêm trọng, Multi-Region có thể giúp chuyển dịch vụ sang Region khác nếu đã được thiết kế và chuẩn bị từ trước.

Không phải cứ tạo EC2 ở hai Region là hệ thống tự động chuyển đổi. Bạn còn phải thiết kế DNS, dữ liệu, đồng bộ, chuyển đổi lưu lượng và quy trình khôi phục.

---

# PHẦN IV. LOAD BALANCER

## 12. Load Balancer là gì?

**Load Balancer** có nghĩa là bộ cân bằng tải.

Hãy tưởng tượng một siêu thị có 4 quầy thanh toán.

Nếu tất cả khách hàng đều xếp hàng tại quầy số 1, trong khi ba quầy còn lại trống, siêu thị sẽ hoạt động rất kém hiệu quả.

Siêu thị cần một nhân viên hướng dẫn khách đến quầy phù hợp.

Load Balancer làm công việc tương tự đối với các máy chủ.

```text
                 1.000 người dùng
                        |
                        v
                 +-------------+
                 | Load        |
                 | Balancer    |
                 +-------------+
                   /    |    \
                  /     |     \
                 v      v      v
              EC2 A  EC2 B  EC2 C
```

Load Balancer nhận yêu cầu từ người dùng và chuyển yêu cầu đến các máy chủ phía sau.

### Vì sao cần Load Balancer?

1. Phân phối yêu cầu đến nhiều máy chủ.
2. Tránh để một máy chủ phải xử lý toàn bộ người dùng.
3. Kiểm tra tình trạng máy chủ.
4. Không gửi yêu cầu mới đến máy chủ không khỏe mạnh theo kết quả health check.
5. Giúp triển khai hệ thống có nhiều EC2 và nhiều AZ.

### Nếu không có Load Balancer?

Người dùng có thể phải truy cập trực tiếp vào từng EC2.

Điều này khiến việc quản lý địa chỉ máy chủ, phân phối tải và thay thế máy chủ gặp lỗi trở nên phức tạp.

## 13. Elastic Load Balancing (ELB) là gì?

**Elastic Load Balancing (ELB)** là dịch vụ Load Balancer do AWS cung cấp.

Thay vì tự xây dựng một máy chủ riêng để phân phối lưu lượng, bạn có thể sử dụng ELB.

AWS quản lý hạ tầng Load Balancer; bạn cấu hình cách nó nhận lưu lượng và chuyển lưu lượng đến các mục tiêu (Targets).

Một số khái niệm quan trọng:

| Thuật ngữ | Ý nghĩa |
|---|---|
| Load Balancer | Bộ cân bằng tải |
| Listener | Cổng tiếp nhận yêu cầu, ví dụ HTTP 80 hoặc HTTPS 443 |
| Rule | Quy tắc quyết định chuyển yêu cầu đi đâu |
| Target | Máy chủ hoặc tài nguyên nhận yêu cầu |
| Target Group | Nhóm các Target |
| Health Check | Kiểm tra Target có hoạt động tốt không |

Ví dụ:

```text
Người dùng
    |
    v
ALB
Listener: HTTPS 443
    |
    v
Target Group
    |
    +---- EC2 A
    |
    +---- EC2 B
    |
    +---- EC2 C
```

## 14. Các loại Load Balancer trên AWS

AWS có bốn loại Load Balancer thường được nhắc đến trong kiến thức nền tảng:

1. Application Load Balancer (ALB).
2. Network Load Balancer (NLB).
3. Gateway Load Balancer (GWLB).
4. Classic Load Balancer (CLB).

### 14.1. Application Load Balancer (ALB)

ALB hoạt động ở **Layer 7 – Application Layer**.

Nói đơn giản, ALB hiểu được các thông tin ở tầng ứng dụng HTTP/HTTPS như:

- Đường dẫn URL.
- Tên miền.
- Một số thông tin trong HTTP request.
- Host hoặc path của yêu cầu.

Ví dụ bạn có website:

- `shop.example.com`
- `admin.example.com`

Bạn có thể cấu hình ALB:

```text
                    Người dùng
                        |
                        v
                       ALB
                        |
              +---------+---------+
              |                   |
              v                   v
     shop.example.com      admin.example.com
              |                   |
              v                   v
         Shop Target         Admin Target
```

Hoặc dùng đường dẫn:

```text
https://example.com/api/*
        |
        v
    API Target Group

https://example.com/images/*
        |
        v
    Image Target Group
```

**ALB phù hợp với:**

- Website.
- Web application.
- REST API chạy HTTP/HTTPS.
- Ứng dụng cần điều hướng theo domain hoặc URL path.
- Microservices sử dụng HTTP.

### 14.2. Network Load Balancer (NLB)

NLB hoạt động ở **Layer 4 – Transport Layer**.

NLB xử lý lưu lượng ở tầng TCP, UDP hoặc TLS theo cấu hình listener.

Nói đơn giản:

- ALB có thể hiểu URL `/products`.
- NLB chủ yếu quan tâm đến kết nối và thông tin mạng ở tầng vận chuyển, không điều hướng dựa trên URL path như ALB.

NLB được thiết kế cho hiệu năng cao, độ trễ thấp và xử lý lượng kết nối lớn.

NLB có thể cung cấp địa chỉ IP tĩnh cho từng Availability Zone và hỗ trợ Elastic IP theo cấu hình.

**NLB phù hợp với:**

- Ứng dụng cần TCP hoặc UDP.
- Dịch vụ yêu cầu độ trễ thấp.
- Hệ thống cần địa chỉ IP tĩnh ở phía Load Balancer.
- Các ứng dụng không chỉ sử dụng HTTP/HTTPS.

### 14.3. Gateway Load Balancer (GWLB)

GWLB hoạt động ở **Layer 3 – Network Layer**.

GWLB được thiết kế để triển khai và mở rộng các thiết bị mạng hoặc thiết bị bảo mật ảo, ví dụ:

- Firewall ảo.
- Thiết bị kiểm tra lưu lượng mạng.
- Hệ thống phát hiện hoặc ngăn chặn xâm nhập.

Hãy tưởng tượng tất cả xe đi vào một khu vực cần đi qua trạm kiểm tra an ninh. GWLB giúp phân phối lưu lượng đến các thiết bị kiểm tra phù hợp.

```text
          Lưu lượng mạng
                 |
                 v
                GWLB
                 |
        +--------+--------+
        |        |        |
        v        v        v
     Firewall Firewall Firewall
        A        B        C
```

Bạn thường không chọn GWLB để cân bằng tải cho website thông thường. Nó dành cho kiến trúc mạng và bảo mật chuyên biệt.

### 14.4. Classic Load Balancer (CLB)

CLB là thế hệ Load Balancer cũ của AWS.

Nó hỗ trợ các chức năng cân bằng tải cơ bản ở Layer 4 và Layer 7.

Trong kiến trúc mới, thông thường nên tìm hiểu ALB hoặc NLB thay vì bắt đầu với CLB, trừ khi bạn đang làm việc với hệ thống cũ cần duy trì tương thích.

### 14.5. Bảng so sánh các loại Load Balancer

| Loại | Layer | Dùng khi nào? | Ví dụ |
|---|---|---|---|
| ALB | Layer 7 | HTTP/HTTPS, điều hướng theo URL hoặc domain | Website, REST API |
| NLB | Layer 4 | TCP/UDP/TLS, hiệu năng và độ trễ thấp | Dịch vụ TCP, ứng dụng cần IP tĩnh |
| GWLB | Layer 3 | Phân phối lưu lượng qua thiết bị mạng/bảo mật ảo | Firewall ảo |
| CLB | Layer 4/7 | Hệ thống cũ | Ứng dụng legacy |

**Mẹo nhớ khi học AWS:**

- ALB = Application = hiểu HTTP request.
- NLB = Network = xử lý kết nối mạng Layer 4.
- GWLB = Gateway = đưa lưu lượng qua thiết bị mạng/bảo mật.
- CLB = Classic = thế hệ cũ.

## 15. Health Check là gì?

Health Check là cơ chế kiểm tra xem máy chủ có đang hoạt động tốt hay không.

Ví dụ:

ALB có ba EC2:

- EC2 A: hoạt động bình thường.
- EC2 B: ứng dụng bị lỗi.
- EC2 C: hoạt động bình thường.

Nếu không kiểm tra sức khỏe, ALB có thể tiếp tục gửi yêu cầu đến EC2 B và người dùng nhận lỗi.

Nếu bật Health Check, ALB có thể phát hiện EC2 B không khỏe mạnh và ngừng gửi yêu cầu mới đến máy chủ đó.

```text
                 ALB
                  |
          +-------+-------+
          |       |       |
          v       v       v
        EC2 A   EC2 B   EC2 C
        Healthy Unhealthy Healthy
          ^               ^
          |               |
       Nhận request     Nhận request

        EC2 B không nhận request mới
        khi được đánh dấu Unhealthy.
```

Health Check có thể kiểm tra:

- Máy chủ có phản hồi không?
- Ứng dụng có trả về mã HTTP hợp lệ không?
- Endpoint kiểm tra sức khỏe có hoạt động không?

Ví dụ ứng dụng có endpoint:

```text
GET /health
```

Nếu ứng dụng hoạt động bình thường, endpoint có thể trả về:

```json
{
  "status": "ok"
}
```

Lưu ý: Cần cấu hình health check phù hợp. Một endpoint `/health` chỉ kiểm tra tiến trình ứng dụng còn chạy chưa chắc đã chứng minh toàn bộ chức năng, cơ sở dữ liệu và dịch vụ phụ thuộc đều khỏe mạnh.

## 16. Sticky Sessions là gì?

Thông thường, Load Balancer có thể chuyển các request của một người dùng đến các EC2 khác nhau.

Ví dụ:

```text
Người dùng A
    |
    v
   ALB
    |
    +---- Request 1 --> EC2 A
    |
    +---- Request 2 --> EC2 B
    |
    +---- Request 3 --> EC2 A
```

Điều này có thể gây vấn đề nếu ứng dụng lưu thông tin đăng nhập hoặc giỏ hàng chỉ trong bộ nhớ của một EC2.

**Sticky Sessions** giúp các request của cùng một người dùng được điều hướng đến cùng một Target trong khoảng thời gian được cấu hình.

```text
Người dùng A
    |
    v
   ALB
    |
    +---- Request 1 --> EC2 A
    +---- Request 2 --> EC2 A
    +---- Request 3 --> EC2 A
```

### Có nên luôn bật Sticky Sessions không?

Không.

Sticky Sessions có thể giúp ứng dụng cũ hoạt động đúng khi session được lưu trên từng máy chủ, nhưng có một số nhược điểm:

- Tải có thể phân phối không đều.
- Nếu Target gặp sự cố, người dùng có thể phải chuyển sang Target khác.
- Không giải quyết triệt để vấn đề lưu trạng thái của ứng dụng.

Một thiết kế thường tốt hơn là xây dựng ứng dụng **Stateless**.

Stateless nghĩa là máy chủ ứng dụng không phụ thuộc vào bộ nhớ riêng của chính nó để giữ trạng thái lâu dài của người dùng.

Ví dụ:

- Session có thể lưu ở Redis.
- Dữ liệu người dùng lưu trong database.
- File tải lên lưu trong S3.

Khi đó, người dùng có thể được xử lý bởi bất kỳ EC2 nào mà không làm mất dữ liệu phiên.

## 17. Cross-Zone Load Balancing là gì?

Cross-Zone Load Balancing cho phép Load Balancer phân phối lưu lượng đến các Target ở nhiều Availability Zone, thay vì chỉ phân phối trong từng AZ.

Ví dụ:

- AZ A có 2 EC2.
- AZ B có 2 EC2.

Khi bật và được áp dụng, Load Balancer có thể phân phối lưu lượng trên các Target thuộc các AZ khác nhau để cân bằng tải tốt hơn.

```text
                    ALB
                     |
          +----------+----------+
          |                     |
          v                     v
        AZ A                  AZ B
      EC2 A1                EC2 B1
      EC2 A2                EC2 B2
```

Mục tiêu là tránh tình trạng một nhóm Target bị quá tải trong khi nhóm khác còn nhiều tài nguyên.

Lưu ý: Hành vi và mặc định của Cross-Zone Load Balancing khác nhau tùy loại Load Balancer và cấu hình. Khi làm bài thi hoặc triển khai thực tế, hãy đọc kỹ yêu cầu của từng dịch vụ.

## 18. SSL/TLS và SNI trên Load Balancer

Khi truy cập website bằng:

```text
https://example.com
```

Dữ liệu giữa trình duyệt và website được bảo vệ bằng HTTPS/TLS.

Thông thường, người dùng cần một chứng chỉ số (SSL/TLS certificate) để xác thực website.

AWS có thể sử dụng **AWS Certificate Manager (ACM)** để quản lý chứng chỉ và gắn chứng chỉ vào Load Balancer phù hợp.

Ví dụ:

```text
Trình duyệt
    |
    | HTTPS
    v
ALB (có TLS Certificate)
    |
    | HTTP hoặc HTTPS tùy cấu hình
    v
EC2
```

Một cách triển khai phổ biến là kết thúc kết nối TLS tại ALB (TLS termination), sau đó ALB chuyển tiếp request đến Target Group.

Điều này giúp giảm công việc xử lý TLS ở các EC2 phía sau, nhưng bạn vẫn phải cân nhắc yêu cầu mã hóa giữa ALB và EC2.

### SNI là gì?

**SNI (Server Name Indication)** là một phần mở rộng của TLS giúp máy chủ biết tên miền mà trình duyệt muốn truy cập ngay trong quá trình bắt tay TLS.

Nhờ SNI, một listener có thể phục vụ nhiều tên miền với các chứng chỉ phù hợp, thay vì phải dùng một chứng chỉ duy nhất cho tất cả tên miền.

Ví dụ:

```text
                    ALB HTTPS :443
                           |
             +-------------+-------------+
             |                           |
             v                           v
       example.com                 shop.example.com
       Certificate A               Certificate B
```

**Ghi nhớ:** SNI thường được nhắc đến khi một Load Balancer cần phục vụ nhiều domain HTTPS.

## 19. Connection Draining / Deregistration Delay

Hãy tưởng tượng một chiếc xe buýt chuẩn bị rời bến.

Nhân viên không nên đuổi những hành khách đã lên xe xuống ngay lập tức. Họ nên cho những hành khách đó thời gian hoàn tất việc lên xe.

Khi một EC2 bị đưa ra khỏi Target Group, Load Balancer có thể cần thời gian để các request đang xử lý hoàn tất.

Cơ chế này thường được gọi là **Connection Draining** hoặc **Deregistration Delay**, tùy ngữ cảnh và phiên bản dịch vụ.

Ví dụ:

```text
EC2 A đang phục vụ request
          |
          v
Được yêu cầu ngừng nhận request mới
          |
          v
Request đang xử lý được phép hoàn tất
          |
          v
EC2 A được gỡ khỏi Target Group
```

Điều này giúp giảm lỗi khi triển khai phiên bản mới hoặc giảm số lượng EC2.

---

# PHẦN V. AUTO SCALING GROUP

## 20. Auto Scaling Group (ASG) là gì?

**Auto Scaling Group (ASG)** là dịch vụ giúp AWS tự động quản lý số lượng EC2 Instance theo cấu hình và chính sách bạn đặt ra.

Hãy tưởng tượng bạn quản lý một cửa hàng:

- Ngày thường cần 2 nhân viên.
- Khi khách đông, cần thêm nhân viên.
- Khi khách ít, giảm bớt nhân viên.
- Nếu một nhân viên nghỉ đột xuất, cần người khác thay thế.

ASG hoạt động tương tự như vậy với EC2.

Ví dụ:

```text
Lượng truy cập thấp
       |
       v
   2 EC2


Lượng truy cập tăng
       |
       v
   4 EC2


Lượng truy cập tăng rất cao
       |
       v
   6 EC2


Lượng truy cập giảm
       |
       v
   2 EC2
```

ASG có thể:

1. Tạo EC2 mới.
2. Xóa EC2 không còn cần thiết.
3. Duy trì số lượng EC2 mong muốn.
4. Thay thế EC2 bị lỗi theo health check và cấu hình.
5. Phân bố EC2 trên nhiều Availability Zone trong cùng Region.

**Điều quan trọng:** ASG không phải là Load Balancer.

- ASG quản lý số lượng EC2.
- ELB phân phối lưu lượng đến các Target.

Hai dịch vụ thường được sử dụng cùng nhau.

## 21. Các thành phần quan trọng của Auto Scaling Group

Khi tạo ASG, bạn thường cần cấu hình những thông tin sau.

### 21.1. Launch Template

Launch Template là mẫu hướng dẫn cách tạo EC2.

Nó có thể chứa:

- AMI (Amazon Machine Image).
- Instance Type.
- Key Pair.
- Security Group.
- EBS Volume.
- IAM Instance Profile.
- User Data.

Hãy tưởng tượng Launch Template là bản hướng dẫn tuyển dụng nhân viên.

Mỗi khi ASG cần thêm một EC2, AWS sẽ dựa vào mẫu này để tạo máy chủ mới.

### 21.2. Minimum Capacity

Minimum Capacity là số lượng EC2 tối thiểu mà ASG được phép duy trì.

Ví dụ:

```text
Minimum = 2
```

ASG sẽ không chủ động giảm số lượng EC2 xuống thấp hơn 2 theo cấu hình bình thường của nó.

### 21.3. Desired Capacity

Desired Capacity là số lượng EC2 mà ASG đang cố gắng duy trì.

Ví dụ:

```text
Desired = 3
```

ASG sẽ cố gắng duy trì 3 EC2.

Nếu một EC2 bị lỗi và được xác định là Unhealthy, ASG có thể tạo EC2 khác để thay thế.

### 21.4. Maximum Capacity

Maximum Capacity là số lượng EC2 tối đa mà ASG được phép mở rộng đến.

Ví dụ:

```text
Maximum = 6
```

ASG không được mở rộng vượt quá 6 EC2 theo giới hạn này.

### 21.5. Ví dụ cấu hình đầy đủ

```text
Minimum Capacity = 2
Desired Capacity = 3
Maximum Capacity = 6
```

Điều đó có nghĩa:

- Ít nhất phải duy trì 2 EC2.
- Mục tiêu hiện tại là 3 EC2.
- Khi cần, có thể tăng lên tối đa 6 EC2.

```text
2 <= Desired Capacity <= 6
```

Một số trạng thái ví dụ:

| Tình huống | Số EC2 mong muốn |
|---|---:|
| Hệ thống ít người dùng | 2 |
| Bình thường | 3 |
| Người dùng tăng | 4 |
| Rất đông người dùng | 6 |
| Người dùng giảm | 3 hoặc 2 |

## 22. ASG hoạt động như thế nào khi EC2 bị lỗi?

Giả sử ASG có 3 EC2:

```text
ASG
 |
 +-- EC2 A: Healthy
 |
 +-- EC2 B: Healthy
 |
 +-- EC2 C: Healthy

Desired Capacity = 3
```

EC2 B bị lỗi.

Nếu ASG phát hiện EC2 B không khỏe mạnh theo cơ chế health check được cấu hình:

```text
ASG
 |
 +-- EC2 A: Healthy
 |
 +-- EC2 B: Unhealthy
 |
 +-- EC2 C: Healthy
```

ASG có thể loại bỏ EC2 B và tạo một EC2 mới.

```text
ASG
 |
 +-- EC2 A: Healthy
 |
 +-- EC2 C: Healthy
 |
 +-- EC2 D: New Instance
```

Mục tiêu là duy trì số lượng máy chủ theo Desired Capacity.

Lưu ý: Việc thay thế phụ thuộc vào loại health check, trạng thái Instance, giới hạn tài nguyên và cấu hình ASG. ASG không thể đảm bảo tạo được máy chủ mới nếu Region/AZ hoặc quota gặp vấn đề.

## 23. Scaling Policies là gì?

Scaling Policy là chính sách quyết định khi nào ASG cần tăng hoặc giảm số lượng EC2.

Có một số cách phổ biến.

### 23.1. Target Tracking Scaling

Đây là cách dễ hình dung nhất.

Bạn đặt một mục tiêu cho chỉ số theo dõi.

Ví dụ:

```text
Mục tiêu CPU trung bình = 50%
```

Nếu CPU tăng cao hơn mức mục tiêu trong điều kiện chính sách, ASG có thể thêm EC2.

Nếu CPU giảm và hệ thống có thể chạy với ít EC2 hơn, ASG có thể giảm EC2.

```text
CPU tăng
   |
   v
ASG thêm EC2
   |
   v
Công việc được chia cho nhiều máy hơn
   |
   v
CPU trung bình có thể giảm
```

Target Tracking giống như đặt điều hòa ở 26°C. Hệ thống cố gắng điều chỉnh để tiến gần đến mức mục tiêu.

Các chỉ số có thể dùng tùy loại ứng dụng và cấu hình, ví dụ CPU utilization hoặc số request trung bình trên mỗi Target của ALB.

### 23.2. Step Scaling

Step Scaling cho phép tăng hoặc giảm số lượng EC2 theo từng mức độ của CloudWatch Alarm.

Ví dụ minh họa:

| CPU trung bình | Hành động |
|---|---|
| Dưới 30% | Có thể giảm 1 EC2 |
| Từ 60% đến dưới 80% | Thêm 1 EC2 |
| Từ 80% trở lên | Thêm 2 EC2 |

Các ngưỡng trên chỉ là ví dụ. Khi triển khai thực tế, cần lựa chọn ngưỡng phù hợp với ứng dụng.

### 23.3. Simple Scaling

Simple Scaling sử dụng CloudWatch Alarm để kích hoạt một hành động tăng hoặc giảm số lượng Instance.

Nó có thể sử dụng thời gian chờ (cooldown) để tránh phản ứng liên tục quá nhanh.

Trong nhiều tình huống mới, AWS thường khuyến nghị cân nhắc Target Tracking hoặc Step Scaling tùy nhu cầu.

### 23.4. Scheduled Scaling

Scheduled Scaling phù hợp khi bạn biết trước thời gian hệ thống sẽ đông người dùng.

Ví dụ:

- 08:00: Website bắt đầu có nhiều người truy cập.
- 12:00: Lượng truy cập tăng mạnh.
- 22:00: Lượng truy cập giảm.

Bạn có thể lên lịch tăng số EC2 trước giờ cao điểm và giảm sau giờ cao điểm.

```text
07:30 -> Tăng lên 5 EC2
12:00 -> Duy trì 5 EC2
22:00 -> Giảm xuống 2 EC2
```

### 23.5. Predictive Scaling

Predictive Scaling sử dụng dữ liệu lịch sử và dự báo để chuẩn bị năng lực trước khi nhu cầu tăng.

Ví dụ:

Một website thường có lượng truy cập tăng mạnh vào 8 giờ sáng các ngày trong tuần.

Predictive Scaling có thể dự báo nhu cầu và chuẩn bị thêm EC2 trước thời điểm đó.

**Cách nhớ:**

| Chính sách | Cách hoạt động |
|---|---|
| Target Tracking | Cố gắng giữ chỉ số quanh một mục tiêu |
| Step Scaling | Tăng/giảm theo từng mức cảnh báo |
| Simple Scaling | Thực hiện hành động theo cảnh báo |
| Scheduled Scaling | Tăng/giảm theo lịch cố định |
| Predictive Scaling | Dự báo nhu cầu dựa trên dữ liệu lịch sử |

## 24. CloudWatch liên quan gì đến Auto Scaling?

Amazon CloudWatch là dịch vụ theo dõi và giám sát tài nguyên AWS.

CloudWatch có thể theo dõi các chỉ số như:

- CPUUtilization.
- NetworkIn.
- NetworkOut.
- RequestCountPerTarget đối với một số kiến trúc dùng ALB.

Khi chỉ số vượt ngưỡng, CloudWatch Alarm có thể kích hoạt hành động liên quan đến Scaling Policy.

```text
       EC2 hoạt động
             |
             v
       CloudWatch
       theo dõi CPU
             |
             v
       CloudWatch Alarm
             |
             v
       Scaling Policy
             |
             v
       Auto Scaling Group
             |
             v
       Thêm hoặc giảm EC2
```

**Cách nhớ:**

- CloudWatch: Quan sát và cảnh báo.
- Scaling Policy: Quyết định cần tăng hay giảm.
- ASG: Thực hiện việc thêm hoặc giảm EC2.

---

# PHẦN VI. KẾT HỢP ELB VÀ ASG

## 25. Vì sao cần kết hợp Load Balancer và Auto Scaling Group?

Hãy xem hai dịch vụ như hai người có nhiệm vụ khác nhau:

- Load Balancer là người điều phối khách đến các quầy.
- Auto Scaling Group là người quản lý số lượng nhân viên/quầy.

Nếu chỉ có Load Balancer:

- Nó có thể phân phối yêu cầu đến các EC2 hiện có.
- Nhưng nó không tự chịu trách nhiệm tạo thêm EC2 để tăng năng lực cho ASG.

Nếu chỉ có Auto Scaling Group:

- Nó có thể tạo và xóa EC2.
- Nhưng bạn vẫn cần một cơ chế phân phối lưu lượng đến các máy chủ.

Kết hợp cả hai:

```text
                       Người dùng
                           |
                           v
                 +-------------------+
                 | Application       |
                 | Load Balancer     |
                 +-------------------+
                           |
                           v
                    Target Group
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
           EC2 A         EC2 B         EC2 C
             \             |             /
              \            |            /
               +-----------+-----------+
                           |
                           v
                    Auto Scaling Group
                           |
              Theo dõi và điều chỉnh
              số lượng EC2 theo chính sách
```

Một kiến trúc hoàn chỉnh thường có:

- ALB nhận request từ người dùng.
- Target Group chứa các EC2.
- ASG quản lý các EC2.
- EC2 được phân bố ở nhiều AZ.
- CloudWatch theo dõi chỉ số và hỗ trợ kích hoạt scaling.

## 26. Ví dụ thực tế: Website đăng ký giải đấu

Hãy tưởng tượng bạn đang xây dựng website cho một giải bóng đá.

Ngày bình thường:

- Có 100 phụ huynh truy cập.
- 2 EC2 đủ xử lý.

Ngày mở đăng ký:

- 10.000 phụ huynh cùng truy cập.
- 2 EC2 có thể quá tải.

Ta thiết kế:

```text
                  10.000 phụ huynh
                         |
                         v
                       ALB
                         |
              +----------+----------+
              |                     |
              v                     v
          Target Group          Health Check
              |
       +------+------+------+
       |      |      |      |
       v      v      v      v
     EC2 A  EC2 B  EC2 C  EC2 D
       AZ A          AZ B
              |
              v
             ASG
              |
              v
        CloudWatch Metrics
              |
              v
       Tự động tăng/giảm EC2
```

Kịch bản hoạt động:

1. Người dùng truy cập website.
2. ALB tiếp nhận request.
3. ALB chuyển request đến các EC2 khỏe mạnh.
4. CloudWatch theo dõi mức sử dụng tài nguyên.
5. Nếu tải tăng và chính sách scaling được kích hoạt, ASG tạo thêm EC2.
6. EC2 mới được đăng ký vào Target Group theo cấu hình.
7. Khi lượng truy cập giảm, ASG có thể giảm số lượng EC2.
8. Nếu một EC2 bị lỗi và được đánh dấu không khỏe mạnh, hệ thống có thể ngừng gửi request đến nó và ASG có thể thay thế nó.

Kết quả: Website có thể phục vụ lượng người dùng thay đổi tốt hơn và giảm nguy cơ ngừng hoạt động khi một EC2 gặp lỗi.

**Nhưng cần nhớ:** ALB và ASG không tự giải quyết mọi vấn đề. Database, Redis, S3, session, giới hạn kết nối và thiết kế ứng dụng cũng phải đáp ứng được lượng tải tăng lên.

---

# PHẦN VII. NHỮNG ĐIỀU CẦN LƯU Ý KHI THIẾT KẾ

## 27. Stateless Application – Ứng dụng không lưu trạng thái cục bộ

Khi chạy một ứng dụng trên nhiều EC2, bạn cần chú ý nơi lưu dữ liệu của người dùng.

Ví dụ:

Người dùng đăng nhập vào EC2 A. Nếu session chỉ nằm trong RAM của EC2 A, request tiếp theo được gửi đến EC2 B có thể không nhận ra người dùng.

Giải pháp thường gặp:

- Lưu session trong Redis.
- Lưu dữ liệu lâu dài trong database.
- Lưu file trong Amazon S3.
- Sử dụng cơ chế token phù hợp cho xác thực.

```text
                  ALB
                   |
            +------+------+
            |             |
            v             v
          EC2 A         EC2 B
            |             |
            +------+------+
                   |
                   v
                 Redis
              (Session dùng chung)
                   |
                   v
                Database
```

Nhờ đó, nhiều EC2 có thể phục vụ cùng một người dùng mà không phụ thuộc vào dữ liệu chỉ tồn tại trong RAM của một máy chủ.

## 28. Auto Scaling không đồng nghĩa với High Availability

Đây là một điểm rất dễ nhầm.

Ví dụ:

Bạn có ASG tạo 5 EC2, nhưng tất cả đều nằm trong cùng một AZ.

Nếu AZ đó gặp sự cố, cả 5 EC2 đều có thể bị ảnh hưởng.

Vì vậy:

- ASG giúp quản lý số lượng EC2.
- Multi-AZ giúp phân tán tài nguyên qua nhiều AZ.
- ELB giúp phân phối lưu lượng.
- Health Check giúp phát hiện Target không khỏe mạnh.

Muốn có HA tốt, cần kết hợp các thành phần phù hợp chứ không chỉ bật ASG.

## 29. Auto Scaling không đồng nghĩa với tiết kiệm chi phí tuyệt đối

Auto Scaling có thể giúp tiết kiệm chi phí bằng cách giảm tài nguyên khi nhu cầu thấp.

Nhưng nếu cấu hình sai:

- ASG có thể tạo quá nhiều EC2.
- Ứng dụng có thể liên tục tăng giảm Instance.
- Tài nguyên có thể tăng cao trong thời gian dài.

Một số nguyên tắc:

1. Đặt Maximum Capacity hợp lý.
2. Theo dõi chi phí và mức sử dụng.
3. Cấu hình CloudWatch Alarm.
4. Chọn Instance Type phù hợp.
5. Kiểm tra tài nguyên sau khi thực hành và xóa những tài nguyên không còn dùng.
6. Không tạo tài nguyên AWS chỉ để thử nghiệm nếu chưa hiểu rõ chi phí.

**Lưu ý:** EC2, Load Balancer, dữ liệu truyền tải và một số thành phần liên quan có thể phát sinh phí. Auto Scaling không phải là tính năng miễn phí đảm bảo mọi tài nguyên đều không tính tiền.

## 30. Những lỗi thiết kế thường gặp

| Lỗi | Hậu quả | Cách cải thiện |
|---|---|---|
| Chỉ có một EC2 | EC2 lỗi có thể làm website ngừng hoạt động | Dùng nhiều EC2 và thiết kế dự phòng |
| Nhiều EC2 nhưng cùng một AZ | Có thể cùng bị ảnh hưởng khi AZ gặp sự cố | Phân bố EC2 qua nhiều AZ |
| Có ASG nhưng không có Load Balancer | Khó phân phối request đến nhiều EC2 | Kết hợp ELB khi kiến trúc cần |
| Có Load Balancer nhưng không có health check phù hợp | Có thể gửi request đến ứng dụng bị lỗi | Cấu hình health check đúng |
| Session chỉ lưu trong RAM một EC2 | Request đến EC2 khác có thể mất session | Dùng Redis hoặc giải pháp lưu session dùng chung |
| Maximum Capacity quá cao | Có thể phát sinh chi phí lớn | Đặt giới hạn và giám sát |
| Health check chỉ kiểm tra sai endpoint | Máy chủ có thể được coi là khỏe dù ứng dụng không phục vụ được | Thiết kế endpoint health check phù hợp |

---

# PHẦN VIII. THỰC HÀNH VÀ BÀI TẬP

## 31. Bài thực hành tư duy: Thiết kế website chịu tải

Bạn được giao thiết kế website đăng ký sự kiện.

Dữ liệu:

- Bình thường có 200 người truy cập.
- Khi mở đăng ký có thể có 5.000 người truy cập.
- Website cần hoạt động nếu một EC2 gặp sự cố.
- Không muốn duy trì quá nhiều EC2 khi không có người dùng.

Hãy thiết kế dựa trên các thành phần:

- Application Load Balancer.
- Auto Scaling Group.
- EC2.
- CloudWatch.
- Ít nhất hai Availability Zone.

### Kết quả mong đợi

```text
Người dùng
    |
    v
Application Load Balancer
    |
    v
Target Group
    |
    +---------- AZ A ----------+
    |                          |
    |                        EC2 A
    |                        EC2 B
    |
    +---------- AZ B ----------+
                               |
                             EC2 C
                             EC2 D

Auto Scaling Group quản lý EC2.
CloudWatch theo dõi chỉ số.
```

### Câu hỏi tự kiểm tra

1. Thành phần nào nhận request từ người dùng?
2. Thành phần nào tạo thêm EC2?
3. Thành phần nào theo dõi CPU?
4. Vì sao cần ít nhất hai AZ?
5. Khi một EC2 bị lỗi, thành phần nào có thể phát hiện và thay thế nó?

## 32. Bài tập: Chọn loại Load Balancer

Hãy chọn ALB, NLB hoặc GWLB cho từng tình huống.

| Tình huống | Gợi ý |
|---|---|
| Website HTTP/HTTPS cần điều hướng theo URL path | ALB |
| API cần phân phối theo hostname | ALB |
| Dịch vụ TCP cần độ trễ thấp | NLB |
| Hệ thống cần phân phối lưu lượng đến firewall ảo | GWLB |
| Ứng dụng UDP | NLB |

Hãy tự giải thích vì sao chọn loại đó trước khi xem lại đáp án.

## 33. Bài tập: Tính số lượng EC2 trong ASG

Bạn có cấu hình:

```text
Minimum Capacity = 2
Desired Capacity = 3
Maximum Capacity = 6
```

Trả lời:

1. ASG đang cố gắng duy trì bao nhiêu EC2?
2. Nếu tải tăng, số EC2 tối đa có thể tăng lên bao nhiêu?
3. Nếu tải giảm, ASG có thể giảm xuống 1 EC2 không?
4. Nếu một EC2 bị lỗi, ASG có thể tạo EC2 mới để duy trì Desired Capacity không?

**Đáp án:**

1. 3 EC2.
2. Tối đa 6 EC2.
3. Không, vì Minimum Capacity là 2.
4. Có thể, nếu health check phát hiện lỗi và ASG có đủ điều kiện để tạo Instance thay thế.

## 34. Bài tập: Không cần tạo tài nguyên AWS có tính phí

Bạn có thể luyện tập bằng cách vẽ sơ đồ và trả lời tình huống trước khi triển khai thật.

Thử tự mô phỏng:

- Ban đầu có 2 EC2.
- Lượng truy cập tăng, ASG tăng lên 4 EC2.
- EC2 ở AZ A gặp lỗi.
- ALB ngừng gửi request mới đến Target không khỏe mạnh.
- ASG thay thế EC2 bị lỗi.
- Lượng truy cập giảm, ASG giảm về 2 EC2.

Mục tiêu là hiểu rõ vai trò từng dịch vụ, không phải tạo thật nhiều tài nguyên trên AWS.

Nếu sau này thực hành trên AWS Console, hãy kiểm tra giá của từng dịch vụ, đặt giới hạn tài nguyên và xóa tài nguyên sau khi hoàn tất.

---

# PHẦN IX. KIẾN THỨC QUAN TRỌNG CHO BÀI THI AWS

## 35. Những từ khóa tiếng Anh cần nhớ

| Từ khóa | Nghĩa tiếng Việt | Liên hệ cần nhớ |
|---|---|---|
| High Availability | Tính sẵn sàng cao | Nhiều AZ, dự phòng |
| Fault Tolerance | Khả năng chịu lỗi | Tiếp tục hoạt động khi có lỗi |
| Scalability | Khả năng mở rộng | Tăng năng lực xử lý |
| Elasticity | Khả năng co giãn | Tự tăng/giảm tài nguyên |
| Vertical Scaling | Mở rộng chiều dọc | Nâng cấp một EC2 |
| Horizontal Scaling | Mở rộng chiều ngang | Thêm nhiều EC2 |
| Scale Up | Tăng cấu hình | Thêm CPU/RAM |
| Scale Down | Giảm cấu hình | Giảm CPU/RAM |
| Scale Out | Thêm máy chủ | Tăng số EC2 |
| Scale In | Giảm máy chủ | Giảm số EC2 |
| Load Balancer | Bộ cân bằng tải | Phân phối request |
| Target Group | Nhóm mục tiêu | Nhóm EC2 hoặc Target |
| Health Check | Kiểm tra sức khỏe | Xác định Target khỏe hay lỗi |
| Auto Scaling Group | Nhóm tự động mở rộng | Quản lý số lượng EC2 |
| Desired Capacity | Số lượng mong muốn | Số EC2 ASG cố gắng duy trì |
| Minimum Capacity | Số lượng tối thiểu | Không giảm thấp hơn mức đặt |
| Maximum Capacity | Số lượng tối đa | Không tăng vượt mức đặt |
| Multi-AZ | Nhiều AZ | Tăng khả năng chịu lỗi cấp AZ |
| Sticky Sessions | Phiên cố định | Giữ người dùng ở cùng Target |
| Cross-Zone Load Balancing | Cân bằng tải xuyên AZ | Phân phối đến Target ở nhiều AZ |
| Deregistration Delay | Thời gian gỡ Target | Cho request đang chạy hoàn tất |
| Target Tracking | Theo dõi mục tiêu | Giữ metric quanh giá trị mong muốn |
| Scheduled Scaling | Mở rộng theo lịch | Tăng/giảm theo thời gian cố định |
| Predictive Scaling | Mở rộng dự đoán | Dự báo tải từ dữ liệu lịch sử |

## 36. Những câu hỏi thường gặp khi làm bài thi

**Câu 1: Website cần phân phối HTTP request theo URL path. Chọn gì?**

Đáp án: Application Load Balancer (ALB), vì ALB hoạt động ở Layer 7 và có thể điều hướng dựa trên path.

**Câu 2: Hệ thống cần Load Balancer cho TCP với độ trễ thấp và lượng kết nối lớn. Chọn gì?**

Đáp án: Network Load Balancer (NLB).

**Câu 3: Hệ thống cần tự động thêm EC2 khi CPU tăng cao. Dùng gì?**

Đáp án: Auto Scaling Group kết hợp Scaling Policy và CloudWatch metrics/alarms phù hợp.

**Câu 4: Cần website vẫn phục vụ khi một AZ gặp sự cố. Cần làm gì?**

Đáp án: Phân bố tài nguyên dự phòng trên nhiều AZ và thiết kế các thành phần phụ thuộc cũng có khả năng chịu lỗi.

**Câu 5: Dịch vụ nào phân phối request đến nhiều EC2?**

Đáp án: Elastic Load Balancing.

**Câu 6: Dịch vụ nào quản lý số lượng EC2 tự động?**

Đáp án: Auto Scaling Group.

**Câu 7: ASG có Minimum = 2, Desired = 3, Maximum = 6. ASG có thể giảm xuống 1 EC2 không?**

Đáp án: Không, vì Minimum Capacity là 2.

---

# PHẦN X. 12 CÂU HỎI ÔN TẬP

> Đây là các câu hỏi tự biên soạn để luyện hiểu kiến thức, không phải câu hỏi chính thức hoặc câu hỏi bị sao chép từ đề thi AWS.

### Câu 1

Một website chỉ có một EC2. Nếu EC2 bị lỗi, website có thể ngừng hoạt động. Cách nào giúp giảm nguy cơ này?

A. Tăng dung lượng ổ cứng của EC2.  
B. Tạo thêm EC2 ở Availability Zone khác và thiết kế cơ chế phân phối lưu lượng.  
C. Đổi tên EC2.  
D. Tắt Health Check.

**Đáp án: B**

**Giải thích:** Nhiều EC2 ở các AZ khác nhau giúp hệ thống có máy chủ dự phòng khi một thành phần gặp sự cố.

### Câu 2

Bạn muốn website tự tăng số lượng EC2 khi lượng truy cập tăng. Dịch vụ nào phù hợp nhất?

A. Amazon S3.  
B. Auto Scaling Group.  
C. AWS IAM.  
D. Amazon Route 53.

**Đáp án: B**

**Giải thích:** ASG quản lý số lượng EC2 và có thể tăng hoặc giảm số Instance theo chính sách.

### Câu 3

Một website cần điều hướng request đến các Target dựa trên URL path như `/api` và `/images`. Bạn nên dùng gì?

A. ALB.  
B. NLB.  
C. GWLB.  
D. EBS.

**Đáp án: A**

**Giải thích:** ALB hoạt động ở Layer 7 và hỗ trợ routing dựa trên thông tin HTTP như path.

### Câu 4

Một ứng dụng cần cân bằng tải cho TCP và yêu cầu độ trễ thấp. Loại nào phù hợp?

A. ALB.  
B. NLB.  
C. S3.  
D. CloudFront.

**Đáp án: B**

**Giải thích:** NLB hoạt động ở Layer 4 và phù hợp với các trường hợp cần xử lý TCP/UDP/TLS theo cấu hình.

### Câu 5

Một ASG có Minimum = 2, Desired = 4, Maximum = 8. Khi tải giảm mạnh, ASG có thể giảm xuống 1 EC2 không?

A. Có.  
B. Không, số lượng tối thiểu là 2.  
C. Có, nếu CPU dưới 10%.  
D. Không, vì Desired luôn cố định ở 4.

**Đáp án: B**

**Giải thích:** Minimum Capacity đặt giới hạn thấp nhất là 2. Desired có thể thay đổi theo chính sách nhưng không được thấp hơn Minimum trong hoạt động bình thường.

### Câu 6

Bạn muốn hệ thống cố gắng giữ CPU trung bình quanh 50%. Chính sách nào phù hợp?

A. Target Tracking Scaling.  
B. Scheduled Scaling.  
C. Sticky Sessions.  
D. Connection Draining.

**Đáp án: A**

**Giải thích:** Target Tracking cố gắng điều chỉnh số lượng EC2 để đưa chỉ số về gần giá trị mục tiêu.

### Câu 7

Website thường có lượng truy cập cao vào 8 giờ sáng mỗi ngày. Bạn muốn tăng EC2 trước thời điểm đó theo lịch. Nên dùng gì?

A. Scheduled Scaling.  
B. NLB.  
C. Sticky Sessions.  
D. Gateway Load Balancer.

**Đáp án: A**

**Giải thích:** Scheduled Scaling thực hiện thay đổi năng lực theo lịch đã định.

### Câu 8

Một hệ thống cần phân phối lưu lượng qua nhiều firewall ảo. Loại Load Balancer nào phù hợp?

A. ALB.  
B. NLB.  
C. GWLB.  
D. CLB.

**Đáp án: C**

**Giải thích:** GWLB được thiết kế để triển khai và mở rộng các thiết bị mạng hoặc bảo mật ảo.

### Câu 9

ALB phát hiện một Target không vượt qua Health Check. Điều gì có thể xảy ra?

A. ALB tiếp tục coi Target đó là khỏe mạnh.  
B. ALB ngừng gửi request mới đến Target không khỏe mạnh.  
C. ALB tự động nâng cấp RAM của Target.  
D. ALB xóa toàn bộ Target Group.

**Đáp án: B**

**Giải thích:** Health Check giúp Load Balancer xác định Target nào đủ điều kiện nhận lưu lượng.

### Câu 10

Một ứng dụng chạy trên nhiều EC2 nhưng session chỉ lưu trong RAM của từng EC2. Vấn đề nào có thể xảy ra?

A. Người dùng có thể mất session khi request được chuyển sang EC2 khác.  
B. EC2 tự động tăng RAM.  
C. ALB không thể nhận HTTPS.  
D. S3 bị xóa dữ liệu.

**Đáp án: A**

**Giải thích:** Trạng thái lưu cục bộ trên một EC2 không tự động được chia sẻ với EC2 khác. Có thể dùng Redis hoặc giải pháp lưu session dùng chung.

### Câu 11

Bạn muốn hệ thống vẫn hoạt động khi một Availability Zone gặp sự cố. Thiết kế nào phù hợp hơn?

A. Đặt tất cả EC2 trong một AZ.  
B. Phân bố EC2 trên nhiều AZ và có cơ chế chuyển lưu lượng.  
C. Chỉ tăng dung lượng ổ cứng.  
D. Tắt Auto Scaling.

**Đáp án: B**

**Giải thích:** Multi-AZ giúp giảm phụ thuộc vào một AZ duy nhất.

### Câu 12

Phát biểu nào mô tả đúng vai trò của ELB và ASG?

A. ELB tạo EC2, ASG phân phối request.  
B. ELB phân phối request, ASG quản lý số lượng EC2.  
C. Cả hai chỉ dùng để lưu file.  
D. ASG thay thế hoàn toàn Database.

**Đáp án: B**

**Giải thích:** ELB và ASG có nhiệm vụ khác nhau nhưng thường kết hợp để xây dựng hệ thống có khả năng mở rộng và sẵn sàng cao.

---

# PHẦN XI. TỔNG KẾT CHƯƠNG 5

## 37. Chỉ cần nhớ 8 điều sau

1. **Scalability:** Hệ thống có khả năng mở rộng để xử lý nhiều công việc hơn.
2. **Elasticity:** Hệ thống tự tăng hoặc giảm tài nguyên theo nhu cầu.
3. **High Availability:** Thiết kế để giảm thời gian ngừng hoạt động.
4. **Vertical Scaling:** Nâng cấp một EC2 mạnh hơn.
5. **Horizontal Scaling:** Thêm nhiều EC2.
6. **Load Balancer:** Phân phối request đến nhiều máy chủ.
7. **Auto Scaling Group:** Tự động quản lý số lượng EC2.
8. **Multi-AZ:** Phân bố tài nguyên ở nhiều Availability Zone để giảm rủi ro khi một AZ gặp sự cố.

## 38. Sơ đồ ghi nhớ toàn chương

```text
                 HIGH AVAILABILITY
                 & SCALABILITY
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      Scalability    High Availability  Elasticity
          |              |              |
          v              v              v
     +----+----+     Multi-AZ        Auto Scaling
     |         |         |               |
     v         v         v               v
  Scale Up  Scale Out  Dự phòng       Scale In/Out
     |         |                         |
     v         v                         v
 Nâng cấp   Thêm EC2                  ASG Policy
 EC2                                  CloudWatch


              LOAD BALANCING
                    |
                    v
                   ELB
                    |
       +------------+------------+
       |            |            |
       v            v            v
      ALB          NLB          GWLB
   Layer 7       Layer 4      Layer 3
   HTTP/HTTPS    TCP/UDP/TLS  Network appliances
```

## 39. Một câu chuyện để nhớ toàn bộ chương

Hãy tưởng tượng bạn mở một cửa hàng online.

Ban đầu chỉ có một nhân viên xử lý đơn hàng. Khi khách đông, bạn thuê thêm nhân viên. Đó là **Horizontal Scaling**.

Bạn có một người điều phối đơn hàng đến các nhân viên. Đó là **Load Balancer**.

Khi cửa hàng đông hơn nữa, hệ thống tự tuyển thêm nhân viên. Khi cửa hàng vắng khách, hệ thống giảm số nhân viên. Đó là **Auto Scaling** và **Elasticity**.

Bạn bố trí nhân viên ở hai địa điểm khác nhau. Nếu một địa điểm gặp sự cố, địa điểm còn lại vẫn có thể làm việc. Đó là **Multi-AZ** và **High Availability**.

Bạn thường xuyên kiểm tra xem nhân viên nào còn làm việc tốt. Đó là **Health Check**.

Và khi cửa hàng có thể phục vụ nhiều khách hơn mà không phải xây dựng lại toàn bộ hệ thống, bạn đã có một hệ thống có khả năng **Scalability**.

---

**Kết thúc Chương 5 – High Availability & Scalability.**

**Chương tiếp theo:** RDS, Aurora & ElastiCache – Tìm hiểu cách AWS quản lý cơ sở dữ liệu và bộ nhớ đệm.
