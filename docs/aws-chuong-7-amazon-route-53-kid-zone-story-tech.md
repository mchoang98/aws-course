# CHƯƠNG 7 — AMAZON ROUTE 53: CUỐN DANH BẠ THẦN KỲ CỦA INTERNET 🌍

> **Series:** AWS từ con số 0  
> **Phong cách:** KidZone StoryTech — học công nghệ bằng những câu chuyện dễ nhớ  
> **Độ khó:** ⭐⭐☆☆☆ (Dành cho người mới bắt đầu)  
> **Nhân vật đồng hành:** An, Bin và cô Mây

---

## 🎯 Sau chương này, chúng mình sẽ biết gì?

Khi học xong, bạn có thể:

- Giải thích vì sao website cần **tên miền (domain)** và **DNS**.
- Hiểu **Amazon Route 53** làm nhiệm vụ gì — và điều gì nó **không** làm.
- Phân biệt **domain**, **DNS**, **IP**, **hosted zone**, **record** và **TTL**.
- Nhận ra các bản ghi **A, AAAA, CNAME, Alias, MX, TXT, NS, SOA**.
- Hiểu những cách Route 53 lựa chọn máy chủ: **Simple, Weighted, Latency, Failover, Geolocation** và một số cách nâng cao.
- Biết thực hành kiểm tra DNS bằng những lệnh đơn giản.
- Tránh những nhầm lẫn và chi phí không đáng có khi sử dụng AWS.

---

## 7.1. 🏠 Câu chuyện mở đầu: Cửa hàng robot của An biến mất!

Một ngày đẹp trời, An và Bin mở một cửa hàng bán robot đồ chơi trên Internet.

An vui vẻ khoe:

> **An:** “Tớ đã xây xong website! Máy chủ EC2 đang chạy rất tốt. Chỉ cần khách hàng truy cập vào địa chỉ `203.0.113.10` là có thể xem robot của chúng mình!”

Bin nhíu mày:

> **Bin:** “Ơ… nhưng ai mà nhớ nổi dãy số dài như vậy? Tớ còn chưa nhớ hết số điện thoại của mẹ nữa!”

An thấy Bin nói có lý.

Hãy tưởng tượng bạn muốn ghé thăm nhà một người bạn:

- Nếu bạn chỉ có một dãy tọa độ dài và khó nhớ, việc tìm nhà sẽ rất bất tiện.
- Nếu bạn có một địa chỉ dễ nhớ như **“Nhà của bạn An, đường Cầu Vồng”**, mọi chuyện đơn giản hơn nhiều.

Website cũng giống như vậy.

Máy chủ có một địa chỉ số gọi là **IP address**. Con người lại thích những cái tên dễ đọc như `kidzone.example`.

Vậy ai sẽ giúp chuyển **tên dễ nhớ** thành **địa chỉ máy chủ**?

**Đó là DNS!**

> 🧠 **Nhớ nhanh:** DNS giống như **danh bạ điện thoại của Internet**. Bạn nhớ tên người cần gọi, danh bạ giúp tìm số điện thoại tương ứng.

*Lưu ý: `kidzone.example` và địa chỉ `203.0.113.10` chỉ là ví dụ minh họa, không phải website/máy chủ thật để truy cập.*

---

## 7.2. 📒 DNS là gì? Cuốn danh bạ biết nói

**DNS** là viết tắt của **Domain Name System** — Hệ thống phân giải tên miền.

Tên nghe có vẻ khó, nhưng hãy xem cuộc hội thoại này:

> **Bin:** “Tớ muốn vào `kidzone.example`!”  
> **Máy tính:** “Để tớ tìm xem tên này thuộc về địa chỉ IP nào nhé.”  
> **Hệ thống DNS:** “Trong danh bạ ghi rằng tên đó tương ứng với `203.0.113.10`.”  
> **Máy tính:** “Tìm thấy rồi! Bây giờ tớ có thể kết nối đến máy chủ.”

### Ba thứ quan trọng cần phân biệt

| Khái niệm | Hình dung như | Ví dụ |
|---|---|---|
| **Domain (tên miền)** | Tên cửa hàng | `kidzone.example` |
| **IP address (địa chỉ IP)** | Địa chỉ ngôi nhà | `203.0.113.10` |
| **DNS** | Cuốn danh bạ tra tên để tìm địa chỉ | `kidzone.example` → `203.0.113.10` |

**Điểm rất quan trọng:** DNS **không phải** là chiếc xe chở dữ liệu của website.

DNS chủ yếu giúp tìm **địa chỉ để kết nối**. Sau khi có địa chỉ, trình duyệt mới thực sự gửi yêu cầu HTTP/HTTPS đến máy chủ hoặc dịch vụ đích.

### 🗺️ Sơ đồ chuyến đi của một yêu cầu

```text
👧 An gõ kidzone.example vào trình duyệt
                │
                ▼
     📒 Máy tính hỏi hệ thống DNS
                │
                ▼
   🔎 DNS tìm địa chỉ IP tương ứng
                │
                ▼
       ✅ Nhận về 203.0.113.10
                │
                ▼
 🌐 Trình duyệt kết nối đến máy chủ
                │
                ▼
      🤖 Website robot hiện ra!
```

Trong thực tế, hệ thống phân giải DNS có thể phải hỏi qua nhiều cấp và dùng dữ liệu đã lưu tạm (**cache**). Chúng ta chỉ đang đơn giản hóa câu chuyện để dễ hình dung.

### 🎒 Kiểm tra trí nhớ

**Câu hỏi:** Nếu website đổi máy chủ và có IP mới, chúng ta bắt buộc phải đổi tên miền không?

**Đáp án:** **Không!** Chúng ta có thể cập nhật bản ghi DNS để tên miền cũ trỏ tới địa chỉ mới.

