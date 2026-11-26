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
  - full-blueprint
---

# CCAR-P Mock Exam — Set 02 (Full 7-Domain Mini-Test — 14 Câu)

> [!info] Hướng dẫn thi thử mô phỏng
> - **Cấu trúc:** 14 câu hỏi tình huống phủ đủ **7/7 domain** theo đúng tỷ trọng blueprint chính thức của kỳ thi **CCAR-P (Claude Certified Architect – Professional)**.
> - **Thời gian chuẩn:** **25 phút** (~1.7 phút/câu, giống tốc độ thi thật 70 câu / 120 phút).
> - **Ngưỡng đạt tham khảo:** Từ **11/14 câu (~78%)** trở lên.
> - **Cách làm:** Ghi đáp án ra nháp trước khi mở callout **Đáp án & Phân tích bẫy**. Ghi mọi câu sai hoặc câu chọn còn phân vân vào [[Mock Exam Mistake Log Template]].

| Domain | Trọng số đề thật | Số câu trong Set 02 |
| :--- | :---: | :---: |
| 1. Integration (MCP, Tool Design, RAG, Auth) | 19% | Câu 1, 2, 3 |
| 2. Solution Design & Architecture | 17% | Câu 4, 5 |
| 3. Evaluation, Testing & Optimization | 16% | Câu 6, 7 |
| 4. Governance, Safety & Risk Management | 14% | Câu 8, 9 |
| 5. Stakeholder Communication & Lifecycle Management | 14% | Câu 10, 11 |
| 6. Claude Models, Prompting & Context Engineering | 13% | Câu 12, 13 |
| 7. Developer Productivity & Operational Enablement | 7% | Câu 14 |

---

## Domain 1: Integration (19%)

### Câu 1 — MCP Context Hygiene & Payload Bloat

**Tình huống:**
Một doanh nghiệp xây dựng trợ lý chăm sóc khách hàng bằng Claude thông qua Anthropic API và kết nối tới kho tài liệu nội bộ cùng cơ sở dữ liệu đơn hàng qua **Model Context Protocol (MCP)**. Khi agent gọi một MCP tool để tra cứu, server trả về toàn bộ lịch sử đơn hàng nhiều megabyte kèm tài liệu chính sách thô dưới dạng một chuỗi JSON khổng lồ. Sau vài lượt gọi tool liên tiếp, Claude bắt đầu lệch khỏi persona ban đầu, bỏ qua ràng buộc trong system prompt, đồng thời chi phí token và độ trễ tăng vọt.

Thay đổi kiến trúc nào giải quyết hiệu quả nhất vấn đề suy giảm chất lượng này mà vẫn giữ nguyên giá trị truy xuất?

- **A.** Tăng tham số `max_tokens` của model để Claude có thể đọc và tái tạo đầy đủ toàn bộ payload trước khi đưa ra câu trả lời.
- **B.** Thiết kế lại MCP tool để thực hiện **lọc phía server (server-side filtering), phân trang (pagination) và chunking**, chỉ trả về các trường dữ liệu đích hoặc bản tóm tắt súc tích thay vì payload thô nhiều megabyte.
- **C.** Tự động chèn lại toàn bộ khối system prompt vào cuối mỗi tin nhắn người dùng để ép model tuân thủ lại chỉ dẫn.
- **D.** Loại bỏ hoàn toàn MCP và chuyển sang hệ thống vector-database RAG truyền thống với ngưỡng tương đồng (similarity threshold) cố định.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Thiết kế MCP server chuẩn production phải tôn trọng ngân sách context window. Đổ payload thô nhiều MB vào context gây hiện tượng *"noise-in-the-middle"* và **attention drift** (trôi ngữ cảnh). Lọc, phân trang và chỉ trả về trường cần thiết ngay tại MCP server tối ưu hóa tỷ lệ *signal-to-noise*, giảm token và giữ độ chính xác suy luận.
> - **A sai:** `max_tokens` chỉ giới hạn độ dài **output** sinh ra, không giải quyết phình to **input context** từ tool response.
> - **C sai:** Lặp lại system prompt ở cuối mỗi user turn (*tail prompt stuffing*) làm hỏng cấu trúc hội thoại và phá vỡ prefix của **Prompt Caching**.
> - **D sai:** Bỏ MCP chỉ vì lỗi kích thước payload là phản ứng thái quá; bản thân MCP server hoàn toàn có thể hỗ trợ semantic filtering và pagination.
>
> *Nguồn tổng hợp:* `szymonpaluch.com` / `claudepractice.com` / `certstud.com`

---

### Câu 2 — Monolithic Agent Tool Overload & Capability Decomposition

**Tình huống:**
Một hệ thống agent tự động phân loại yêu cầu bồi thường bảo hiểm (*claims triage*) đã tăng trưởng từ **6 tool lên 34 tool** sau 18 tháng khi nhiều nhóm sản phẩm độc lập cùng đóng góp tính năng. Dữ liệu telemetry trên production cho thấy độ chính xác phân loại giảm từ **91% xuống 78%**, còn độ trễ chọn tool trung bình tăng thêm **2.3 giây/request**.

Một kỹ sư junior đề xuất đặt thêm một **"tool router agent"** phía trước agent hiện tại để tự động lọc bớt danh sách tool theo từng request.

Với vai trò Principal Solution Architect, bạn nên trình bày **2 phát hiện nền tảng** nào để thuyết phục áp dụng **phân rã năng lực (capability decomposition)** thay vì chỉ đắp thêm một lớp router agent? *(Chọn **2** đáp án)*

