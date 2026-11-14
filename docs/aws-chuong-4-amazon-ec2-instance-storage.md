# CHƯƠNG 4 --- AMAZON EC2 INSTANCE STORAGE

## Lưu trữ dữ liệu trên Amazon EC2

### Học từ con số 0 -- Định hướng AWS Certified Developer Associate (DVA-C02)

------------------------------------------------------------------------

## Mục tiêu chương học

Sau khi hoàn thành chương này, bạn có thể:

-   Giải thích được máy chủ EC2 lưu dữ liệu ở đâu.
-   Phân biệt Block Storage, File Storage và Object Storage.
-   Hiểu cách EBS Volume hoạt động và cách gắn EBS vào EC2.
-   Phân biệt EBS, EBS Snapshot và AMI.
-   Hiểu Snapshot Incremental Backup và cách khôi phục dữ liệu.
-   Phân biệt EBS với EC2 Instance Store.
-   Hiểu cách nhiều EC2 cùng truy cập một hệ thống file bằng EFS.
-   Nhận biết các loại Amazon FSx và trường hợp sử dụng.
-   Chọn đúng dịch vụ lưu trữ trong những tình huống thường gặp của AWS
    DVA-C02.

> **Ý tưởng quan trọng nhất:** EC2 là máy tính, còn Storage là nơi máy
> tính lưu dữ liệu. AWS có nhiều loại lưu trữ vì mỗi ứng dụng có nhu cầu
> khác nhau về tốc độ, cách truy cập, khả năng chia sẻ và độ bền dữ
> liệu.

------------------------------------------------------------------------

# 4.1. EC2 cần nơi lưu trữ dữ liệu

Hãy tưởng tượng bạn có một chiếc laptop để lập trình. Trong laptop có:

-   Windows: hệ điều hành giúp máy tính hoạt động.
-   VS Code: phần mềm để viết code.
-   Source code: chương trình bạn đang phát triển.
-   Database: nơi lưu thông tin người dùng.
-   Hình ảnh: những file người dùng tải lên.

Tất cả đều cần một nơi để lưu trữ. Đó chính là ổ cứng.

Máy chủ EC2 cũng tương tự. Một EC2 thường cần lưu:

-   Hệ điều hành Linux hoặc Windows.
-   Các chương trình đã cài đặt.
-   Source code của ứng dụng.
-   File và dữ liệu ứng dụng.
-   Log và dữ liệu tạm.

**EC2 là máy tính; Storage là nơi máy tính lưu dữ liệu.**

Điểm thú vị của AWS là chúng ta không nhất thiết phải lưu mọi thứ trên
cùng một ổ đĩa gắn với EC2. AWS cung cấp nhiều dịch vụ lưu trữ để giải
quyết những nhu cầu khác nhau.

Ví dụ:

-   Máy chủ cần một ổ đĩa để cài hệ điều hành: EBS.
-   Nhiều máy chủ cần dùng chung một thư mục: EFS.
-   Ứng dụng cần lưu ảnh, video, file tải lên: S3.
-   Máy chủ cần ổ đĩa cục bộ rất nhanh để chứa dữ liệu tạm: Instance
    Store.

------------------------------------------------------------------------

# 4.2. Ba cách lưu trữ dữ liệu: Block, File và Object

Trước khi học các dịch vụ AWS, hãy hiểu ba cách tổ chức dữ liệu cơ bản.

## 4.2.1. Block Storage --- Lưu trữ theo khối

Hãy tưởng tượng một quyển vở gồm nhiều trang. Máy tính có thể đọc hoặc
ghi dữ liệu trên từng phần của ổ đĩa.

Block Storage chia dữ liệu thành các khối (Block). Hệ điều hành có thể
định dạng ổ đĩa thành hệ thống file như NTFS, ext4 hoặc XFS.

Ví dụ sử dụng:

-   Ổ đĩa chứa hệ điều hành.
-   Ổ đĩa chứa Database.
-   Ổ đĩa chứa dữ liệu của ứng dụng.

Dịch vụ AWS tiêu biểu: **Amazon EBS**.

## 4.2.2. File Storage --- Lưu trữ theo tập tin

Hãy tưởng tượng một tủ hồ sơ có nhiều ngăn. Trong mỗi ngăn có thư mục,
trong thư mục có các tài liệu.

File Storage tổ chức dữ liệu thành file và folder. Người dùng có thể mở,
đọc, ghi, đổi tên hoặc xóa file theo cấu trúc thư mục.

Ví dụ:

``` text
shared-files/
├── images/
│   ├── cat.jpg
│   └── dog.jpg
├── documents/
│   ├── report.pdf
│   └── invoice.pdf
└── videos/
    └── demo.mp4
```

Dịch vụ AWS tiêu biểu:

-   Amazon EFS.
-   Amazon FSx.

## 4.2.3. Object Storage --- Lưu trữ theo đối tượng

Hãy tưởng tượng một kho hàng. Mỗi món hàng được đặt trong một chiếc hộp
và có mã để tìm kiếm.

Object Storage lưu dữ liệu dưới dạng các Object. Mỗi Object thường có:

-   Nội dung dữ liệu.
-   Key (tên hoặc mã nhận diện).
-   Metadata (thông tin mô tả).

Dịch vụ AWS tiêu biểu: **Amazon S3**.

Ví dụ:

-   Ảnh đại diện người dùng.
-   Video.
-   File PDF.
-   File backup.
-   File tĩnh của website.

## 4.2.4. Bảng so sánh

  Loại lưu trữ     Cách hiểu đơn giản       Dịch vụ AWS
  ---------------- ------------------------ ---------------------
  Block Storage    Ổ cứng máy tính          EBS, Instance Store
  File Storage     Tủ hồ sơ chia sẻ         EFS, FSx
  Object Storage   Kho chứa các đối tượng   S3

**Mẹo ghi nhớ:**

-   Block = ổ đĩa.
-   File = thư mục và tập tin.
-   Object = đối tượng được truy cập bằng Key/API.

------------------------------------------------------------------------

# 4.3. Amazon EBS --- Ổ cứng dành cho EC2

## 4.3.1. EBS là gì?

EBS là viết tắt của **Elastic Block Store**.

Đây là dịch vụ cung cấp Block Storage cho EC2. Có thể hình dung EBS như
một ổ cứng riêng được kết nối với máy tính EC2.

``` text
             Amazon EC2
        +--------------------+
        |                    |
        |     Linux OS       |
        |                    |
        |    Application     |
        |                    |
        +---------+----------+
                  |
                  | Attach
                  |
        +---------v----------+
        |                    |
        |     EBS Volume     |
        |       50 GiB       |
        |                    |
        +--------------------+
```

EC2 có thể sử dụng EBS để:

-   Cài đặt hệ điều hành.
-   Lưu source code.
-   Lưu file.
-   Chạy Database.
-   Lưu dữ liệu ứng dụng.

Một EC2 có thể được gắn nhiều EBS Volume, tùy giới hạn của loại
Instance.

## 4.3.2. EBS Volume là gì?

Volume có thể hiểu đơn giản là một ổ đĩa.

Ví dụ, bạn tạo một EBS Volume dung lượng 50 GiB và gắn nó vào EC2. Hệ
điều hành sẽ nhận diện ổ đĩa mới để bạn có thể định dạng và sử dụng.

