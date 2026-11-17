# Báo cáo cá nhân — 2A202603829_NguyenHongThang

## 1. Thông tin thành viên

- **Họ tên:** Nguyễn Hồng Thắng
- **MSSV:** 2A202603829
- **Email:** [Bổ sung email]
- **GitHub:** https://github.com/thangdktd

## 2. Phần việc phụ trách

- **Shape A — Workflow (CP2 + CP3)** - Day 08 — From Workflows to Agents.

- Xây dựng quy trình xử lý yêu cầu hoàn tiền bằng Python, không sử dụng LLM để đưa ra quyết định.

- Một test cá nhân để kiểm tra trường hợp thiếu thông tin đơn hàng.

## 3. Đóng góp cụ thể

- Hoàn thiện hàm `run_claim()` trong `src/lab8/shapes/workflow.py`.
- Xây dựng luồng kiểm tra claim, đơn hàng, quyền truy cập và tính số tiền bồi thường.
- Sử dụng `durability.issue_refund()` để thực hiện hoàn tiền theo cơ chế của hệ thống.
- Xử lý các trường hợp thiếu thông tin, từ chối hoàn tiền, lỗi và crash.
- Ghi trace để theo dõi quá trình xử lý.
- Bổ sung test `test_claim_missing_order_id_returns_needs_info_no_refund` trong `src/tests/test_workflow_thangdktd.py`.

**Quyết định kỹ thuật:** sử dụng các hàm và policy có sẵn thay vì để LLM tự quyết định số tiền hoàn trả. Việc này giúp quy trình dễ kiểm tra và hạn chế sai sót.

## 4. Bằng chứng

**Các file đã thực hiện:**

- `src/lab8/shapes/workflow.py`
- `src/tests/test_workflow_thangdktd.py`

**Kết quả kiểm thử:**

| Kiểm thử | Kết quả |
|---|---|
| Workflow student tests | 62 passed, 0 failed |
| Test cá nhân | 1 passed, 0 failed |
| Full test suite | 179 passed, 124 failed |

Các test Workflow đã đạt. Các lỗi còn lại trong full suite được báo cáo thuộc Shape B — Single Agent và Shape C — Orchestrator chưa hoàn thiện.

**Commit chính:** [Bổ sung commit hash sau khi commit]

**Branch cá nhân:** `thangdktd`

## 5. Công cụ AI đã dùng

- ChatGPT giúp tôi hiểu yêu cầu, phân tích lỗi và hướng dẫn kiểm thử.
- Cline hỗ trợ đọc source code, triển khai Workflow và viết test cá nhân.
- Tôi kiểm tra kết quả bằng cách chạy `py_compile` và `pytest`, đối chiếu kết quả với yêu cầu bài tập.

Ban đầu, file `workflow.py` thiếu hàm `run_claim()` nên chương trình bị lỗi import. Sau khi sửa và chạy lại các bài test, phần Workflow đã đạt 62/62 test.

## 6. Tự đánh giá

Qua bài tập này, tôi hiểu rõ hơn sự khác nhau giữa Workflow và AI Agent.

Workflow thực hiện các bước theo quy trình xác định trước, còn Agent có thể tự lựa chọn hành động dựa trên tình huống.

Tôi cũng hiểu thêm về các khái niệm `trace`, `idempotency`, `crash recovery` và tầm quan trọng của việc kiểm thử trước khi đưa chương trình vào sử dụng.