- **A.** Một số tool đã không được gọi lần nào trong 90 ngày lưu lượng gần nhất.
- **B.** Router agent đề xuất sẽ làm phát sinh thêm một lần gọi model trên mỗi request đến.
- **C.** Mô tả (tool descriptions) của các tool bị chồng chéo ngữ nghĩa giữa nhiều loại hồ sơ bồi thường trong cùng một bộ tool.
- **D.** Toàn bộ 34 tool thực chất đang phục vụ **4 miền quy trình bồi thường tách biệt (four distinct claim workflow domains)** trong phạm vi triage.
- **E.** Pattern router agent đã được tài liệu hóa rộng rãi trong nhiều tài liệu cộng đồng về agent.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **C và D**
>
> **Giải thích:**
> - **C đúng:** Mô tả tool chồng chéo về mặt ngữ nghĩa gây nhiễu trực tiếp cho cơ chế chọn tool của Claude, là nguyên nhân gốc rễ khiến độ chính xác tụt từ 91% xuống 78%.
> - **D đúng:** Nhồi nhét 4 domain nghiệp vụ tách biệt vào một monolithic agent làm phình context và vượt quá tải nhận thức của model. Giải pháp kiến trúc đúng là **capability decomposition** — tách thành các sub-agent chuyên biệt theo từng domain nghiệp vụ rõ ràng.
> - **A sai:** Xóa tool không dùng là tác vụ dọn dẹp (*housekeeping*), không giải quyết vấn đề ranh giới domain bị hòa lẫn.
> - **B sai:** Latency tăng thêm của router chỉ là triệu chứng, chưa phải lập luận kiến trúc cốt lõi để biện minh cho việc phân rã domain.
> - **E sai:** Mức độ phổ biến trong tài liệu không phải căn cứ thiết kế kiến trúc; đắp router lên một monolith lỗi thiết kế là *band-aid anti-pattern*.
>
> *Nguồn tổng hợp:* `itexams.com` / `examtopics.com` / `studytech.ai`

---

### Câu 3 — Enterprise RAG: Exact Match & Context Fragmentation

**Tình huống:**
Một tập đoàn tài chính triển khai trợ lý nội bộ trả lời câu hỏi tuân thủ chính sách trên kho **45,000 tài liệu PDF**, hướng dẫn bảo mật và sổ tay kỹ thuật. Trong giai đoạn UAT, hệ thống lộ ra 2 lỗi nghiêm trọng:
1. **Lỗi khớp chính xác (Exact Match Failure):** Khi nhân viên hỏi mã lỗi hoặc mã điều khoản cụ thể (ví dụ: *"Policy SEC-8902"* hoặc *"Error code E1042"*), tìm kiếm vector thuần túy thường bỏ sót tài liệu đích và trả về các đoạn tóm tắt chung chung.
2. **Phân mảnh ngữ cảnh (Context Fragmentation):** Cắt chunk cố định (*fixed-size chunking*) tách rời nội dung điều khoản khỏi tiêu đề mục hoặc chú thích bảng, khiến Claude suy diễn sai quy định.

Yêu cầu độ trễ truy vấn dưới **1.5 giây**. Tổ hợp thay đổi kiến trúc nào giải quyết triệt để cả hai lỗi trên?

- **A.** Chuyển hoàn toàn sang tìm kiếm từ khóa BM25 để bắt mã chính xác, đồng thời dùng fixed-size chunking với overlap bằng 0 để tiết kiệm token.
- **B.** Triển khai **Hybrid Search** kết hợp dense vector embeddings với sparse search (**BM25**) thông qua Reciprocal Rank Fusion (RRF), áp dụng **chunking theo cấu trúc tài liệu hoặc Contextual Retrieval**, và bổ sung bước **Cross-encoder Reranking**.
- **C.** Tăng kích thước mỗi chunk lên toàn bộ tài liệu để không bao giờ bị cắt đứt ngữ cảnh, và nâng `top_k` của vector search lên thật cao để Claude tự lọc nhiễu.
- **D.** Loại bỏ hoàn toàn RAG và nạp toàn bộ 45,000 tài liệu vào **Prompt Cache** của Claude ở mỗi request.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Dense embeddings mạnh về ngữ nghĩa nhưng yếu ở mã định danh hiếm (`SEC-8902`, `E1042`) $\rightarrow$ **Hybrid Search (Vector + BM25 + RRF)** giải quyết lỗi (1). **Structure-aware chunking / Anthropic Contextual Retrieval** gắn lại ngữ cảnh cha (tiêu đề, bảng, tài liệu gốc) cho từng chunk $\rightarrow$ giải quyết lỗi (2). **Cross-encoder Reranker** chắt lọc lại top ứng viên chính xác nhất đưa vào context Claude mà vẫn đảm bảo SLA < 1.5s.
> - **A sai:** Bỏ hoàn toàn vector search sẽ làm gãy các truy vấn ngôn ngữ tự nhiên mang tính khái niệm/diễn đạt lại.
> - **C sai:** Đưa cả tài liệu lớn với `top_k` cao làm tràn nhiễu context, giảm độ chính xác suy luận và tăng mạnh chi phí/latency.
> - **D sai:** 45,000 tài liệu vượt xa giới hạn thực tế của context window và Prompt Caching.
>
> *Nguồn tổng hợp:* `claude.com` (Contextual Retrieval) / `aicertguides.com` / `preporato.com`

---

## Domain 2: Solution Design & Architecture (17%)

### Câu 4 — Workflow vs. Autonomous Agent Pattern

**Tình huống:**
Bạn đang thiết kế kiến trúc cho pipeline xử lý tài liệu theo lô (*batch document-processing*) với chuỗi bước xử lý cố định, bất biến trên mọi tài liệu: *trích xuất metadata $\rightarrow$ kiểm tra hợp lệ schema $\rightarrow$ tóm tắt nội dung $\rightarrow$ lưu vào cơ sở dữ liệu*. Định dạng đầu vào và yêu cầu đầu ra hoàn toàn nhất quán giữa các tài liệu.

Pattern kiến trúc nào phù hợp nhất và vì sao?

