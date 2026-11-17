# Báo cáo cá nhân — MSSV_HoTen

Đổi tên file theo quy tắc `MSSV_HoTen.md`.

Ví dụ: `20241234_NguyenVanAn.md`.

## 1. Thông tin thành viên

- Họ tên: Nguyễn Bảo Duy    
- MSSV: 2A202603890
- Email:
- GitHub: baoduy2048

## 2. Phần việc phụ trách

Orchestrator + Handoff (shape C)

## 3. Đóng góp cụ thể

commit: 677a71cc66387f281c6525eb67da762af20b83c5
đã thêm 32 test trong test_orchestrator_handoff.py
Các quyết định kỹ thuật:
- Handoff dùng facts={} và chỉ cho investigator 4 tool đọc. Không có tool ghi, không có claim row hay ticket.
- Số tiền lấy từ compute_compensation tính trên dòng đơn trong database, không theo số ngày trễ khách tự ghi trong ticket
- Truyền quote đầy đủ vào issue_refund, vì host tự trừ phần đã hoàn.
- Truyền crash_at vào issue_refund. Khi crash thì ghi trace rồi ném lại InjectedCrash, không nuốt lỗi.
- Lỗi bất ngờ được bắt lại và trả failed.
## 4. Bằng chứng

Link commit, tên test, run id hoặc trích đoạn report liên quan.

https://github.com/VinUni-AI20k/K5-Day8-From-Workflows-to-Agents/commit/677a71cc66387f281c6525eb67da762af20b83c5
**Các file đã thực hiện:**

- `src/lab8/shapes/orchestrator.py`
- `src/tests/test_orchestrator.py`

**Kết quả kiểm thử:**

| Kiểm thử | Kết quả |
|---|---|
| orchestrator tests | 62 passed, 0 failed |
| Test T11 & T12 | 3 passed,  failed |
| Test tự thêm | 32 passed, 0 failed |

## 5. Công cụ AI đã dùng


sử dụng claude để vẽ sơ đồ luồng công việc dựa trên các thông tin đã cung cấp từ đó viết code cho orchestrator 

## 6. Tự đánh giá

Hiểu được cách orchestrator và investigator hoạt động. Nên sử dụng script code ở đâu thay vì cho llm quyết định