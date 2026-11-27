---
type: source
topic: cai
status: done
up: "[[Hub - cAI]]"
created: 2026-11-27
tags:
  - type/source
  - topic/cai
  - brief
---

# cAI — Product Brief (tóm tắt từ Claude Artifact)

> [!info] Nguồn
> Tóm tắt nội dung của `https://claude.ai/artifact/CzTERjiqtCast5Wpoxw1Qq`. Claude đọc artifact này ngày 18/11/2026. Đây là bản tóm tắt, không phải nguyên văn. Cần đối chiếu lại với artifact gốc trước khi trích dẫn chính thức.

## Ý tưởng

cAI là một bộ **agent team theo từng phòng ban**, chạy trên **một nền tảng dùng chung, nhiều tenant**. Mỗi team cắm vào quy trình làm việc của phòng ban đó, và người chỉ duyệt ở các cổng quan trọng. Brief mô tả bốn phòng ban (Tuyển dụng, Tài chính, Pháp chế, Giáo dục), cộng HR Buddy và đội lan toả cAI Spark. Nguyên tắc chung: *"Build một lần, mọi phòng ban dùng."*

## Mục tiêu

- Chuyển việc lặp lại từ người sang agent. Ví dụ, Tuyển dụng muốn chuyển 8 trên 9 giai đoạn cho agent.
- Người vẫn nắm quyền với các quyết định về tiền, về nhân sự và về rủi ro pháp lý.
- Mỗi team chỉ được nghiệm thu khi người dùng trong P&L dùng thật, đo bằng adoption analytics.
- Với Giáo dục, đo tỉ lệ xem hết video, điểm quiz và điểm thi so với một lớp đối chứng.

## Người dùng

- **Nội bộ:** recruiter, hiring manager; kế toán, kế toán trưởng, CFO; các đơn vị kinh doanh tự phục vụ; chuyên viên pháp chế; toàn bộ nhân viên dùng hướng dẫn của cAI Spark.
- **Bên ngoài:** ứng viên, nhà cung cấp, đối tác; ở VinUni và Vinschool có sinh viên, học sinh phổ thông, giáo viên và phụ huynh.

## Yêu cầu

- Mỗi phòng ban theo một khung chuẩn: Tài chính theo APQC PCF 9.0, Pháp chế theo CLM + CLOC Core 12, Tuyển dụng theo phễu tuyển dụng, Giáo dục theo thang Bloom.
- Mỗi phòng có **3 cụm chuỗi giá trị**, mỗi cụm 2–4 agent, cụm trước bàn giao cho cụm sau. Thêm một **lớp giám sát chạy cắt ngang**: analytics, fairness, kiểm soát, bảo mật, an toàn trẻ em.
- **Cổng HITL bắt buộc** ở: JD, sàng lọc vùng xám, go/no-go, ký offer, giải ngân, ký báo cáo tài chính, redline điều khoản rủi ro cao.
- **Skill và connector dùng chung:** OCR, fuzzy matching, text-to-SQL, quy trình HITL, audit log; kết nối ERP, ATS, HRIS, LMS, ngân hàng, cổng thuế, Zalo.
- **Quản trị:** P&L theo tenant, phân quyền theo role, audit log, dữ liệu tự host.
- **Một agent chỉ được coi là xong khi:** đạt ngưỡng eval, chạy được trên staging với dữ liệu tổng hợp, có audit log và cổng HITL, có hướng dẫn do Đội Lan Tỏa làm, và đã demo cho phòng ban xác nhận.

## Ràng buộc

- Đội triển khai 60 người: 40 người trong 12 team cụm (mỗi phòng 10 người), 20 người cho platform, chất lượng và an toàn, Spark, kiến trúc và PMO.
- Phase 1 dài 12 tuần: tuần 1–2 setup; tuần 3–8 xây agent theo sprint 2 tuần; tuần 9–12 pilot ở từng phòng.
- Phương án thay thế: nếu cần nghiệm thu sớm, dồn 40 người vào hai phòng (mỗi phòng 20 người), hai phòng còn lại chuyển sang phase 2.
- Agent không được tự hành động với việc có hậu quả lớn. Không hợp đồng nào gửi cho đối tác, không offer nào gửi đi, không lệnh chi nào ra ngân hàng khi chưa có người duyệt. Tờ khai thuế do agent lập nhưng người nộp thuế tự nộp.
- Ngoài phạm vi: mua sắm (Pháp chế); lương và thương mại quốc tế (Tài chính). Lương thuộc HR Buddy.
- Vinschool: guard an toàn trẻ em phải chạy trước mọi cuộc hội thoại với học sinh.

## Quyết định đã chốt sau brief

- 2026-11-25: **cAI là sản phẩm bán được.** Tiêu chí 1 trong [[Hub - cAI]] (B2B sellable, license sạch) vì vậy là ràng buộc cứng.

## Liên kết

- Hub: [[Hub - cAI]]
- Áp dụng kiến trúc vào brief: [[04 - Áp dụng Reference Architecture vào đề bài cAI]]