- **A.** **Workflow pattern** (điều phối tuần tự tất định — sequential orchestration), vì các bước đã xác định rõ, dự đoán được, tối ưu hóa chi phí và tốc độ thực thi mà không cần vòng lặp tự chủ.
- **B.** **Autonomous multi-agent pattern**, vì tách nhiệm vụ cho nhiều sub-agent độc lập sẽ giúp thích ứng tốt hơn với mọi ngoại lệ văn bản.
- **C.** **Orchestrator-worker pattern**, vì việc chạy song song bước trích xuất và bước tóm tắt sẽ giảm thiểu tổng độ trễ.
- **D.** **Human-in-the-loop agentic router**, vì tác vụ batch luôn cần vòng lặp tự sửa lỗi để tránh lỗi chèn dữ liệu vào database.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **A**
>
> **Giải thích:**
> - **A đúng:** Theo nguyên tắc cốt lõi của Anthropic (*Building Effective Agents*), luôn chọn giải pháp đơn giản nhất đáp ứng yêu cầu. Khi luồng xử lý tuyến tính, cố định và không phụ thuộc vào quyết định động của model, **Deterministic Workflow** (prompt chaining) vượt trội về chi phí, độ trễ và độ tin cậy.
> - **B, C, D sai:** Đều đưa vào độ phức tạp điều phối không cần thiết (*over-engineering*), làm tăng chi phí token và rủi ro vòng lặp không kiểm soát (ngoài ra ở C, bước tóm tắt/lưu cần hoàn tất tuần tự chứ không cần orchestrator động).
>
> *Nguồn tổng hợp:* `itexams.com` / `aiwisdom.dev` / `timolabs.dev`

---

### Câu 5 — Multi-Agent State Passing & Context Isolation

**Tình huống:**
Bạn đang rà soát một hệ thống nghiên cứu multi-agent gồm một **Coordinator Agent** giao việc cho **Web-Search Sub-Agent** và **Synthesis Sub-Agent**. Log đánh giá cho thấy trong **30% lượt chạy**, báo cáo tổng hợp cuối cùng bị thiếu các số liệu cụ thể và tên nguồn trích dẫn mà Web-Search Sub-Agent thực ra đã tìm được. Khi trace payload, bạn phát hiện prompt mà Coordinator gửi cho Synthesis Sub-Agent chỉ chứa chủ đề báo cáo cấp cao và yêu cầu định dạng, mà không kèm trạng thái kết quả trung gian (*intermediate scratchpad*).

Cách khắc phục kiến trúc vững chắc nhất là gì?

- **A.** Tăng kích thước context window toàn cục của Coordinator model để nó tự động chia sẻ ngầm toàn bộ kết quả tìm kiếm sang các sub-agent.
- **B.** Chỉnh sửa tầng điều phối để truyền tường minh các **structured context artifacts** (ví dụ: JSON scratchpad hoặc state payload chứa đầy đủ các object nguồn và số liệu đã truy xuất) trực tiếp trong payload bàn giao cho Synthesis Sub-Agent.
- **C.** Thay toàn bộ hệ thống multi-agent bằng một prompt nguyên khối duy nhất chạy zero-shot một lần.
- **D.** Thêm vòng lặp Evaluator-Optimizer ép Synthesis Sub-Agent tự đoán các con số bị thiếu cho đến khi vượt qua bước kiểm tra.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Trong kiến trúc multi-agent (hub-and-spoke hoặc hierarchical), mỗi sub-agent có ngữ cảnh cô lập (*isolated context*). Muốn không thất thoát dữ liệu (*data starvation*), tầng orchestration phải đóng gói và truyền tường minh các artifact có cấu trúc (source objects, facts, citations) giữa các bước bàn giao.
> - **A sai:** Tăng context window của Coordinator không tự động khiến sub-agent nhìn thấy dữ liệu vì sub-agent chạy trong context tách biệt.
> - **C sai:** Gom thành một prompt khổng lồ phá vỡ tính mô-đun và dễ gặp trần token/context degradation.
> - **D sai:** Ép model tự đoán số liệu bị thiếu là tiếp tay cho ảo giác (*hallucination*) và đốt chi phí vô ích.
>
> *Nguồn tổng hợp:* `szymonpaluch.com` / `preporato.com`

---

## Domain 3: Evaluation, Testing & Optimization (16%)

### Câu 6 — Isolating Multi-Component Regressions & LLM-as-a-Judge Bias

**Tình huống:**
Một hệ thống multi-agent rà soát hợp đồng chạy trên Claude vừa phát hành bản cập nhật gồm đồng thời **3 thay đổi**: sửa system prompt, nâng cấp MCP server truy xuất tài liệu, và bổ sung một sub-agent kiểm tra trích dẫn. Sau khi deploy, giám sát cho thấy độ trễ $p_{95}$ nhảy vọt từ **70 giây lên 150 giây**, chi phí token tăng mạnh và agent thỉnh thoảng bỏ sót các điều khoản pháp lý sâu từng bắt được trước đây.

Đội ngũ kỹ thuật cần xây dựng khung kiểm thử hồi quy tự động để: (1) khoanh vùng chính xác thành phần nào gây tăng độ trễ/suy giảm chất lượng, và (2) đánh giá quy mô lớn các tiêu chí định tính về độ chính xác phân tích điều khoản cho các bản phát hành sau.

Gói giải pháp kiến trúc nào phù hợp nhất?

