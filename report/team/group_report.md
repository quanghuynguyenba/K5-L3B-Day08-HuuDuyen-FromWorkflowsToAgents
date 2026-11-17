# Báo cáo nhóm — Lab 8 LogiX-VN

Tối đa 5 trang ngoài phụ lục. Số liệu lấy từ file trong `src/reports/`, không chép tay từ terminal.

## 1. Thông tin nhóm

- Tên nhóm: HuuDuyen
- Thành viên: xem `TEAM.md`
- Commit chốt (trùng `report/team/decision_adr.md`): `157f662`
- Repo: https://github.com/quanghuynguyenba/K5-L3B-Day08-HuuDuyen-FromWorkflowsToAgents

## 2. Mục tiêu và cách tiếp cận

Bài lab yêu cầu cài cùng một quy trình xử lý claim hoàn phí vận chuyển LogiX-VN trên bốn kiến trúc, rồi chọn kiến trúc **ít tự chủ nhất mà vẫn làm được việc** dựa trên số đo, không mặc định multi-agent tốt hơn.

Nhóm chia việc theo shape:

| Thành viên | Shape / phần việc |
| --- | --- |
| Nguyễn Hồng Thắng | A `workflow` |
| Nguyễn Minh Quang | B `single` |
| Nguyễn Bảo Duy | C `orchestrator` |
| Nguyễn Bá Quang Huy | D `swarm` (đối chứng) + benchmark / crash / báo cáo nhóm |

Thứ tự làm: (1) smoke + cổng host xanh; (2) hiện thực A/B/C theo docstring `TODO CP2/CP3`; (3) giữ swarm làm baseline; (4) chạy ma trận student, benchmark và crash suite; (5) viết ADR + research card từ file trong `src/reports/`.

## 3. Giải pháp

**A — Workflow.** Các bước Python cố định: `get_claim_view` → kiểm `order_id` / ownership qua `lookup_order` → `compute_compensation` → `durability.issue_refund`. Model không chọn tool và không tính tiền. Trace chỉ ghi field đã sanitize; truyền `crash_at` vào host và không bắt `InjectedCrash`.

**B — Single.** Một vòng ReAct tối đa 8 bước (`lab8.runtime.run_react`). `decide` chỉ được chọn tool đọc; sau vòng lặp, code shape gọi `issue_refund` với **quote đầy đủ**. Host là người ghi duy nhất (approval, trần theo đơn, idempotency).

**C — Orchestrator.** Orchestrator dựng `handoff.v1` bằng `make_handoff` (allowlist + tool chỉ đọc), chạy `run_readonly_investigators`, quote từ order view có cấu trúc, rồi gọi host. Trace ghi handoff đã sanitize, không ghi parent context / `parent_secret`.

**D — Swarm (có sẵn).** Peer/parse/draft qua MockLLM + investigator read-only. Cố ý có `REFERENCE_WEAKNESS`: khi `seed % 5 == 0` và delay trong ticket khác DB, swarm tin ticket — dùng để đo variance, không phải shape thắng sẵn.

Ranh giới thống nhất ở cả bốn shape: tiền do `compute_compensation`; approval / ledger / idempotency `refund:{claim_id}` do host; chữ trong ticket là dữ liệu người dùng, không phải lệnh.

**E - Bonus.** Nghiên cứu mở rộng: Topic B7 — Kiểm toán Grader (EMNLP 2026)

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

Số liệu từ `src/reports/benchmark.json` (14 claim × seeds `11,22,33,44,55` = 70 trial / shape):

| Shape | Pass rate | Pass^5 | Seed min–max | Variance | Cost/success (VND mô phỏng) | Mean / p95 steps |
| --- | ---: | ---: | --- | ---: | ---: | --- |
| workflow | 1.000 | 1.000 | 1.000–1.000 | 0.000000 | 0.00 | 3.79 / 4.0 |
| single | 1.000 | 1.000 | 1.000–1.000 | 0.000000 | 30.85 | 5.21 / 6.0 |
| orchestrator | 1.000 | 1.000 | 1.000–1.000 | 0.000000 | 57.13 | 5.36 / 6.0 |
| swarm | 0.986 | 0.929 | 0.929–1.000 | 0.000816 | 49.57 | 2.80 / 3.0 |

Crash: `src/reports/crash.json` → `crash_safety` = `20/20`.

Swarm có đúng 1 trial fail public: `R-swarm-C-002-s55`, reason `status resolved != denied` (tin ticket “trễ 5 ngày” trong khi order ghi 2 ngày).

Quyết định kiến trúc và lý do: xem `report/team/decision_adr.md` → chọn **workflow**. Phiếu nghiên cứu R2 và ablation topology swarm→workflow: xem `report/team/research_card.md`.

## 5. Khó khăn và cách xử lý

- PowerShell chặn `Activate.ps1` (`running scripts is disabled`). Xử lý: `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` hoặc gọi thẳng `.\.venv\Scripts\python.exe`.
- `pip install` lần đầu timeout khi tải wheel. Xử lý: cài lại với `--timeout 180 --retries 5` vào đúng interpreter venv trong `src/`.
- Ma trận student lúc đầu đỏ ở A/B/C là bình thường (stub). Mỗi shape xanh 62 test trước khi merge; sau khi đủ bốn shape, benchmark mới có ý nghĩa so sánh.
- Một số trường email/GitHub còn thiếu (ví dụ Thắng) sẽ bổ sung khi có đủ thông tin.

## 6. Tổng hợp đóng góp

Chi tiết đóng góp của từng thành viên nằm trong `TEAM.md` (bảng đóng góp) và báo cáo cá nhân `report/team/MSSV_HoTen.md`.

Tóm tắt: Thắng — workflow + `test_workflow_thangdktd.py`; Quang — single + bonus + `test_single_agent.py` ; Duy — orchestrator + `test_orchestrator_handoff.py`; Huy — swarm eval/report + `test_swarm_nguyenbaquanghuy.py`.


## 7. Hạn chế và hướng cải thiện

MockLLM không phải model sản xuất; latency là `unavailable`. Kết luận “chọn workflow” dựa trên bộ public 14 claim; cổng hidden 40×5 do giảng viên chấm có thể đảo quyết định nếu workflow dưới 0.75 trong khi shape khác vẫn đạt. Swarm cố ý yếu ở verification; nếu production cần đọc ticket mơ hồ thì cần verifier có cấu trúc (không tóm tắt LLM) trước khi ghi tiền. Nhóm chưa làm bonus.

## 8. Tài liệu tham khảo

- `README.md`, `CHECKPOINTS.md`, `SUBMISSION.md`, `RUBRIC.md`, `RULES.md`
- `docs/business_rules.md`, `docs/contracts.md`, `docs/architecture.md`, `docs/metrics.md`
- `src/reports/benchmark.json`, `src/reports/crash.json`
- MAST (NeurIPS 2025), *Why Do Multi-Agent LLM Systems Fail?* — khung phân loại lỗi dùng trong research card R2