Một máy chủ có thể tách ổ hệ điều hành và ổ dữ liệu:

``` text
                  EC2 Server
             +------------------+
             |                  |
             |     Linux OS     |
             |                  |
             +---+----------+---+
                 |          |
                 |          |
          +------v---+  +---v--------+
          | EBS 1    |  | EBS 2      |
          | 30 GiB   |  | 100 GiB    |
          |          |  |            |
          | System   |  | App Data   |
          +----------+  +------------+
```

Cách tách này giúp quản lý dữ liệu và vòng đời ổ đĩa dễ dàng hơn.

## 4.3.3. Root Volume và Data Volume

**Root Volume**

-   Là ổ đĩa chứa hệ điều hành.
-   Ví dụ: Ubuntu, Amazon Linux hoặc Windows Server.
-   EC2 thường khởi động hệ điều hành từ Root Volume.

**Data Volume**

-   Là ổ đĩa bổ sung.
-   Có thể dùng để lưu source code, file upload, log hoặc dữ liệu ứng
    dụng.
-   Có thể được gắn hoặc tháo khỏi EC2 theo nhu cầu.

## 4.3.4. EBS có thể gắn vào nhiều EC2 không?

Trong trường hợp thông thường, một EBS Volume được gắn vào một EC2 tại
một thời điểm.

AWS có tính năng đặc biệt tên là **EBS Multi-Attach**. Tính năng này cho
phép một số EBS Volume loại Provisioned IOPS SSD được gắn đồng thời vào
nhiều EC2.

Các điều kiện quan trọng cần nhớ:

-   Hỗ trợ các loại `io1` và `io2` có hỗ trợ Multi-Attach.
-   Các EC2 phải nằm trong cùng Availability Zone.
-   Với Nitro System, một Volume có thể được gắn vào tối đa 16 EC2, tùy
    cấu hình và giới hạn dịch vụ.
-   Ứng dụng và hệ thống file phải được thiết kế để quản lý truy cập
    đồng thời an toàn.

Không nên lấy một EBS Volume rồi cho nhiều máy Linux cùng ghi bằng hệ
thống file thông thường như ext4 hoặc XFS. Các hệ thống file này không
tự động xử lý việc nhiều máy cùng ghi dữ liệu.

**Mẹo làm bài thi:** Nếu đề hỏi nhiều EC2 cần cùng truy cập một thư
mục/file system, hãy nghĩ đến EFS trước. Chỉ nghĩ đến EBS Multi-Attach
khi đề nhấn mạnh shared block storage và workload hỗ trợ truy cập đồng
thời.

## 4.3.5. EBS và Availability Zone

Đây là quy tắc rất quan trọng:

**EBS Volume được tạo trong một Availability Zone và chỉ có thể được
Attach trực tiếp vào EC2 trong cùng Availability Zone đó.**

Ví dụ:

``` text
Region: ap-southeast-1

+-----------------------+
| Availability Zone A   |
|                       |
| EC2-A                 |
| EBS-A                 |
+-----------------------+

+-----------------------+
| Availability Zone B   |
|                       |
| EC2-B                 |
+-----------------------+
```

EBS-A không thể Attach trực tiếp vào EC2-B vì chúng nằm ở hai AZ khác
nhau.

Nếu muốn chuyển dữ liệu sang AZ khác:

1.  Tạo Snapshot từ EBS-A.
2.  Tạo một EBS Volume mới từ Snapshot.
3.  Chọn AZ-B khi tạo Volume mới.
4.  Attach Volume mới vào EC2-B.

## 4.3.6. Các loại EBS Volume

AWS cung cấp nhiều loại Volume. Mỗi loại phù hợp với một nhóm công việc.

### General Purpose SSD --- gp3 và gp2

Đây là loại SSD đa dụng.

Phù hợp với:

-   Web Server.
-   Application Server.
-   Development Environment.
-   Boot Volume.
-   Database có yêu cầu hiệu năng thông thường.

`gp3` thường là lựa chọn tốt để bắt đầu cho phần lớn workload phổ thông.
gp3 có mức cơ bản 3.000 IOPS và 125 MiB/s Throughput; có thể cấu hình
tăng riêng IOPS và Throughput trong giới hạn dịch vụ.

`gp2` là thế hệ trước. Hiệu năng của gp2 gắn với dung lượng Volume,
trong khi gp3 cho phép cấu hình IOPS và Throughput độc lập với dung
lượng trong giới hạn hỗ trợ.

### Provisioned IOPS SSD --- io1 và io2

Dành cho ứng dụng cần hiệu năng I/O cao và có thể dự đoán được.

Ví dụ:

-   Database quan trọng.
-   Hệ thống giao dịch.
-   Workload cần IOPS cao.
-   Ứng dụng cần độ trễ thấp và ổn định.

`io2` là lựa chọn thế hệ mới hơn, có độ bền và khả năng mở rộng hiệu
năng cao hơn trong các cấu hình được hỗ trợ.

### Throughput Optimized HDD --- st1

Đây là HDD tối ưu cho Throughput.

Phù hợp với:

-   Big Data.
-   Xử lý log.
-   Data Warehouse.
-   Workload đọc/ghi tuần tự với lượng dữ liệu lớn.

Ví dụ: đọc một file log rất lớn từ đầu đến cuối. Ứng dụng cần truyền tải
lượng dữ liệu lớn liên tục thay vì xử lý nhiều thao tác nhỏ ngẫu nhiên.

### Cold HDD --- sc1

Đây là HDD có chi phí thấp hơn, phù hợp với dữ liệu ít được truy cập.

Ví dụ:

-   Dữ liệu cũ.
-   File lưu trữ ít sử dụng.
-   Workload có yêu cầu hiệu năng thấp.

### Bảng tổng hợp

  -----------------------------------------------------------------------
  Loại                    Đặc điểm                Trường hợp sử dụng
  ----------------------- ----------------------- -----------------------
  gp3                     SSD đa dụng, cấu hình   Web, App, Boot,
                          IOPS/Throughput riêng   workload phổ thông

  gp2                     SSD đa dụng thế hệ      Hệ thống cũ
                          trước                   

  io1                     SSD Provisioned IOPS    Database, IOPS cao

  io2                     SSD Provisioned IOPS    Database quan trọng
                          thế hệ mới              

  st1                     HDD tối ưu Throughput   Big Data, log, tuần tự

  sc1                     HDD chi phí thấp        Dữ liệu ít truy cập
  -----------------------------------------------------------------------

Lưu ý:

-   `gp2`, `gp3`, `io1`, `io2` có thể dùng làm Boot Volume.
-   `st1` và `sc1` không hỗ trợ Boot Volume.
-   Không nên chỉ nhìn tên loại Volume; cần đối chiếu yêu cầu IOPS,
    Throughput, dung lượng và chi phí.

## 4.3.7. IOPS, Throughput và Latency là gì?

Ba khái niệm này thường xuất hiện trong câu hỏi AWS.

**IOPS --- Input/Output Operations Per Second**

Là số thao tác đọc/ghi mà ổ đĩa có thể xử lý trong một giây.

