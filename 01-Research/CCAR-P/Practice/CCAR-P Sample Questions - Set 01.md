---
type: practice
topic: ccar-p
status: in-progress
up: "[[Hub - CCAR-P]]"
tags:
  - type/practice
  - topic/ccar-p
  - mock-exam
  - practice-questions
---

# CCAR-P Sample Questions — Set 01

> [!tip] Cách tự luyện hiệu quả với file này
> Đọc kỹ **ràng buộc cứng** trong từng tình huống, tự chọn đáp án ra giấy trước khi bấm mở callout **Đáp án & Phân tích bẫy**. Sau khi làm xong, ghi các câu sai vào [[Mock Exam Mistake Log Template]].

---

## Câu 1 — Solution Design & Architecture (Domain 1 / Resilience)

**Tình huống:**
Một khách hàng doanh nghiệp đang thiết kế hệ thống chăm sóc khách hàng lưu lượng cao sử dụng Claude. Vào giờ cao điểm, lưu lượng tăng đột biến khiến hệ thống thường xuyên chạm giới hạn tần suất gọi API (`HTTP 429`) của Anthropic, làm suy giảm trải nghiệm người dùng cuối.

Với vai trò Lead Architect, bạn nên triển khai pattern kiến trúc nào để giải quyết vấn đề này mà vẫn đảm bảo độ tin cậy của hệ thống?

- **A.** Tăng độ dài prompt và gộp nhiều truy vấn người dùng vào một lệnh gọi API duy nhất để giảm tổng số request.
- **B.** Triển khai hàng đợi xử lý bất đồng bộ (asynchronous request queue) kết hợp với cơ chế `exponential backoff` và `jitter`, đồng thời bổ sung lớp caching dự phòng cho các câu hỏi tĩnh thường gặp.
- **C.** Vượt giới hạn của Anthropic API bằng cách tự triển khai một LLM mã nguồn mở chưa căn chỉnh (unaligned) tại local để gánh lượng truy cập tràn trong giờ cao điểm.
- **D.** Cấu hình vòng lặp retry chặt ở phía client để gửi lại ngay lập tức các request lỗi mỗi 50 mili-giây cho đến khi thành công.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Sử dụng đúng các pattern chuẩn production để xử lý rate limit (queue + exponential backoff with jitter + caching cho static FAQs) mà không làm biến đổi hành vi model.
> - **A sai:** Gộp nhiều query của nhiều user khác nhau làm tăng nguy cơ vượt context window, suy giảm chất lượng trả lời và rò rỉ chéo ngữ cảnh.
> - **C sai:** Chuyển sang một model khác hành vi trong giờ cao điểm gây không nhất quán chất lượng và rủi ro an toàn/tuân thủ.
> - **D sai:** Retry dồn dập mỗi 50ms không có backoff/jitter gây ra hiệu ứng *Thundering Herd*, làm lỗi `429` nghiêm trọng hơn.

---

## Câu 2 — Integration & Security (Domain 3 / Compliance)

**Tình huống:**
Một công ty dịch vụ tài chính muốn tích hợp Claude vào hệ thống tìm kiếm tri thức nội bộ. Bộ phận tuân thủ bảo mật (Security Compliance) yêu cầu bắt buộc **không có bất kỳ thông tin định danh cá nhân (PII) nào của khách hàng hoặc bí mật thương mại độc quyền được rời khỏi VPC doanh nghiệp** hoặc bị đưa vào tập dữ liệu huấn luyện của bên thứ ba.

Sự kết hợp kiểm soát kiến trúc nào đáp ứng tốt nhất yêu cầu tuân thủ này? *(Chọn **2** đáp án)*

