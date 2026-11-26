---
type: practice
topic: ccar-p
status: done
up: "[[Hub - CCAR-P]]"
tags:
  - type/practice
  - topic/ccar-p
  - mock-exam
  - cheatsheet
  - anti-patterns
---

# CCAR-P Exam Traps & Anti-Patterns Cheatsheet

> [!abstract] Mục đích tài liệu
> Kỳ thi **CCAR-P (Claude Certified Architect – Professional)** không kiểm tra học thuộc lòng lý thuyết rời rạc mà kiểm tra **phán đoán kiến trúc trong tình huống production**. Phần lớn đáp án nhiễu (*distractors*) là những phương án "nghe có vẻ chạy được ở bản demo" nhưng là **anti-pattern** khi chạy thực tế ở quy mô doanh nghiệp.
>
> Tổng hợp từ các tài liệu luyện thi cộng đồng trên internet: `sarveshtalele/claude-architect-exam-guide`, `anuragsirish/claude-certified-architect-prep`, `claudecertprep.com`, `certsafari.com`, `timolabs.dev`, `szymonpaluch.com`.

---

## 1. Top 10 Bẫy Đề Thi (Anti-Patterns) Kinh Điển của CCAR-P

| # | Bẫy trong đáp án nhiễu (Anti-Pattern) | Vì sao sai ở quy mô Enterprise | Mẫu kiến trúc CCAR-P đúng |
| :-: | :--- | :--- | :--- |
| **1** | **Dùng System Prompt để ép tuân thủ bảo mật / thứ tự tool** *(VD: "Không được lộ PII", "Luôn gọi validate trước khi thanh toán")* | Prompt mang tính xác suất (*probabilistic*), dễ bị prompt injection hoặc trôi ngữ cảnh vượt qua; không qua được kiểm toán compliance. | **Deterministic code/infra controls:** PII redaction/tokenization ở gateway, programmatic hooks, prerequisite gates bằng code, HITL trước hành động không thể đảo ngược. |
| **2** | **Tin vào `temperature=0.0` để cam kết 0% hallucination / 100% determinism** | `temperature=0.0` chỉ giảm độ ngẫu nhiên lúc sampling, không đảm bảo determinism tuyệt đối và không ngăn ảo giác khi thiếu grounding. | Ghi rõ bản chất xác suất và rủi ro tồn dư trong **ADR**, kết hợp **RAG + citation + deterministic post-verification** và **Shadow $\rightarrow$ Canary rollout**. |
| **3** | **Phá vòng lặp agent bằng cách parse text** *(VD: kiểm tra chuỗi `"I am done"`)* | Văn bản tự nhiên của LLM mang tính mô tả (*descriptive*), không phải tín hiệu điều khiển luồng đáng tin cậy. | Kiểm tra trường cấu trúc **`stop_reason`** (`tool_use`, `end_turn`, `max_tokens`) từ API response. |
| **4** | **Dùng điểm tự tin tự khai báo (self-reported confidence score) của LLM để tự duyệt/phân luồng** | LLM dễ *hallucinate* độ tự tin cao ngay cả khi câu trả lời sai hoàn toàn. | Dùng kiểm chứng tất định (schema/DB check), bộ phân loại đã hiệu chuẩn hoặc quy tắc ngưỡng dựa trên bằng chứng truy xuất. |
| **5** | **Dùng Batch API cho luồng người dùng tương tác thời gian thực (real-time)** | Batch API là bất đồng bộ (SLA hàng giờ), chỉ tối ưu chi phí/thông lượng cho tác vụ nền; dùng cho chat live sẽ làm treo trải nghiệm user. | Dùng **Batch API** cho xử lý nền khối lượng lớn (**sau khi đã test/tune prompt trên tập mẫu nhỏ**); dùng Synchronous API + Caching + Queue/Backoff cho luồng live. |
| **6** | **Đắp "Router Agent" lên một Monolithic Agent phình to (30+ tools chồng chéo)** | Chỉ là miếng dán tạm (*band-aid*), tăng thêm 1 lượt gọi model mà không giải quyết chồng chéo ngữ nghĩa và lẫn lộn domain. | **Capability Decomposition:** tách monolithic agent thành các sub-agent chuyên biệt theo ranh giới nghiệp vụ rõ ràng và làm sắc nét tool descriptions. |
| **7** | **Đổ payload thô hàng MB từ MCP tool vào context window rồi tăng `max_tokens`** | `max_tokens` chỉ chỉnh output; payload khổng lồ gây *attention drift*, nhiễu ở giữa (*lost-in-the-middle*) và đội chi phí token. | **Server-side filtering, pagination & targeted schemas** ngay tại MCP server để giữ tỷ lệ *signal-to-noise* cao. |
| **8** | **Chèn dữ liệu động (timestamp, user_id) ở đầu prompt khi dùng Prompt Caching** | Prompt Caching khớp theo tiền tố chính xác (*exact prefix matching*); 1 token thay đổi ở đầu gây 100% cache miss và tốn phí cache write. | Đặt toàn bộ phần **tĩnh** (tools, system prompt, tài liệu nền) lên đầu trước `cache_control`, đẩy dữ liệu động xuống cuối. |
| **9** | **Bật Opus hoặc Extended Thinking cho mọi request "cho chắc ăn"** | Gây đội chi phí và độ trễ nghiêm trọng ở các tác vụ khối lượng cao, độ phức tạp thấp (routing, classification, extraction). | **Model Ladder:** Haiku cho volume lớn/đơn giản $\rightarrow$ Sonnet (+ bounded Extended Thinking `budget_tokens`) cho phức tạp $\rightarrow$ Opus cho high-stakes, luôn dựa trên bằng chứng eval. |
| **10** | **Chỉ dùng TLS 1.3 trên Public API khi đề yêu cầu "không đi qua internet công cộng"** | Mã hóa đường truyền (*encryption in transit*) $\neq$ cô lập mạng (*network path isolation*). | Dùng **Amazon Bedrock + AWS PrivateLink (Interface VPC Endpoints)** hoặc **Vertex AI + Private Service Connect (PSC)**. |

