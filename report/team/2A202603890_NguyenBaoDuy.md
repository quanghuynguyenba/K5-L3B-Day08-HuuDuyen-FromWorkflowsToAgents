# Báo cáo cá nhân — 2A202603890_NguyenBaoDuy

> Bản khung dựa trên commit/code đã merge. Có thể bổ sung mục AI đã dùng và tự đánh giá khi có tài liệu cá nhân.

## 1. Thông tin thành viên

- Họ tên: Nguyễn Bảo Duy
- MSSV: 2A202603890
- Email: baoduy2048@gmail.com
- GitHub: baoduy2048

## 2. Phần việc phụ trách

- Shape C — Orchestrator + read-only investigators (`src/lab8/shapes/orchestrator.py`).
- Test ranh giới handoff / trace / crash (`src/tests/test_orchestrator_handoff.py`).

## 3. Đóng góp cụ thể

- **Commit chính:**
  - `677a71c` — hiện thực `run_claim` cho orchestrator (CP2/CP3).
  - `70f1ac4` — thêm `test_orchestrator_handoff.py`.
- **Hiện thực kỹ thuật:**
  - Đọc claim qua `get_claim_view` (không đưa `parent_secret` / ticket thô vào trace).
  - Tạo handoff bằng `make_handoff` với tool chỉ đọc: `lookup_order`, `lookup_policy`, `lookup_refund_history`, `compute_compensation`.
  - Chạy `run_readonly_investigators`; quote từ order view có cấu trúc; truyền **quote đầy đủ** vào `durability.issue_refund`.
  - Truyền `crash_at`, không nuốt `InjectedCrash`; flush trace trước khi crash.
- **Test đã thêm:**
  - Không lộ `CANARY_SECRET_*` / `parent_secret` trong trace và kết quả.
  - Handoff chỉ còn field allowlist và tool read-only.
  - Không copy nguyên văn ticket vào trace.
  - Crash rồi resume vẫn một dòng ledger / một audit committed.

## 4. Bằng chứng

- File: `src/lab8/shapes/orchestrator.py`, `src/tests/test_orchestrator_handoff.py`
- Ma trận student: `python -m pytest -q -m student -o addopts= -k orchestrator` → 62 passed (theo kết quả nhóm sau merge).
- Benchmark public (`src/reports/benchmark.json` → `by_shape.orchestrator`): pass rate 1.0, pass^5 1.0, variance 0.0, cost/success ≈ 57.13 VND mô phỏng.
- Run id mẫu: `R-orchestrator-C-001-s11`

## 5. Công cụ AI đã dùng

- (Bổ sung sau)

## 6. Tự đánh giá

- (Bổ sung sau)