- **A.** Chỉ dựa vào system prompt hướng dẫn Claude bỏ qua và tự xóa mọi thông tin PII mà nó nhìn thấy.
- **B.** Định tuyến toàn bộ lưu lượng API qua một enterprise gateway bảo mật, nơi chạy chương trình tự động lọc bỏ hoặc mã hóa/token hóa PII bằng Named Entity Recognition (NER) trước khi gửi payload ra ngoài.
- **C.** Tận dụng cam kết bảo mật dữ liệu thương mại của Anthropic (xác nhận dữ liệu gửi qua API không dùng để train model) kết hợp với cơ chế quản lý API key đúng chuẩn thông qua kho lưu trữ bí mật (secret store) an toàn.
- **D.** Tắt kiểm tra chứng chỉ TLS trên các microservice nội bộ để tăng tốc độ truyền tải stream token cho người dùng doanh nghiệp.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B và C**
>
> **Giải thích:**
> - **B và C đúng:** Kiểm soát bảo mật trong môi trường tài chính phải là **deterministic controls ở lớp hạ tầng/code** (tiền xử lý loại bỏ/tokenize PII trước khi payload rời VPC — B) kết hợp với **cam kết quản trị dữ liệu thương mại và quản lý secret an toàn** (C).
> - **A sai (Bẫy kinh điển CCAR-P):** Dựa vào System Prompt để đảm bảo bảo mật là phi tất định (non-deterministic), không vượt qua được kiểm toán compliance.
> - **D sai:** Tắt TLS tạo ra lỗ hổng bảo mật nghiêm trọng.

---

## Câu 3 — Evaluation, Testing & Optimization (Domain 4 / CI/CD Evals)

**Tình huống:**
Nhóm của bạn vừa cập nhật prompt template và các ví dụ few-shot cho trợ lý tự động refactor code sử dụng Claude. Trước khi đẩy thay đổi lên production, ban lãnh đạo yêu cầu bằng chứng khách quan cho thấy phiên bản mới tốt hơn rõ rệt và **không gây lỗi hồi quy (regression)** trên các cú pháp biên (edge-case syntax).

Phương pháp đánh giá nào vững chắc và chuẩn kiến trúc nhất để triển khai?

- **A.** Kiểm tra thủ công 3 đoạn code điển hình trong Anthropic Console Playground và deploy nếu thấy kết quả ổn.
- **B.** Chạy pipeline đánh giá tự động trong CI/CD dựa trên một **golden dataset** gồm nhiều test case đa dạng, đánh giá đầu ra bằng các assertion lập trình sẵn kết hợp với **LLM-as-a-judge** để so sánh với phiên bản nền (baseline).
- **C.** Phát hành lên 100% lưu lượng production và theo dõi số lượng ticket khiếu nại của khách hàng trong 48 giờ đầu.
- **D.** Chỉ đo mức giảm độ trễ token trung bình để kết luận việc tối ưu prompt đã thành công hay chưa.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Chuẩn mực đánh giá hệ thống LLM cấp enterprise đòi hỏi eval lặp lại được và tự động hóa qua CI/CD trên golden dataset (gồm cả edge cases), kết hợp deterministic assertions (ví dụ: parse syntax/chạy unit test) + LLM-as-a-judge đối chiếu baseline.
> - **A sai:** Thử tay 3 mẫu mang tính chủ quan, không phát hiện được regression ở các ca biên.
> - **C sai:** Đẩy 100% traffic mà chưa eval gây rủi ro sự cố diện rộng trên production.
> - **D sai:** Latency thấp hơn không chứng minh code refactor vẫn đúng về mặt chức năng.

---

## Câu 4 — Governance, Safety & Risk Management (Domain 5 / Grounding)

**Tình huống:**
Một công ty quản lý tài sản dùng Claude để soạn thảo bản ghi nhớ đánh giá mức độ phù hợp đầu tư (*investment-suitability memos*) để chuyên viên tư vấn rà soát và làm việc trực tiếp với khách hàng. Bộ phận compliance đặc biệt lo ngại một dạng lỗi nghiêm trọng duy nhất: **một con số bị bịa đặt hoặc ảo giác (ví dụ: giá trị tài sản, tỷ suất lợi nhuận)** nằm xen kẽ trong một bản ghi nhớ có văn phong rất mạch lạc, dẫn đến quyết định sai lệch cho khách hàng.