- **A.** Chuyển toàn bộ production sang Claude Haiku để kéo độ trễ xuống, thay toàn bộ chấm điểm tự động bằng kiểm duyệt con người (HITL) cho 100% lưu lượng, và dùng exact-string matching trên đầu ra cuối cùng.
- **B.** Triển khai **component-level tracing & telemetry** (đo riêng thời gian thực thi và số token theo từng sub-agent/MCP tool), xây dựng **golden dataset gồm 200 hợp đồng lịch sử có các ca biên**, và thiết lập bộ **offline LLM-as-a-judge** dùng rubric đa tiêu chí có biện pháp chống **position bias / verbosity bias** chạy tự động trong CI/CD trước khi merge.
- **C.** Nạp toàn bộ lịch sử pháp lý nhiều năm vào mọi prompt, bật chế độ Extended Thinking liên tục không giới hạn ngân sách token cho mọi tác vụ con, và chạy A/B test trực tiếp trên 100% người dùng thật mà không cần baseline offline.
- **D.** Gỡ bỏ MCP server, chuyển sang xuất file theo lô hàng đêm, và gộp tất cả thành một system prompt duy nhất.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Để cô lập nguyên nhân trong bản release đổi 3 thành phần cùng lúc, bắt buộc phải có **observability/tracing ở cấp thành phần** (latency + token breakdown từng sub-agent & MCP call). Với tiêu chí định tính (chất lượng phân tích điều khoản), chuẩn CCAR-P là **Golden Dataset + Offline LLM-as-a-Judge** dùng rubric tách biệt từng tiêu chí, đã hiệu chuẩn với chuyên gia người thật và khử thiên kiến (đảo vị trí chống *position bias*, chấm theo rubric chống *verbosity bias*) gắn vào cổng CI/CD.
> - **A sai:** Hạ xuống Haiku làm giảm sâu năng lực suy luận hợp đồng phức tạp; exact-string matching không đánh giá được ngữ nghĩa; 100% HITL không thể scale.
> - **C sai:** Bật Extended Thinking không giới hạn làm tăng thêm độ trễ và chi phí; A/B test thẳng lên live user khi chưa qua offline eval vi phạm quản trị rủi ro.
> - **D sai:** Phá bỏ kiến trúc truy xuất động và tính mô-đun thay vì chẩn đoán đúng điểm nghẽn.
>
> *Nguồn tổng hợp:* `szymonpaluch.com` / `certsafari.com` / `hinterbuild.com`

---

### Câu 7 — Agentic Control Flow & Programmatic Enforcement

**Tình huống:**
Một đội ngũ phát triển xây dựng agent tự động xử lý hoàn tiền cho khách hàng. Trong code hiện tại của vòng lặp agentic:
1. Vòng lặp kiểm tra chuỗi phản hồi văn bản của Claude xem có chứa cụm từ `"I am done"` hoặc `"Task complete"` để dừng vòng lặp.
2. Thứ tự bắt buộc `verify_customer_identity` trước khi gọi `issue_refund` chỉ được ghi trong system prompt.
3. Agent tự gán một điểm tự tin (*self-reported confidence score* từ 0–100%) trong văn bản để hệ thống quyết định có tự động duyệt hoàn tiền hay không.

Theo chuẩn kiến trúc CCAR-P, nhận định nào mô tả chính xác nhất các lỗi thiết kế nghiêm trọng (*anti-patterns*) cần sửa trong hệ thống này? *(Chọn **2** đáp án)*

- **A.** Phải dừng vòng lặp agent dựa trên tín hiệu cấu trúc của API như `stop_reason` (`tool_use` so với `end_turn`), không được parse cụm từ ngôn ngữ tự nhiên trong text đầu ra.
- **B.** Cần viết lại system prompt dài và nhấn mạnh hơn bằng chữ in hoa để đảm bảo Claude luôn gọi `verify_customer_identity` trước `issue_refund`.
- **C.** Phải thực thi điều kiện tiên quyết (`verify_customer_identity` trước `issue_refund`) bằng **hook/gate lập trình tất định ở tầng code/API** và thay thế điểm tự tin tự khai báo của LLM bằng bước kiểm chứng tất định hoặc quy tắc ngưỡng dựa trên dữ liệu đã xác thực.
- **D.** Nên tăng `temperature` lên `0.9` để điểm tự tin tự báo cáo của model phản ánh sát xác suất thực tế hơn.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **A và C**
>
> **Giải thích:**
> - **A đúng:** Văn bản tự nhiên của LLM mang tính mô tả chứ không mang tính điều khiển luồng (*descriptive, not authoritative*). Luồng vòng lặp agent phải dựa vào `stop_reason` (`end_turn`, `tool_use`, `max_tokens`) từ cấu trúc response của API.
> - **C đúng:** Cả 2 bẫy kinh điển của CCAR-P đều xuất hiện ở đây: (1) dùng prompt thay cho code để ép thứ tự hành động có rủi ro tài chính, và (2) tin vào **self-reported confidence score** của LLM (LLM rất dễ *hallucinate* mức độ tự tin cao dù đang trả lời sai). Cả hai đều phải chuyển sang cơ chế kiểm soát tất định bằng code.
> - **B sai:** Viết in hoa trong prompt vẫn là kiểm soát xác suất (*probabilistic*), không đảm bảo tuân thủ 100%.
> - **D sai:** Tăng `temperature` chỉ làm đầu ra ngẫu nhiên hơn, không làm điểm tự tin hiệu chuẩn chính xác hơn.
>
> *Nguồn tổng hợp:* `sarveshtalele/claude-architect-exam-guide` / `claudecertprep.com` / `claudearchitectcertification.com`

---

## Domain 4: Governance, Safety & Risk Management (14%)

### Câu 8 — Indirect Prompt Injection & High-Stakes HITL

**Tình huống:**
Đội ngũ kỹ thuật triển khai trợ lý tài chính sử dụng Claude có quyền gọi các API nội bộ, bao gồm cả API **thực hiện lệnh chuyển khoản thanh toán cho nhà cung cấp bên ngoài**. Trong buổi đánh giá bảo mật, chuyên gia rủi ro cảnh báo về nguy cơ **Indirect Prompt Injection**: kẻ tấn công có thể nhúng chỉ dẫn độc hại ẩn bên trong file PDF hóa đơn nhà cung cấp (được nạp qua RAG), đánh lừa Claude tạo lệnh chuyển tiền trái phép sang tài khoản giả mạo.