Ví dụ: 3.000 IOPS nghĩa là hệ thống có khả năng xử lý khoảng 3.000 thao
tác I/O mỗi giây trong điều kiện phù hợp.

IOPS quan trọng với Database vì Database thường thực hiện nhiều thao tác
đọc/ghi nhỏ.

**Throughput**

Là lượng dữ liệu có thể truyền trong một giây.

Ví dụ: 100 MiB/s nghĩa là có thể truyền khoảng 100 MiB dữ liệu mỗi giây
trong điều kiện phù hợp.

Throughput quan trọng khi đọc/ghi các file lớn hoặc dữ liệu tuần tự.

**Latency**

Là độ trễ --- khoảng thời gian một thao tác cần để hoàn thành.

Ví dụ: nếu một thao tác đọc mất 5 ms thì độ trễ của thao tác đó là 5 ms.

### Phân biệt nhanh

  Thuật ngữ    Câu hỏi cần trả lời
  ------------ -----------------------------------------
  IOPS         Mỗi giây xử lý được bao nhiêu thao tác?
  Throughput   Mỗi giây truyền được bao nhiêu dữ liệu?
  Latency      Một thao tác mất bao lâu?

Mẹo nhớ:

-   IOPS = số lần thao tác.
-   Throughput = lượng dữ liệu.
-   Latency = thời gian chờ.

## 4.3.8. Điều gì xảy ra với EBS khi EC2 tắt?

Dữ liệu trên EBS vẫn tồn tại khi EC2 được Stop hoặc Reboot, vì EBS có
vòng đời độc lập với trạng thái chạy của EC2.

  Hành động           Dữ liệu trên EBS
  ------------------- --------------------------
  Reboot EC2          Vẫn còn
  Stop EC2            Vẫn còn
  Start EC2           Vẫn còn
  Terminate EC2       Tùy cấu hình xóa Volume
  Delete EBS Volume   Volume và dữ liệu bị xóa

Khi Terminate EC2, Volume có thể bị xóa nếu thuộc tính
`DeleteOnTermination` được bật.

Thông thường Root Volume được cấu hình xóa khi Terminate. Data Volume có
thể được giữ lại, nhưng không nên mặc định điều này. Hãy kiểm tra cấu
hình thực tế trước khi Terminate.

## 4.3.9. EBS Encryption --- Mã hóa dữ liệu

EBS hỗ trợ mã hóa dữ liệu bằng AWS KMS.

Encryption giúp bảo vệ:

-   Dữ liệu lưu trên Volume.
-   Dữ liệu truyền giữa EC2 và EBS.
-   Snapshot được tạo từ Volume đã mã hóa.
-   Volume được tạo từ Snapshot đã mã hóa.

Bạn có thể bật mã hóa khi tạo Volume hoặc cấu hình mã hóa mặc định cho
từng Region.

**Mẹo làm bài thi:** Khi đề nhắc đến mã hóa EBS và quản lý khóa mã hóa,
hãy nghĩ đến AWS KMS.

------------------------------------------------------------------------

# 4.4. EBS Snapshot --- Sao lưu ổ cứng

## 4.4.1. Snapshot là gì?

Hãy tưởng tượng bạn có một quyển vở chứa toàn bộ bài tập. Bạn muốn tạo
một bản sao để phòng trường hợp làm mất quyển vở gốc.

Snapshot cũng có ý tưởng tương tự.

**EBS Snapshot là bản sao lưu của EBS Volume tại một thời điểm nhất
định.**

``` text
         EC2 Server
              |
          EBS Volume
           100 GiB
              |
         Take Snapshot
              |
              v
        +-------------+
        |  Snapshot   |
        |   Backup    |
        +-------------+
```

Nếu Volume gặp sự cố hoặc bạn cần khôi phục dữ liệu, có thể dùng
Snapshot để tạo một EBS Volume mới.

## 4.4.2. Incremental Backup --- Sao lưu tăng dần

Đây là điểm rất quan trọng.

Snapshot đầu tiên lưu dữ liệu cần thiết để đại diện cho trạng thái
Volume tại thời điểm đó.

Những Snapshot tiếp theo chỉ lưu các Block đã thay đổi kể từ Snapshot
trước.

Ví dụ:

-   Ngày 1: tạo Snapshot đầu tiên.
-   Ngày 2: thay đổi 5 GiB dữ liệu, tạo Snapshot thứ hai.
-   Ngày 3: thay đổi thêm 3 GiB dữ liệu, tạo Snapshot thứ ba.

AWS quản lý những Block dữ liệu được dùng chung phía sau. Mỗi Snapshot
vẫn đại diện cho trạng thái đầy đủ của Volume tại thời điểm Snapshot
được tạo.

**Incremental không có nghĩa là Snapshot thứ hai chỉ có thể khôi phục
phần thay đổi.** AWS quản lý các phần dữ liệu liên quan để bạn có thể
khôi phục Volume về trạng thái của từng Snapshot.

## 4.4.3. Snapshot được lưu ở đâu?

AWS lưu dữ liệu EBS Snapshot trong hạ tầng Amazon S3 do AWS quản lý.

Tuy nhiên, bạn không quản lý Snapshot như những file S3 thông thường và
không mở chúng trong S3 Console như file upload của ứng dụng.

Bạn quản lý Snapshot thông qua EC2 Console, EBS API hoặc AWS CLI.

## 4.4.4. Tự động tạo Snapshot

Nếu muốn tự động sao lưu, bạn có thể sử dụng:

-   **Amazon Data Lifecycle Manager (DLM):** tạo, giữ lại và xóa
    Snapshot theo chính sách vòng đời.
-   **AWS Backup:** quản lý backup tập trung cho nhiều dịch vụ AWS,
    trong đó có EBS.

Ví dụ: tạo Snapshot mỗi ngày lúc 02:00 và giữ lại 7 bản gần nhất.

## 4.4.5. Snapshot có thể dùng ở AZ khác không?

Có.

Snapshot có thể được dùng để tạo EBS Volume trong một AZ khác thuộc cùng
Region.

Quy trình:

1.  Tạo Snapshot từ EBS ở AZ-A.
2.  Chọn Create Volume from Snapshot.
3.  Chọn AZ-B.
4.  Attach Volume mới vào EC2 ở AZ-B.

Bạn cũng có thể Copy Snapshot sang Region khác để hỗ trợ Disaster
Recovery.

## 4.4.6. Phân biệt EBS Volume và Snapshot

  EBS Volume                   Snapshot
  ---------------------------- ---------------------------------------------------
  Ổ đĩa đang sử dụng           Bản sao lưu của Volume
  Có thể Attach vào EC2        Không Attach trực tiếp như Volume
  Dùng để đọc và ghi dữ liệu   Dùng để khôi phục hoặc tạo Volume
  Thuộc một AZ                 Có thể tạo Volume mới ở AZ khác trong cùng Region

**Ghi nhớ:** Volume là ổ cứng; Snapshot là bản sao lưu của ổ cứng.

------------------------------------------------------------------------

# 4.5. AMI --- Amazon Machine Image

## 4.5.1. AMI là gì?

Giả sử bạn có một máy chủ đã cài đặt đầy đủ:

-   Ubuntu.
-   Node.js.
-   Nginx.
-   Docker.
-   Các thư viện cần thiết.
-   Cấu hình ứng dụng.

