# Team

## Thông tin nhóm

- Tên nhóm: HuuDuyen
- Lớp: L3B
- Repo nhóm: `K5-L3B-Day08-HuuDuyen-FromWorkflowsToAgents` (xem [SUBMISSION.md](SUBMISSION.md))
- URL repo, nhánh nộp, commit chốt: https://github.com/quanghuynguyenba/K5-L3B-Day08-HuuDuyen-FromWorkflowsToAgents · nhánh `main` · commit chốt xem `report/team/decision_adr.md`

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 | Nguyễn Hồng Thắng | 2A202603829 | | thangdktd | workflow |
|   2 | Nguyễn Minh Quang | 2A202603791 | 26ai.quangnm3@vinuni.edu.vn | QuangNEU | single + bonus |
|   3 | Nguyễn Bá Quang Huy | 2A202603926 | quanghuynguyenba@gmail.com | quanghuynguyenba | swarm / eval+report |
|   4 | Nguyễn Bảo Duy | 2A202603890 | baoduy2048@gmail.com | baoduy2048 | orchestrator |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Nguyễn Hồng Thắng | workflow | 9905c72 | test_workflow_thangdktd.py |
| Nguyễn Minh Quang | single agent & bonus B7 | d694417 | test_single_agent.py, test_bonus_grader_audit.py |
| Nguyễn Bá Quang Huy | swarm / eval+report | 157f662 | test_swarm_nguyenbaquanghuy.py |
| Nguyễn Bảo Duy | orchestrator | 70f1ac4 | test_orchestrator_handoff.py |

## Cá nhân

Mỗi thành viên đồng thời tạo một báo cáo riêng trong `report/` theo dạng:

```text
report/team/MSSV_HoTen.md
```

Ví dụ: `report/team/20241234_NguyenVanAn.md`.

Sao chép mục dưới đây cho từng thành viên.

## 2A202603829_NguyenHongThang

### Phần việc phụ trách

- Shape A — Workflow (`src/lab8/shapes/workflow.py`).

### Đóng góp cụ thể

- Hiện thực workflow deterministic CP2/CP3; thêm test thiếu `order_id` → `needs_info`.
- Chi tiết: `report/team/2A202603829_NguyenHongThang.md`.

### Bằng chứng

- 62/62 student tests shape workflow; commit `9905c72`.

## 2A202603791_NguyenMinhQuang

### Phần việc phụ trách

- Shape B — Single ReAct Agent (`src/lab8/shapes/single.py`).
- Mở rộng nghiên cứu: Topic B7 — Kiểm toán Grader (`src/bonus/grader_audit.py`).

### Đóng góp cụ thể

- Hiện thực Single Agent với bounded ReAct loop (tối đa 8 bước), read-only tool allowlist, xử lý trần đơn hàng và an toàn crash (commit `d694417`).
- Viết 4 unit tests độc lập tại `src/tests/test_single_agent.py`.
- Hiện thực framework kiểm toán biến hình bonus B7 (`src/bonus/grader_audit.py`), đề xuất 2 quan hệ biến hình mới (`cross_shape_isolation`, `permutation_order_invariance`).
- Viết 14 test cases tại `src/tests/test_bonus_grader_audit.py`, xuất file số liệu `src/reports/bonus_grader_audit.json` và hoàn thành báo cáo `report/team/bonus_report.md`.

### Bằng chứng

- 62/62 test student shape single passed; crash safety 20/20.
- 4/4 test tại `src/tests/test_single_agent.py` passed.
- 14/14 test tại `src/tests/test_bonus_grader_audit.py` passed; `check_submission` xác nhận `[ OK ] report/team/bonus_report.md: đã khai topic`.
- Chi tiết xem tại `report/team/2A202603791_NguyenMinhQuang.md` và `report/team/bonus_report.md`.

## 2A202603926_NguyenBaQuangHuy

### Phần việc phụ trách

- Shape D — Swarm reference: kiểm thử, benchmark, phân tích điểm yếu seed 55; tổng hợp báo cáo nhóm / ADR / research card.

### Đóng góp cụ thể

- Thêm `src/tests/test_swarm_nguyenbaquanghuy.py` khóa điểm yếu T02 seed 55 và kiểm không lộ secret.
- Chạy benchmark 4 shape × 5 seed và crash suite; điền `decision_adr.md`, `research_card.md`, `group_report.md`.

### Bằng chứng

- `src/reports/benchmark.json`, `src/reports/crash.json` (`20/20`).
- Chi tiết: `report/team/2A202603926_NguyenBaQuangHuy.md`.

## 2A202603890_NguyenBaoDuy

### Phần việc phụ trách

- Shape C — Orchestrator (`src/lab8/shapes/orchestrator.py`).

### Đóng góp cụ thể

- Commit `677a71c` (hiện thực orchestrator) và `70f1ac4` (`test_orchestrator_handoff.py`: handoff allowlist, không lộ secret, crash/resume một refund).

### Bằng chứng

- 62/62 student tests shape orchestrator trên bộ public.
- Chi tiết: `report/team/2A202603890_NguyenBaoDuy.md` (có thể bổ sung thêm khi có tài liệu cá nhân).
