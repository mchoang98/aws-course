# AWS CHƯƠNG 6
# RDS, Aurora & ElastiCache
## Cơ sở dữ liệu trên AWS – nơi lưu dữ liệu và cách giúp ứng dụng chạy nhanh hơn

> **Mục tiêu chương học:** Sau chương này, bạn có thể giải thích được cơ sở dữ liệu là gì, Amazon RDS giúp chúng ta những việc nào, Aurora khác RDS thông thường ra sao, ElastiCache dùng để làm gì và khi nào nên chọn từng dịch vụ.

---

## Bản đồ bài học

1. Cơ sở dữ liệu là gì?
2. Vì sao không nên tự quản lý database trên một máy EC2 trong mọi trường hợp?
3. Amazon RDS là gì?
4. Các thành phần quan trọng của RDS
5. Những loại database được RDS hỗ trợ
6. Single-AZ và Multi-AZ
7. Read Replica – bản sao dùng để đọc
8. Backup và Snapshot
9. Bảo mật RDS
10. Amazon Aurora là gì?
11. Aurora hoạt động như thế nào?
12. Aurora Serverless
13. Amazon ElastiCache là gì?
14. Cache hoạt động như thế nào?
15. Valkey, Redis OSS và Memcached
16. So sánh RDS, Aurora và ElastiCache
17. Ví dụ thiết kế một ứng dụng thực tế
18. Những lỗi thường gặp và lưu ý về chi phí
19. Kiến thức cần nhớ khi thi AWS
20. Câu hỏi ôn tập

---

# 6.1. Cơ sở dữ liệu là gì?

Hãy tưởng tượng em có một cửa hàng bán đồ chơi.

Mỗi ngày cửa hàng cần ghi lại:

- Tên món đồ chơi.
- Giá bán.
- Số lượng còn trong kho.
- Tên người mua.
- Địa chỉ giao hàng.
- Đơn hàng đã thanh toán hay chưa.

Nếu chỉ ghi tất cả vào giấy, sau một thời gian em sẽ có hàng nghìn tờ giấy. Khi muốn tìm đơn hàng của một khách hàng, em phải lật từng tờ. Việc này rất mất thời gian và dễ nhầm lẫn.

**Cơ sở dữ liệu (Database)** giống như một chiếc tủ hồ sơ điện tử. Nó giúp chúng ta lưu trữ, sắp xếp, tìm kiếm và cập nhật thông tin.

Ví dụ, một database bán hàng có thể chứa các bảng:

| Bảng | Lưu thông tin |
|---|---|
| `users` | Khách hàng |
| `products` | Sản phẩm |
| `orders` | Đơn hàng |
| `order_items` | Các sản phẩm trong từng đơn hàng |
| `payments` | Thanh toán |

## 6.1.1. Bảng, hàng và cột

Một database quan hệ thường tổ chức dữ liệu thành các bảng.

Ví dụ bảng `students`:

| id | name | age |
|---:|---|---:|
| 1 | An | 10 |
| 2 | Bình | 11 |
| 3 | Chi | 10 |

- **Bảng (Table):** giống một bảng tính Excel.
- **Cột (Column):** mô tả một loại thông tin, chẳng hạn `name` hoặc `age`.
- **Hàng (Row):** thông tin của một đối tượng cụ thể.
- **Khóa chính (Primary Key):** mã duy nhất để phân biệt từng hàng, ví dụ `id`.
- **Khóa ngoại (Foreign Key):** dùng để liên kết dữ liệu giữa các bảng.

Ví dụ: bảng `orders` có cột `user_id`. Cột này cho biết đơn hàng thuộc về khách hàng nào trong bảng `users`.

## 6.1.2. Database và Database Engine khác nhau thế nào?

Hãy tưởng tượng:

- Database là những cuốn sổ chứa thông tin.
- Database Engine là người quản lý biết cách đọc, ghi, tìm kiếm và sắp xếp những cuốn sổ đó.

Một số database engine phổ biến:

- MySQL.
- PostgreSQL.
- MariaDB.
- Microsoft SQL Server.
- Oracle Database.

Chúng có cách hoạt động và tính năng khác nhau, nhưng đều có thể giúp ứng dụng lưu trữ và truy vấn dữ liệu.

## 6.1.3. SQL là gì?

SQL là ngôn ngữ dùng để nói chuyện với các hệ quản trị cơ sở dữ liệu quan hệ.

Ví dụ:

```sql
-- Lấy danh sách tất cả học sinh
SELECT * FROM students;

-- Tìm học sinh có id bằng 1
SELECT * FROM students
WHERE id = 1;

-- Thêm một học sinh mới
INSERT INTO students (name, age)
VALUES ('Minh', 12);

-- Cập nhật tuổi của học sinh
UPDATE students
SET age = 13
WHERE id = 1;
```

Em không cần học thuộc tất cả câu lệnh ngay bây giờ. Chỉ cần nhớ: ứng dụng gửi câu lệnh SQL, database xử lý câu lệnh và trả kết quả.

---

# 6.2. Vì sao cần dịch vụ quản lý database trên AWS?

Giả sử em có một website bán hàng chạy trên EC2.

Ban đầu, em cài MySQL trực tiếp lên máy EC2 đó. Cách này hoàn toàn có thể hoạt động. Nhưng em sẽ phải tự lo nhiều công việc:

1. Cài đặt MySQL.
2. Cấu hình ổ cứng.
3. Tạo tài khoản và phân quyền.
4. Sao lưu dữ liệu.
5. Cập nhật phiên bản và bản vá bảo mật.
6. Theo dõi CPU, RAM và dung lượng ổ cứng.
7. Khôi phục dữ liệu khi có sự cố.
8. Tìm cách chuyển sang máy khác khi máy chủ bị hỏng.

Nếu database có vấn đề, website có thể không hoạt động và khách hàng không thể mua hàng.

AWS cung cấp dịch vụ quản lý database để giảm bớt những công việc này.

Thay vì tự mua một chiếc máy tính, cài database và chăm sóc nó mỗi ngày, em có thể thuê dịch vụ database được AWS quản lý.

Đó là lý do Amazon RDS ra đời.

---

# 6.3. Amazon RDS là gì?

**Amazon RDS (Amazon Relational Database Service)** là dịch vụ giúp chúng ta tạo, vận hành và quản lý cơ sở dữ liệu quan hệ trên AWS.

Nói đơn giản:

> RDS giống như việc thuê một chiếc tủ hồ sơ điện tử đã có người quản lý. Em tập trung sử dụng dữ liệu, còn AWS giúp quản lý nhiều công việc liên quan đến máy chủ database.

Khi sử dụng RDS, AWS có thể hỗ trợ:

- Tạo và cấp phát máy chủ database.
- Quản lý phần cứng bên dưới.
- Thực hiện các hoạt động sao lưu tự động.
- Hỗ trợ cập nhật và bảo trì database theo cấu hình.
- Theo dõi tình trạng hoạt động.
- Hỗ trợ triển khai nhiều Availability Zone để tăng tính sẵn sàng.
- Hỗ trợ mã hóa dữ liệu.

Tuy nhiên, **RDS không có nghĩa là AWS làm tất cả thay em**.

Em vẫn cần chịu trách nhiệm:

- Thiết kế bảng và cấu trúc dữ liệu.
- Viết câu truy vấn SQL.
- Tạo và quản lý người dùng database.
- Phân quyền truy cập phù hợp.
- Tối ưu câu truy vấn và chỉ mục (index).
- Chọn cấu hình database phù hợp.
- Kiểm tra dữ liệu và chiến lược khôi phục.
- Kiểm soát chi phí.

## 6.3.1. RDS khác với việc tự cài MySQL trên EC2 thế nào?