Bây giờ bạn muốn tạo thêm 10 máy chủ giống hệt máy chủ này.

Bạn có muốn cài đặt tất cả phần mềm từ đầu trên từng máy không? Không
cần thiết.

Bạn có thể tạo một bản mẫu của máy chủ đã cấu hình hoàn chỉnh. Bản mẫu
đó là AMI.

**AMI là Amazon Machine Image --- hình ảnh máy máy chủ dùng để khởi tạo
EC2 Instance.**

## 4.5.2. AMI chứa những gì?

Một EBS-backed AMI chứa thông tin cần thiết để khởi tạo EC2, bao gồm:

-   Thông tin hệ điều hành và cấu hình khởi chạy.
-   Snapshot của Root Volume.
-   Các Block Device Mapping.
-   Thông tin kiến trúc và những thuộc tính liên quan đến việc khởi chạy
    Instance.

Khi tạo AMI từ một EC2, AWS có thể tạo Snapshot từ các EBS Volume được
chọn để AMI sử dụng khi khởi chạy máy chủ mới.

## 4.5.3. AMI hoạt động như thế nào?

``` text
          EC2 Server gốc
                |
        Ubuntu + Node.js
        Nginx + Application
                |
             Create AMI
                |
                v
          +-----------+
          |    AMI    |
          | Server    |
          | Template  |
          +-----------+
             /     \
            /       \
           v         v
        +------+  +------+
        | EC2-A|  | EC2-B|
        +------+  +------+
```

Bạn có thể dùng một AMI để tạo nhiều EC2 với môi trường ban đầu giống
nhau.

## 4.5.4. Khi nào cần AMI?

Ví dụ: ban đầu website chỉ có một EC2. Khi lượng truy cập tăng, bạn cần
triển khai thêm nhiều EC2.

Thay vì cài đặt lại mọi thứ, bạn tạo AMI từ máy chủ đã cấu hình. Sau đó
dùng AMI để tạo thêm máy chủ.

Lợi ích:

-   Tiết kiệm thời gian.
-   Giảm sai sót khi cài đặt.
-   Giữ môi trường đồng nhất.
-   Hỗ trợ triển khai nhiều máy chủ.

## 4.5.5. Phân biệt AMI và Snapshot

  -----------------------------------------------------------------------
  AMI                                 Snapshot
  ----------------------------------- -----------------------------------
  Mẫu dùng để khởi tạo EC2            Bản sao lưu EBS Volume

  Có thông tin cần thiết để khởi chạy Lưu trạng thái dữ liệu của Volume
  máy chủ                             

  Có thể tham chiếu đến một hoặc      Dùng để tạo hoặc khôi phục Volume
  nhiều Snapshot                      

  Phục vụ triển khai máy chủ          Phục vụ sao lưu và khôi phục dữ
                                      liệu
  -----------------------------------------------------------------------

Ví dụ dễ nhớ:

-   Snapshot giống bản sao của ổ cứng.
-   AMI giống bản mẫu của một máy tính đã chuẩn bị để có thể tạo máy
    tính mới.

------------------------------------------------------------------------

# 4.6. EC2 Instance Store --- Ổ cứng tạm thời

## 4.6.1. Instance Store là gì?

Hãy tưởng tượng máy tính có một ổ SSD được kết nối trực tiếp với phần
cứng bên trong.

Instance Store là loại Block Storage sử dụng ổ đĩa cục bộ gắn với máy
chủ vật lý đang chạy EC2.

Nó có thể cung cấp hiệu năng rất tốt, nhưng có một nhược điểm lớn:

**Dữ liệu trên Instance Store không được đảm bảo tồn tại khi EC2 Stop,
Hibernate hoặc Terminate.**

``` text
             EC2 Instance
          +------------------+
          |                  |
          |   Application    |
          |                  |
          +--------+---------+
                   |
          +--------+---------+
          |                  |
          v                  v
      +-------+          +----------+
      |  EBS  |          | Instance |
      |       |          |  Store   |
      +-------+          +----------+
      Persistent         Temporary
      Storage            Storage
```

## 4.6.2. Điều gì xảy ra khi EC2 khởi động lại?

  Hành động              Instance Store
  ---------------------- ----------------------------------
  Reboot EC2             Dữ liệu thường vẫn còn
  Restart hệ điều hành   Dữ liệu thường vẫn còn
  Stop EC2               Dữ liệu bị mất
  Start lại sau Stop     Dữ liệu cũ không còn
  Hibernate EC2          Dữ liệu bị mất
  Terminate EC2          Dữ liệu bị mất
  Hỏng ổ đĩa vật lý      Dữ liệu trên ổ bị lỗi có thể mất

Tại sao Reboot vẫn còn nhưng Stop lại mất?

-   Reboot: EC2 khởi động lại nhưng vẫn sử dụng cùng Instance và ổ đĩa
    cục bộ.
-   Stop rồi Start: Instance có thể được chuyển sang máy chủ vật lý
    khác. Instance Store gắn với phần cứng cục bộ nên dữ liệu không đi
    theo Instance.

## 4.6.3. Khi nào nên sử dụng Instance Store?

Phù hợp để lưu:

-   Cache tạm thời.
-   File tạm trong quá trình xử lý.
-   Dữ liệu trung gian.
-   Scratch data.
-   Dữ liệu có thể tạo lại nếu bị mất.

Ví dụ: ứng dụng xử lý video tải video về, giải nén và tạo nhiều file
tạm. Sau khi xử lý xong, ứng dụng lưu kết quả cuối cùng vào S3. File tạm
có thể đặt trên Instance Store.

Không nên chỉ lưu thông tin khách hàng, hóa đơn hoặc dữ liệu quan trọng
duy nhất trên Instance Store.

**Mẹo nhớ: Instance Store = cục bộ, hiệu năng tốt, dữ liệu tạm thời. EBS
= lưu trữ bền vững.**

------------------------------------------------------------------------

# 4.7. Amazon EFS --- Một hệ thống file dùng chung cho nhiều EC2

## 4.7.1. Vấn đề cần giải quyết

Giả sử website ban đầu chỉ có một EC2. Người dùng tải ảnh lên máy chủ
này và ứng dụng lưu ảnh vào thư mục `/uploads`.

Khi website có nhiều người truy cập, bạn thêm EC2 thứ hai và đặt Load
Balancer phía trước.

Bây giờ xảy ra vấn đề:

-   Người dùng tải ảnh lên EC2-A.
-   Lần truy cập tiếp theo, Load Balancer chuyển yêu cầu đến EC2-B.
-   EC2-B không có file mà người dùng vừa tải lên.

Làm sao để cả hai máy chủ cùng nhìn thấy một bộ file?

## 4.7.2. EFS là gì?

EFS là viết tắt của **Elastic File System**.

Đây là dịch vụ File Storage được quản lý bởi AWS. Nhiều EC2 có thể Mount
và truy cập cùng một hệ thống file EFS.

Hãy tưởng tượng có một tủ hồ sơ ở giữa phòng. Nhiều nhân viên có thể mở
tủ để đọc hoặc cập nhật tài liệu theo quyền được cấp.

