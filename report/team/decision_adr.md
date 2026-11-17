# Decision record

Điền sau dấu `:` trên cùng dòng, không xóa nhãn. Giảng viên và `python -m lab8.check_submission` (chạy trong `src/`) đọc các dòng này bằng máy. Không đổi tên hay dời file này khỏi `report/team/`.
`Shape chọn` phải là đúng một trong: `workflow`, `single`, `orchestrator`, `swarm`. Đây là shape được chấm cổng hidden.

- Shape chọn: workflow
- Commit hash: ecf4f5a
- Run id dẫn chứng: R-workflow-C-001-s11
- Pass rate / pass^5 / seed min-max / variance: 1.0 / 1.0 / 1.0–1.0 / 0.0
- Cost per success (mô phỏng, VND): 0.0
- Crash safety: 20/20 (src/reports/crash.json)
- Secret leak / approval bypass: 0 / 0 (quan sát trên bộ public + host tests; grader hidden chấm cuối)

Ví dụ một dòng đã điền: `- Shape chọn: workflow`, `- Crash safety: 20/20 (src/reports/crash.json)`.

## Vì sao shape này

Trên cùng corpus public 14 claim × 5 seed (`src/reports/benchmark.json`), workflow đạt pass rate 1.0 và pass^5 1.0 với seed variance 0.0, trong khi swarm chỉ đạt khoảng 0.986 / 0.929 vì fail cố ý ở C-002 seed 55. Single và orchestrator cũng đạt 1.0 / 1.0 nhưng cost/success mô phỏng cao hơn (single ≈ 30.85 VND, orchestrator ≈ 57.13 VND) và có thêm model/tool calls. Mục tiêu lab là chọn kiến trúc **ít tự chủ nhất mà vẫn làm được việc**; workflow đáp ứng đúng tiêu chí đó trên số liệu công khai hiện có.

## Có đáng đổi lấy multi-agent không

Không, với bằng chứng public hiện tại. Multi-agent (orchestrator/swarm) không cải thiện pass rate so với workflow, lại tăng cost và (ở swarm) tăng variance do tin chữ ticket. Nhóm sẽ đảo quyết định chỉ khi trên bộ hidden workflow dưới ngưỡng 0.75 trong khi single/orchestrator ≥ 0.75 và vẫn giữ must-pass (không approval bypass, không lộ secret, crash 20/20), hoặc khi bài toán thật cần đọc ticket mơ hồ mà rules-only không đủ.

## Trace và bằng chứng

Trace workflow ghi các event đã sanitize: `claim_view` (không ticket thô), `lookup_order` / quyết định trạng thái, `quote` từ `compute_compensation`, và kết quả host. Cố ý **không** ghi `parent_secret`, claim/order row thô, SĐT, địa chỉ, tên hàng hay header. Scanner grader đọc `trace_path` và mọi file `<run_id>*` cùng thư mục; nếu ghi log ngoài chỗ đó thì không được coi là đã chứng minh sạch secret — phải để artifact nằm đúng contract v1. Bằng chứng chạy: `src/reports/benchmark.json`, `src/reports/crash.json`.

## Giới hạn

MockLLM không phải model sản xuất. Latency để `unavailable`. Không sửa JSON cho đủ 0.75. Commit hash `ecf4f5a` là merge chứa đủ bốn shape; sau khi commit thêm báo cáo/benchmark cần chạy lại `git rev-parse --short HEAD` và cập nhật dòng Commit hash rồi commit tiếp theo [SUBMISSION.md](../../SUBMISSION.md).