| Công việc | MySQL tự cài trên EC2 | Amazon RDS |
|---|---|---|
| Tạo máy chủ | Em tự tạo EC2 | AWS cấp phát hạ tầng database |
| Cài database | Em tự cài và cấu hình | AWS hỗ trợ tạo database engine |
| Quản lý hệ điều hành | Em chịu trách nhiệm | AWS quản lý phần lớn hệ điều hành bên dưới |
| Sao lưu | Em tự xây dựng nếu cần | Có tính năng backup tự động |
| Cập nhật database | Em chủ động quản lý | Có tùy chọn bảo trì và cập nhật do AWS quản lý |
| Quyền kiểm soát máy chủ | Cao hơn | Bị giới hạn ở mức dịch vụ |
| Công việc vận hành | Nhiều hơn | Ít hơn |

**Ví dụ dễ hiểu:**

- EC2 tự cài MySQL: em thuê một căn nhà trống, tự mua tủ, tự lắp đặt và tự sửa chữa.
- RDS: em thuê một căn phòng đã có tủ hồ sơ và có đội ngũ quản lý tòa nhà hỗ trợ các công việc vận hành.

RDS phù hợp khi em muốn tập trung phát triển ứng dụng thay vì dành quá nhiều thời gian quản lý máy chủ database.

## 6.3.2. RDS có phải là một database không?

Cần phân biệt ba khái niệm:

- **RDS:** dịch vụ AWS dùng để quản lý database.
- **MySQL/PostgreSQL:** phần mềm database engine.
- **Database:** nơi lưu dữ liệu của ứng dụng.

Ví dụ: em tạo một RDS instance và chọn engine là MySQL. Khi đó AWS quản lý một máy chủ database MySQL cho em.

---

# 6.4. Các thành phần quan trọng của RDS

Khi tạo một database trên RDS, em sẽ gặp một số khái niệm sau.

## 6.4.1. DB Instance

DB Instance là môi trường máy chủ database mà RDS cung cấp.

Có thể hình dung DB Instance như một chiếc máy tính chuyên dùng để chạy database.

Nó có:

- CPU để xử lý câu lệnh.
- RAM để làm việc với dữ liệu.
- Storage để lưu dữ liệu.
- Database Engine như MySQL hoặc PostgreSQL.

Ví dụ tên một DB Instance:

```text
school-database
```

Tên này giúp em nhận biết database trong AWS Console.

## 6.4.2. DB Instance Class

DB Instance Class quyết định cấu hình phần cứng của database.

Ví dụ:

- Máy nhỏ: CPU và RAM ít, phù hợp ứng dụng nhỏ.
- Máy lớn: CPU và RAM nhiều hơn, xử lý được nhiều công việc hơn.

Nếu database có nhiều người truy cập cùng lúc, em có thể cần nâng cấp instance class.

Đây là một hình thức **Scale Up (Vertical Scaling)** – tăng sức mạnh của một máy chủ.

## 6.4.3. Storage

Storage là nơi lưu dữ liệu lâu dài.

Ví dụ:

- Thông tin khách hàng.
- Đơn hàng.
- Sản phẩm.
- Lịch sử thanh toán.

Đừng nhầm RAM với Storage:

- RAM giống mặt bàn làm việc: dùng để xử lý dữ liệu tạm thời.
- Storage giống tủ hồ sơ: dùng để lưu dữ liệu lâu dài.

RDS có nhiều lựa chọn lưu trữ. Loại ổ đĩa và mức hiệu năng cần chọn tùy theo lượng dữ liệu, tốc độ đọc/ghi và ngân sách.

## 6.4.4. Endpoint

Ứng dụng cần biết địa chỉ database để kết nối.

RDS cung cấp một địa chỉ gọi là **Endpoint**.

Ví dụ minh họa:

```text
school-db.abc123.ap-southeast-1.rds.amazonaws.com
```

Ứng dụng sẽ sử dụng endpoint, port, username và password để kết nối.

Ví dụ cấu hình MySQL:

```env
DB_HOST=school-db.abc123.ap-southeast-1.rds.amazonaws.com
DB_PORT=3306
DB_USERNAME=app_user
DB_PASSWORD=your-secret-password
DB_NAME=school
```

Đây chỉ là ví dụ minh họa. Không được đưa mật khẩu thật lên GitHub hoặc ghi trực tiếp vào mã nguồn.

## 6.4.5. Port

Port là cổng giao tiếp của database.

Một số port phổ biến:

| Database Engine | Port mặc định |
|---|---:|
| MySQL | 3306 |
| MariaDB | 3306 |
| PostgreSQL | 5432 |
| Microsoft SQL Server | 1433 |
| Oracle Database | 1521 |

Ví dụ: khi ứng dụng muốn kết nối MySQL, nó thường kết nối đến port `3306`.

## 6.4.6. Parameter Group

Parameter Group là nhóm cấu hình các tham số của database engine.

Ví dụ, một số tham số có thể ảnh hưởng đến:

- Số lượng kết nối.
- Bộ nhớ dành cho một số hoạt động.
- Cách database ghi log.
- Hành vi của database engine.

Có thể hiểu Parameter Group giống bảng cài đặt nâng cao của MySQL hoặc PostgreSQL.

Không nên thay đổi tham số một cách tùy tiện. Cần hiểu tác dụng và kiểm tra khả năng tương thích của engine trước khi chỉnh sửa.

---

# 6.5. Những loại database RDS hỗ trợ

RDS hỗ trợ nhiều database engine quan hệ.

| Engine | Giải thích đơn giản |
|---|---|
| MySQL | Phổ biến trong các website và ứng dụng web |
| PostgreSQL | Có nhiều tính năng mạnh, phù hợp truy vấn phức tạp |
| MariaDB | Hệ quản trị quan hệ có nguồn gốc từ MySQL |
| Oracle Database | Thường gặp trong hệ thống doanh nghiệp lớn |
| Microsoft SQL Server | Phù hợp với nhiều hệ thống sử dụng công nghệ Microsoft |
| IBM Db2 | Thường được dùng trong các hệ thống doanh nghiệp |

Lưu ý:

- RDS không bắt buộc em phải sử dụng MySQL.
- Khi tạo database, em cần chọn engine phù hợp với ứng dụng.
- Mỗi engine có phiên bản, giới hạn và tính năng được hỗ trợ khác nhau.
- Không phải mọi tính năng của các engine đều giống nhau.

**Ví dụ:** Nếu ứng dụng đang sử dụng MySQL, em có thể chọn Amazon RDS for MySQL để giảm công việc quản lý hạ tầng mà không cần chuyển sang một loại database hoàn toàn khác.

---

# 6.6. Single-AZ và Multi-AZ

Đây là phần rất quan trọng khi học RDS.

Trước tiên, hãy nhớ:

- **AZ (Availability Zone):** một khu vực trung tâm dữ liệu độc lập trong một AWS Region.
- Một Region có thể có nhiều AZ.
- Đặt hệ thống ở nhiều AZ giúp giảm nguy cơ toàn bộ hệ thống ngừng hoạt động khi một AZ gặp sự cố.

## 6.6.1. Single-AZ là gì?

Single-AZ nghĩa là database được triển khai trong một Availability Zone.

Hãy tưởng tượng cửa hàng chỉ có một chiếc tủ hồ sơ ở một tòa nhà.

Nếu tòa nhà gặp sự cố, em có thể không truy cập được tủ hồ sơ đó.

```text
AWS Region
└── AZ A
    └── RDS Primary Database
```

Ưu điểm:

- Cấu hình đơn giản.
- Thường có chi phí thấp hơn Multi-AZ.

Nhược điểm:

- Khả năng chịu lỗi ở cấp Availability Zone thấp hơn.
- Nếu instance hoặc AZ gặp sự cố, database có thể bị gián đoạn trong thời gian khắc phục.