---

## 7.3. 🧭 Amazon Route 53 là gì?

Cô Mây xuất hiện và nói:

> “Các em đã biết DNS là cuốn danh bạ. Vậy nếu chúng ta cần một người quản lý danh bạ thật chuyên nghiệp, có khả năng chọn địa chỉ phù hợp và theo dõi hệ thống, AWS có dịch vụ **Amazon Route 53**.”

**Amazon Route 53** là dịch vụ DNS do AWS cung cấp.

Hãy tưởng tượng Route 53 là **người quản lý các biển chỉ đường** cho cửa hàng của An.

Route 53 có ba nhóm công việc chính:

1. **Đăng ký tên miền (Domain registration):** Giúp bạn mua và quản lý tên miền thuộc những đuôi được hỗ trợ.
2. **Quản lý DNS (DNS routing):** Lưu những quy tắc để trả lời câu hỏi “Tên miền này dẫn đến đâu?”.
3. **Kiểm tra sức khỏe (Health checking):** Theo dõi một số tài nguyên hoặc điểm truy cập; khi kết hợp với cấu hình định tuyến phù hợp, có thể giúp chọn đích dự phòng.

> 💡 **Vì sao có số 53?** DNS truyền thống chủ yếu sử dụng **cổng mạng số 53** (port 53). Đó là nguồn gốc của cái tên Route 53.

### Route 53 KHÔNG phải là gì?

Đây là điều rất dễ nhầm:

- Route 53 **không thay thế EC2** để chạy chương trình.
- Route 53 **không thay thế RDS** để lưu cơ sở dữ liệu.
- Route 53 **không phải** bộ cân bằng tải xử lý từng yêu cầu HTTP như Application Load Balancer.
- Chỉ tạo một bản ghi DNS **không có nghĩa** website tự hoạt động hoặc đã có HTTPS.

**Ghi nhớ:** Route 53 giúp người dùng **tìm đúng cửa**; còn bên trong ngôi nhà vẫn cần máy chủ và những dịch vụ khác.

---

## 7.4. 🏷️ Domain là tên cửa hàng, còn Hosted Zone là cuốn sổ quản lý địa chỉ

An quyết định đặt tên miền cho cửa hàng là:

`kidzone.example`

Nhưng làm thế nào Route 53 biết tên đó dẫn đến đâu?

Cô Mây đưa cho An một cuốn sổ.

Trên bìa ghi:

**Hosted Zone — Sổ quản lý DNS của kidzone.example**

Bên trong có các dòng:

| Tên | Loại bản ghi | Giá trị minh họa |
|---|---|---|
| `kidzone.example` | A | `203.0.113.10` |
| `www.kidzone.example` | CNAME | `kidzone.example` |
| `api.kidzone.example` | A | `203.0.113.20` |

Mỗi dòng như vậy được gọi là một **DNS record (bản ghi DNS)**.

### Hosted Zone là gì?

**Hosted Zone** là nơi Route 53 lưu tập hợp các bản ghi DNS của một tên miền hoặc phạm vi tên miền.

Chúng ta có hai loại quan trọng:

**1. Public Hosted Zone — Danh bạ công khai**

Ví dụ: Bạn muốn khách hàng trên Internet tìm được website `kidzone.example`.

Các DNS resolver trên Internet có thể hỏi những máy chủ DNS có thẩm quyền của hosted zone công khai này, **nếu tên miền đã được cấu hình trỏ đúng đến chúng**.

**2. Private Hosted Zone — Danh bạ nội bộ**

Ví dụ: Một hệ thống có website, API và database trong mạng riêng AWS VPC.

Các máy bên trong VPC được liên kết có thể tìm tên như `api.internal.example` mà không cần công bố bản ghi đó ra DNS công khai.

> 🏫 **Hình dung:** Public Hosted Zone như bảng địa chỉ treo ngoài cổng trường. Private Hosted Zone như danh sách phòng nội bộ chỉ những người trong trường được sử dụng.

**Quan trọng:** Private Hosted Zone không tự làm máy chủ an toàn. Quyền truy cập thực tế còn phụ thuộc VPC, Security Group, firewall và cấu hình ứng dụng.

### Một cái bẫy rất thường gặp

> “Em đã tạo Public Hosted Zone trên Route 53, sao tên miền vẫn chưa chạy?”

Có thể vì tên miền của bạn **chưa được cấu hình dùng name server của Hosted Zone đó**.

Tạo sổ danh bạ là một việc. Nói cho Internet biết **phải hỏi cuốn sổ nào** lại là một việc khác.

Phần tiếp theo sẽ giải thích.

---

## 7.5. 👮 Name Server (NS) — Ai giữ cuốn danh bạ?

Hãy tưởng tượng thành phố có rất nhiều cuốn danh bạ.

Bin hỏi:

> “Nếu có hàng triệu cuốn sổ, làm sao Internet biết phải hỏi cuốn nào để tìm `kidzone.example`?”

Cô Mây trả lời:

> “Nhờ hệ thống DNS phân cấp, và những **Name Server** có thẩm quyền.”

Khi bạn tạo một Public Hosted Zone, Route 53 thường cấp cho nó một nhóm **bốn name server**. Các địa chỉ thật sẽ được hiển thị trong bản ghi **NS** của Hosted Zone.

**NS record** cho biết những máy chủ tên miền có thẩm quyền đối với vùng DNS.

### Khi tên miền mua ở nơi khác

Ví dụ bạn mua `myrobotshop.com` tại một nhà đăng ký tên miền, nhưng muốn quản lý DNS bằng Route 53:

1. Tạo Public Hosted Zone `myrobotshop.com` trong Route 53.
2. Xem những name server Route 53 đã cấp.
3. Vào trang quản lý tên miền tại nhà đăng ký.
4. Cập nhật name server của tên miền thành **đúng nhóm name server Route 53**.
5. Tạo hoặc kiểm tra các bản ghi DNS trong Hosted Zone.

> 🚦 **Cực kỳ quan trọng:** Không tự ý thay name server của tên miền đang hoạt động nếu chưa sao chép đầy đủ các bản ghi hiện tại, đặc biệt là bản ghi email. Nếu làm sai, cả website hoặc email có thể ngừng hoạt động.

Nếu đăng ký tên miền trực tiếp bằng Route 53, AWS thường tự tạo Hosted Zone công khai và cấu hình name server tương ứng cho tên miền mới.

---

## 7.6. 🧩 Các loại DNS Record — Mỗi chiếc thẻ có một nhiệm vụ

Bây giờ, cô Mây mở hộp thẻ chỉ đường. Mỗi chiếc thẻ có một chữ cái khác nhau.

### 🅰️ A Record — “Tên này có địa chỉ IPv4 nào?”

Ví dụ:

```text
kidzone.example  →  203.0.113.10
```

**A Record** dùng để ánh xạ tên miền đến một địa chỉ **IPv4**.

Hình dung: **Tên của An → địa chỉ nhà của An.**

### 🅰️🅰️🅰️🅰️ AAAA Record — “Địa chỉ IPv6 là gì?”

Internet còn có hệ địa chỉ **IPv6** dài hơn IPv4.

Ví dụ minh họa:

```text
kidzone.example  →  2001:db8::10
```

**AAAA Record** ánh xạ tên miền đến địa chỉ IPv6.

> 🧠 Mẹo nhớ: A là địa chỉ IPv4; AAAA là địa chỉ IPv6.

### 🔁 CNAME Record — “Đây là tên gọi khác của ai?”

Ví dụ:

```text
www.kidzone.example  →  kidzone.example
```

**CNAME** nói rằng một tên là **bí danh** (tên gọi khác) của tên miền khác.

Hình dung:

> “Bạn Bin còn được gọi là Siêu Nhân Bin. Muốn tìm Siêu Nhân Bin thì cứ tìm Bin!”

**Lưu ý quan trọng:** Không được đặt CNAME ở tên miền gốc (ví dụ `kidzone.example`) theo quy tắc DNS thông thường. CNAME cũng không thể cùng tồn tại với các bản ghi khác tại cùng tên đó.

### 🪄 Alias Record — Tấm thẻ đặc biệt của Route 53

Giả sử website của An sử dụng **Application Load Balancer** hoặc **CloudFront**.

Những dịch vụ này có thể có một tên DNS riêng. Bạn không nên cố gắng sao chép thủ công địa chỉ IP thay đổi của chúng.

Route 53 cung cấp **Alias**, một tính năng riêng giúp trỏ tên miền đến **một số tài nguyên AWS được hỗ trợ**.

Ví dụ khái niệm:

```text
kidzone.example
       │
       ▼
  Alias (kiểu A)
       │
       ▼
  Application Load Balancer
```

**Tại sao Alias hữu ích?**

- Có thể sử dụng tại **tên miền gốc** với đích phù hợp.
- Tiện khi trỏ tới các dịch vụ AWS được Route 53 hỗ trợ.
- Route 53 có thể theo dõi việc thay đổi địa chỉ của một số đích AWS thay vì bạn tự cập nhật IP.

**Cần phân biệt:** Alias **không phải** một loại DNS record chuẩn độc lập như CNAME. Nó là một tính năng của Route 53 nằm trên những bản ghi phù hợp, chẳng hạn A/AAAA.

### ✉️ MX Record — Bưu điện của tên miền

Nếu An muốn có email:

`hello@kidzone.example`

Thì Internet cần biết **máy chủ nào nhận thư** cho tên miền.

Đó là nhiệm vụ của **MX (Mail Exchange)**.

MX record không dùng để mở website. Nó nói cho hệ thống email biết nơi chuyển thư đến.

### 📝 TXT Record — Mẩu giấy xác minh

TXT record lưu thông tin dạng văn bản, thường được dùng để:

- Chứng minh bạn sở hữu hoặc kiểm soát tên miền.
- Cấu hình các quy tắc hỗ trợ xác thực email như SPF, DKIM, DMARC.
- Đáp ứng yêu cầu xác minh của các dịch vụ khác.

Ví dụ, một dịch vụ có thể yêu cầu bạn thêm một chuỗi xác minh cụ thể vào TXT record.

### 📚 NS và SOA — Thông tin của cuốn danh bạ

- **NS (Name Server):** Chỉ ra máy chủ DNS có thẩm quyền.
- **SOA (Start of Authority):** Lưu những thông tin quản trị quan trọng của vùng DNS.

Trong Route 53, một Hosted Zone mới thường có sẵn NS và SOA.

> 🧠 **Mẹo nhớ cả hộp thẻ:**  
> A = địa chỉ IPv4 · AAAA = địa chỉ IPv6 · CNAME = tên khác · Alias = trỏ thuận tiện tới đích được Route 53 hỗ trợ · MX = email · TXT = ghi chú/xác minh · NS = người giữ danh bạ · SOA = thông tin quản trị danh bạ.

---

## 7.7. ⏳ TTL — Vì sao đổi địa chỉ mà bạn vẫn nhìn thấy website cũ?

Một hôm, An chuyển cửa hàng từ máy chủ cũ sang máy chủ mới.

An cập nhật DNS xong và reo lên:

> “Đổi rồi! Bây giờ tất cả mọi người sẽ thấy website mới ngay lập tức!”

Nhưng Bin vẫn truy cập vào địa chỉ cũ.

Tại sao?

Cô Mây mỉm cười:

> “Bởi vì một số hệ thống DNS đang giữ bản sao danh bạ trong một khoảng thời gian!”

Đó là ý tưởng của **TTL — Time To Live**.

TTL thường được tính bằng **giây**. Nó thông báo một giá trị DNS có thể được DNS resolver lưu trong bộ nhớ đệm bao lâu trước khi cần hỏi lại.

Ví dụ:

```text
Record: kidzone.example
TTL: 300 giây
```

300 giây tương đương **5 phút**.

Điều này có nghĩa DNS resolver có thể sử dụng kết quả đã lưu trong một khoảng thời gian thay vì hỏi Route 53 mỗi lần.

### TTL ngắn và TTL dài

| TTL | Ưu điểm | Điều cần lưu ý |
|---|---|---|
| **Ngắn**, ví dụ 60–300 giây | Thay đổi có thể được nhìn thấy sớm hơn khi cache cũ hết hạn | Có thể tăng số lần hỏi DNS |
| **Dài**, ví dụ 3.600 giây | Ít cần hỏi DNS hơn | Thay đổi có thể mất lâu hơn để được nhìn thấy |

**Đừng hiểu nhầm:** Đổi TTL từ 3.600 xuống 60 giây **không xóa ngay** những bản sao DNS đang được lưu với TTL cũ.

> 🚦 **Mẹo thực tế:** Trước khi chuyển máy chủ, hãy cân nhắc giảm TTL từ sớm, kiểm tra cấu hình mới và chỉ đổi bản ghi khi đã sẵn sàng.

---

## 7.8. 🚦 Routing Policy — Route 53 quyết định chỉ đường như thế nào?

Cửa hàng robot của An ngày càng nổi tiếng.

Từ một máy chủ nhỏ, An đã có **nhiều máy chủ**.

Vấn đề mới xuất hiện:

> “Nếu có nhiều địa chỉ, Route 53 sẽ trả lời địa chỉ nào?”

Cách Route 53 quyết định phản hồi DNS được gọi là **Routing Policy** (chính sách định tuyến).

### 1. Simple Routing — Một địa chỉ quen thuộc 🏠

An chỉ có một máy chủ.

```text
kidzone.example → Máy chủ A
```

**Simple Routing** phù hợp với cấu hình DNS đơn giản.

Nó **không tự động kiểm tra sức khỏe rồi chuyển sang máy khác** chỉ vì bạn ghi nhiều IP trong một record.

**Dùng khi:** Website nhỏ, một đích chính hoặc nhu cầu DNS cơ bản.

### 2. Weighted Routing — Chia bánh theo tỉ lệ 🍕

An chuẩn bị thử một giao diện website mới.

- Máy chủ A: giao diện cũ.
- Máy chủ B: giao diện mới.

An muốn phần lớn DNS trả lời về A, chỉ một phần nhỏ về B.

Ví dụ:

```text
Máy chủ A: Weight = 80
Máy chủ B: Weight = 20
```

Hình dung có 100 lá phiếu:

- A được 80 phiếu.
- B được 20 phiếu.

**Weighted Routing** giúp phân phối **tỉ lệ phản hồi DNS** theo trọng số đã cấu hình.

> ⚠️ Đây **không bảo đảm** chính xác 80% người dùng hoặc 80% request HTTP sẽ vào A, vì DNS còn có bộ nhớ đệm và nhiều yếu tố khác.

**Dùng khi:** Thử nghiệm triển khai mới, chuyển dần lưu lượng giữa các hệ thống.

### 3. Latency Routing — Chọn đường có độ trễ thấp 🚀

An có hai hệ thống:

- Một hệ thống ở Singapore.
- Một hệ thống ở Frankfurt.

Khách ở nhiều nơi trên thế giới cùng truy cập website.

Route 53 có thể lựa chọn đích dựa trên **độ trễ mạng được AWS đánh giá** giữa người truy cập và các vùng AWS đã cấu hình.

**Latency Routing** không đơn giản là chọn điểm gần nhất trên bản đồ.

> 🧠 Ví dụ: Nhà gần chưa chắc đi nhanh nếu đường bị tắc! Điều quan trọng là **độ trễ**, không chỉ là số kilômét.

**Dùng khi:** Một ứng dụng được triển khai ở nhiều AWS Region.

### 4. Failover Routing — Cửa hàng dự phòng luôn sẵn sàng 🛟

Một ngày mưa bão, máy chủ chính của An gặp sự cố.

Nếu chỉ có một nơi bán hàng, khách sẽ không thể truy cập.

An chuẩn bị:

- **Primary:** Máy chủ chính.
- **Secondary:** Máy chủ dự phòng.

Khi được cấu hình đúng cùng cơ chế xác định tình trạng hoạt động, Route 53 có thể trả lời DNS về Secondary khi Primary không còn đáp ứng điều kiện sức khỏe.

```text
                👧 Khách hàng
                     │
                     ▼
                  Route 53
                     │
            Máy chủ chính khỏe?
                /         \
              Có           Không
              │              │
              ▼              ▼
         Primary         Secondary
          🏠               🏠
```

**Dùng khi:** Muốn có phương án dự phòng kiểu **active-passive**.

**Điều cần nhớ:** Failover không xuất hiện tự động chỉ vì bạn có hai máy chủ. Bạn cần **cấu hình routing và cơ chế health evaluation phù hợp**.

### 5. Geolocation Routing — Mỗi quốc gia, một bảng chỉ đường 🌏

An muốn hiển thị cửa hàng bằng nhiều ngôn ngữ:

- Khách từ Việt Nam: hệ thống phù hợp cho Việt Nam.
- Khách từ Nhật Bản: hệ thống phù hợp cho Nhật Bản.
- Khách còn lại: hệ thống mặc định.

