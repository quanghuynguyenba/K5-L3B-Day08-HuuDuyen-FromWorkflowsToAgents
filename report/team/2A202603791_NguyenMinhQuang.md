# Báo cáo cá nhân — 2A202603791_NguyenMinhQuang

## 1. Thông tin thành viên

- Họ tên: Nguyễn Minh Quang
- MSSV: 2A202603791
- Email: 26ai.quangnm3@vinuni.edu.vn
- GitHub: QuangNEU

## 2. Phần việc phụ trách

- **Phụ trách chính Shape B — Single ReAct Agent** (`src/lab8/shapes/single.py`):
  - Xây dựng vòng lặp ReAct giới hạn bước (`max_steps=8`), cơ chế tương tác với các read tool thông qua allowlist và kết nối an toàn với Host.
  - Viết bộ kiểm thử độc lập cho Single Agent (`src/tests/test_single_agent.py`).
- **Phụ trách trọn gói phần Điểm cộng Bonus B7 — Kiểm toán Grader** (`src/bonus/`):
  - Thiết kế và phát triển framework kiểm toán biến hình (Metamorphic Testing Framework) cho bộ đo benchmark dựa trên nghiên cứu EMNLP 2026 (*Rethinking Evaluation of MAS Efficiency*).
  - Đề xuất các quan hệ biến hình mới, viết bộ kiểm thử tự động `src/tests/test_bonus_grader_audit.py` và hoàn thiện báo cáo khoa học `report/team/bonus_report.md`.

## 3. Đóng góp cụ thể

- **Về Shape B (Single Agent):**
  - **Commit chính:** `d694417` — `feat(single): implement bounded ReAct agent and add comprehensive unit tests`.
  - **Test đã thêm:** Thêm 4 test case trong `src/tests/test_single_agent.py`:
    - `test_single_agent_overcomes_swarm_weakness_on_t02`: Kiểm tra Single Agent không bị lừa bởi văn bản ticket trên T02 seed 55 như Swarm.
    - `test_single_agent_resists_prompt_injection`: Kiểm tra khả năng kháng injection trên C-009.
    - `test_single_agent_trace_contract_and_no_secret_leak`: Đảm bảo tuân thủ Trace Contract v1 và không rò rỉ secret/PII.
    - `test_single_agent_crash_recovery_leaves_single_refund`: Xác nhận khả năng resume an toàn sau crash (chỉ có 1 refund và 1 audit).
  - **Quyết định kỹ thuật:**
    - Thiết lập ranh giới nghiêm ngặt: ReAct loop chạy với `worker=True` chỉ được phép dùng read tools (`lookup_order`, `compute_compensation`). Thao tác ghi tiền hoàn toàn do Host thực hiện sau khi vòng lặp kết thúc.
    - Xử lý trần theo đơn: Truyền quote đầy đủ vào `issue_refund`, để Host tự trừ số tiền đã hoàn trước đó nhằm tránh lỗi trừ hai lần ở T14.
    - Xử lý crash an toàn: Truyền `crash_at` trực tiếp vào `issue_refund`, không nuốt `InjectedCrash`, ghi flush trace trước khi crash.