Kiểm soát kiến trúc nào giảm thiểu trực tiếp và hiệu quả nhất rủi ro cụ thể này?

- **A.** Fine-tune một model Claude riêng vào mỗi đêm dựa trên các memo lịch sử và bảng tính tài chính đã xác minh để model tự ghi nhớ cấu trúc dữ liệu định giá.
- **B.** Triển khai pipeline RAG có yêu cầu trích dẫn nguồn chặt chẽ, kết hợp với **bước hậu kiểm tất định (deterministic post-processing validation)** tự động đối chiếu bằng code toàn bộ các con số được sinh ra với bản ghi gốc từ cơ sở dữ liệu đã truy xuất.
- **C.** Chuyển workload sang vòng lặp multi-agent tự chủ: agent chính viết nháp, agent thứ hai phản biện giọng văn, agent thứ ba định dạng đầu ra.
- **D.** Mở rộng system prompt để cảnh báo rõ: *"Cảnh báo: Không được ảo giác số liệu. Hãy kiểm tra kỹ toàn bộ dữ liệu tài chính trước khi xuất kết quả vì sai sót có thể gây thiệt hại tài chính nghiêm trọng."*

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Kết hợp **grounding** (RAG kèm citation) với **kiểm chứng bằng code ở lớp ngoài (deterministic verification)** để đối chiếu mọi con số với nguồn DB trước khi đưa tới mắt người dùng — giải quyết trực diện bài toán số liệu chính xác.
> - **A sai:** Fine-tuning không loại bỏ được ảo giác số liệu thời gian thực, tốn kém và dễ lệch dữ liệu (*data drift*).
> - **C sai:** Multi-agent phản biện giọng văn làm tăng latency/chi phí mà không neo đầu ra vào nguồn sự thật (*source of truth*).
> - **D sai:** Nhấn mạnh cảnh báo trong prompt vẫn không đảm bảo 100% về mặt xác suất khi xử lý ngữ cảnh phức tạp.

---

## Câu 5 — Claude Models, Prompting & Context Engineering (Domain 2 / Cost & Prompt Caching)

**Tình huống:**
Một trợ lý dự báo doanh thu đã vượt ngân sách chi phí hàng tháng **38%**. Kết quả profiling cho thấy 3 thành phần đóng góp chi phí:
1. Phần mở đầu chính sách dài **6,000 token** lặp lại y nguyên ở mọi request (chiếm **45%** chi phí).
2. Truy xuất các đoạn dữ liệu bán hàng lịch sử trung bình **3,000 token/call** (chiếm **30%** chi phí).
3. Suy luận trên dòng model cao cấp nhất (flagship tier) (chiếm **25%** chi phí).

Các bên liên quan (stakeholders) đặt điều kiện bắt buộc: **độ chính xác của dự báo phải giữ nguyên không đổi**.

Bạn nên ưu tiên thực hiện **2** biện pháp tối ưu nào trước tiên để giảm chi phí mà không ảnh hưởng đến độ chính xác? *(Chọn **2** đáp án)*