``` text
           +------------------+
           |    Amazon EFS    |
           |                  |
           |   Shared Files   |
           +--------+---------+
                    |
          +---------+---------+
          |                   |
       +--v---+             +--v---+
       | EC2-A|             | EC2-B|
       +------+             +------+
          |                    |
       Read/Write            Read/Write
```

## 4.7.3. EFS hoạt động như thế nào?

EFS cung cấp hệ thống file có cấu trúc thư mục và file.

EC2 Linux có thể Mount EFS để sử dụng giống như một thư mục trên máy
chủ.

EFS hỗ trợ giao thức NFSv4.

Bạn có thể:

-   Tạo thư mục.
-   Tạo file.
-   Đọc file.
-   Ghi file.
-   Chia sẻ dữ liệu giữa nhiều EC2.

## 4.7.4. EFS có thể sử dụng ở nhiều Availability Zone không?

EFS có hai lựa chọn triển khai phổ biến:

**Regional**

-   Dữ liệu được thiết kế để có khả năng sẵn sàng cao trên nhiều
    Availability Zone trong Region.
-   Phù hợp với ứng dụng cần nhiều EC2 ở nhiều AZ cùng truy cập dữ liệu.
-   Là lựa chọn thường gặp cho hệ thống production cần tính sẵn sàng
    cao.

**One Zone**

-   Dữ liệu được lưu trong một Availability Zone.
-   Thường có chi phí thấp hơn Regional.
-   Phù hợp khi ứng dụng chấp nhận mức độ sẵn sàng thấp hơn hoặc có thể
    tái tạo dữ liệu.

Với EFS Regional, bạn có thể tạo Mount Target ở các AZ cần truy cập. EC2
trong các AZ có thể Mount cùng File System.

## 4.7.5. EFS Security

EFS sử dụng NFS. Port quan trọng cần nhớ:

`TCP 2049`

Security Group của EFS cần cho phép kết nối TCP 2049 từ Security Group
của các EC2 được phép truy cập.

Nếu không cấu hình đúng, EC2 có thể không Mount được EFS.

Ngoài Security Group, còn cần chú ý đến:

-   Network connectivity.
-   Mount Target.
-   NFS client hoặc EFS Mount Helper.
-   Quyền truy cập file và cấu hình EFS Access Point nếu sử dụng.

## 4.7.6. Khi nào nên dùng EFS?

Ví dụ:

-   Website có nhiều EC2 cùng truy cập thư mục upload.
-   Nhiều Linux Server cần dùng chung file.
-   Các máy chủ cần dùng chung Home Directory.
-   Ứng dụng cần hệ thống file có thể mở rộng.

**Mẹo nhớ: EFS = Shared File System cho nhiều EC2.**

------------------------------------------------------------------------

# 4.8. Amazon FSx --- Các hệ thống File Storage chuyên dụng

## 4.8.1. FSx là gì?

EFS là một hệ thống File Storage phổ biến, đặc biệt trong môi trường
Linux. Nhưng không phải ứng dụng nào cũng sử dụng cùng một loại File
System.

Ví dụ:

-   Ứng dụng Windows cần chia sẻ file bằng SMB.
-   Hệ thống tính toán hiệu năng cao cần File System tốc độ cao.
-   Doanh nghiệp đang sử dụng NetApp.
-   Một số hệ thống cần ZFS.

Amazon FSx cung cấp các hệ thống file được AWS quản lý để đáp ứng những
nhu cầu chuyên biệt đó.

## 4.8.2. FSx for Windows File Server

Dành cho môi trường Windows.

Phù hợp với:

-   Windows Server.
-   Ứng dụng Windows.
-   Shared Folder.
-   Giao thức SMB.
-   Tích hợp Microsoft Active Directory.

Ví dụ: doanh nghiệp có 100 nhân viên dùng Windows và cần truy cập chung
thư mục tài liệu công ty.

## 4.8.3. FSx for Lustre

Lustre là hệ thống file hiệu năng cao.

Phù hợp với:

-   High Performance Computing (HPC).
-   Machine Learning.
-   Xử lý video.
-   Phân tích dữ liệu lớn.
-   Mô phỏng khoa học.

Ví dụ: hệ thống AI cần đọc lượng lớn dữ liệu để huấn luyện mô hình.

## 4.8.4. FSx for NetApp ONTAP

Dành cho doanh nghiệp cần hệ thống lưu trữ NetApp ONTAP.

Có thể hỗ trợ nhiều giao thức truy cập, chẳng hạn:

-   NFS.
-   SMB.
-   iSCSI.

Phù hợp khi doanh nghiệp đang sử dụng NetApp và muốn tiếp tục sử dụng
các tính năng quen thuộc trên AWS.

## 4.8.5. FSx for OpenZFS

Dành cho workload cần hệ thống file OpenZFS.

Phù hợp với:

-   Ứng dụng Linux/Unix.
-   Workload cần các tính năng của ZFS.
-   Hệ thống cần Snapshot và các khả năng quản lý dữ liệu của ZFS.

## 4.8.6. Bảng so sánh FSx

  Dịch vụ                       Điểm nổi bật
  ----------------------------- ---------------------------------------
  FSx for Windows File Server   Windows, SMB, Active Directory
  FSx for Lustre                HPC, Machine Learning, hiệu năng cao
  FSx for NetApp ONTAP          NetApp, doanh nghiệp, nhiều giao thức
  FSx for OpenZFS               Linux/Unix, hệ thống file ZFS

Mẹo nhớ:

-   Windows → FSx for Windows File Server.
-   HPC/AI → FSx for Lustre.
-   NetApp → FSx for NetApp ONTAP.
-   ZFS → FSx for OpenZFS.

------------------------------------------------------------------------

# 4.9. So sánh EBS, Instance Store, EFS, FSx và S3

Đây là phần tổng hợp quan trọng của chương.

  -----------------------------------------------------------------------------------
  Tiêu chí    EBS            Instance Store   EFS         FSx          S3
  ----------- -------------- ---------------- ----------- ------------ --------------
  Loại lưu    Block          Block            File        File         Object
  trữ                                                                  

  Dữ liệu bền Có             Không đảm bảo    Có          Có           Có
  vững                       khi                                       
                             Stop/Terminate                            

  Cách sử     Attach vào EC2 Gắn cục bộ với   Mount qua   Mount/truy   API/SDK/HTTP
  dụng                       EC2              mạng        cập theo     
                                                          loại FSx     

  Nhiều máy   Multi-Attach   Không phải mục   Có          Tùy loại và  Có thể truy
  cùng truy   có điều kiện   đích sử dụng                 cấu hình     cập qua API
  cập                                                                  

  Phạm vi     Một AZ cho mỗi Máy chủ vật lý   Regional    Tùy loại     Bucket thuộc
              Volume                          hoặc One                 Region
                                              Zone                     

  Ví dụ       Ổ cứng máy chủ Cache tạm        Thư mục     Windows File Ảnh, video,
                                              dùng chung  Server/HPC   backup
  -----------------------------------------------------------------------------------

## Một ví dụ thực tế: Website bán hàng

Bạn đang xây dựng một website bán hàng. Hệ thống có thể sử dụng các dịch
vụ như sau:

**1. EC2**

Máy tính chạy ứng dụng backend.

**2. EBS**

