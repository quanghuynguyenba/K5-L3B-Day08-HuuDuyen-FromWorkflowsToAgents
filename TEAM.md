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
| Nguyễn Minh Quang | single agent | d694417 | test_single_agent.py |

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

### Đóng góp cụ thể

- Hiện thực Single Agent với bounded ReAct loop (tối đa 8 bước), read-only tool allowlist, xử lý trần đơn hàng và an toàn crash.
- Viết 4 unit tests độc lập tại `src/tests/test_single_agent.py` (commit `d694417`).

### Bằng chứng

- 62/62 test student shape single passed.
- 4/4 test tại `src/tests/test_single_agent.py` passed.
- Chi tiết xem tại `report/team/2A202603791_NguyenMinhQuang.md`.

## MSSV_HoTen

### Phần việc phụ trách

-

### Đóng góp cụ thể

-

### Bằng chứng

-