- **A.** Giảm số lượng đoạn dữ liệu bán hàng lịch sử được truy xuất trong mỗi lượt truy vấn.
- **B.** Cắt bớt phần mở đầu chính sách để loại bỏ các điều khoản ít quan trọng khỏi prompt.
- **C.** Bật **Prompt Caching** cho phần mở đầu chính sách tĩnh (6,000 token) xuyên suốt các request lặp lại.
- **D.** Chuyển toàn bộ workload sang dòng model Claude nhỏ hơn, nhanh hơn cho mọi truy vấn.
- **E.** Cache các đoạn dữ liệu bán hàng thường xuyên được truy xuất dùng chung giữa nhiều truy vấn hàng ngày.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **C và E**
>
> **Giải thích:**
> - **C và E đúng:** Phần policy preamble 6,000 token chiếm tỷ trọng lớn nhất (45%) và hoàn toàn tĩnh $\rightarrow$ bật **Prompt Caching** trên prefix này giúp giảm mạnh chi phí xử lý input mà không thay đổi bất kỳ nội dung hay năng lực suy luận nào. Tương tự, caching các sales chunks hay dùng chung (E) cắt giảm chi phí truy xuất/xử lý lặp lại mà vẫn giữ nguyên thông tin đầu vào cho model.
> - **A, B, D sai:** Cắt giảm context truy xuất (A), cắt bớt policy (B), hoặc hạ cấp model cho toàn bộ workload (D) đều có nguy cơ làm suy giảm độ chính xác dự báo — vi phạm trực tiếp ràng buộc cứng của stakeholder.

---

## Câu 6 — Batch Processing & Cost Optimization *(Kiến thức nền tảng chung CCAR-F / CCAR-P)*

**Tình huống:**
Một pipeline xử lý dữ liệu doanh nghiệp cần phân tích **50,000 tài liệu tài chính dày đặc**. Hệ thống không yêu cầu xử lý đồng bộ thời gian thực (real-time synchronous), nhưng **hiệu quả chi phí** và **thông lượng cao (high throughput)** là hai ràng buộc thiết kế hàng đầu.

Cách tiếp cận kiến trúc tối ưu nhất để triển khai workload này theo đúng best practices của Anthropic là gì?

- **A.** Tinh chỉnh prompt theo cách tương tác trên một mẫu đại diện nhỏ để tối đa hóa tỷ lệ thành công ngay lần chạy đầu, sau đó xử lý toàn bộ 50,000 tài liệu qua **Batch API**.
- **B.** Gửi ngay toàn bộ 50,000 tài liệu qua Batch API, phát hiện lỗi ở quy mô lớn rồi gửi lại những tài liệu bị lỗi.
- **C.** Xử lý 50,000 tài liệu bằng Synchronous API để tinh chỉnh prompt linh hoạt cho từng tài liệu.
- **D.** Bắt đầu gửi từng lô 5,000 tài liệu lên production để vừa chạy vừa học các dạng lỗi phát sinh.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **A**
>
> **Giải thích:**
> - **A đúng:** Quy trình chuẩn khi xử lý batch quy mô lớn là **validate & tune prompt/schema trên tập mẫu nhỏ trước**, sau đó mới đẩy toàn bộ sang **Message Batch API** (chi phí thấp hơn và rate limit cao hơn cho tác vụ bất đồng bộ).
> - **B và D sai:** Đẩy khối lượng lớn vào Batch API trước khi kiểm thử trên tập mẫu dễ dẫn đến lỗi hệ thống hàng loạt (ví dụ: sai format output) gây lãng phí tiền và thời gian chạy lại.
> - **C sai:** Dùng Synchronous API cho 50,000 tài liệu khi không cần real-time vừa đắt đỏ vừa dễ đụng rate limit.

---

## Nguồn tham khảo tổng hợp cho Set 01
- Câu 1–3: Tình huống minh họa phong cách CCAR-P tổng hợp từ các hướng dẫn chuẩn bị thi CCAR-P (StudyTech AI / Szymon Paluch / Timo Labs).
- Câu 4: Tình huống minh họa kiểu đề CCAR-P từ tài liệu luyện thi cộng đồng (Preporato / StudyTech AI).
- Câu 5: Tình huống tối ưu Prompt Caching (Domain 2) từ ngân hàng câu hỏi luyện tập CCAR-P (CertLibrary).
- Câu 6: Mẫu câu hỏi chuẩn bị từ kho GitHub cộng đồng (`avidevelops/claude-architect-exam-prep` / `ankitrahejagatech/claude-certified-architect-prep`).