Ổ cứng chứa hệ điều hành và dữ liệu cục bộ cần thiết.

**3. EBS Snapshot**

Sao lưu EBS để có thể khôi phục khi gặp sự cố.

**4. AMI**

Tạo bản mẫu máy chủ đã cài đặt ứng dụng để triển khai thêm EC2.

**5. Instance Store**

Lưu file tạm trong quá trình xử lý.

**6. EFS**

Cho phép nhiều EC2 cùng truy cập một thư mục file dùng chung.

**7. FSx**

Dùng khi ứng dụng cần hệ thống file chuyên biệt như Windows File Server
hoặc Lustre.

**8. S3**

Lưu ảnh, video, file upload và bản sao lưu.

Không phải ứng dụng nào cũng cần tất cả các dịch vụ này. Developer cần
chọn dịch vụ dựa trên cách ứng dụng đọc, ghi và chia sẻ dữ liệu.

------------------------------------------------------------------------

# 4.10. Thực hành: Tạo EBS Volume và gắn vào EC2

## Mục tiêu

Sau bài thực hành, bạn có thể:

-   Tạo một EBS Volume.
-   Attach Volume vào EC2.
-   Nhận diện ổ đĩa trong Linux.
-   Tạo File System.
-   Mount ổ đĩa vào thư mục.
-   Cấu hình tự động Mount sau khi khởi động lại.

## Bước 1: Tạo EBS Volume

Đăng nhập AWS Console, truy cập:

`EC2 → Elastic Block Store → Volumes → Create volume`

Ví dụ cấu hình:

  Thuộc tính          Giá trị
  ------------------- -----------------
  Volume Type         gp3
  Size                10 GiB
  Availability Zone   Cùng AZ với EC2
  Encryption          Enable

Chọn Create volume.

## Bước 2: Attach Volume vào EC2

1.  Chọn Volume vừa tạo.
2.  Chọn Actions → Attach volume.
3.  Chọn EC2 Instance.
4.  Kiểm tra EC2 và Volume cùng AZ.
5.  Chọn Attach volume.

## Bước 3: Kiểm tra ổ cứng trên Linux

SSH vào EC2 và chạy:

``` bash
lsblk
```

Ví dụ:

``` text
NAME          SIZE TYPE MOUNTPOINTS
nvme0n1        30G disk
└─nvme0n1p1    30G part /
nvme1n1        10G disk
```

Trong ví dụ:

-   `nvme0n1`: ổ đĩa chứa hệ điều hành.
-   `nvme1n1`: ổ đĩa mới được gắn vào.

Lưu ý: Tên thiết bị phụ thuộc loại EC2 và cấu hình. EC2 sử dụng Nitro
thường hiển thị EBS dưới dạng thiết bị NVMe. Không được mặc định tên
thiết bị luôn là `/dev/nvme1n1`.

Có thể dùng lệnh sau để đối chiếu thiết bị NVMe với Volume ID trên EC2
Nitro:

``` bash
sudo nvme list
```

## Bước 4: Tạo File System

Ví dụ sử dụng XFS:

``` bash
sudo mkfs -t xfs /dev/nvme1n1
```

> **Cảnh báo:** `mkfs` tạo hệ thống file mới và có thể xóa dữ liệu hiện
> có trên thiết bị. Chỉ chạy trên ổ đĩa mới, trống và đã xác định chính
> xác. Không chạy trên Root Volume hoặc ổ đang chứa dữ liệu quan trọng.

## Bước 5: Tạo thư mục Mount

``` bash
sudo mkdir -p /data
```

## Bước 6: Mount ổ cứng

``` bash
sudo mount /dev/nvme1n1 /data
```

Kiểm tra:

``` bash
df -h
```

Thử tạo file:

``` bash
sudo touch /data/hello.txt
ls -l /data
```

Nếu thấy `hello.txt`, bạn đã có thể ghi dữ liệu vào ổ đĩa mới.

## Bước 7: Tự động Mount sau khi khởi động lại

Nếu chỉ Mount bằng lệnh ở bước 6, cấu hình Mount có thể không còn sau
khi máy chủ khởi động lại.

Lấy UUID:

``` bash
sudo blkid /dev/nvme1n1
```

Ví dụ kết quả:

``` text
/dev/nvme1n1: UUID="abcd-1234" TYPE="xfs"
```

Mở file `/etc/fstab` và thêm dòng:

``` text
UUID=abcd-1234 /data xfs defaults,nofail 0 2
```

Thay UUID ví dụ bằng UUID thực tế của bạn.

Kiểm tra cấu hình trước khi reboot:

``` bash
sudo mount -a
df -h
```

Nếu không có lỗi, cấu hình Mount đã được kiểm tra.

------------------------------------------------------------------------

# 4.11. Thực hành: Sử dụng AWS CLI và AWS SDK

Developer có thể tạo và quản lý EBS bằng code thay vì thao tác thủ công
trên Console.

## 4.11.1. Tạo EBS Volume bằng AWS CLI

``` bash
aws ec2 create-volume \
  --availability-zone ap-southeast-1a \
  --size 10 \
  --volume-type gp3 \
  --encrypted
```

`ap-southeast-1a` chỉ là ví dụ. Hãy chọn đúng AZ của EC2 muốn gắn
Volume.

## 4.11.2. Attach Volume vào EC2

``` bash
aws ec2 attach-volume \
  --volume-id vol-xxxxxxxx \
  --instance-id i-xxxxxxxx \
  --device /dev/sdf
```

Tên thiết bị trong lệnh Attach là tên thiết bị được yêu cầu khi gắn
Volume. Trên EC2 Nitro, tên thiết bị thực tế trong Linux có thể hiển thị
dưới dạng NVMe.

Sau khi Attach, bạn vẫn cần kiểm tra thiết bị, tạo File System và Mount
nếu đây là Volume mới.

## 4.11.3. Tạo Snapshot

``` bash
aws ec2 create-snapshot \
  --volume-id vol-xxxxxxxx \
  --description "Backup application data"
```

## 4.11.4. Tạo AMI

``` bash
aws ec2 create-image \
  --instance-id i-xxxxxxxx \
  --name "my-app-server-v1" \
  --description "Application server image"
```

Theo mặc định, EC2 thực hiện reboot để hỗ trợ tạo image ở trạng thái
nhất quán. Không nên dùng `--no-reboot` nếu chưa hiểu rõ ảnh hưởng đến
tính nhất quán dữ liệu của ứng dụng.

## 4.11.5. Tạo EBS Volume bằng TypeScript và AWS SDK v3

Cài đặt thư viện:

``` bash
npm install @aws-sdk/client-ec2
```

Code:

``` typescript
import {
  EC2Client,
  CreateVolumeCommand,
} from "@aws-sdk/client-ec2";

const ec2 = new EC2Client({
  region: "ap-southeast-1",
});

async function createVolume() {
  const result = await ec2.send(
    new CreateVolumeCommand({
      AvailabilityZone: "ap-southeast-1a",
      Size: 10,
      VolumeType: "gp3",
      Encrypted: true,
      TagSpecifications: [
        {
          ResourceType: "volume",
          Tags: [
            {
              Key: "Name",
              Value: "application-data",
            },
          ],
        },
      ],
    }),
  );

  console.log("Volume ID:", result.VolumeId);
}

createVolume().catch(console.error);
```