---

## 2. Phương Pháp Loại Trừ Nhanh Khi Làm Bài (Method of Elimination)

Khi đọc một câu hỏi tình huống CCAR-P dài (~150–250 từ), thực hiện theo 4 bước trong ~90 giây:

1. **Gạch chân ràng buộc cứng (Hard Constraints):**
   - *Accuracy must not drop* $\rightarrow$ Loại ngay các phương án cắt giảm context, cắt policy hoặc hạ cấp model toàn bộ.
   - *No data leaves VPC / Zero public internet* $\rightarrow$ Loại Direct public API (kể cả có TLS 1.3).
   - *Irreversible / Financial action* $\rightarrow$ Loại phương án hoàn toàn tự chủ không có HITL gate bằng code.
   - *Nothing working can break* $\rightarrow$ Loại vá nóng ad-hoc, loại tự sửa prompt, ưu tiên Golden Dataset + CI/CD Regression Gate.
2. **Kiểm tra đúng tầng kiến trúc (Right Layer Diagnosis):**
   - Lỗi truy xuất mã chính xác (`E1042`, `SEC-8902`) $\rightarrow$ tầng **Retrieval** (Hybrid BM25 + Vector + Reranking), không phải đổi model lớn hơn.
   - Lỗi mất dữ liệu giữa các sub-agent $\rightarrow$ tầng **Orchestration State Passing**, không phải tăng context window.
   - Lỗi 429 giờ cao điểm $\rightarrow$ tầng **Resilience** (Async Queue + Exponential Backoff with Jitter + Static FAQ Cache), không phải retry 50ms hay gộp nhiều user vào 1 prompt.
3. **Phát hiện dấu hiệu "Detective-for-Preventive":**
   - Ghi log, audit hàng tháng, hay theo dõi ticket khiếu nại 48h là *detective* (phát hiện sau khi đã xảy ra thiệt hại). Nếu đề yêu cầu **ngăn chặn rủi ro** (PII leak, chuyển khoản nhầm, regression lên production), luôn chọn biện pháp *preventive* (gateway lọc PII, HITL gate, CI/CD eval gate).
4. **Cảnh giác với "Over-Engineering":**
   - Quy trình tuần tự cố định $\rightarrow$ Chọn **Workflow**, loại Multi-Agent tự chủ.
   - Tác vụ đơn giản đã qua ngưỡng eval với Haiku $\rightarrow$ Không nhảy lên Opus.

---

## 3. Liên Kết Luyện Tập
- Nguồn đề thi online & GitHub: [[CCAR-P Mock Exam Resources]]
- Thực hành bộ câu hỏi mẫu: [[CCAR-P Sample Questions - Set 01]] | [[CCAR-P Mock Exam - Full Mini-Test (Set 02 - 14 Câu theo 7 Domain)]]
- Theo dõi lỗi sai: [[Mock Exam Mistake Log Template]]