Tổ hợp kiểm soát kiến trúc nào tuân thủ đúng nhất nguyên tắc Governance & Safety của CCAR-P?

- **A.** Ghi rõ trong system prompt yêu cầu Claude bỏ qua mọi chỉ dẫn mâu thuẫn nằm trong file hóa đơn, đồng thời ghi log lại mỗi khi có yêu cầu chuyển khoản được tạo ra.
- **B.** Tách biệt nghiêm ngặt về mặt cấu trúc (parse dữ liệu hóa đơn không tin cậy vào **strict data schema**), áp dụng nguyên tắc **least-privilege** cho các tool, và bắt buộc có **cổng phê duyệt Human-in-the-Loop (HITL) ở tầng lập trình** trước khi bất kỳ API thanh toán nào được thực thi.
- **C.** Giảm kích thước context window xuống nhỏ nhất có thể để hạn chế lượng văn bản bên ngoài model đọc được, kết hợp kiểm toán chọn mẫu hàng tháng sau khi đã thanh toán.
- **D.** Cho toàn bộ hóa đơn đi qua một LLM nhỏ thứ hai để lọc các từ khóa mang tính tấn công prompt injection trước khi chuyển văn bản sang Claude.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Áp dụng đồng bộ 3 nguyên tắc phòng thủ chiều sâu (*defense-in-depth*): (1) Tách bạch dữ liệu không tin cậy khỏi chỉ dẫn thực thi bằng schema chặt chẽ; (2) Least-privilege scoping cho tool; (3) Với hành động **không thể đảo ngược / rủi ro tài chính cao** (chuyển tiền ra ngoài), bắt buộc phải có **preventive HITL gate bằng code** chặn trước lệnh gọi API.
> - **A sai:** Chỉ dựa vào system prompt không ngăn được prompt injection tinh vi; ghi log chỉ là *detective control* (phát hiện sau sự cố), không ngăn chặn tiền bị chuyển đi.
> - **C sai:** Giảm context làm giảm năng lực nghiệp vụ mà không chặn được injection; kiểm toán hàng tháng là quá muộn đối với giao dịch tài chính.
> - **D sai:** Bộ lọc từ khóa hoặc LLM sanitizer dễ bị vượt mặt bằng kỹ thuật mã hóa/ẩn ý ngữ nghĩa (*semantic obfuscation*); bảo mật phải đặt ở biên kiến trúc thực thi.
>
> *Nguồn tổng hợp:* `aiwisdom.dev` / `studytech.ai` / `cloudyeti.io`

---

### Câu 9 — Enterprise Network Isolation & Private Connectivity

**Tình huống:**
Một ngân hàng triển khai trợ lý hỗ trợ khách hàng chạy Claude. Do quy định pháp lý và chính sách bảo mật nội bộ cực kỳ nghiêm ngặt, **không một gói tin nào chứa PII khách hàng hoặc telemetry nội bộ được phép đi qua mạng internet công cộng (public internet)**.

Đội kỹ thuật đưa ra 3 phương án triển khai:
1. **Direct Anthropic API** qua public HTTPS endpoint chuẩn với mã hóa TLS 1.3.
2. **Amazon Bedrock** triển khai trong tài khoản AWS nội bộ, sử dụng **Interface VPC Endpoints (AWS PrivateLink)** với security group giới hạn chặt chẽ.
3. **Google Cloud Vertex AI** truy cập qua public global HTTP(S) load balancer có bật VPC Service Controls (VPC-SC).

Phương án nào đáp ứng **TỐT NHẤT** yêu cầu tuân thủ về đường truyền dữ liệu này?

- **A.** Phương án 1 (Direct Anthropic API), vì TLS 1.3 đã cung cấp mã hóa đầu cuối rất mạnh trên đường truyền.
- **B.** Phương án 2 (Amazon Bedrock qua VPC Interface Endpoints / AWS PrivateLink), vì toàn bộ lưu lượng API đi hoàn toàn bên trong mạng trục riêng (private backbone) của nhà cung cấp cloud mà không đi ra internet công cộng.
- **C.** Phương án 3 (Vertex AI qua public endpoint kèm VPC-SC), vì VPC-SC ngăn rò rỉ dữ liệu ngay cả khi đi qua tuyến mạng công cộng.
- **D.** Cả 3 phương án đều đạt tuân thủ như nhau vì đều mã hóa dữ liệu trong quá trình truyền tải (encryption in transit).

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Các khung tuân thủ nghiêm ngặt (tài chính, y tế, chính phủ) phân biệt rất rõ giữa **mã hóa đường truyền (encryption in transit)** và **cô lập đường đi mạng (network path isolation)**. Khi đề bài yêu cầu *"zero public internet routing"*, đáp án chuẩn là dùng hyperscaler hosted Claude thông qua **AWS PrivateLink (Interface VPC Endpoints)** hoặc **Google Cloud Private Service Connect (PSC)**.
> - **A và D sai:** TLS 1.3 mã hóa gói tin nhưng lưu lượng vẫn định tuyến qua public internet, vi phạm ràng buộc cứng của đề.
> - **C sai:** VPC-SC bảo vệ chu vi tài nguyên nhưng trong phương án 3 lưu lượng vẫn đi qua public endpoint thay vì Private Service Connect.
>
> *Nguồn tổng hợp:* `exam-labs.com` / CCAR-P Deployment & Compliance Blueprint

---

## Domain 5: Stakeholder Communication & Lifecycle Management (14%)

### Câu 10 — Pilot Change Management & Preventing Silent Regressions

**Tình huống:**
Tổ chức của bạn đang chạy thử nghiệm (*pilot*) trợ lý soạn thảo điều khoản pháp lý trên Claude cho **40 luật sư nội bộ**. Tổng cố vấn pháp lý (General Counsel — nhà tài trợ dự án) thường xuyên gửi email phản hồi ad-hoc sau khi thấy một kết quả chưa ưng ý và yêu cầu chỉnh prompt ngay trong ngày.