**Geolocation Routing** có thể trả lời khác nhau dựa trên **vị trí địa lý được xác định từ thông tin DNS/IP**.

Nó không nhất thiết xác định chính xác người dùng đang đứng ở đâu.

> 🧠 Mẹo nhớ: **Geolocation = Địa lý**. Dùng khi bạn muốn chọn đích theo khu vực, quốc gia hoặc những phạm vi được hỗ trợ.

**Lưu ý:** Nên có bản ghi **Default** cho trường hợp vị trí không xác định hoặc không khớp quy tắc.

### 6. Geoproximity Routing — Chọn và điều chỉnh vùng phục vụ 🧲

Cách này cũng liên quan tới vị trí địa lý nhưng tập trung vào khoảng cách giữa người dùng và **vị trí tài nguyên**, đồng thời hỗ trợ điều chỉnh vùng phục vụ bằng một tham số gọi là **bias**.

Hình dung An có hai cửa hàng và có thể “mở rộng” vùng khách thường được dẫn tới một cửa hàng.

**Đây là cách nâng cao.** Mới học chỉ cần phân biệt:

- **Geolocation:** Chọn theo vị trí người dùng khớp quy tắc.
- **Geoproximity:** Chọn theo tương quan vị trí và có thể điều chỉnh vùng phục vụ.

### 7. Multi-value Answer Routing — Đưa ra nhiều địa chỉ khỏe mạnh 🎲

An có một nhóm máy chủ cùng phục vụ website.

Với **Multi-value Answer Routing**, Route 53 có thể trả về **tối đa 8 bản ghi khỏe mạnh**, được chọn từ những bản ghi phù hợp.

Đây là cách phản hồi nhiều giá trị DNS, **không thay thế đầy đủ** một Application Load Balancer.

### 8. IP-based Routing — Chọn theo nhóm IP 🗂️

An có nhu cầu quản lý định tuyến theo những **dải IP cụ thể** được cấu hình.

**IP-based Routing** cho phép tạo quy tắc dựa trên các khối địa chỉ IP (CIDR) để quyết định phản hồi.

Đây cũng là chủ đề nâng cao và chưa cần thực hành ở bài đầu tiên.

### 📊 Bảng chọn nhanh Routing Policy

| Routing Policy | Hình dung dễ nhớ | Phù hợp khi |
|---|---|---|
| **Simple** | Một bảng chỉ đường cơ bản | Một đích hoặc cấu hình đơn giản |
| **Weighted** | Chia bánh theo tỉ lệ | Muốn phân phối tỉ lệ DNS |
| **Latency** | Chọn đường đi nhanh | Nhiều Region, quan tâm độ trễ |
| **Failover** | Có cửa hàng dự phòng | Cần chuyển đích khi có sự cố |
| **Geolocation** | Khách ở đâu, chỉ đường theo đó | Phân phối theo vị trí |
| **Geoproximity** | Điều chỉnh vùng phục vụ | Chính sách theo vị trí tài nguyên |
| **Multi-value** | Trả về nhiều lựa chọn khỏe mạnh | Nhiều đích, cần lọc theo sức khỏe |
| **IP-based** | Quy tắc theo dải IP | Kiểm soát đường đi cho từng mạng |

> 📌 **Mẹo học AWS:** Hãy nhớ trước 5 từ: **Simple – Weighted – Latency – Failover – Geolocation**. Những loại còn lại học sâu khi bạn cần triển khai thực tế.

---

## 7.9. 🩺 Health Check — Bác sĩ của máy chủ

Bin hỏi:

> “Nếu Route 53 phải chuyển sang máy chủ dự phòng, nó biết máy chủ chính bị hỏng bằng cách nào?”

Cô Mây nói:

> “Chúng ta có thể sử dụng **Health Check** — giống như một bác sĩ thường xuyên kiểm tra tình trạng của máy chủ.”

Ví dụ, hệ thống của An có đường dẫn:

`https://myrobotshop.com/health`

Khi mọi thứ bình thường, API trả về một mã HTTP thành công phù hợp với quy tắc kiểm tra.

Khi ứng dụng gặp sự cố, phép kiểm tra có thể không đạt.

### Health Check có thể làm gì?

Tùy kiểu health check, Route 53 có thể:

- Kiểm tra một endpoint thông qua HTTP, HTTPS hoặc TCP.
- Dựa trên một CloudWatch alarm.
- Kết hợp tình trạng của một số health check khác.

Sau đó, tình trạng này có thể được sử dụng trong chính sách routing được cấu hình phù hợp, chẳng hạn Failover.

### Nhưng Health Check KHÔNG làm gì?

- Không tự sửa code bị lỗi.
- Không tự phục hồi database.
- Không tự tạo một máy chủ dự phòng.
- Không tự đảm bảo người dùng đang kết nối được chuyển ngay sang hệ thống khác.

Ngoài ra, thay đổi DNS còn chịu ảnh hưởng của TTL và cache.

> 🏥 **Ví dụ:** Bác sĩ có thể phát hiện cửa hàng đang đóng cửa. Nhưng muốn khách đi sang cửa hàng dự phòng, An vẫn cần chuẩn bị cửa hàng thứ hai và bản đồ chỉ đường từ trước!

---

## 7.10. 🏗️ Route 53 kết hợp với EC2, ALB và CloudFront như thế nào?

Hãy nối kiến thức các chương AWS trước lại.

### Trường hợp A: Website nhỏ chạy trên EC2

```text
👧 Khách hàng
     │
     ▼
📒 Route 53 trả lời IP
     │
     ▼
🖥️ EC2 (web server)
     │
     ▼
🤖 Trang web hiển thị
```

Bạn có thể tạo **A Record** trỏ đến một **Elastic IP** đã gắn cho EC2.

