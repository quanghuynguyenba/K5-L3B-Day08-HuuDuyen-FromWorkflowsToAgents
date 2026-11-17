# Báo cáo cá nhân — 2A202603926_NguyenBaQuangHuy

## 1. Thông tin thành viên

- Họ tên: Nguyễn Bá Quang Huy
- MSSV: 2A202603926
- Email: quanghuynguyenba@gmail.com
- GitHub: quanghuynguyenba

## 2. Phần việc phụ trách

- Shape D — Swarm reference (`src/lab8/shapes/swarm_reference.py`): không viết lại shape, dùng làm baseline đối chứng cost/variance.
- Eval / report: chạy ma trận student cho swarm, khóa điểm yếu seed 55 bằng test cá nhân, chạy benchmark 4 shape và crash suite, điền `decision_adr.md`, `research_card.md`, `group_report.md`, cập nhật `TEAM.md`.

## 3. Đóng góp cụ thể

- **Test đã thêm:** `src/tests/test_swarm_nguyenbaquanghuy.py`
  - `test_swarm_reference_weakness_visible_on_t02_seed_55`: seed 11 pass / seed 55 fail đúng như điểm yếu công bố.
  - `test_swarm_t02_seed_55_records_model_override`: xác nhận event `model_override` với `delay_days=5` và status `resolved` / proposal 20000.
  - `test_swarm_trace_and_result_never_leak_canary_secret`: trace + RunResult không chứa `CANARY_SECRET_*` / `parent_secret`.
- **Benchmark / crash:** tạo `src/reports/benchmark.json` và dùng `src/reports/crash.json` (`20/20`).
- **Quyết định kỹ thuật / phân tích:**
  - Giữ nguyên khối `REFERENCE_WEAKNESS` — đây là tín hiệu variance của lab, không phải bug cần “sửa cho xanh benchmark”.
  - Phân loại fail `R-swarm-C-002-s55` là **lỗi kiểm chứng** (tin ticket hơn DB), dùng làm ablation R2 so với workflow.
  - Đề xuất shape chọn = `workflow` vì pass 1.0 / pass^5 1.0 / variance 0 với cost thấp nhất trên cùng corpus và seed.

## 4. Bằng chứng

- Lệnh kiểm test cá nhân: `python -m pytest -q tests/test_swarm_nguyenbaquanghuy.py`
- Lệnh ma trận swarm: `python -m pytest -q -m student -o addopts= -k swarm` → kỳ vọng 62 passed
- Lệnh swarm reference: `python -m pytest -q tests/test_swarm_reference.py` → 13 passed
- Benchmark:

```text
python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json
```

- Chỉ số swarm lấy từ `src/reports/benchmark.json` → `by_shape.swarm`:
  - pass_rate ≈ 0.985714
  - pass_5 ≈ 0.928571
  - seed_min–max ≈ 0.928571–1.0
  - seed_variance ≈ 0.000816
  - cost_per_success_vnd ≈ 49.57
  - mean_steps / p95_steps = 2.8 / 3.0
  - Fail duy nhất: `R-swarm-C-002-s55`, reason `status resolved != denied`
- Crash: `src/reports/crash.json` → `crash_safety: 20/20`
- ADR run id đối chứng workflow: `R-workflow-C-001-s11`

## 5. Công cụ AI đã dùng

- **Công cụ:** Cursor (Composer).
- **Dùng vào:** đọc `SUBMISSION.md` / `CHECKPOINTS.md`, cấu trúc báo cáo, diễn giải điểm yếu swarm seed 55, soạn nháp báo cáo nhóm/cá nhân và research card R2.
- **Tự kiểm tra:** chạy lại smoke/pytest/benchmark trong `src/` với venv; đối chiếu số liệu với `src/reports/benchmark.json` và `crash.json`; không sửa tay JSON report; không bịa latency.

## 6. Tự đánh giá

- Hoàn thành phần swarm-as-baseline: hiểu luồng handoff → investigator → quote → host, và giải thích được vì sao seed 55 làm hỏng T02.
- Đóng góp bằng chứng đo lường (benchmark, crash) và khung quyết định kiến trúc cho nhóm.
- Đã cập nhật commit hash `157f662` trong `TEAM.md` và `report/team/decision_adr.md`.
