# Báo cáo nhóm — Lab 8 LogiX-VN

Tối đa 5 trang ngoài phụ lục. Số liệu lấy từ file trong `src/reports/`, không chép tay từ terminal.

## 1. Thông tin nhóm

- Tên nhóm:
- Thành viên: xem `TEAM.md`
- Commit chốt (trùng `report/team/decision_adr.md`):

## 2. Mục tiêu và cách tiếp cận

Nhóm hiểu bài toán thế nào, chia việc ra sao, thứ tự làm bốn shape.

...

## 3. Giải pháp

Mô tả ba shape nhóm viết (workflow, single, orchestrator): mỗi shape đọc gì, quyết định gì, gọi host ra sao. Chỉ rõ ranh giới code / model (tham khảo [docs/architecture.md](../../docs/architecture.md)), cách handoff được sanitize và trace ghi những event nào.

...

### 3.4. Nghiên cứu mở rộng: Topic B7 — Kiểm toán Grader (EMNLP 2026)

Nhóm lựa chọn thực hiện đề tài điểm cộng **Topic B7 (Kiểm toán Grader)** dựa trên nghiên cứu *Rethinking Evaluation of MAS Efficiency* (EMNLP 2026).
- **Mục tiêu:** Kiểm toán tính đúng đắn của chính thước đo (evaluator/grader) bằng phương pháp kiểm thử biến hình (*Metamorphic Testing*), đảm bảo mọi kết luận về hiệu năng agent không bị sai lệch do lỗi từ bộ đo.
- **Hiện thực (`src/bonus/grader_audit.py`):**
  - Đánh giá 3 ứng viên grader (`grader_a`, `grader_b`, `grader_c`) trước các quan hệ biến hình về lỗi hạ tầng (`metamorphic_infra`), nhân bản dữ liệu (`metamorphic_duplicate`), và luật bồi thường nghiệp vụ (`metamorphic_claim_delay`).
  - Đề xuất thêm **2 quan hệ biến hình mới**:
    1. *Cách ly dữ liệu đa shape (`cross_shape_isolation`)*: Đảm bảo việc thêm dữ liệu của shape khác không làm thay đổi các chỉ số đo lường của shape hiện tại.
    2. *Bất biến thứ tự trial (`permutation_order_invariance`)*: Đảm bảo thứ tự xuất hiện của các trial không làm đổi các chỉ số thống kê.
- **Kết quả kiểm toán:**
  - `grader_b` là bản chuẩn xác (**Sound Grader**), thỏa mãn 100% các quan hệ biến hình.
  - `grader_a` bị bắt lỗi cộng dồn chi phí ảo (`extra` cost) trên từng `claim_id` duy nhất, phá vỡ tính tỉ lệ tuyến tính của chi phí (tỉ số chi phí khi nhân đôi đạt 1.6684 thay vì 2.0).
  - `grader_c` bị bắt lỗi gán `passed=False` cho các trial bị lỗi hạ tầng (`infra_error: true`), trừng phạt oan agent và làm giảm pass rate từ 75.00% xuống 56.25% (-18.75%).
- **Bằng chứng:** Bộ kiểm thử `src/tests/test_bonus_grader_audit.py` (14/14 test cases passed), báo cáo dữ liệu `src/reports/bonus_grader_audit.json` và báo cáo khoa học `report/team/bonus_report.md`.

## 4. Kết quả

Bảng so sánh 4 shape trên bộ public (từ `src/reports/benchmark.json`): pass rate, pass^5, seed min–max, variance, cost/success, mean/p95 steps. Kèm crash safety (`src/reports/crash.json`) và, nếu có, kết quả tự kiểm trên `student_drop/`.

...

Quyết định kiến trúc và lý do: xem `report/team/decision_adr.md`. Phiếu nghiên cứu và ablation: xem `report/team/research_card.md`.

## 5. Khó khăn và cách xử lý

Lỗi gặp phải (kèm lệnh, output hoặc ảnh chụp) và cách nhóm xử lý.

...

## 6. Tổng hợp đóng góp

Chi tiết đóng góp của từng thành viên nằm trong `TEAM.md` (bảng đóng góp) và báo cáo cá nhân `report/team/MSSV_HoTen.md`.

- **Nguyễn Minh Quang (2A202603791):**
  - Phụ trách chính Shape B — Single ReAct Agent (`src/lab8/shapes/single.py`), vượt qua 62/62 test student và crash safety 20/20.
  - Phụ trách trọn gói phần nghiên cứu mở rộng Điểm cộng Topic B7 (Kiểm toán Grader): phát triển framework `src/bonus/grader_audit.py`, đề xuất 2 quan hệ biến hình mới, viết 14 unit tests tại `src/tests/test_bonus_grader_audit.py`, xuất báo cáo `src/reports/bonus_grader_audit.json` và hoàn thành báo cáo `report/team/bonus_report.md` mang lại tối đa +10 điểm bonus cho nhóm.

## 7. Hạn chế và hướng cải thiện

MockLLM không phải model sản xuất; latency là `unavailable`. Nêu thêm giới hạn của nhóm và điều kiện làm kết luận thay đổi.

...

## 8. Tài liệu tham khảo

...