**Vì sao Elastic IP?** Vì public IPv4 thông thường của EC2 có thể thay đổi trong một số tình huống, chẳng hạn khi dừng rồi khởi động lại instance. Elastic IP là địa chỉ IPv4 tĩnh do AWS quản lý và có thể được bạn sử dụng theo cấu hình phù hợp.

> 💰 Chú ý: Public IPv4/Elastic IP có thể phát sinh phí theo chính sách AWS.

### Trường hợp B: Website có nhiều EC2

```text
👧 Khách hàng
     │
     ▼
📒 Route 53
     │
     ▼
⚖️ Application Load Balancer (ALB)
     ├────────► EC2 số 1
     ├────────► EC2 số 2
     └────────► EC2 số 3
```

Trong mô hình này, bạn thường dùng **Alias A/AAAA** (nếu phù hợp) để trỏ từ domain đến ALB.

- **Route 53:** Giúp tìm ALB.
- **ALB:** Tiếp nhận và phân phối các yêu cầu ứng dụng đến những đích phù hợp.

Hai công việc này **khác nhau**.

### Trường hợp C: Website dùng CloudFront

```text
👧 Khách hàng khắp thế giới
               │
               ▼
            Route 53
               │
               ▼
         ☁️ CloudFront
               │
               ▼
        🗃️ Nguồn nội dung
```

Route 53 có thể dùng Alias trỏ đến CloudFront distribution.

Nhưng để website dùng tên miền riêng và HTTPS hoạt động, bạn còn phải cấu hình **custom domain** và **TLS certificate** phù hợp cho CloudFront.

> 🎯 Nhớ nhanh: **Route 53 = tên miền đến đâu?** · **ALB = request vào máy chủ nào?** · **CloudFront = đưa nội dung đến người dùng hiệu quả hơn như thế nào?**

---

## 7.11. 🧪 Phòng thí nghiệm số 1 — Tự tra danh bạ Internet (không cần AWS)

Bạn **không cần trả tiền** hay tạo tài khoản AWS để làm bài thí nghiệm này.

### 🪟 Trên Windows (Command Prompt hoặc PowerShell)

Mở terminal và nhập:

```powershell
nslookup example.com
```

Máy tính sẽ hỏi DNS và hiển thị các thông tin như địa chỉ IP nhận được.

Muốn hỏi xem một tên miền có các name server nào:

```powershell
nslookup -type=NS example.com
```

Nếu dùng PowerShell, bạn cũng có thể thử:

```powershell
Resolve-DnsName example.com -Type A
```

### 🐧 Trên Linux / macOS

Thử:

```bash
nslookup example.com
```

Hoặc dùng `dig` nếu máy đã cài công cụ này:

```bash
dig example.com A
dig example.com NS
```

### 🎒 Nhiệm vụ dành cho nhà thám hiểm

1. Tra bản ghi **A** của một tên miền.
2. Tra bản ghi **NS** của cùng tên miền.
3. Tìm xem kết quả A và NS có điểm gì khác nhau.
4. Thử lại sau vài phút và quan sát.

> 🔐 **An toàn:** Chỉ dùng các công cụ tra DNS bình thường, không thay đổi cấu hình mạng của người khác. Thao tác tra cứu này khác với việc quét hoặc tấn công máy chủ.

---

## 7.12. 🧰 Phòng thí nghiệm số 2 — Tạo bản ghi trên Route 53 (có thể phát sinh phí)

Bài này dành cho người đã có tài khoản AWS và **sở hữu một tên miền thật**.

**Nếu chỉ muốn học khái niệm, bạn có thể đọc mà không thực hiện.**

### 🎯 Mục tiêu

Làm cho tên miền như `myrobotshop.com` trỏ đến một máy chủ web của bạn.

### Bước 1. Chuẩn bị

Bạn cần:

- Tài khoản AWS có quyền quản lý Route 53 phù hợp.
- Tên miền thật do bạn sở hữu.
- Một website đang chạy ở đích, ví dụ EC2 với Elastic IP, hoặc ALB/CloudFront đã cấu hình.
- Đã kiểm tra chi phí Route 53 và các dịch vụ liên quan.

**Không dùng `203.0.113.10` để triển khai thật** — đó chỉ là địa chỉ dành cho tài liệu minh họa.

### Bước 2. Tạo Hosted Zone (nếu chưa có)

1. Vào **AWS Management Console**.
2. Tìm **Route 53**.
3. Chọn **Hosted zones**.
4. Chọn **Create hosted zone**.
5. Nhập tên miền bạn sở hữu.
6. Chọn **Public hosted zone** nếu muốn quản lý DNS công khai.
7. Tạo Hosted Zone và xem bản ghi **NS**, **SOA**.

**Cẩn thận:** Kiểm tra xem bạn đã có Hosted Zone đúng cho tên miền đó chưa; tạo trùng thường khiến bạn phải quản lý nhiều bộ name server và có thể tăng phí.

### Bước 3. Cấu hình name server tại nhà đăng ký

Nếu domain được mua từ nơi khác, hãy cập nhật tên miền để dùng **những name server do Route 53 cấp cho Hosted Zone vừa tạo**.

Chỉ thực hiện khi bạn đã ghi lại và chuẩn bị đầy đủ các DNS record cũ (đặc biệt MX/TXT liên quan đến email).

Nếu DNS của bạn đang được quản lý ở một dịch vụ khác và bạn không muốn chuyển, bạn **không bắt buộc phải dùng Route 53**.

### Bước 4. Tạo bản ghi A

Giả sử bạn đang dùng EC2 với **Elastic IP thật**:

1. Chọn Hosted Zone cần chỉnh.
2. Chọn **Create record**.
3. Để trống **Record name** nếu muốn trỏ tên miền gốc.
4. Chọn **Record type: A**.
5. Tắt tùy chọn Alias nếu bạn định nhập trực tiếp IPv4.
6. Điền Elastic IP thật.
7. Chọn **Simple routing**.
8. Chọn TTL phù hợp, ví dụ **300 giây**.
9. Lưu bản ghi.