Tháng trước, 2 lần vá prompt nóng trong ngày đã sửa được lỗi cụ thể được báo cáo nhưng lại vô tình gây ra lỗi hồi quy âm thầm — phá vỡ quy chuẩn định dạng điều khoản ở các tình huống khác và chỉ bị phát hiện sau nhiều ngày. Ràng buộc tối thượng mà General Counsel đặt ra trước khi mở rộng pilot là: *"Không có bất kỳ tính năng nào đang chạy ổn định được phép hỏng khi chúng ta vá lỗi."*

Với vai trò Lead Architect, bạn nên quản trị vòng phản hồi và vòng đời thay đổi này như thế nào?

- **A.** Gắn nút phản hồi trực tiếp trên giao diện pilot kết nối vào một pipeline tự động cho phép Claude tự viết lại system prompt của chính mình hàng ngày theo góp ý người dùng.
- **B.** Thiết lập **quy trình đánh giá và cổng phát hành chính thức (formal evaluation & release gate)**: gom mọi yêu cầu thay đổi prompt và chạy bắt buộc qua bộ **regression test suite trên golden dataset nền** trước khi đẩy bản cập nhật lên môi trường pilot/production.
- **C.** Yêu cầu General Counsel phê duyệt bằng văn bản mọi thay đổi prompt hàng tuần và chỉ cấp quyền sửa prompt cho lãnh đạo cấp cao.
- **D.** Khôi phục lại prompt ban đầu, đóng băng toàn bộ cập nhật trong suốt thời gian còn lại của pilot và yêu cầu luật sư tự sửa tay offline.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Hệ thống AI mang tính xác suất rất nhạy cảm với *prompt drift*. Thay vì vá nóng ad-hoc, quản trị vòng đời chuẩn CCAR-P biến phản hồi stakeholder thành các test case mới bổ sung vào golden dataset, đồng thời chạy hồi quy toàn diện qua release gate để đảm bảo mọi hành vi đang đúng không bị vỡ.
> - **A sai:** Tự động cho model tự sửa system prompt mà không có kiểm chứng/phê duyệt tạo ra rủi ro quản trị và hành vi không kiểm soát cực lớn.
> - **C sai:** Đẩy gánh nặng duyệt kỹ thuật sang stakeholder phi kỹ thuật tạo nút thắt cổ chai và không có cơ chế kỹ thuật để phát hiện lỗi hồi quy ngầm.
> - **D sai:** Đóng băng hoàn toàn làm chết vòng lặp cải tiến của giai đoạn pilot thay vì giải quyết gốc rễ quy trình phát hành.
>
> *Nguồn tổng hợp:* `szymonpaluch.com` / `timolabs.dev` / `pratechlabs.com`

---

### Câu 11 — Unsupportable Deterministic SLAs, ADRs & Safe Rollout

**Tình huống:**
Bộ phận rủi ro và tuân thủ của một khách hàng doanh nghiệp yêu cầu đưa vào hợp đồng một **SLA cam kết tuyệt đối**: hệ thống tóm tắt văn bản pháp lý chạy Claude phải đạt **0% ảo giác sự kiện (zero hallucinations)** và **99.9% tính tất định (determinism)** trên mọi đầu ra. Bạn cần cập nhật **Architecture Decision Record (ADR)** và đề xuất chiến lược triển khai phù hợp.

Tổ hợp quyết định kiến trúc nào đúng nhất theo nguyên tắc quản trị của CCAR-P?

- **A.** Cập nhật ADR khẳng định đầu ra hoàn toàn tất định nhờ thiết lập `temperature=0.0`, rồi deploy thẳng lên 100% lưu lượng production bằng chiến lược blue-green.
- **B.** Từ chối thẳng SLA mà không ghi nhận tài liệu, thay bằng hướng dẫn nội bộ nhóm và chạy canary deployment để bỏ qua quy trình theo dõi tuân thủ.
- **C.** Cập nhật ADR để **trình bày minh bạch bản chất xác suất của LLM, các đánh đổi (trade-offs) và rủi ro tồn dư (residual risks)** thay vì cam kết điều không thể bảo chứng; đồng thời đề xuất lộ trình phát hành theo từng pha gồm **Shadow Testing** (chạy song song không trả về user) tiếp nối bằng **Canary Rollout** tỷ lệ nhỏ kèm guardrail đánh giá ngữ nghĩa tự động.
- **D.** Chấp nhận ký SLA tất định để làm hài lòng stakeholder, rồi âm thầm gắn thêm một model dự phòng phía sau mà không ghi vào ADR.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **C**
>
> **Giải thích:**
> - **C đúng:** Một trọng tâm lớn của Domain 5 là **không ký các SLA bất khả thi cho hệ thống xác suất**. ADR chuẩn phải nêu rõ giới hạn kỹ thuật, đánh đổi và rủi ro tồn dư. Chiến lược triển khai chuẩn rủi ro thấp là **Shadow Testing $\rightarrow$ Canary Rollout** có đo lường guardrail tự động trước khi mở rộng toàn bộ.
> - **A sai (Bẫy kinh điển):** `temperature=0.0` giảm tính ngẫu nhiên trong sampling nhưng **KHÔNG đảm bảo 100% determinism** (do tính toán dấu phẩy động GPU/batching) và **KHÔNG loại bỏ được hallucination**.
> - **B và D sai:** Bỏ qua tài liệu hóa ADR hoặc cố tình che giấu rủi ro và ký cam kết sai sự thật đều vi phạm nghiêm trọng chuẩn mực governance doanh nghiệp.
>
> *Nguồn tổng hợp:* `certsafari.com` / `szymonpaluch.com`

---

