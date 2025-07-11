
```markdown
# Database Resource Optimization với Machine Learning

*Một giải pháp chủ động để dự báo và tự động điều chỉnh tài nguyên cơ sở dữ liệu nhằm giảm chi phí và cải thiện hiệu suất.*

---

## Tóm tắt dự án
Trong bối cảnh điện toán đám mây phát triển mạnh mẽ, việc quản lý và tối ưu hóa chi phí cho các dịch vụ cơ sở dữ liệu (CSDL) trên AWS đang trở thành một thách thức lớn đối với nhiều doanh nghiệp. Tình trạng cấp phát tài nguyên thủ công hoặc dựa trên các quy tắc tĩnh thường dẫn đến hai vấn đề chính: thừa tài nguyên (over-provisioning) gây lãng phí chi phí, hoặc thiếu tài nguyên (under-provisioning) làm suy giảm hiệu suất ứng dụng và ảnh hưởng đến trải nghiệm người dùng. Các phương pháp quản lý truyền thống tỏ ra kém hiệu quả trong việc đối phó với các mẫu khối lượng công việc (workload) biến đổi phức tạp và khó dự đoán.

Workshop này đề xuất xây dựng một hệ thống thông minh sử dụng Machine Learning (ML) trên nền tảng AWS để giải quyết bài toán trên. Giải pháp sẽ tự động thu thập các chỉ số hoạt động theo thời gian thực từ các CSDL như Amazon RDS và Aurora, bao gồm CPU utilization, memory usage, IOPS, và số lượng kết nối. Dữ liệu lịch sử này sẽ được sử dụng để huấn luyện các mô hình ML, cụ thể là các mô hình dự báo chuỗi thời gian (time-series forecasting), nhằm dự đoán chính xác nhu cầu tài nguyên trong tương lai gần.

Dựa trên kết quả dự báo của mô hình, hệ thống sẽ đưa ra các khuyến nghị tối ưu hóa hoặc tự động thực thi các hành động điều chỉnh. Các hành động này có thể bao gồm việc tăng hoặc giảm quy mô (scaling up/down) của các phiên bản CSDL, hoặc điều chỉnh các tham số cấu hình một cách phù hợp. Kiến trúc giải pháp sẽ tận dụng sức mạnh của các dịchg vụ AWS được quản lý và có khả năng mở rộng cao như Amazon SageMaker để xây dựng và triển khai mô hình ML, AWS Lambda cho các tác vụ tự động hóa, Amazon Kinesis để xử lý luồng dữ liệu, và Amazon S3 làm nơi lưu trữ dữ liệu bền vững.

**Mục tiêu chính của dự án là mang lại ba lợi ích cốt lõi:**
* **Tối ưu hóa chi phí:** Giảm thiểu chi phí vận hành CSDL trên AWS bằng cách loại bỏ việc cấp phát thừa tài nguyên, chỉ trả tiền cho những gì thực sự cần thiết.
* **Cải thiện hiệu suất và độ tin cậy:** Đảm bảo CSDL luôn có đủ tài nguyên để xử lý các đỉnh tải đột ngột, từ đó duy trì hiệu suất ổn định và nâng cao độ tin cậy của ứng dụng.
* **Tăng cường hiệu quả vận hành:** Tự động hóa các quy trình giám sát và điều chỉnh thủ công, giải phóng thời gian cho đội ngũ DevOps và kỹ sư để tập trung vào các nhiệm vụ chiến lược hơn.

Dự án sẽ được triển khai theo nhiều giai đoạn, từ thu thập dữ liệu, xây dựng mô hình, đến tích hợp và tự động hóa. Các chỉ số thành công (Success Metrics) sẽ được định nghĩa rõ ràng, chẳng hạn như phần trăm giảm chi phí CSDL hàng tháng và giảm thời gian phản hồi (latency) của truy vấn. Phân tích lợi tức đầu tư (ROI) sẽ chứng minh rằng chi phí phát triển và vận hành hệ thống sẽ nhanh chóng được bù đắp bởi khoản tiết kiệm đáng kể từ việc tối ưu hóa tài nguyên. Bằng cách áp dụng AIOps (AI for IT Operations), giải pháp này không chỉ giải quyết một vấn đề kỹ thuật cấp bách mà còn mang lại giá trị kinh doanh lâu dài, giúp doanh nghiệp xây dựng một hạ tầng công nghệ hiệu quả, linh hoạt và có tính cạnh tranh cao.

---

## 1. Tuyên bố vấn đề
### Tình hình hiện tại
Trong hầu hết các tổ chức, việc quản lý tài nguyên cho các cơ sở dữ liệu (CSDL) như Amazon RDS hoặc Aurora được thực hiện theo cách thủ công và phản ứng. Các đội ngũ DevOps hoặc quản trị viên CSDL (DBA) thường cấp phát tài nguyên dựa trên kinh nghiệm hoặc dự đoán tải cao nhất (peak load). Cách tiếp cận này dẫn đến một trong hai kịch bản không mong muốn:
* **Cấp phát thừa (Over-provisioning):** Để đảm bảo ứng dụng không bị sập khi có lượng truy cập đột biến, các kỹ sư thường chọn các loại instance CSDL lớn hơn nhiều so với nhu cầu trung bình. Điều này dẫn đến việc tài nguyên (CPU, RAM) phần lớn thời gian không được sử dụng hết, gây lãng phí chi phí đáng kể trên hóa đơn AWS hàng tháng.
* **Cấp phát thiếu (Under-provisioning):** Để tiết kiệm chi phí, một số tổ chức lại cấp phát tài nguyên ở mức tối thiểu. Khi khối lượng công việc tăng đột ngột (ví dụ: trong các chiến dịch marketing, giờ cao điểm), CSDL trở nên quá tải, dẫn đến hiệu suất ứng dụng chậm, thời gian phản hồi kéo dài và thậm chí là ngừng hoạt động.

### Thách thức chính
* **Khó dự đoán Workload:** Khối lượng công việc của CSDL thường biến động theo chu kỳ (ngày/đêm, ngày trong tuần) và các sự kiện đột xuất. Việc dự đoán chính xác các đỉnh tải này bằng phương pháp thủ công là cực kỳ khó khăn.
* **Phản ứng chậm:** Quá trình phát hiện vấn đề hiệu suất, phân tích nguyên nhân và thực hiện điều chỉnh (ví dụ: nâng cấp instance) thường mất nhiều thời gian, dẫn đến việc người dùng đã bị ảnh hưởng trước khi sự cố được khắc phục.
* **Sự phức tạp của việc tinh chỉnh:** Việc chọn đúng loại instance, cấu hình IOPS, và tinh chỉnh hàng trăm tham số CSDL đòi hỏi kiến thức chuyên sâu và tốn nhiều thời gian thử nghiệm.
* **Thiếu tự động hóa thông minh:** Các công cụ tự động mở rộng (auto-scaling) hiện có của AWS cho RDS thường dựa trên các ngưỡng (threshold) đơn giản (ví dụ: CPU > 80%). Cách tiếp cận này mang tính phản ứng (reactive), không có khả năng chuẩn bị trước cho các đỉnh tải đã được dự báo.

### Tác động đến các bên liên quan
* **Đội ngũ DevOps/SRE:** Chịu áp lực liên tục trong việc giám sát hệ thống, thường xuyên phải "chữa cháy" các sự cố về hiệu suất vào ban đêm hoặc cuối tuần.
* **Nhà phát triển (Developers):** Bị ảnh hưởng bởi hiệu suất CSDL chậm, gây khó khăn trong việc phát triển và kiểm thử các tính năng mới.
* **Bộ phận Tài chính (Finance):** Đối mặt với hóa đơn AWS khó dự đoán và chi phí vận hành (OPEX) cao hơn mức cần thiết.
* **Người dùng cuối (End Users):** Trải nghiệm sản phẩm kém (tải trang chậm, lỗi giao dịch), làm giảm sự hài lòng và lòng trung thành.

### Hậu quả kinh doanh
* **Chi phí vận hành cao (TCO):** Lãng phí tiền bạc cho các tài nguyên không được sử dụng.
* **Giảm doanh thu:** Hiệu suất kém có thể dẫn đến việc khách hàng từ bỏ giỏ hàng, giảm tỷ lệ chuyển đổi và gây tổn thất doanh thu trực tiếp.
* **Tổn hại thương hiệu:** Trải nghiệm người dùng không ổn định làm suy giảm uy tín và hình ảnh thương hiệu.
* **Giảm năng suất nội bộ:** Các kỹ sư tốn thời gian vào việc quản lý hạ tầng thay vì tạo ra các giá trị kinh doanh mới.

---

## 2. Kiến trúc giải pháp
### Tổng quan kiến trúc
Giải pháp được thiết kế theo một quy trình khép kín, dựa trên các dịch vụ serverless và được quản lý của AWS để đảm bảo tính tự động, khả năng mở rộng và hiệu quả chi phí. Luồng hoạt động chính bao gồm: Thu thập dữ liệu -> Xử lý và lưu trữ -> Huấn luyện và dự báo ML -> Hành động và thông báo.

### Các dịch vụ AWS được sử dụng
* **Amazon RDS / Aurora:** Là CSDL mục tiêu cần tối ưu hóa.
* **Amazon CloudWatch:** Thu thập các chỉ số hiệu suất cơ bản theo thời gian (metrics) như CPUUtilization, DatabaseConnections, FreeableMemory, ReadIOPS, WriteIOPS.
* **RDS Performance Insights:** Cung cấp dữ liệu chi tiết hơn về tải CSDL và các truy vấn tốn nhiều tài nguyên nhất (top queries), giúp phân tích sâu hơn.
* **Amazon Kinesis Data Firehose:** Dịch vụ để thu thập và truyền tải (stream) dữ liệu metrics từ CloudWatch đến S3 một cách đáng tin cậy.
* **Amazon S3 (Simple Storage Service):** Đóng vai trò là data lake, lưu trữ toàn bộ dữ liệu metrics lịch sử để phục vụ cho việc phân tích và huấn luyện mô hình ML.
* **AWS Glue:** Dịch vụ ETL (Extract, Transform, Load) được sử dụng để chạy các công việc xử lý dữ liệu, làm sạch và chuẩn bị (data preparation) dữ liệu từ S3 trước khi đưa vào huấn luyện.
* **Amazon SageMaker:** Nền tảng ML toàn diện. Được sử dụng để:
    * **SageMaker Studio/Notebooks:** Khám phá dữ liệu và phát triển mô hình.
    * **SageMaker Training Jobs:** Huấn luyện các mô hình dự báo chuỗi thời gian (ví dụ: DeepAR, ARIMA) trên quy mô lớn.
    * **SageMaker Endpoints / Batch Transform:** Triển khai mô hình để đưa ra dự đoán (inference) theo thời gian thực hoặc theo lô.
* **AWS Lambda:** Đóng vai trò là "bộ não" điều phối. Các hàm Lambda sẽ được kích hoạt theo lịch trình (ví dụ: hàng giờ) để:
    * Gọi SageMaker endpoint để lấy kết quả dự báo.
    * Phân tích kết quả và quyết định hành động cần thiết (ví dụ: cần nâng cấp instance db.t3.medium lên db.t3.large).
    * Thực thi hành động thông qua AWS SDK (ví dụ: gọi API để sửa đổi RDS instance) hoặc gửi thông báo.
* **Amazon SNS (Simple Notification Service):** Gửi thông báo (email, SMS, tin nhắn Slack) đến các quản trị viên về các khuyến nghị tối ưu hóa hoặc các hành động đã được tự động thực hiện.
* **IAM (Identity and Access Management):** Quản lý quyền truy cập an toàn cho tất cả các dịch vụ, đảm bảo các thành phần chỉ có quyền hạn tối thiểu cần thiết để hoạt động (Principle of Least Privilege).

### Thiết kế thành phần
* **Data Ingestion:** Một quy tắc Amazon EventBridge (CloudWatch Events) được lên lịch chạy 5 phút một lần, kích hoạt một hàm Lambda. Hàm này lấy các metrics mới nhất từ CloudWatch và Performance Insights, sau đó đẩy dữ liệu vào Kinesis Data Firehose. Firehose tự động gom nhóm dữ liệu và ghi vào S3 bucket dưới dạng các file có cấu trúc (ví dụ: Parquet).
* **Model Training:** Một quy tắc EventBridge khác được lên lịch chạy hàng tuần (hoặc khi cần), kích hoạt một công việc AWS Glue để xử lý toàn bộ dữ liệu lịch sử trong S3. Sau khi Glue hoàn thành, nó sẽ kích hoạt một SageMaker Training Job để huấn luyện lại mô hình ML với dữ liệu mới nhất, đảm bảo mô hình không bị lỗi thời (model drift).
* **Prediction & Action:** Hàng giờ, một quy tắc EventBridge kích hoạt hàm Lambda "Prediction". Hàm này chuẩn bị dữ liệu đầu vào (ví dụ: chuỗi thời gian của 24 giờ qua) và gọi SageMaker endpoint để dự báo nhu cầu tài nguyên cho 6 giờ tới.
* **Decision Logic:** Hàm Lambda "Action" chứa logic để diễn giải dự báo. Ví dụ: "Nếu CPU dự báo sẽ vượt 80% trong hơn 15 phút liên tục, và instance hiện tại là db.t3.medium, hãy tạo một khuyến nghị nâng cấp lên db.t3.large."
* **Automation & Notification:** Dựa trên cấu hình, hàm "Action" có thể:
    * **Chế độ khuyến nghị:** Gửi một tin nhắn chi tiết qua SNS đến kênh của đội ngũ DevOps, nêu rõ vấn đề và hành động đề xuất.
    * **Chế độ tự động:** Trực tiếp gọi AWS RDS API để sửa đổi instance. Một thông báo về hành động đã thực hiện sẽ được gửi qua SNS.

### Kiến trúc bảo mật
* **Mã hóa:** Tất cả dữ liệu được mã hóa khi lưu trữ (at rest) trên S3 và RDS bằng AWS KMS. Dữ liệu được mã hóa khi truyền (in transit) bằng TLS.
* **Mạng:** Tất cả các tài nguyên (RDS, Lambda, SageMaker endpoints) được đặt trong một VPC (Virtual Private Cloud) riêng biệt. Security Groups được cấu hình chặt chẽ để chỉ cho phép lưu lượng truy cập cần thiết giữa các thành phần.
* **Quyền hạn:** Các IAM Roles được định nghĩa chi tiết cho từng dịch vụ. Ví dụ: Lambda role chỉ có quyền đọc từ S3 và gọi SageMaker, không có quyền xóa S3 bucket.

### Thiết kế khả năng mở rộng
* **Serverless:** Việc sử dụng Lambda, Kinesis, S3, Glue giúp hệ thống tự động mở rộng theo khối lượng dữ liệu mà không cần quản lý máy chủ.
* **SageMaker:** Các công việc huấn luyện và các endpoint của SageMaker có thể dễ dàng mở rộng để xử lý các tập dữ liệu lớn hơn hoặc lượng yêu cầu dự báo cao hơn.
* **Kiến trúc phi đồng bộ:** Việc sử dụng Kinesis và S3 giúp tách rời các thành phần, cho phép hệ thống thu thập dữ liệu hoạt động độc lập với hệ thống huấn luyện và dự báo, tăng cường độ tin cậy.

---

* **Quản lý dự án (Part-time):** Theo dõi tiến độ, quản lý rủi ro và giao tiếp với các bên liên quan.
---
## 5. Ước tính ngân sách

Phần này phân tích chi phí dự án theo hai giai đoạn: **Chi phí phát triển một lần** trong 1 tháng của workshop và **Chi phí vận hành/ROI dự kiến** nếu giải pháp được triển khai thực tế.

### 💰 Chi phí Phát triển (Workshop 1 tháng)
Đây là chi phí một lần để xây dựng và hoàn thành sản phẩm khả thi tối thiểu (MVP) trong workshop.

* **Chi phí Nhân sự:** Đây là chi phí lớn nhất.
    * *Giả định:* Lương trung bình cho một kỹ sư Cloud/ML tại Việt Nam là khoảng **$2,500 USD/tháng**.
    * *Tính toán:* `$2,500/kỹ sư × 2 kỹ sư × 1 tháng`
    * **Tổng chi phí phát triển ước tính:** **`$5,000 USD`**

* **Chi phí Hạ tầng Workshop:**
    * Các chi phí cho `SageMaker training`, `hosting endpoint`, và các dịch vụ khác trong 1 tháng phát triển.
    * Phần lớn các dịch vụ này có thể nằm trong **Bậc miễn phí của AWS (AWS Free Tier)** cho các tài khoản mới.
    * **Tổng chi phí hạ tầng trong 1 tháng workshop (dự phòng):** **`~$100 - $200 USD`**

**➡️ Tổng chi phí đầu tư ban đầu (ước tính):** **`$5,200 USD`**

---

### 📈 Chi phí Vận hành và Phân tích ROI (Dự kiến sau triển khai)
Đây là các chi phí và lợi ích được dự báo hàng tháng nếu hệ thống được đưa vào vận hành thực tế.

#### Chi phí Vận hành Hàng tháng

* **Chi phí Hạ tầng AWS:**
    * **Amazon SageMaker Endpoint (`ml.t3.medium`):** ~$55/tháng
    * **Amazon SageMaker Training Jobs (4 giờ/tháng):** ~$10/tháng
    * **AWS Lambda, Kinesis, Glue, S3:** ~$25/tháng
    * **Tổng cộng chi phí hạ tầng:** **`~$90 - $120 USD/tháng`**

* **Chi phí Nhân sự Bảo trì:**
    * *Giả định:* Cần khoảng 4-5 giờ/tuần của kỹ sư để giám sát và tinh chỉnh mô hình.
    * *Tính toán:* `(5 giờ/tuần × 4 tuần) × ($2,500/160 giờ/tháng) ≈ 20 giờ × $15.6/giờ`
    * **Chi phí thời gian bảo trì:** **`~$312 USD/tháng`**

**➡️ Tổng chi phí vận hành hàng tháng (Dự kiến):** `~$120 (Hạ tầng) + ~$312 (Nhân sự) =` **`~$432 USD`**

#### Phân tích Lợi tức Đầu tư (ROI)

* **Bối cảnh Giả định:**
    * Một cụm CSDL RDS `db.r5.2xlarge` đang hoạt động với chi phí: **`$1,200/tháng`**.
    * Hệ thống ML phát hiện rằng CSDL có thể chạy ở mức `db.r5.xlarge` (`$600/tháng`) trong 80% thời gian và chỉ cần nâng cấp trong 20% thời gian còn lại.

* **Tiềm năng Tiết kiệm:**
    * *Chi phí mới:* `($600 × 80%) + ($1,200 × 20%) = $480 + $240 = $720/tháng`.
    * **Số tiền tiết kiệm được hàng tháng:** `$1,200 - $720 =` **`$480/tháng`**.

* **Kết luận ROI:**
    * **Lợi nhuận ròng hàng tháng:** `$480 (Tiết kiệm) - $120 (Chi phí hạ tầng ML) =` **`$360/tháng`**.
    * **Thời gian hoàn vốn (Break-Even Point):** `(Tổng chi phí đầu tư) / (Lợi nhuận ròng hàng tháng) = $5,200 / $360`
    * **Kết quả:** Chi phí phát triển ban đầu sẽ được hoàn vốn sau khoảng **14-15 tháng**.
    ---

## 6. Đánh giá rủi ro
### Ma trận rủi ro
* **Rủi ro 1: Mô hình dự báo không chính xác**
    * *Mô tả:* Mô hình đưa ra dự báo sai, dẫn đến quyết định cấp phát tài nguyên sai (thiếu hoặc thừa).
    * *Mức độ ảnh hưởng:* Cao.
    * *Khả năng xảy ra:* Trung bình.
* **Rủi ro 2: "Cold Start" cho hành động tự động**
    * *Mô tả:* Thời gian để thay đổi kích thước instance RDS (vài phút) có thể không đủ nhanh để đáp ứng một đỉnh tải cực kỳ đột ngột.
    * *Mức độ ảnh hưởng:* Trung bình.
    * *Khả năng xảy ra:* Cao.
* **Rủi ro 3: Trôi dạt mô hình (Model Drift)**
    * *Mô tả:* Mẫu workload thay đổi theo thời gian (ví dụ: do ra mắt tính năng mới), làm giảm độ chính xác của mô hình đã cũ.
    * *Mức độ ảnh hưởng:* Trung bình.
    * *Khả năng xảy ra:* Cao.
* **Rủi ro 4: Chi phí vận hành ML vượt ngân sách**
    * *Mô tả:* Sử dụng các instance quá lớn cho việc huấn luyện hoặc hosting, hoặc lỗi trong code gây ra các vòng lặp vô hạn.
    * *Mức độ ảnh hưởng:* Thấp.
    * *Khả năng xảy ra:* Trung bình.
* **Rủi ro 5: Lỗ hổng bảo mật**
    * *Mô tả:* Cấu hình sai IAM role có thể cho phép truy cập trái phép vào dữ liệu nhạy cảm hoặc thực hiện các hành động phá hoại.
    * *Mức độ ảnh hưởng:* Rất cao.
    * *Khả năng xảy ra:* Thấp.

### Chiến lược giảm thiểu
* **Đối với Rủi ro 1 (Mô hình không chính xác):**
    * Sử dụng nhiều thuật toán và chọn mô hình có sai số thấp nhất (ví dụ: MAPE, RMSE).
    * Bắt đầu với "chế độ khuyến nghị" và yêu cầu con người xác thực trước khi chuyển sang tự động hoàn toàn.
    * Thiết lập các quy tắc an toàn (guardrails), ví dụ: không bao giờ giảm hơn một cấp instance trong một lần.
* **Đối với Rủi ro 2 (Vấn đề "Cold Start"):**
    * Kết hợp dự báo dài hạn (vài giờ) với phát hiện bất thường ngắn hạn (vài phút).
    * Đối với các CSDL quan trọng, sử dụng Amazon Aurora Serverless v2, có khả năng mở rộng gần như tức thì, và dùng ML để dự báo chi phí thay vì điều chỉnh capacity.
* **Đối với Rủi ro 3 (Trôi dạt mô hình):**
    * Triển khai quy trình MLOps hoàn chỉnh với việc tự động huấn luyện lại mô hình theo lịch trình (ví dụ: hàng tuần).
    * Sử dụng Amazon SageMaker Model Monitor để tự động phát hiện sự suy giảm chất lượng của mô hình.
* **Đối với Rủi ro 4 (Chi phí vượt ngân sách):**
    * Sử dụng AWS Budgets để thiết lập cảnh báo khi chi phí vượt ngưỡng.
    * Tối ưu hóa mã nguồn và chọn các loại instance phù hợp với từng tác vụ (huấn luyện, dự báo).
* **Đối với Rủi ro 5 (Lỗ hổng bảo mật):**
    * Tuân thủ nguyên tắc quyền hạn tối thiểu (Principle of Least Privilege) cho tất cả các IAM role.
    * Sử dụng các công cụ như IAM Access Analyzer để kiểm tra và xác thực các policy.
    * Thực hiện đánh giá bảo mật định kỳ.

### Kế hoạch dự phòng
* Nếu mô hình ML hoạt động không ổn định, hệ thống sẽ tự động chuyển về chế độ "chỉ thông báo" và tắt tính năng tự động hành động.
* Duy trì các quy tắc auto-scaling dựa trên ngưỡng của CloudWatch như một phương án dự phòng cho các trường hợp hệ thống ML gặp sự cố.

---

## 7. Kết quả mong đợi
### Chỉ số thành công (Success Metrics)
* **Tài chính:**
    * Giảm chi phí hóa đơn Amazon RDS/Aurora hàng tháng ít nhất 15-20%.
    * Tỷ lệ hoàn vốn (ROI) dương trong vòng 12 tháng.
* **Hiệu suất:**
    * Giảm 25% số lần cảnh báo "CPU Utilization High".
    * Giảm thời gian phản hồi trung vị (P50) và phân vị thứ 95 (P95) của các truy vấn CSDL.
* **Vận hành:**
    * Tự động hóa >80% các hành động điều chỉnh quy mô CSDL.
    * Giảm 50% thời gian mà các kỹ sư DevOps phải bỏ ra để xử lý các sự cố liên quan đến hiệu suất CSDL.

### Lợi ích kinh doanh
* **Giảm tổng chi phí sở hữu (TCO):** Tối ưu hóa chi tiêu trên đám mây, cho phép tái đầu tư vào các lĩnh vực khác.
* **Cải thiện trải nghiệm khách hàng:** Đảm bảo ứng dụng luôn nhanh và đáng tin cậy, tăng sự hài lòng và giữ chân người dùng.
* **Tăng tốc độ đổi mới:** Giải phóng nguồn lực kỹ thuật khỏi các công việc vận hành lặp đi lặp lại, cho phép họ tập trung vào việc phát triển sản phẩm.

### Cải tiến kỹ thuật
* Chuyển đổi từ mô hình quản lý hạ tầng phản ứng (reactive) sang chủ động (proactive) và dự đoán (predictive).
* Xây dựng một nền tảng AIOps có thể tái sử dụng và mở rộng cho các tài nguyên khác (ví dụ: EC2, EKS).
* Thiết lập một quy trình MLOps hoàn chỉnh, từ thu thập dữ liệu đến giám sát mô hình trong môi trường production.

### Giá trị lâu dài
Dự án này không chỉ là một giải pháp tối ưu hóa chi phí một lần. Nó tạo ra một hệ thống thông minh, liên tục học hỏi và thích nghi với sự thay đổi của môi trường kinh doanh. Nó đặt nền móng cho một văn hóa kỹ thuật dựa trên dữ liệu (data-driven), giúp công ty xây dựng các ứng dụng hiệu quả, có khả năng mở rộng và mang lại lợi thế cạnh tranh bền vững trong kỷ nguyên số.
```