Với **ALB hoặc CloudFront**, hãy cân nhắc cấu hình **Alias** đến tài nguyên được hỗ trợ thay vì tự nhập một IP thay đổi theo thời gian.

### Bước 5. Kiểm tra kết quả

Trên Windows:

```powershell
nslookup myrobotshop.com
```

Bạn có thể kiểm tra NS:

```powershell
nslookup -type=NS myrobotshop.com
```

Nếu website không mở được, hãy phân biệt ba câu hỏi:

1. **DNS đã trả về đúng đích chưa?**
2. **Máy chủ/dịch vụ có đang chạy và cho phép kết nối HTTP/HTTPS không?**
3. **Domain và chứng chỉ HTTPS đã được cấu hình đúng chưa?**

**DNS đúng không đồng nghĩa website chắc chắn hoạt động.**

### Bước 6. Dọn dẹp sau khi thực hành

Nếu bạn không cần tiếp tục sử dụng:

- Xóa các bản ghi thử nghiệm không còn dùng.
- Cân nhắc xóa Hosted Zone thử nghiệm nếu không có dịch vụ nào phụ thuộc vào nó.
- Kiểm tra Billing để biết chi phí thực tế.
- Chỉ xóa Hosted Zone sau khi đã xác nhận tên miền không còn dùng các NS đó, tránh gây gián đoạn website và email.

---

## 7.13. 💸 Route 53 có miễn phí không?

Cô Mây hỏi:

> “Các em nhớ gì về AWS? Đám mây rất tiện, nhưng phải biết quản lý chi phí!”

**Route 53 không phải là dịch vụ mặc định miễn phí hoàn toàn.**

Theo bảng giá công khai của AWS tại thời điểm biên soạn (tháng 11/2026), những khoản quan trọng gồm:

| Khoản phí | Mức tham khảo | Lưu ý |
|---|---|---|
| **Hosted Zone** | **0,50 USD/zone/tháng** cho 25 zone đầu tiên | Theo bảng giá AWS; zone tiếp theo có bậc giá khác |
| **Standard DNS queries** | **0,40 USD / 1 triệu truy vấn** trong bậc đầu tiên | Một số loại routing có giá truy vấn khác |
| **Đăng ký tên miền** | Tùy đuôi tên miền | Tính riêng phí DNS |
| **Health Check** | Tùy loại và điều kiện | Có trường hợp đủ điều kiện ưu đãi |
| **EC2, ALB, CloudFront, IPv4…** | Tính riêng | Không nằm trong phí Hosted Zone |

**Ví dụ đơn giản:** Nếu chỉ tạo một Public Hosted Zone và sử dụng rất ít DNS query, khoản cơ bản thường đáng chú ý nhất là **phí Hosted Zone khoảng 0,50 USD/tháng**; phí khác phụ thuộc cách sử dụng.

