# Báo cáo cá nhân — 2A202603791_NguyenMinhQuang

## 1. Thông tin thành viên

- Họ tên: Nguyễn Minh Quang
- MSSV: 2A202603791
- Email: 26ai.quangnm3@vinuni.edu.vn
- GitHub: QuangNEU

## 2. Phần việc phụ trách

- Phụ trách chính: **Shape B — Single ReAct Agent** (`src/lab8/shapes/single.py`).
- Xây dựng vòng lặp ReAct giới hạn bước (`max_steps=8`), cơ chế tương tác với các read tool thông qua allowlist và kết nối an toàn với Host.
- Viết bộ kiểm thử độc lập cho Single Agent (`src/tests/test_single_agent.py`).

## 3. Đóng góp cụ thể

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

## 4. Bằng chứng

- **Commit Hash:** [`d694417`]
- **Tên test và lệnh kiểm thử:**
  - Chạy bộ unit test tự viết: `pytest -q tests/test_single_agent.py` -> Kết quả: `4 passed in 0.18s`.
  - Chạy ma trận kiểm thử sinh viên: `pytest -q -m student -o addopts= -k "single and not swarm and not workflow and not orchestrator"` -> Kết quả: `62 passed in 2.06s`.
  - Chạy kiểm thử an toàn Crash Suite: `python -m lab8.crash_test --count 20 --out reports/crash.json` -> Kết quả: `crash_safety 20/20`.
- **Run ID & Trích đoạn kết quả mẫu (RunResult & Trace):**
  - Run ID: `R-single-C-001-s11` (sinh ra tại `src/traces/R-single-C-001-s11.json`).
  ```json
  {
    "run_id": "R-single-C-001-s11",
    "claim_id": "C-001",
    "shape": "single",
    "seed": 11,
    "status": "resolved",
    "proposal_amount": 35000,
    "refund_effects": [
      {
        "claim_id": "C-001",
        "idempotency_key": "refund:C-001",
        "amount_vnd": 35000,
        "status": "committed"
      }
    ],
    "committed_audit_count": 1,
    "model_calls": 5,
    "tool_calls": 3,
    "cost_estimate": 33.16,
    "trace_path": "traces/R-single-C-001-s11.json",
    "error_reason": null
  }
  ```
- **Kết quả từ công cụ thẩm định tự động của bài lab:**
  - `python -m lab8.check_submission` ghi nhận:
    `[ OK ] shape single: format đúng, 12/12 claim public pass ở seed 11`.
    `[ OK ] reports/crash.json: 20/20`.

## 5. Công cụ AI đã dùng

- **Công cụ:** Antigravity AI Assistant.
- **Mục đích:** Hỗ trợ phân tích mã giả, rà soát các corner case của luật bồi thường LogiX-VN và thiết kế cấu trúc test case.
- **Cách tự kiểm chứng kết quả:** Tự chạy lại toàn bộ test suite bằng lệnh pytest trong terminal ảo `.venv`, đối chiếu thủ công với `docs/contracts.md` và kiểm tra cấu trúc file trace JSON sinh ra để chắc chắn không chứa `CANARY_SECRET_*`.

## 6. Tự đánh giá

- Hoàn thành yêu cầu kỹ thuật và nghiệp vụ được giao cho Shape B (Single Agent).
- Đáp ứng trọn vẹn cả 3 tiêu chí Must-Pass (Không bypass approval, không rò rỉ bí mật, an toàn tuyệt đối khi crash 20/20).
- Có đầy đủ commit kỹ thuật, test case mới được host và student suite công nhận, và báo cáo cá nhân chuẩn chỉnh.

