# Team

## Thông tin nhóm

- Tên nhóm: 
- Lớp: L3B
- Repo nhóm: `K5-L3A-DAY08-TenNhom-FromWorkflowsToAgents` (xem [SUBMISSION.md](SUBMISSION.md))
- URL repo, nhánh nộp, commit chốt:

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 |        |      |       |        |         |
|   2 |  Nguyễn Minh Quang  | 2A202603791  | 26ai.quangnm3@vinuni.edu.vn  |  QuangNEU   |   single   |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Nguyễn Minh Quang | single agent & bonus B7 | d694417 | test_single_agent.py, test_bonus_grader_audit.py |

## Cá nhân

Mỗi thành viên đồng thời tạo một báo cáo riêng trong `report/` theo dạng:

```text
report/team/MSSV_HoTen.md
```

Ví dụ: `report/team/20241234_NguyenVanAn.md`.

Sao chép mục dưới đây cho từng thành viên.

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

## MSSV_HoTen

### Phần việc phụ trách

-

### Đóng góp cụ thể

-

### Bằng chứng

-