Single-AZ thường phù hợp với môi trường học tập, thử nghiệm hoặc ứng dụng không yêu cầu mức sẵn sàng cao.

## 6.6.2. Multi-AZ là gì?

Multi-AZ giúp tăng khả năng sẵn sàng của database bằng cách triển khai thêm một bản dự phòng ở Availability Zone khác.

Ví dụ:

```text
                 AWS Region
                /          \
             AZ A          AZ B
              |              |
        Primary DB  ---->  Standby DB
              |
        Ứng dụng đọc/ghi
```

Trong mô hình Multi-AZ DB instance truyền thống:

1. Ứng dụng kết nối đến database chính.
2. Database chính xử lý các thao tác đọc và ghi.
3. RDS sao chép dữ liệu sang Standby ở AZ khác.
4. Nếu database chính gặp sự cố, RDS có thể tự động chuyển sang Standby bằng cơ chế failover.

**Điểm rất quan trọng:** Standby trong mô hình Multi-AZ DB instance truyền thống không được thiết kế để ứng dụng dùng làm nơi đọc dữ liệu thông thường.

Mục đích chính của Standby là tăng tính sẵn sàng và hỗ trợ failover, không phải tăng khả năng xử lý truy vấn đọc.

## 6.6.3. Multi-AZ DB Cluster có gì khác?

Ngoài mô hình Multi-AZ DB instance truyền thống, RDS còn có mô hình Multi-AZ DB cluster.

Mô hình này gồm:

- Một Writer DB Instance.
- Hai Reader DB Instance.
- Ba Availability Zone khác nhau trong cùng Region.

Hai Reader có thể phục vụ truy vấn đọc, đồng thời giúp tăng khả năng sẵn sàng.

Mô hình Multi-AZ DB cluster có phạm vi hỗ trợ engine khác với Multi-AZ DB instance; cần kiểm tra engine và Region trước khi chọn.

## 6.6.4. Khi nào nên dùng Multi-AZ?

Ví dụ:

- Website bán vé cần hoạt động ổn định.
- Hệ thống thanh toán.
- Ứng dụng quản lý học sinh.
- Hệ thống quản lý đơn hàng quan trọng.

Nếu database tạm thời không hoạt động, người dùng có thể không đăng nhập, không đặt hàng hoặc không thanh toán được.

Multi-AZ giúp giảm nguy cơ gián đoạn do sự cố ở một Availability Zone.

### Ghi nhớ

**Multi-AZ = ưu tiên High Availability (tính sẵn sàng cao).**

Đừng nhầm Multi-AZ DB instance truyền thống với Read Replica. Hai tính năng có mục đích khác nhau.

---

# 6.7. Read Replica – Bản sao dùng để đọc

Hãy tưởng tượng một thư viện có một nhân viên duy nhất.

Mọi người đều đến nhờ nhân viên này:

- Tìm sách.
- Mượn sách.
- Trả sách.
- Tra cứu thông tin.

Khi số lượng người tăng lên, nhân viên chính sẽ rất bận.

Giải pháp: tạo thêm một bản sao danh mục sách để nhiều người có thể tra cứu cùng lúc.

Đó chính là ý tưởng của **Read Replica**.

## 6.7.1. Read Replica hoạt động như thế nào?

Read Replica là một bản sao database được dùng để xử lý các truy vấn đọc.

```text
                 Ứng dụng
                    |
             ----------------
             |              |
          Ghi dữ liệu     Đọc dữ liệu
             |              |
        Primary DB       Read Replica
             |              ^
             |              |
             ---- Sao chép --
```

Ví dụ:

- `INSERT`: ghi dữ liệu vào Primary.
- `UPDATE`: cập nhật dữ liệu ở Primary.
- `DELETE`: xóa dữ liệu ở Primary.
- `SELECT`: có thể chuyển sang Read Replica nếu phù hợp.

Khi dữ liệu ở Primary thay đổi, RDS sao chép thay đổi sang Read Replica.

Với Read Replica của RDS thông thường, quá trình sao chép thường là **asynchronous replication (sao chép bất đồng bộ)**.

Điều này có nghĩa là dữ liệu ở Read Replica có thể chậm hơn Primary một khoảng thời gian ngắn.

Hiện tượng này gọi là **Replication Lag**.

## 6.7.2. Tại sao cần Read Replica?

Ví dụ website có 100.000 người truy cập:

- 95.000 lượt chỉ xem thông tin sản phẩm.
- 5.000 lượt thực hiện các thao tác ghi dữ liệu.

Nếu tất cả truy vấn đều gửi đến một database, database chính có thể phải xử lý quá nhiều yêu cầu đọc.

Ta có thể:

- Gửi các truy vấn ghi đến Primary.
- Gửi các truy vấn đọc phù hợp đến Read Replica.

Nhờ đó, Primary có thêm tài nguyên để xử lý các thao tác ghi.

## 6.7.3. Read Replica có tự động thay thế Primary không?

Không nên mặc định như vậy.

Read Replica thông thường chủ yếu được tạo để mở rộng khả năng đọc. Nếu Primary gặp sự cố, em có thể cần chủ động promote Read Replica thành một database độc lập, tùy tình huống và cấu hình.

Điều này khác với Multi-AZ DB instance truyền thống, nơi RDS quản lý Standby và thực hiện failover tự động.

## 6.7.4. Read Replica có thể nằm ở Region khác không?

Có.

RDS hỗ trợ tạo Read Replica ở một AWS Region khác trong những trường hợp được hỗ trợ.

Ví dụ:

```text
Region Singapore
└── Primary Database
          |
          | Sao chép dữ liệu
          v
Region Tokyo
└── Read Replica
```

Điều này có thể hữu ích khi:

- Người dùng ở khu vực khác cần đọc dữ liệu gần họ hơn.
- Cần hỗ trợ kế hoạch khôi phục sau thảm họa.
- Muốn có bản sao dữ liệu ở Region khác.

### Ghi nhớ

**Read Replica = Scale Read (mở rộng khả năng đọc).**

**Multi-AZ DB instance truyền thống = High Availability (tăng khả năng sẵn sàng).**

Đây là một trong những điểm dễ nhầm nhất khi học AWS.

---

# 6.8. Backup và Snapshot

Hãy tưởng tượng em có một cuốn sổ ghi toàn bộ đơn hàng.

Một ngày em vô tình xóa mất một trang. Nếu không có bản sao, em có thể mất dữ liệu quan trọng.

Backup giúp chúng ta có cơ hội khôi phục dữ liệu khi xảy ra sự cố.

RDS có hai khái niệm quan trọng: Automated Backup và Snapshot.

## 6.8.1. Automated Backup

Automated Backup là cơ chế sao lưu tự động do RDS hỗ trợ.

Tùy cấu hình và loại triển khai, RDS có thể:

- Tạo bản sao lưu định kỳ.
- Lưu dữ liệu backup trong thời gian được cấu hình.
- Hỗ trợ khôi phục database về một thời điểm trong khoảng thời gian cho phép.

Khả năng khôi phục về một thời điểm cụ thể được gọi là **Point-in-Time Recovery (PITR)**.

Ví dụ:

- 10:00: database có 1.000 đơn hàng.
- 10:30: một lỗi phần mềm xóa nhầm dữ liệu.
- Em có thể khôi phục database về thời điểm trước khi xảy ra lỗi, nếu thời điểm đó nằm trong khoảng backup được hỗ trợ.

## 6.8.2. Snapshot

Snapshot là một bản chụp dữ liệu database tại một thời điểm.

Em có thể tạo Snapshot thủ công trước khi:

- Nâng cấp phiên bản database.
- Thay đổi cấu hình quan trọng.
- Chạy một thao tác có nguy cơ ảnh hưởng dữ liệu.

Ví dụ:

```text
Trước khi nâng cấp database
          |
          v
    Tạo Snapshot
          |
          v
    Nâng cấp database
          |
          v
     Kiểm tra hệ thống
```

Nếu có sự cố, Snapshot có thể được dùng để tạo lại database.

Snapshot thủ công thường tiếp tục được giữ cho đến khi em xóa nó. Vì vậy, cần quản lý Snapshot để tránh phát sinh chi phí không cần thiết.

## 6.8.3. Automated Backup và Snapshot khác nhau thế nào?

| Tiêu chí | Automated Backup | Manual Snapshot |
|---|---|---|
| Cách tạo | Tự động theo cấu hình | Người dùng chủ động tạo |
| Mục đích | Sao lưu định kỳ, hỗ trợ PITR | Lưu một trạng thái cụ thể |
| Thời gian lưu | Theo retention period | Thường giữ đến khi xóa |
| Tình huống | Khôi phục sau lỗi dữ liệu | Trước khi nâng cấp hoặc thay đổi lớn |

**Lưu ý:** Backup không thay thế hoàn toàn cho thiết kế High Availability. Multi-AZ cũng không thay thế cho backup. Một hệ thống tốt cần lựa chọn cả khả năng dự phòng lẫn chiến lược sao lưu phù hợp.

---

# 6.9. Bảo mật Amazon RDS

Database thường chứa thông tin rất quan trọng:

- Tên và email khách hàng.
- Mật khẩu đã mã hóa hoặc thông tin xác thực.
- Đơn hàng.
- Thông tin thanh toán.
- Dữ liệu nội bộ.

Nếu database bị truy cập trái phép, hậu quả có thể rất nghiêm trọng.

Có một số lớp bảo vệ cần chú ý.

## 6.9.1. Đặt database trong Private Subnet

Hãy tưởng tượng:

- Website là quầy tiếp khách.
- Database là kho lưu hồ sơ.
- Khách hàng có thể truy cập quầy tiếp khách.
- Nhưng khách hàng không được tự ý đi vào kho hồ sơ.

Tương tự, kiến trúc phổ biến là:

```text
Internet
   |
   v
Application / Load Balancer
   |
   v
EC2 hoặc Backend trong VPC
   |
   v
RDS trong Private Subnet
```

Người dùng Internet không nên kết nối trực tiếp đến database.

Backend trong VPC sẽ kết nối đến RDS.

## 6.9.2. Security Group

Security Group hoạt động như một chiếc cổng kiểm soát kết nối mạng.

Ví dụ:

- Security Group của Backend: `sg-backend`.
- Security Group của Database: `sg-database`.

Ta cấu hình Inbound Rule của `sg-database`:

| Type | Port | Source |
|---|---:|---|
| MySQL/Aurora | 3306 | `sg-backend` |

Điều đó có nghĩa là chỉ những tài nguyên thuộc Security Group Backend được phép kết nối vào cổng MySQL của database.

Không nên mở port database cho toàn bộ Internet bằng `0.0.0.0/0` trong môi trường production.

## 6.9.3. Encryption

RDS hỗ trợ mã hóa dữ liệu theo nhiều cách:

- **Encryption at rest:** mã hóa dữ liệu được lưu trên ổ đĩa và các bản backup được hỗ trợ.
- **Encryption in transit:** mã hóa dữ liệu khi truyền qua mạng, thường thông qua TLS/SSL.

Có thể hiểu:

- Khi hồ sơ nằm trong tủ: cần khóa tủ.
- Khi hồ sơ được gửi qua đường vận chuyển: cần bảo vệ nội dung trên đường đi.

## 6.9.4. Quản lý mật khẩu

Không nên:

- Ghi mật khẩu database trực tiếp vào source code.
- Commit file `.env` chứa mật khẩu lên GitHub.
- Dùng chung tài khoản quản trị cho mọi ứng dụng.
- Cấp quyền cao hơn mức ứng dụng cần.

Có thể dùng AWS Secrets Manager hoặc giải pháp quản lý secrets phù hợp để lưu và quản lý thông tin đăng nhập.

---

# 6.10. Amazon Aurora là gì?

**Amazon Aurora** là một relational database engine được AWS quản lý, tương thích với MySQL và PostgreSQL.

Nói đơn giản:

- RDS giúp em chạy các database engine như MySQL và PostgreSQL mà không phải tự quản lý toàn bộ hạ tầng.
- Aurora là một database engine do AWS xây dựng, có khả năng tương thích với MySQL và PostgreSQL, đồng thời có kiến trúc lưu trữ và triển khai riêng.

Aurora cũng được quản lý thông qua Amazon RDS.

## 6.10.1. Aurora khác MySQL thông thường thế nào?

Hãy tưởng tượng:

- MySQL thông thường giống một chiếc xe phổ biến, có nhiều người biết cách sử dụng.
- Aurora giống một chiếc xe được thiết kế lại với hệ thống động cơ và cách phân phối công việc riêng, nhưng vẫn tương thích với nhiều thao tác và công cụ quen thuộc của MySQL.

Aurora được thiết kế để tận dụng hạ tầng AWS.

Một số đặc điểm:

- Tương thích với MySQL hoặc PostgreSQL.
- Có kiến trúc lưu trữ phân tán.
- Có thể triển khai nhiều instance trong một cluster.
- Có cơ chế tự động thay thế Writer khi gặp sự cố trong các tình huống được hỗ trợ.
- Có Reader Endpoint để phân phối kết nối đọc.
- Có tùy chọn Aurora Serverless.

Không nên hiểu rằng mọi ứng dụng MySQL/PostgreSQL đều có thể chuyển sang Aurora mà không cần kiểm tra. Cần kiểm tra phiên bản tương thích, extension, tính năng và cấu hình của ứng dụng.

## 6.10.2. Aurora lưu dữ liệu như thế nào?

Đây là một điểm nổi bật của Aurora.

Aurora sử dụng một **cluster volume** – một vùng lưu trữ dùng chung cho các database instance trong cluster.

Dữ liệu được sao chép trên nhiều Availability Zone trong cùng Region. Kiến trúc lưu trữ Aurora duy trì sáu bản sao dữ liệu trên ba AZ.

Minh họa đơn giản:

```text
                  Aurora Cluster
                       |
                Shared Storage
                       |
          ---------------------------
          |            |            |
         AZ A         AZ B         AZ C
       Copies        Copies        Copies
```

Hình minh họa chỉ nhằm giải thích ý tưởng, không mô tả đầy đủ cấu trúc vật lý của hệ thống lưu trữ.

Nhờ kiến trúc này, Aurora có thể tăng độ bền dữ liệu và hỗ trợ khả năng sẵn sàng cao.

## 6.10.3. Aurora Cluster gồm những gì?

Một Aurora DB Cluster thường có:

- **Writer Instance:** instance chính, xử lý thao tác ghi và cũng có thể xử lý đọc.
- **Reader Instances:** các instance phục vụ đọc, giúp mở rộng khả năng đọc và hỗ trợ failover.
- **Cluster Volume:** vùng lưu trữ dữ liệu dùng chung.
- **Endpoints:** địa chỉ kết nối giúp ứng dụng truy cập đúng loại instance.

Ví dụ:

```text
                    Aurora Cluster
                           |
             -----------------------------
             |                           |
        Writer Instance             Reader Instances
             |                           |
       INSERT / UPDATE                 SELECT
       DELETE / SELECT                 SELECT
             |                           |
             -------- Cluster Volume -----
```

## 6.10.4. Aurora Endpoints

Aurora có nhiều loại endpoint. Hai loại cần nhớ nhất:

### Cluster Endpoint

Cluster Endpoint thường trỏ đến Writer Instance hiện tại.

Ứng dụng dùng endpoint này cho các thao tác ghi như:

- `INSERT`
- `UPDATE`
- `DELETE`

Khi Writer thay đổi sau failover, endpoint giúp ứng dụng không phải tự ghi cứng địa chỉ của từng máy chủ.

### Reader Endpoint

Reader Endpoint dùng để phân phối các kết nối đọc đến các Reader Instance có trong cluster.

Ứng dụng có thể dùng nó cho các truy vấn như:

```sql
SELECT * FROM products;
```

Reader Endpoint giúp phân phối tải đọc, nhưng không có nghĩa mọi truy vấn đọc đều lập tức nhìn thấy dữ liệu mới nhất trong mọi tình huống. Ứng dụng cần hiểu đặc tính nhất quán và độ trễ phù hợp với nhu cầu của mình.

## 6.10.5. Aurora Replicas

Aurora Replica là các Reader Instance trong Aurora Cluster.

Chúng có hai vai trò quan trọng:

1. Giúp xử lý thêm các truy vấn đọc.
2. Có thể được nâng cấp thành Writer khi Writer hiện tại gặp sự cố.

Đây là điểm khác biệt đáng chú ý khi so sánh Aurora với cách triển khai Read Replica thông thường của RDS.

---

# 6.11. Aurora Serverless

Thông thường, khi tạo database, em cần chọn cấu hình máy chủ.

Ví dụ:

- CPU bao nhiêu?
- RAM bao nhiêu?
- Instance class nào?
- Có cần tăng cấu hình không?

Nhưng có lúc ứng dụng không hoạt động liên tục với cùng một lượng truy cập.

Ví dụ:

- Ban ngày có 10.000 người truy cập.
- Ban đêm chỉ có 100 người truy cập.
- Trong một sự kiện đặc biệt, lượng truy cập tăng đột ngột.

Nếu luôn giữ cấu hình rất lớn, em có thể phải trả tiền cho tài nguyên không sử dụng hết.

**Aurora Serverless** giúp đơn giản hóa việc quản lý năng lực xử lý database bằng cách tự động điều chỉnh capacity theo nhu cầu trong phạm vi cấu hình cho phép.

Có thể hình dung:

- Ít người dùng: database sử dụng ít capacity hơn.
- Nhiều người dùng: database có thể tăng capacity.
- Khi nhu cầu giảm: capacity có thể giảm theo.

## 6.11.1. Aurora Serverless có nghĩa là không có máy chủ?

Không.

Serverless không có nghĩa là hệ thống không dùng máy chủ.

Nó có nghĩa là người dùng không cần trực tiếp quản lý hoặc lựa chọn toàn bộ máy chủ bên dưới theo cách truyền thống.

AWS vẫn sử dụng hạ tầng máy chủ để chạy database.

## 6.11.2. Khi nào nên cân nhắc Aurora Serverless?

- Ứng dụng có lượng truy cập thay đổi.
- Môi trường phát triển hoặc thử nghiệm có nhu cầu không ổn định.
- Ứng dụng có những thời điểm cao điểm và thấp điểm rõ ràng.
- Muốn giảm công việc dự đoán capacity.

Tuy nhiên, Serverless không mặc định luôn rẻ hơn cấu hình cố định. Cần đo tải thực tế, kiểm tra mức capacity tối thiểu/tối đa và tính chi phí trước khi triển khai.

---

# 6.12. Amazon ElastiCache là gì?

Đây là dịch vụ thứ ba của chương.

**Amazon ElastiCache** là dịch vụ cache được AWS quản lý. Nó giúp lưu dữ liệu trong bộ nhớ để ứng dụng có thể lấy dữ liệu thường xuyên sử dụng nhanh hơn.

Để hiểu ElastiCache, trước tiên cần hiểu Cache.

## 6.12.1. Cache là gì?

Hãy tưởng tượng em thường xuyên hỏi mẹ:

- Hôm nay ăn gì?
- Mấy giờ đi học?
- Cặp sách để ở đâu?

Nếu lần nào mẹ cũng phải mở một cuốn sổ lớn để tìm câu trả lời, việc đó sẽ mất thời gian.

Nhưng nếu em ghi những câu trả lời thường dùng lên một tờ giấy nhỏ đặt ngay trên bàn, em có thể xem rất nhanh.

Tờ giấy nhỏ đó giống như Cache.

Trong máy tính:

- Database là nơi lưu dữ liệu chính.
- Cache là nơi giữ một số dữ liệu để truy cập nhanh hơn.

Cache thường sử dụng RAM – bộ nhớ có tốc độ truy cập nhanh.

## 6.12.2. Vì sao cần Cache?

Ví dụ website bán hàng có 10.000 người cùng xem thông tin một sản phẩm.

Nếu mỗi người đều gửi truy vấn đến database, database phải xử lý rất nhiều yêu cầu giống nhau.

Ta có thể lưu thông tin sản phẩm thường xuyên được xem trong cache.

```text
Người dùng
    |
    v
Ứng dụng
    |
    v
  Cache
    |
    | Không có dữ liệu
    v
 Database
```

Khi cache đã có dữ liệu, ứng dụng có thể trả kết quả mà không cần truy vấn database.

Lợi ích:

- Giảm số lần truy vấn database.
- Tăng tốc độ phản hồi.
- Giảm tải cho database.
- Hỗ trợ ứng dụng xử lý nhiều yêu cầu hơn.

## 6.12.3. Cache Hit và Cache Miss

Hai khái niệm rất quan trọng:

### Cache Hit

Dữ liệu đã có trong cache.

Ví dụ:

1. Người dùng yêu cầu xem sản phẩm có ID `100`.
2. Ứng dụng kiểm tra cache.
3. Cache đã có dữ liệu sản phẩm `100`.
4. Cache trả dữ liệu ngay.

Đây gọi là Cache Hit.

### Cache Miss

Dữ liệu chưa có trong cache hoặc đã hết hạn.

Ví dụ:

1. Người dùng yêu cầu sản phẩm có ID `200`.
2. Ứng dụng kiểm tra cache.
3. Cache không tìm thấy dữ liệu.
4. Ứng dụng truy vấn database.
5. Database trả dữ liệu.
6. Ứng dụng lưu dữ liệu vào cache để lần sau dùng lại.

Đây gọi là Cache Miss.

## 6.12.4. Cache hoạt động theo mô hình Lazy Loading

Một cách sử dụng cache phổ biến là Lazy Loading, còn gọi là Cache-Aside.

Quy trình:

```text
Người dùng yêu cầu dữ liệu
          |
          v
   Kiểm tra Cache
          |
     Có dữ liệu?
       /    \
     Có      Không
     |         |
     v         v
 Trả dữ liệu  Đọc Database
               |
               v
          Lưu vào Cache
               |
               v
           Trả dữ liệu
```

Ví dụ:

```javascript
async function getProduct(productId) {
  const cacheKey = `product:${productId}`;

  // 1. Kiểm tra cache
  const cachedProduct = await redis.get(cacheKey);

  if (cachedProduct) {
    return JSON.parse(cachedProduct);
  }

  // 2. Không có trong cache thì đọc database
  const product = await database.findProduct(productId);

  if (!product) {
    return null;
  }

  // 3. Lưu kết quả vào cache trong 5 phút
  await redis.set(
    cacheKey,
    JSON.stringify(product),
    "EX",
    300
  );

  return product;
}
```

Đây là ví dụ minh họa theo phong cách Redis client. Cú pháp cụ thể có thể khác tùy thư viện và phiên bản đang sử dụng.

## 6.12.5. TTL là gì?

TTL là viết tắt của Time To Live.