- **Về Phần Bonus B7 (Kiểm toán Grader - Grader Audit):**
  - **Mã nguồn cốt lõi (`src/bonus/grader_audit.py` & `src/bonus/__init__.py`):**
    - Hiện thực logic nạp fixture, định lượng sai lệch và đánh giá ma trận 3 ứng viên (`grader_a`, `grader_b`, `grader_c`) trước các quan hệ biến hình.
    - Đề xuất và hiện thực **2 quan hệ metamorphic mới**:
      + `cross_shape_isolation`: Đảm bảo tính độc lập dữ liệu đa shape, việc thêm trials của shape khác (`single`) không làm bẩn/thay đổi metrics của shape mục tiêu (`workflow`).
      + `permutation_order_invariance`: Đảm bảo tính bất biến đối với thứ tự xuất hiện của các trial trong mảng.
  - **Bộ kiểm thử biến hình (`src/tests/test_bonus_grader_audit.py`):**
    - Viết 6 hàm test toàn diện với 14 test cases (`14 passed`), chỉ ra chính xác:
      + `grader_b`: Là bản chuẩn (**Sound Grader**), thỏa mãn 100% các quan hệ metamorphic.
      + `grader_a`: Bị bắt lỗi nhân bản chi phí (`ratio_total_cost = 1.6684 != 2.0`) và lỗi phụ thuộc thứ tự duyệt do tích lũy chi phí ảo (`extra` cost).
      + `grader_c`: Bị bắt lỗi trừng phạt oan lỗi hạ tầng (`infra_error: true` bị gán `passed=False`), làm tụt pass rate oan từ 75.00% xuống 56.25% (-18.75%).
      + Kiểm tra quan hệ nghiệp vụ `delay_days_2_vs_3`: lật đúng từ 0đ lên 35.000đ khi delay từ 2 lên 3 ngày theo điều khoản `SHIP-LATE-03`.
  - **Báo cáo học thuật & số liệu định lượng:**
    - Xuất file kết quả tự động `src/reports/bonus_grader_audit.json` (Run ID: `audit-1794888367`).
    - Viết hoàn chỉnh báo cáo `report/team/bonus_report.md` theo chuẩn khung EMNLP 2026, trả lời sâu sắc cả 3 câu hỏi nghiên cứu bắt buộc.

## 4. Bằng chứng

- **Bằng chứng Shape B (Single Agent):**
  - Chạy bộ unit test tự viết: `pytest -q tests/test_single_agent.py` -> Kết quả: `4 passed in 0.18s`.
  - Chạy ma trận kiểm thử sinh viên: `pytest -q -m student -o addopts= -k "single and not swarm and not workflow and not orchestrator"` -> Kết quả: `62 passed in 2.06s`.
  - Chạy kiểm thử an toàn Crash Suite: `python -m lab8.crash_test --count 20 --out reports/crash.json` -> Kết quả: `crash_safety 20/20`.
  - Run ID: `R-single-C-001-s11` (sinh ra tại `src/traces/R-single-C-001-s11.json`).

- **Bằng chứng Phần Bonus B7 (Kiểm toán Grader):**
  - Chạy bộ kiểm thử biến hình: `pytest -q tests/test_bonus_grader_audit.py` -> Kết quả: `14 passed in 0.06s`.
  - Lệnh tái lập kết quả tự động: `python -m bonus.grader_audit --out reports/bonus_grader_audit.json`.
  - File báo cáo số liệu: `src/reports/bonus_grader_audit.json` (chứa đầy đủ ma trận lỗi định lượng và trạng thái của 3 grader).
  - Báo cáo khoa học: `report/team/bonus_report.md`.
  - Thẩm định tự động: `python -m lab8.check_submission` xác nhận `[ OK ] report/team/bonus_report.md: đã khai topic`.

## 5. Công cụ AI đã dùng

- **Công cụ:** Antigravity AI Assistant.
- **Mục đích:** Hỗ trợ phân tích mã giả, rà soát các corner case của luật bồi thường LogiX-VN, thiết kế cấu trúc test case cho Single Agent và thiết kế các quan hệ kiểm thử biến hình (Metamorphic Relations) cho Topic B7.
- **Cách tự kiểm chứng kết quả:** Tự chạy lại toàn bộ test suite bằng lệnh pytest trong terminal ảo `.venv`, đối chiếu thủ công với `docs/contracts.md`, kiểm tra tính toàn vẹn toán học của các tỷ số chi phí và pass rate trong file `bonus_grader_audit.json`.

## 6. Tự đánh giá

- Hoàn thành yêu cầu kỹ thuật và nghiệp vụ được giao cho Shape B (Single Agent).
- Đáp ứng trọn vẹn cả 3 tiêu chí Must-Pass (Không bypass approval, không rò rỉ bí mật, an toàn tuyệt đối khi crash 20/20).
- Đóng góp phần Điểm cộng Bonus B7 (+10 điểm tối đa) cho nhóm với đầy đủ code, test, số liệu định lượng tự động và báo cáo nghiên cứu chất lượng cao.