*Không coi ví dụ này là báo giá trọn gói. Giá và điều kiện miễn phí có thể thay đổi; kiểm tra trực tiếp tại [AWS Route 53 Pricing](https://aws.amazon.com/route53/pricing/).*

### Những sai lầm có thể làm mất tiền

- Tạo nhiều Hosted Zone thử nghiệm rồi bỏ quên.
- Bật Health Check hoặc tính năng nâng cao mà không xem bảng giá.
- Mua tên miền chỉ để làm một bài tập ngắn.
- Quên tài nguyên phía sau website như EC2, ALB hay Public IPv4.
- Nghĩ rằng đăng ký tên miền và quản lý DNS là cùng một khoản phí.

> 💡 **Mẹo tiết kiệm:** Với người chỉ đang học DNS, hãy thực hành bằng `nslookup` và sơ đồ trước. Khi thực sự cần quản lý tên miền trên AWS, mới tạo Route 53 Hosted Zone.

---

## 7.14. 🧠 Những nhầm lẫn mà cả người lớn cũng hay mắc

### ❓ 1. Có tên miền là website sẽ chạy?

**Không.** Bạn còn cần dịch vụ phục vụ nội dung, cấu hình mạng và ứng dụng phù hợp.

### ❓ 2. Có EC2 là tự có tên miền đẹp?

**Không.** EC2 có thể có public IP hoặc public DNS name do AWS cấp, nhưng tên miền riêng cần bạn sở hữu và cấu hình.

### ❓ 3. Tạo Hosted Zone xong là Internet lập tức dùng nó?

**Chưa chắc.** Tên miền còn cần được cấu hình đúng name server hoặc cơ chế ủy quyền DNS.

### ❓ 4. Route 53 chuyển hướng mọi HTTP request?

**Không.** Route 53 chủ yếu phản hồi **truy vấn DNS**. HTTP redirect (như 301/302) là chuyện khác và cần cấu hình ở web server hoặc dịch vụ phù hợp.

### ❓ 5. Weighted 90/10 nghĩa là chính xác 90 người trong 100 người sẽ đến máy chủ A?

**Không.** Trọng số ảnh hưởng đến phản hồi DNS, không đảm bảo tỉ lệ người dùng hay request chính xác tuyệt đối.

### ❓ 6. Cứ bật Health Check là website tự phục hồi?

**Không.** Bạn cần có hệ thống dự phòng, routing phù hợp và kế hoạch xử lý sự cố.

### ❓ 7. TTL 60 giây nghĩa là chắc chắn toàn bộ thế giới đổi IP trong 60 giây?

**Không.** DNS còn có cache ở nhiều tầng và hành vi của trình duyệt, ứng dụng, mạng.

### ❓ 8. Route 53 chỉ hoạt động với website chạy trên AWS?

**Không.** Route 53 có thể quản lý DNS cho tên miền trỏ tới những tài nguyên bên ngoài AWS; khả năng Alias chỉ áp dụng cho một số đích được hỗ trợ.

---

## 7.15. 🎮 Thử thách: Bạn có trở thành “Nhà chỉ đường Internet” không?

Hãy thử trả lời **trước khi nhìn đáp án** nhé!

**Câu 1.** Bạn muốn `kidzone.example` trỏ trực tiếp tới IPv4. Nên dùng bản ghi nào?

A. MX · B. A · C. TXT

**Câu 2.** Khách hàng không thể nhớ IP của máy chủ. Hệ thống nào giúp tìm địa chỉ từ tên miền?

A. DNS · B. RDS · C. IAM

**Câu 3.** Cửa hàng có 2 máy chủ, muốn đích dự phòng thay thế đích chính khi có lỗi và đã cấu hình theo dõi sức khỏe. Chọn kiểu routing nào?

A. Simple · B. Failover · C. Geolocation

**Câu 4.** Một website có máy chủ ở nhiều AWS Region và muốn lựa chọn theo độ trễ mạng.

A. Latency · B. Weighted · C. MX

**Câu 5.** Bản ghi nào thường phục vụ việc nhận email?

A. MX · B. AAAA · C. NS

**Câu 6.** `www.kidzone.example` được khai báo như tên khác của `kidzone.example`. Bản ghi nào thường phù hợp?

A. CNAME · B. TXT · C. SOA

**Câu 7.** Route 53 có tự chạy code website không?

A. Có · B. Không

**Câu 8.** Với Weighted 80/20, có đảm bảo đúng 80% HTTP request tới đích thứ nhất không?

A. Có · B. Không

**Câu 9.** TTL dùng để làm gì?

A. Tính thời gian cache DNS · B. Mua tên miền · C. Lưu ảnh sản phẩm

**Câu 10.** Nếu tạo Hosted Zone nhưng chưa cấu hình name server chính xác, tên miền có chắc chắn dùng Hosted Zone đó không?

A. Chắc chắn · B. Không

<details>
<summary>🎉 Bấm xem đáp án</summary>

1. **B — A Record**.
2. **A — DNS**.
3. **B — Failover Routing**.
4. **A — Latency Routing**.
5. **A — MX Record**.
6. **A — CNAME Record**.
7. **B — Không**.
8. **B — Không**.
9. **A — Thời gian cache DNS**.
10. **B — Không**.

**Tự chấm điểm:**
- **9–10 câu đúng:** 🏆 Chuyên gia chỉ đường nhí!
- **7–8 câu đúng:** 🌟 Nhà thám hiểm DNS rất khá!
- **5–6 câu đúng:** 🚀 Đã hiểu nền tảng, thử ôn lại nhé!
- **Dưới 5 câu đúng:** 🧩 Đọc lại câu chuyện cửa hàng robot và làm thử lần nữa.

</details>

---

## 7.16. 🏁 Tổng kết: 6 điều phải nhớ

Sau chuyến phiêu lưu cùng An, Bin và cô Mây, chúng ta đã học được:

1. **Domain** là tên dễ nhớ; **IP** là địa chỉ máy chủ.
2. **DNS** là hệ thống giúp tra tên miền để tìm thông tin kết nối.
3. **Route 53** là dịch vụ AWS để quản lý DNS, đăng ký tên miền và kiểm tra tình trạng hoạt động.
4. **Hosted Zone** giống như cuốn sổ chứa các DNS record; **NS** giúp chỉ ra những máy chủ DNS có thẩm quyền.
5. **Routing Policy** giúp Route 53 chọn cách phản hồi DNS: đơn giản, chia tỉ lệ, chọn theo độ trễ, dự phòng hoặc vị trí địa lý.
6. **TTL, Health Check và chi phí** đều quan trọng khi đưa website vào sử dụng thật.

### 🌈 Lời nhắn từ cô Mây

> “Một website có thể có máy chủ rất mạnh, nhưng nếu không ai biết địa chỉ để tìm đến, nó giống như một cửa hàng đẹp nằm giữa khu rừng mà chẳng có biển chỉ đường. Route 53 chính là người giúp chúng ta đặt những tấm biển ấy đúng chỗ!”

### 🔭 Chương tiếp theo

**Chương 8 — Amazon VPC Basics: Xây dựng thành phố mạng riêng trên AWS.**

Nếu Route 53 giúp mọi người tìm **đường đến cửa hàng**, thì VPC sẽ dạy chúng ta cách xây **đường phố, cổng ra vào và khu vực riêng** cho những máy chủ bên trong thế giới AWS.

---

## 📚 Tài liệu AWS chính thức để phụ huynh và giáo viên tham khảo

Tài liệu biên soạn theo cách kể chuyện để trẻ em dễ hiểu; các ví dụ công nghệ và lưu ý kỹ thuật được đối chiếu với tài liệu chính thức:

1. [Amazon Route 53 Concepts](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html) — DNS, Hosted Zone, TTL, Routing Policy.
2. [Supported DNS Record Types](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html) — A, AAAA, CNAME, MX, NS, TXT...
3. [Choosing a Routing Policy](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html) — Các loại routing của Route 53.
4. [Choosing Between Alias and Non-Alias Records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html) — Khác biệt giữa Alias và CNAME.
5. [Configuring DNS Routing for a New Domain](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-configuring-new-domain.html) — Tạo DNS và cấu hình name server.
6. [AWS Route 53 Pricing](https://aws.amazon.com/route53/pricing/) — Bảng giá chính thức.

---

*KidZone StoryTech — Khi công nghệ phức tạp trở thành một câu chuyện dễ hiểu.*