TTL cho biết dữ liệu được phép tồn tại trong cache bao lâu trước khi hết hạn.

Ví dụ:

```text
Key: product:100
Value: Thông tin sản phẩm
TTL: 300 giây
```

Sau 300 giây, dữ liệu có thể hết hạn và ứng dụng cần lấy dữ liệu mới từ database khi có yêu cầu tiếp theo.

TTL giúp hạn chế việc cache giữ dữ liệu cũ quá lâu.

Tuy nhiên, TTL không giải quyết mọi vấn đề nhất quán dữ liệu. Khi dữ liệu quan trọng thay đổi, ứng dụng có thể cần chủ động cập nhật hoặc xóa cache liên quan.

---

# 6.13. Valkey, Redis OSS và Memcached

Amazon ElastiCache hỗ trợ ba cache engine chính:

- Valkey.
- Redis OSS.
- Memcached.

Đây là những công nghệ được dùng để lưu trữ và truy xuất dữ liệu trong bộ nhớ.

## 6.13.1. Redis OSS là gì?

Redis OSS là một hệ thống lưu trữ dữ liệu trong bộ nhớ, thường được dùng làm cache.

Redis OSS hỗ trợ nhiều kiểu dữ liệu, chẳng hạn:

- String.
- Hash.
- List.
- Set.
- Sorted Set.

Ví dụ:

```text
SET user:1:name "An"
GET user:1:name
```

Kết quả:

```text
"An"
```

Redis OSS còn có nhiều khả năng khác tùy phiên bản và cấu hình.

Trong hệ thống web, Redis thường được dùng để:

- Cache dữ liệu.
- Lưu session.
- Đếm lượt truy cập.
- Giới hạn số lần gọi API (rate limiting).
- Lưu dữ liệu tạm thời.
- Hỗ trợ hàng đợi hoặc xử lý thông điệp khi ứng dụng và thư viện được thiết kế phù hợp.

## 6.13.2. Valkey là gì?

Valkey là một dự án lưu trữ dữ liệu trong bộ nhớ mã nguồn mở, tương thích với nhiều cách sử dụng và giao thức quen thuộc của Redis OSS.

AWS ElastiCache hỗ trợ Valkey như một cache engine.

Nếu em đã từng làm việc với Redis, nhiều khái niệm và cách thao tác sẽ khá quen thuộc khi tìm hiểu Valkey. Tuy nhiên, vẫn cần kiểm tra khả năng tương thích của client, lệnh và tính năng trước khi chuyển đổi.

## 6.13.3. Memcached là gì?

Memcached là một hệ thống cache trong bộ nhớ, có thiết kế đơn giản.

Nó phù hợp với những trường hợp cần lưu các đối tượng dữ liệu tạm thời để truy xuất nhanh.

Ví dụ:

- Cache thông tin sản phẩm.
- Cache kết quả truy vấn.
- Cache nội dung trang web.

Memcached thường phù hợp khi cần một hệ thống cache đơn giản và có thể mở rộng bằng cách thêm hoặc bớt node.

## 6.13.4. So sánh Valkey/Redis OSS và Memcached

| Tiêu chí | Valkey / Redis OSS | Memcached |
|---|---|---|
| Lưu trữ trong RAM | Có | Có |
| Kiểu dữ liệu | Nhiều cấu trúc dữ liệu | Đơn giản hơn |
| Mục đích | Cache và nhiều tình huống lưu dữ liệu trong bộ nhớ | Cache đơn giản |
| Tính năng nâng cao | Phong phú, tùy engine và phiên bản | Tập trung vào cache |
| Phù hợp | Session, counter, leaderboard, cache phức tạp | Cache object đơn giản |

Không có engine nào tốt nhất cho mọi trường hợp. Cần chọn dựa trên loại dữ liệu, tính năng cần dùng, khả năng mở rộng, độ bền dữ liệu và chi phí.

## 6.13.5. ElastiCache có những kiểu triển khai nào?

AWS cho phép triển khai ElastiCache theo hai hướng chính:

### Serverless Cache

- Không cần tự chọn và quản lý số lượng node theo cách truyền thống.
- AWS tự điều chỉnh tài nguyên theo nhu cầu trong phạm vi dịch vụ.
- Phù hợp khi muốn giảm công việc quản lý capacity.

### Node-based Cluster

- Em chọn loại node và số lượng node.
- Có quyền kiểm soát cấu hình chi tiết hơn.
- Phù hợp khi cần chủ động thiết kế cluster và tài nguyên.

Cả hai hướng đều là dịch vụ được AWS quản lý, nhưng mức độ kiểm soát và cách tính chi phí khác nhau.

---

# 6.14. RDS, Aurora và ElastiCache khác nhau thế nào?

Đây là phần cần hiểu thật rõ.

Hãy tưởng tượng một trường học:

- **RDS:** kho hồ sơ chính của trường, lưu thông tin học sinh và điểm số.
- **Aurora:** một hệ thống kho dữ liệu quan hệ do AWS thiết kế với kiến trúc riêng, có khả năng tương thích MySQL/PostgreSQL.
- **ElastiCache:** chiếc bàn nhỏ đặt gần nhân viên, để những thông tin thường xuyên cần xem luôn sẵn sàng.

Bảng so sánh:

| Tiêu chí | Amazon RDS | Amazon Aurora | Amazon ElastiCache |
|---|---|---|---|
| Mục đích | Quản lý database quan hệ | Database quan hệ tương thích MySQL/PostgreSQL | Lưu dữ liệu cache trong bộ nhớ |
| Công nghệ | MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Db2 | Aurora MySQL-compatible hoặc PostgreSQL-compatible | Valkey, Redis OSS, Memcached |
| Nơi lưu chính | Storage của database | Cluster storage phân tán | Chủ yếu trong bộ nhớ |
| Dùng SQL | Có, tùy engine | Có | Không dùng như database SQL thông thường |
| Mục đích chính | Lưu dữ liệu lâu dài | Lưu dữ liệu quan hệ với kiến trúc Aurora | Tăng tốc truy cập dữ liệu |
| Ví dụ | Lưu đơn hàng | Database cho ứng dụng cần kiến trúc Aurora | Cache thông tin sản phẩm |

**Điều quan trọng:**

ElastiCache không nên được xem là nơi lưu dữ liệu chính duy nhất của ứng dụng.

Cache có thể hết hạn, bị xóa hoặc bị thay thế. Nếu dữ liệu quan trọng chỉ tồn tại trong cache, ứng dụng có thể mất dữ liệu khi cache gặp sự cố hoặc dữ liệu bị loại bỏ.

Thông thường:

- RDS/Aurora lưu dữ liệu chính.
- ElastiCache giữ bản sao tạm thời của dữ liệu thường xuyên sử dụng.

---

# 6.15. Ví dụ thiết kế một ứng dụng thực tế

Hãy xây dựng một website bán hàng.

Ứng dụng cần:

- Người dùng đăng nhập.
- Xem sản phẩm.
- Tạo đơn hàng.
- Thanh toán.
- Xem lịch sử mua hàng.

Ta có thể thiết kế như sau:

```text
                    Internet
                       |
                       v
                 Web / Mobile App
                       |
                       v
                 Backend API
                       |
          ---------------------------
          |                         |
          v                         v
      ElastiCache                  RDS
   (Cache / Session)        (Database chính)
                                    |
                              Users / Products
                              Orders / Payments
```

Cách hoạt động:

### Khi người dùng xem sản phẩm

1. Backend nhận yêu cầu.
2. Backend kiểm tra ElastiCache.
3. Nếu có dữ liệu, trả về ngay.
4. Nếu không có, backend truy vấn RDS.
5. Backend lưu kết quả vào cache.
6. Trả dữ liệu cho người dùng.