## Domain 6: Claude Models, Prompting & Context Engineering (13%)

### Câu 12 — Model Tier Routing & Extended Thinking Trade-offs

**Tình huống:**
Một doanh nghiệp vận hành nền tảng AI phục vụ 3 luồng tác vụ khác nhau:
1. **Luồng 1 (1.2 triệu request/ngày):** Phân loại ý định ticket hỗ trợ khách hàng vào 12 danh mục cố định và trích xuất mã đơn hàng ra JSON; yêu cầu độ trễ cực thấp và chi phí tối thiểu.
2. **Luồng 2 (40,000 request/ngày):** Phân tích code pull request phức tạp nhiều file và kiểm tra lỗi logic tinh vi.
3. **Luồng 3 (500 request/ngày):** Tổng hợp chiến lược thẩm định M&A có giá trị giao dịch hàng trăm triệu USD, nơi một sai sót suy luận sâu có thể gây tổn thất tài chính nghiêm trọng.

Sau khi đo chuẩn trên golden dataset, chiến lược phân bổ model tier và cấu hình suy luận nào thể hiện đúng triết lý kiến trúc của Anthropic nhất?

- **A.** Dùng **Claude Opus** bật **Extended Thinking** với ngân sách token tối đa cho cả 3 luồng để đảm bảo chất lượng đồng đều và đơn giản hóa vận hành.
- **B.** Dùng **Claude Haiku** cho Luồng 1 (không bật Extended Thinking để tối ưu tốc độ & chi phí); dùng **Claude Sonnet** (kết hợp **Extended Thinking** có giới hạn `budget_tokens` khi cần cho các ca khó) cho Luồng 2; và dành **Claude Opus** cho Luồng 3 có rủi ro nghiệp vụ tối cao.
- **C.** Dùng **Claude Haiku** cho cả 3 luồng để tiết kiệm chi phí hạ tầng, sau đó dùng prompt ép model suy nghĩ kỹ hơn cho Luồng 3.
- **D.** Dùng **Claude Sonnet** cho Luồng 1 nhưng bật Extended Thinking để tăng tốc độ phân loại JSON, còn Luồng 2 và Luồng 3 chuyển sang fine-tune model mã nguồn mở nhỏ.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Triết lý *"Model Ladder"* của Anthropic trong CCAR-P: **dùng model nhỏ nhất, nhanh nhất đạt ngưỡng chất lượng trên tập eval, dùng Extended Thinking (`budget_tokens` / effort) như núm tinh chỉnh mịn, và chỉ leo thang lên tier cao hơn khi eval chứng minh cần thiết**. Haiku lý tưởng cho classification/extraction khối lượng lớn; Sonnet (+ bounded Extended Thinking) cân bằng cho code/phân tích phức tạp; Opus dành cho bài toán high-stakes cần trần suy luận cao nhất.
> - **A sai:** Dùng Opus + Extended Thinking cho 1.2 triệu ticket phân loại đơn giản sẽ gây bùng nổ chi phí và độ trễ (*over-provisioning anti-pattern*).
> - **C sai:** Tiết kiệm vài xu tiền token ở Luồng 3 có thể gây thiệt hại tài chính khổng lồ (*false economy*).
> - **D sai:** Extended Thinking làm **tăng** độ trễ và token suy luận nội bộ, không phải công cụ để tăng tốc độ phân loại đơn giản.
>
> *Nguồn tổng hợp:* `haimaker.ai` / `claudelearn.co` / CCAR-P Model Selection Guide

---

### Câu 13 — Prompt Caching Prefix Stability

**Tình huống:**
Một đội kỹ thuật đã bật **Prompt Caching** cho hệ thống trợ lý tư vấn luật với system prompt + bộ tài liệu điều luật tĩnh dài 18,000 token, kỳ vọng giảm mạnh chi phí input token. Tuy nhiên sau 1 tuần chạy production, dashboard billing cho thấy **tỷ lệ cache hit gần như bằng 0%**, thậm chí chi phí còn tăng nhẹ do phí ghi cache (*cache write premium*).

Khi kiểm tra cấu trúc request payload gửi lên Anthropic API, kiến trúc sư thấy cấu trúc hiện tại như sau:
```
[System Prompt: "Thời gian hiện tại là {timestamp_tới_từng_giây}. User ID: {user_id}. Bạn là trợ lý pháp lý..."]
[Bộ điều luật tĩnh 18,000 token + cache_control: ephemeral]
[Câu hỏi người dùng]
```

Nguyên nhân gốc rễ và cách sửa đúng nhất là gì?

- **A.** Prompt Caching chỉ hoạt động khi toàn bộ nội dung request nhỏ hơn 4,096 token; cần cắt bớt bộ điều luật xuống dưới ngưỡng này.
- **B.** Do các trường dữ liệu động (`timestamp` tới từng giây và `user_id`) được đặt **ở đầu prefix** trước điểm đánh dấu cache, làm thay đổi hash prefix trên mỗi request khiến cache bị vô hiệu hóa hoàn toàn. Cần di chuyển toàn bộ nội dung **tĩnh** (vai trò hệ thống, tool definitions, bộ điều luật 18,000 token) lên đầu tiên và đặt điểm `cache_control` ở cuối phần tĩnh, còn các dữ liệu động theo request đặt ở phía sau.
- **C.** Do Prompt Caching không hỗ trợ đồng thời với system prompt; phải chuyển toàn bộ bộ điều luật vào trường `assistant` message.
- **D.** Cần gọi Batch API thay vì Synchronous API thì Prompt Caching mới tạo ra cache hit.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Cơ chế Prompt Caching của Anthropic hoạt động theo nguyên tắc **exact prefix matching** (khớp tiền tố chính xác từ đầu payload theo thứ tự `tools` $\rightarrow$ `system` $\rightarrow$ `messages`). Chỉ cần 1 token thay đổi ở phía trước breakpoint (như timestamp từng giây hay `user_id`), toàn bộ phần cache phía sau sẽ bị *cache miss* và phải trả phí cache write lặp lại. Đưa toàn bộ thành phần tĩnh lên đầu và đẩy phần biến thiên động xuống sau breakpoint sẽ khôi phục tỷ lệ cache hit cao.
> - **A sai:** Ngược lại, Prompt Caching yêu cầu kích thước tối thiểu (thường 1,024 hoặc 2,048 token tùy dòng model), và càng hiệu quả với prefix dài.
> - **C và D sai:** Prompt Caching hoàn toàn hỗ trợ `system` prompt và Synchronous API.
>
> *Nguồn tổng hợp:* `certsafari.com` / `preporato.com` / Anthropic Prompt Caching Best Practices