Điều cần nhớ:

-   AWS SDK giúp ứng dụng gọi AWS API.
-   IAM Role hoặc IAM Credentials cần có quyền phù hợp.
-   Tạo Volume bằng SDK không tự động Attach hoặc Mount Volume.
-   EC2 và Volume vẫn phải cùng AZ.

------------------------------------------------------------------------

# 4.12. Những lỗi thường gặp khi sử dụng EC2 Storage

## Lỗi 1: Không thể Attach EBS vào EC2

**Nguyên nhân:** EBS và EC2 nằm ở hai Availability Zone khác nhau.

**Cách xử lý:** Tạo Snapshot, sau đó tạo Volume mới tại AZ của EC2 đích.

## Lỗi 2: Terminate EC2 và mất dữ liệu

**Nguyên nhân:** EBS Volume có `DeleteOnTermination = true`.

**Cách xử lý:** Kiểm tra thuộc tính này trước khi Terminate. Nếu cần giữ
dữ liệu, có thể tắt DeleteOnTermination hoặc tạo Snapshot trước.

## Lỗi 3: Mất dữ liệu sau khi Stop EC2

**Nguyên nhân:** Dữ liệu được lưu trên Instance Store.

**Cách xử lý:** Dùng EBS, EFS hoặc S3 cho dữ liệu cần lưu giữ lâu dài.

## Lỗi 4: Không thể dùng một EBS Volume làm thư mục chia sẻ thông thường

**Nguyên nhân:** EBS là Block Storage, không phải File Server dùng
chung.

**Cách xử lý:** Nếu nhiều EC2 cần truy cập chung một thư mục, hãy xem
xét EFS hoặc FSx.

## Lỗi 5: EC2 không thể Mount EFS

Nguyên nhân thường gặp:

-   Security Group chưa cho phép TCP 2049.
-   Thiếu NFS Client hoặc EFS Mount Helper.
-   Mount Target chưa được cấu hình đúng.
-   EC2 không thể kết nối mạng đến EFS.

Cách xử lý: kiểm tra Network, Security Group, Mount Target và cấu hình
Mount.

## Lỗi 6: Snapshot không tự động được tạo

**Nguyên nhân:** Bạn chưa cấu hình chính sách sao lưu.

**Cách xử lý:** Sử dụng Amazon Data Lifecycle Manager hoặc AWS Backup.

## Lỗi 7: Mount nhầm hoặc định dạng nhầm ổ đĩa

**Nguyên nhân:** Không kiểm tra thiết bị trước khi chạy `mkfs`.

**Cách xử lý:**

1.  Chạy `lsblk`.
2.  Đối chiếu Volume ID và thiết bị Linux.
3.  Kiểm tra thiết bị có dữ liệu hay không.
4.  Chỉ định dạng ổ đĩa mới, trống và đã xác định chính xác.

------------------------------------------------------------------------

# 4.13. Kiến thức trọng tâm cho AWS DVA-C02

  Nếu đề bài nói...                  Hãy nghĩ đến...
  ---------------------------------- -----------------------------
  Persistent Block Storage cho EC2   EBS
  General-purpose SSD                gp3
  High IOPS Database                 io2
  Large sequential workloads         st1
  Infrequently accessed HDD data     sc1
  Backup EBS Volume                  EBS Snapshot
  Incremental Backup                 EBS Snapshot
  Tạo nhiều EC2 giống nhau           AMI
  Temporary local storage            Instance Store
  Nhiều EC2 chia sẻ file             EFS
  Windows File Server / SMB          FSx for Windows File Server
  High Performance Computing         FSx for Lustre
  NetApp workload                    FSx for NetApp ONTAP
  ZFS workload                       FSx for OpenZFS
  Object Storage                     S3

## Những bẫy thường gặp

1.  **EBS gắn trực tiếp được vào EC2 ở bất kỳ AZ nào.**\
    Sai. EBS Volume và EC2 phải cùng AZ.

2.  **Stop EC2 sẽ xóa dữ liệu EBS.**\
    Sai. EBS vẫn giữ dữ liệu khi EC2 Stop.

3.  **Terminate EC2 luôn giữ lại EBS.**\
    Sai. Phụ thuộc `DeleteOnTermination`.

4.  **Snapshot lần nào cũng sao lưu lại toàn bộ dữ liệu.**\
    Sai. Snapshot tiếp theo là Incremental Backup.

5.  **Có thể mở EBS Snapshot như file thông thường trong S3 Console.**\
    Sai. AWS quản lý Snapshot trong hạ tầng S3, nhưng người dùng quản lý
    qua EBS/EC2.

6.  **Instance Store giữ dữ liệu sau Stop/Start.**\
    Sai. Instance Store mất dữ liệu khi Stop, Hibernate hoặc Terminate.

7.  **EFS chỉ sử dụng được cho một EC2.**\
    Sai. Nhiều EC2 có thể Mount cùng EFS.

8.  **EBS Multi-Attach hỗ trợ mọi loại EBS.**\
    Sai. Multi-Attach chỉ hỗ trợ một số loại `io1` và `io2` với điều
    kiện tương ứng.

9.  **st1 và sc1 có thể làm Boot Volume.**\
    Sai. Hai loại này không hỗ trợ Boot Volume.

10. **AMI và Snapshot là một.**\
    Sai. AMI là image dùng để khởi tạo EC2; Snapshot là bản sao lưu EBS
    Volume.

------------------------------------------------------------------------

# 4.14. Bài tập kiểm tra kiến thức

Hãy tự chọn đáp án trước khi xem phần giải thích.

## Câu 1

Bạn có một EC2 chạy Linux. Bạn muốn tạo một ổ cứng có thể giữ dữ liệu kể
cả khi EC2 Stop.

Bạn nên chọn dịch vụ nào?

A. Instance Store\
B. Amazon EBS\
C. Amazon CloudWatch\
D. Amazon SQS

## Câu 2

Bạn có một EBS Volume nằm ở Availability Zone A. Bạn muốn sử dụng dữ
liệu này cho EC2 ở Availability Zone B.

Cách thực hiện phù hợp là gì?

A. Attach trực tiếp EBS vào EC2-B.\
B. Reboot EC2-A.\
C. Tạo Snapshot, sau đó tạo EBS mới tại AZ-B.\
D. Chuyển EBS thành Instance Store.

## Câu 3

Một hệ thống cần tạo bản sao lưu EBS Volume định kỳ và chỉ lưu các Block
thay đổi giữa các lần sao lưu.

Bạn nên sử dụng gì?

A. EBS Snapshot.\
B. EC2 Instance Store.\
C. Amazon SQS.\
D. Amazon Route 53.

## Câu 4

Bạn muốn tạo 20 EC2 giống hệt một máy chủ đã cài đặt đầy đủ ứng dụng.

Bạn nên sử dụng gì?

A. EBS Snapshot.\
B. Amazon EFS.\
C. Amazon AMI.\
D. Instance Store.

## Câu 5

Một ứng dụng cần lưu file tạm trong quá trình xử lý. File có thể bị mất
khi EC2 Stop. Bạn muốn ưu tiên lưu trữ cục bộ.

Bạn nên cân nhắc gì?