### Khi người dùng tạo đơn hàng

1. Backend nhận yêu cầu đặt hàng.
2. Backend kiểm tra thông tin cần thiết.
3. Backend ghi đơn hàng vào RDS.
4. Nếu cache đang giữ thông tin liên quan, backend cập nhật hoặc xóa cache phù hợp.
5. Trả kết quả cho người dùng.

Với hệ thống cần độ sẵn sàng cao, có thể cân nhắc Multi-AZ hoặc Aurora tùy yêu cầu.

Với hệ thống có lượng đọc lớn, có thể cân nhắc Read Replica hoặc Reader Instance để phân phối truy vấn đọc.

Không nên thêm mọi dịch vụ AWS ngay từ đầu. Hãy bắt đầu với kiến trúc đơn giản, đo hiệu năng rồi mới mở rộng.

---

# 6.16. Một số khái niệm nâng cao cần biết

## 6.16.1. RDS Proxy

RDS Proxy là dịch vụ proxy giúp quản lý và chia sẻ kết nối đến database.

Hãy tưởng tượng database chỉ tiếp nhận được một số lượng kết nối nhất định.

Nếu hàng nghìn yêu cầu cùng tạo kết nối mới, database có thể bị quá tải.

RDS Proxy đứng giữa ứng dụng và RDS:

```text
Application
     |
     v
  RDS Proxy
     |
     v
     RDS
```

RDS Proxy có thể giúp:

- Gom và tái sử dụng kết nối.
- Giảm áp lực tạo kết nối mới.
- Hỗ trợ ứng dụng có số lượng kết nối tăng giảm đột ngột.
- Hỗ trợ một số kịch bản failover và quản lý kết nối.

RDS Proxy thường đáng cân nhắc với ứng dụng có nhiều kết nối ngắn hạn, chẳng hạn một số hệ thống dùng AWS Lambda.

## 6.16.2. Replication Lag

Replication Lag là khoảng trễ giữa database chính và bản sao.

Ví dụ:

```text
10:00:00 - Primary cập nhật số lượng hàng = 5
10:00:01 - Ứng dụng đọc từ Read Replica
10:00:01 - Replica vẫn hiển thị số lượng hàng = 6
10:00:02 - Replica nhận dữ liệu mới
```

Nếu ứng dụng cần đọc dữ liệu vừa ghi ngay lập tức, cần cân nhắc đọc từ Primary hoặc thiết kế cơ chế phù hợp.

## 6.16.3. Failover

Failover là quá trình chuyển hoạt động từ thành phần chính sang thành phần dự phòng khi thành phần chính gặp sự cố.

Ví dụ:

```text
Primary gặp sự cố
       |
       v
Phát hiện lỗi
       |
       v
Chuyển sang Standby/Replica phù hợp
       |
       v
Ứng dụng kết nối lại
```

Failover giúp giảm thời gian gián đoạn nhưng không đảm bảo ứng dụng không bị gián đoạn một giây nào.

Ứng dụng vẫn cần có cơ chế retry và xử lý lỗi kết nối phù hợp.

---

# 6.17. Những lỗi thường gặp khi sử dụng RDS và ElastiCache

## Lỗi 1: Mở database cho toàn bộ Internet

Không nên đặt Inbound Rule của database là:

```text
0.0.0.0/0
```

cho port MySQL hoặc PostgreSQL trong môi trường production.

Nên giới hạn nguồn kết nối bằng Security Group của backend hoặc nguồn thực sự cần thiết.

## Lỗi 2: Nghĩ rằng Multi-AZ giúp đọc nhanh hơn

Với mô hình Multi-AZ DB instance truyền thống, Standby chủ yếu phục vụ High Availability, không phải Read Scaling.

Nếu cần mở rộng khả năng đọc, hãy tìm hiểu Read Replica.

## Lỗi 3: Nghĩ rằng Read Replica luôn có dữ liệu mới nhất

Read Replica của RDS thông thường có thể có Replication Lag.

Không nên dùng nó cho mọi truy vấn mà không hiểu yêu cầu nhất quán dữ liệu.

## Lỗi 4: Nghĩ rằng Multi-AZ thay thế Backup

Multi-AZ giúp database tiếp tục hoạt động khi gặp một số sự cố.

Backup giúp khôi phục dữ liệu khi xảy ra lỗi như xóa nhầm hoặc hỏng dữ liệu.

Hai tính năng giải quyết hai vấn đề khác nhau.

## Lỗi 5: Nghĩ rằng Cache là database chính

Cache không nên là nơi duy nhất lưu dữ liệu quan trọng nếu ứng dụng không có thiết kế bảo đảm độ bền dữ liệu tương ứng.

Hãy giữ dữ liệu chính trong database phù hợp và dùng cache để tăng tốc.

## Lỗi 6: Lưu dữ liệu vào cache nhưng không đặt TTL hoặc cơ chế xóa cache

Dữ liệu cache có thể cũ so với database.

Cần thiết kế TTL, cập nhật cache hoặc xóa cache khi dữ liệu chính thay đổi.

## Lỗi 7: Chọn cấu hình quá lớn

Database và cache đều có chi phí.

Không nên chọn instance lớn chỉ vì nghĩ rằng cấu hình càng lớn thì càng tốt.

Hãy bắt đầu theo nhu cầu, theo dõi CPU, RAM, kết nối, I/O và tăng cấu hình khi có dữ liệu chứng minh cần thiết.

---

# 6.18. Chi phí cần chú ý

RDS, Aurora và ElastiCache đều có thể phát sinh chi phí theo tài nguyên và cách sử dụng.

Một số yếu tố thường ảnh hưởng đến chi phí:

| Dịch vụ | Những yếu tố có thể tính phí |
|---|---|
| RDS | Thời gian chạy instance, loại instance, storage, I/O tùy loại cấu hình, backup vượt mức miễn phí nếu có |
| Aurora | Capacity/instance, storage, I/O tùy cấu hình, backup và truyền dữ liệu |
| ElastiCache | Loại engine, node hoặc mức sử dụng Serverless, dung lượng cache, xử lý và truyền dữ liệu |

Các điểm cần nhớ:

1. Multi-AZ thường tốn kém hơn Single-AZ vì cần thêm tài nguyên dự phòng.
2. Read Replica là tài nguyên bổ sung và thường phát sinh chi phí riêng.
3. Snapshot được giữ lâu có thể phát sinh chi phí lưu trữ.
4. Cache lớn hơn nhu cầu có thể làm tăng chi phí.
5. Serverless không có nghĩa là miễn phí.
6. Xóa ứng dụng không đồng nghĩa với việc database và cache tự động bị xóa.

Trước khi triển khai production, hãy kiểm tra bảng giá theo Region, engine, loại instance và cấu hình thực tế.

---

# 6.19. Kiến thức cần nhớ khi thi AWS

Đây là bảng tổng hợp các khái niệm dễ xuất hiện trong câu hỏi AWS.

| Nếu đề bài nói... | Hãy nghĩ đến... |
|---|---|
| Muốn quản lý MySQL/PostgreSQL mà không tự quản lý máy chủ | Amazon RDS |
| Cần database tương thích MySQL/PostgreSQL với kiến trúc Aurora | Amazon Aurora |
| Cần tăng khả năng sẵn sàng cho RDS | Multi-AZ |
| Cần chuyển sang database dự phòng khi Primary gặp sự cố | Multi-AZ / Failover |
| Cần tăng khả năng xử lý truy vấn đọc | Read Replica |
| Cần bản sao database ở Region khác | Cross-Region Read Replica, nếu engine hỗ trợ |
| Cần khôi phục dữ liệu về một thời điểm | Automated Backup / Point-in-Time Recovery |
| Cần lưu trạng thái database trước khi nâng cấp | Manual Snapshot |
| Cần cache dữ liệu để giảm truy vấn database | ElastiCache |
| Cần cache với nhiều cấu trúc dữ liệu | Valkey hoặc Redis OSS |
| Cần cache đơn giản | Memcached |
| Muốn giảm việc tự quản lý capacity của cache | ElastiCache Serverless |
| Ứng dụng tạo quá nhiều kết nối database | Cân nhắc RDS Proxy |