---

## Domain 7: Developer Productivity & Operational Enablement (7%)

### Câu 14 — Scaling Claude Code Across an Enterprise Engineering Org

**Tình huống:**
Một tổ chức kỹ thuật phần mềm đang triển khai **Claude Code** cho **120 lập trình viên**. Qua đợt rà soát pilot, đội Platform phát hiện 3 vấn đề:
1. **Lệch cấu hình (Environment Drift):** Quy ước repo, lệnh build/test kiểm chứng và cấu hình MCP server kết nối hệ thống metrics nội bộ đang nằm trong file cấu hình global cá nhân (`~/.claude.json`) của một kỹ sư senior, khiến phiên làm việc của các lập trình viên hành xử không đồng nhất.
2. **Ràng buộc bảo mật:** Chính sách an ninh công ty cấm tuyệt đối việc lưu hardcoded API keys hoặc credentials dịch vụ nội bộ trong mã nguồn repository dùng chung.
3. **Nút thắt năng suất:** Lập trình viên phải gõ tay lại nhiều lần các bước kiểm thử phức tạp vì thiếu mẫu workflow chuẩn hóa.

Chiến lược nào vừa chuẩn hóa hiệu quả năng suất vừa tuân thủ tuyệt đối yêu cầu bảo mật?

- **A.** Viết script onboarding sao chép thẳng file `~/.claude.json` cá nhân của kỹ sư senior vào thư mục home của cả 120 người, đồng thời lưu token dịch vụ nội bộ dạng plaintext trong file `.env` commit ở thư mục gốc repository.
- **B.** Chuẩn hóa **cấu hình cấp dự án (project-level configuration)** được quản lý bằng version control trong repository (quy ước dự án như `CLAUDE.md`, project settings, custom slash commands/skills và định nghĩa MCP endpoints dùng chung), đồng thời nạp thông tin xác thực lúc chạy thông qua **biến môi trường cục bộ hoặc hệ thống quản lý bí mật (secret manager/vault)** mà không commit bất kỳ secret nào vào repo.
- **C.** Bắt buộc toàn bộ 120 lập trình viên chạy Claude Code qua một cloud sandbox container tập trung dù gây thêm độ trễ mạng 3 giây trên mỗi lần gọi tool.
- **D.** Ban hành chính sách cấm toàn bộ MCP server tùy chỉnh và cấu hình theo dự án, chỉ cho phép chạy cấu hình mặc định để đảm bảo an toàn tối đa.

> [!success]- Đáp án & Phân tích bẫy (Bấm để mở)
> **Đáp án đúng:** **B**
>
> **Giải thích:**
> - **B đúng:** Domain 7 đề cao mô hình **"Paved Paths"** — chuẩn hóa mặc định dùng chung ở cấp project (`CLAUDE.md`, `.mcp.json` / project settings, shared commands) được version-control cùng codebase để mọi developer có trải nghiệm đồng nhất, đồng thời tách riêng bí mật xác thực ra ngoài repo bằng env var / secret vault để thỏa mãn ràng buộc bảo mật mà không làm chậm tốc độ phát triển.
> - **A sai:** Copy cấu hình global vẫn gây drift theo thời gian, và commit `.env` chứa token vào repo vi phạm trực tiếp ràng buộc bảo mật cứng.
> - **C sai:** Đưa toàn bộ tác vụ CLI tương tác cục bộ sang sandbox thêm 3 giây độ trễ mỗi tool call phá hủy trải nghiệm phát triển (DevEx) không cần thiết.
> - **D sai:** Cấm mọi tùy biến đi ngược mục tiêu *Developer Productivity & Operational Enablement*.
>
> *Nguồn tổng hợp:* `preporato.com` / `cloudyeti.io` / `aiwisdom.dev`

---

## Bảng Tự Chấm Điểm Set 02

| Domain | Câu hỏi | Số câu đúng | Tỷ lệ |
| :--- | :--- | :---: | :---: |
| 1. Integration | 1, 2, 3 | / 3 | |
| 2. Solution Design & Architecture | 4, 5 | / 2 | |
| 3. Evaluation, Testing & Optimization | 6, 7 | / 2 | |
| 4. Governance, Safety & Risk Management | 8, 9 | / 2 | |
| 5. Stakeholder Communication & Lifecycle | 10, 11 | / 2 | |
| 6. Claude Models, Prompting & Context Eng. | 12, 13 | / 2 | |
| 7. Developer Productivity & Enablement | 14 | / 1 | |
| **Tổng cộng** | **14 câu** | **/ 14** | |

- **12–14/14:** Rất vững tư duy kiến trúc CCAR-P, sẵn sàng làm full mock 63–70 câu trên [[CCAR-P Mock Exam Resources]].
- **9–11/14:** Nền tảng khá tốt — kiểm tra kỹ các bẫy đã dính tại [[CCAR-P Exam Traps & Anti-Patterns Cheatsheet]] và ghi vào [[Mock Exam Mistake Log Template]].
- **< 9/14:** Ôn lại các domain bị sai trước khi chuyển sang bộ đề tiếp theo.