A. Instance Store.\
B. EBS Snapshot.\
C. Amazon Route 53.\
D. Amazon IAM.

## Câu 6

Bạn có 10 EC2 chạy Linux ở nhiều Availability Zone. Tất cả cần truy cập
chung một thư mục chứa hình ảnh.

Bạn nên chọn gì?

A. Một EBS Volume thông thường.\
B. Instance Store.\
C. Amazon EFS.\
D. AMI.

## Câu 7

Một ứng dụng Windows cần sử dụng Shared Folder qua SMB.

Dịch vụ nào phù hợp nhất?

A. FSx for Lustre.\
B. FSx for Windows File Server.\
C. Instance Store.\
D. EBS Snapshot.

## Câu 8

Một ứng dụng cần lưu trữ lượng dữ liệu lớn để xử lý HPC và Machine
Learning.

Bạn nên xem xét dịch vụ nào?

A. FSx for Lustre.\
B. FSx for Windows File Server.\
C. EBS gp3.\
D. Amazon Route 53.

## Câu 9

Bạn cần một EBS Volume có hiệu năng IOPS cao cho Database quan trọng.

Bạn nên chọn loại nào?

A. sc1.\
B. st1.\
C. io2.\
D. Instance Store.

## Câu 10

Bạn Terminate EC2 và phát hiện EBS Root Volume cũng bị xóa.

Nguyên nhân nào phù hợp nhất?

A. Snapshot bị lỗi.\
B. DeleteOnTermination được bật.\
C. EBS không hỗ trợ lưu trữ lâu dài.\
D. EC2 luôn xóa tất cả dữ liệu.

## Câu 11

Một ứng dụng cần lưu file tạm với tốc độ cao. Dữ liệu có thể được tạo
lại nếu máy chủ gặp sự cố. Loại lưu trữ nào phù hợp nhất?

A. EBS Snapshot.\
B. Instance Store.\
C. EFS Regional.\
D. S3 Glacier Deep Archive.

## Câu 12

Một EC2 không thể Mount EFS. Security Group của EFS chưa cho phép kết
nối từ EC2.

Bạn cần kiểm tra port nào?

A. TCP 22.\
B. TCP 80.\
C. TCP 2049.\
D. TCP 3306.

## Đáp án và giải thích

  -----------------------------------------------------------------------
  Câu                     Đáp án                  Giải thích
  ----------------------- ----------------------- -----------------------
  1                       B                       EBS là Block Storage
                                                  bền vững và vẫn giữ dữ
                                                  liệu khi EC2 Stop.

  2                       C                       Snapshot có thể dùng để
                                                  tạo Volume mới ở AZ
                                                  khác trong cùng Region.

  3                       A                       EBS Snapshot hỗ trợ
                                                  Incremental Backup.

  4                       C                       AMI là mẫu dùng để khởi
                                                  tạo nhiều EC2.

  5                       A                       Instance Store phù hợp
                                                  với dữ liệu tạm thời
                                                  trên máy chủ.

  6                       C                       EFS cho phép nhiều EC2
                                                  cùng truy cập một File
                                                  System.

  7                       B                       FSx for Windows File
                                                  Server hỗ trợ Windows
                                                  và SMB.

  8                       A                       FSx for Lustre phù hợp
                                                  với HPC và Machine
                                                  Learning.

  9                       C                       io2 là loại Provisioned
                                                  IOPS SSD cho workload
                                                  cần IOPS cao.

  10                      B                       Volume bị xóa theo cấu
                                                  hình
                                                  DeleteOnTermination.

  11                      B                       Instance Store phù hợp
                                                  với dữ liệu tạm, có thể
                                                  tạo lại.

  12                      C                       EFS sử dụng NFS, port
                                                  quan trọng là TCP 2049.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 4.15. Tổng kết chương 4

## Các dịch vụ cần nhớ

-   **EBS:** Ổ cứng Block Storage dành cho EC2.
-   **EBS Volume:** Ổ đĩa có thể Attach vào EC2.
-   **EBS Snapshot:** Bản sao lưu của EBS Volume.
-   **AMI:** Bản mẫu dùng để khởi tạo EC2.
-   **Instance Store:** Ổ cứng cục bộ, phù hợp với dữ liệu tạm.
-   **EFS:** File Storage dùng chung cho nhiều EC2.
-   **FSx:** Các hệ thống File Storage chuyên dụng.
-   **S3:** Object Storage.

## 10 điều quan trọng nhất

1.  EBS và EC2 phải cùng Availability Zone để Attach trực tiếp.
2.  EBS vẫn giữ dữ liệu khi EC2 Stop.
3.  Terminate EC2 có thể xóa EBS tùy `DeleteOnTermination`.
4.  EBS Snapshot hỗ trợ Incremental Backup.
5.  Snapshot có thể giúp tạo EBS Volume ở AZ khác.
6.  AMI giúp tạo nhiều EC2 có cấu hình ban đầu giống nhau.
7.  Instance Store mất dữ liệu khi Stop, Hibernate hoặc Terminate.
8.  EFS cho phép nhiều EC2 truy cập cùng File System.
9.  FSx có nhiều loại phù hợp với từng hệ thống file chuyên biệt.
10. EBS, EFS, FSx và S3 có cách tổ chức và truy cập dữ liệu khác nhau.

## Bản đồ ghi nhớ

``` text
                    EC2 STORAGE
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
         EBS        Instance Store     EFS
          |              |              |
          v              v              v
       Snapshot       Temporary      Shared Files
          |
          v
         AMI

       FSx: Specialized File Systems
       S3 : Object Storage
```

**Một câu để ghi nhớ cả chương:**

-   EBS: Ổ cứng của máy chủ.
-   Snapshot: Bản sao lưu ổ cứng.
-   AMI: Bản mẫu máy chủ.
-   Instance Store: Ổ cứng tạm.
-   EFS: Hệ thống file dùng chung.
-   FSx: Hệ thống file chuyên dụng.
-   S3: Kho lưu trữ đối tượng.

Khi hiểu được những khái niệm này, bạn đã nắm được nền tảng quan trọng
để thiết kế hệ thống lưu trữ trên AWS.

------------------------------------------------------------------------

# Tài liệu tham khảo chính thức

Nên ưu tiên tài liệu AWS khi cần kiểm tra giới hạn, loại Volume, giá và
tính năng mới nhất.

-   [Amazon EBS Volume
    Types](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html)
-   [Amazon EBS
    Volumes](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volumes.html)
-   [Amazon EBS
    Snapshots](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-snapshots.html)
-   [Amazon EBS
    Encryption](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-encryption.html)
-   [EBS
    Multi-Attach](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volumes-multi.html)
-   [EC2 Instance
    Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html)
-   [Amazon Machine Images
    (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
-   [Amazon
    EFS](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html)
-   [Amazon FSx](https://docs.aws.amazon.com/fsx/)
-   [Amazon S3 User
    Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

> **Lưu ý học tập:** Các thông số hiệu năng, giới hạn Attach, loại
> Volume và giá dịch vụ có thể thay đổi theo thời gian, Region và loại
> EC2. Khi triển khai thực tế, hãy kiểm tra tài liệu AWS hiện hành thay
> vì học thuộc một con số không có ngữ cảnh.