## 6.19.1. Những cặp khái niệm dễ nhầm

### Multi-AZ và Read Replica

- Multi-AZ DB instance truyền thống: mục tiêu chính là High Availability.
- Read Replica: mục tiêu chính là Read Scaling.

### Automated Backup và Snapshot

- Automated Backup: sao lưu tự động, hỗ trợ khôi phục theo thời điểm trong khoảng thời gian được giữ.
- Snapshot: bản chụp tại một thời điểm, thường do người dùng chủ động tạo.

### RDS và Aurora

- RDS: dịch vụ quản lý nhiều database engine quan hệ.
- Aurora: database engine của AWS, tương thích MySQL/PostgreSQL và có kiến trúc riêng.

### Database và Cache

- Database: lưu dữ liệu chính.
- Cache: lưu dữ liệu thường xuyên cần truy cập để tăng tốc.

---

# 6.20. Câu hỏi ôn tập

Hãy thử trả lời trước khi xem đáp án.

## Câu 1

Amazon RDS được dùng để làm gì?

A. Lưu trữ hình ảnh và video.  
B. Quản lý cơ sở dữ liệu quan hệ trên AWS.  
C. Tạo máy ảo chạy Windows.  
D. Phân phối nội dung đến người dùng.

## Câu 2

Một ứng dụng đang sử dụng MySQL và muốn giảm công việc quản lý máy chủ database. Nên cân nhắc dịch vụ nào?

A. Amazon RDS for MySQL.  
B. Amazon S3.  
C. Amazon Route 53.  
D. AWS IAM.

## Câu 3

Mục đích chính của Multi-AZ DB instance truyền thống là gì?

A. Tăng dung lượng lưu trữ hình ảnh.  
B. Tăng khả năng sẵn sàng và hỗ trợ failover.  
C. Tăng tốc độ Internet.  
D. Tăng số lượng người dùng IAM.

## Câu 4

Một website có rất nhiều truy vấn SELECT và muốn giảm tải cho database chính. Nên cân nhắc gì?

A. Read Replica.  
B. IAM User.  
C. S3 Bucket.  
D. Route 53 Hosted Zone.

## Câu 5

Read Replica của RDS thông thường có thể gặp vấn đề gì?

A. Không thể lưu dữ liệu.  
B. Replication Lag.  
C. Không thể nhận truy vấn SELECT.  
D. Không có endpoint.

## Câu 6

Tính năng nào giúp khôi phục RDS về một thời điểm trong khoảng thời gian backup được giữ?

A. Point-in-Time Recovery.  
B. Security Group.  
C. Read Endpoint.  
D. IAM Policy.

## Câu 7

Aurora tương thích với những database engine nào?

A. MongoDB và DynamoDB.  
B. MySQL và PostgreSQL.  
C. Redis và Memcached.  
D. Oracle và SQL Server.

## Câu 8

Trong Aurora, Reader Endpoint thường được sử dụng để làm gì?

A. Phân phối kết nối đọc đến Reader Instances.  
B. Ghi dữ liệu vào S3.  
C. Tạo IAM Role.  
D. Quản lý DNS.

## Câu 9

Amazon ElastiCache được sử dụng chủ yếu để làm gì?

A. Lưu trữ dữ liệu cache trong bộ nhớ nhằm tăng tốc truy cập.  
B. Thay thế hoàn toàn mọi database quan hệ.  
C. Quản lý tài khoản AWS.  
D. Tạo máy chủ EC2.

## Câu 10

Nếu ứng dụng kiểm tra cache nhưng không tìm thấy dữ liệu, sau đó phải truy vấn database, tình huống này gọi là gì?

A. Cache Hit.  
B. Cache Miss.  
C. Failover.  
D. Snapshot.

## Đáp án và giải thích

| Câu | Đáp án | Giải thích |
|---:|---|---|
| 1 | B | RDS quản lý database quan hệ trên AWS. |
| 2 | A | RDS for MySQL giúp giảm công việc quản lý hạ tầng MySQL. |
| 3 | B | Multi-AZ DB instance truyền thống tập trung vào High Availability và Failover. |
| 4 | A | Read Replica giúp phân phối truy vấn đọc. |
| 5 | B | Sao chép bất đồng bộ có thể tạo ra Replication Lag. |
| 6 | A | PITR hỗ trợ khôi phục về một thời điểm phù hợp trong khoảng backup. |
| 7 | B | Aurora có phiên bản tương thích MySQL và PostgreSQL. |
| 8 | A | Reader Endpoint phân phối kết nối đọc đến Reader Instances. |
| 9 | A | ElastiCache là dịch vụ cache trong bộ nhớ. |
| 10 | B | Cache Miss xảy ra khi không tìm thấy dữ liệu phù hợp trong cache. |

---

# 6.21. Tóm tắt chương

Nếu chỉ được nhớ 10 điều sau chương này, hãy nhớ:

1. **Database** là nơi lưu trữ và quản lý dữ liệu.
2. **Amazon RDS** giúp tạo và quản lý database quan hệ trên AWS.
3. RDS hỗ trợ nhiều engine như MySQL, PostgreSQL, MariaDB, Oracle, SQL Server và Db2.
4. **Single-AZ** triển khai database trong một AZ; **Multi-AZ** giúp tăng khả năng sẵn sàng.
5. **Multi-AZ DB instance truyền thống** dùng Standby để hỗ trợ failover, không phải để tăng tải đọc.
6. **Read Replica** là bản sao dùng để mở rộng truy vấn đọc; có thể có Replication Lag.
7. **Automated Backup** và **Snapshot** giúp bảo vệ dữ liệu theo những cách khác nhau.
8. **Amazon Aurora** là database engine tương thích MySQL/PostgreSQL, có kiến trúc lưu trữ và cluster riêng.
9. **Amazon ElastiCache** lưu dữ liệu trong bộ nhớ để giảm thời gian truy cập và giảm tải database.
10. **Database lưu dữ liệu chính; Cache lưu dữ liệu tạm thời hoặc bản sao để tăng tốc.**

## Một câu để nhớ toàn bộ chương

> **RDS giúp quản lý database, Aurora cung cấp một database engine được AWS tối ưu, còn ElastiCache giúp ứng dụng lấy dữ liệu thường xuyên nhanh hơn.**

---

# 6.22. Tài liệu tham khảo chính thức từ AWS

Các trang dưới đây là tài liệu chính thức. Tên tính năng, phiên bản engine, Region hỗ trợ và giá có thể thay đổi theo thời gian.

1. [Amazon RDS – What is Amazon RDS?](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
2. [Amazon RDS – Multi-AZ DB instance deployments](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZSingleStandby.html)
3. [Amazon RDS – Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)
4. [Amazon RDS – Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)
5. [Amazon Aurora – What is Amazon Aurora?](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Welcome.html)
6. [Amazon Aurora – Storage](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html)
7. [Amazon Aurora – Endpoints](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.Endpoints.html)
8. [Amazon ElastiCache – What is Amazon ElastiCache?](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.html)
9. [Amazon ElastiCache – Comparing Valkey, Redis OSS and Memcached](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/SelectEngine.html)
10. [Amazon ElastiCache – Caching strategies](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/Strategies.html)

---

*Hết chương 6 – RDS, Aurora & ElastiCache.*
