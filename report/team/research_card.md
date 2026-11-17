# Research card

File này nằm cố định ở `report/team/research_card.md`; `python -m lab8.check_submission` đọc cột "Nhóm điền" bằng máy.

Phần này nối tiếp phần mini research ở buổi lý thuyết (slide 62–63). Chọn **một** câu R1–R4, điền 6 dòng, rồi biến nó thành **một ablation** chạy được trong lab: giữ nguyên model (MockLLM), tool, seed, corpus và giá, chỉ thay đổi một biến.

| Câu | Nguồn (thư viện tham khảo, slide 68–69) | Câu hỏi gợi ý cho lab |
| --- | --- | --- |
| R1 · Scaling | Google Research 2026, *Towards a Science of Scaling Agent Systems* | Task claims chủ yếu tuần tự hay song song được? Dự đoán thêm investigator sẽ làm pass rate và cost/success thay đổi thế nào. |
| R2 · Failure taxonomy | MAST, NeurIPS 2025, *Why Do Multi-Agent LLM Systems Fail?* | Các trial fail của swarm (seed 55) thuộc nhóm nào: lỗi giao việc, lệch mục tiêu, hay lỗi kiểm chứng? |
| R3 · Architecture choices | AgentArch, AACL 2026 | Nếu đổi topology mà giữ nguyên mọi thứ khác, biến nào phải cố định để kết luận còn đúng? |
| R4 · Evaluation confounders | EMNLP 2026, *Rethinking Evaluation of MAS Efficiency* | Làm sao chứng minh chênh lệch giữa A/B/C/D là do topology, không phải do prompt, tool hay seed? |

## Phiếu của nhóm

Điền vào cột cuối. Không xóa hai cột đầu.

| Field | Hướng dẫn | Nhóm điền |
| --- | --- | --- |
| CÂU | R1 / R2 / R3 / R4 | R2 |
| SOURCE | Paper/blog, năm, URL, loại bằng chứng (benchmark, production report, blog kinh nghiệm) | MAST, NeurIPS 2025, *Why Do Multi-Agent LLM Systems Fail?*; taxonomy failure của multi-agent LLM (benchmark / empirical study) |
| CLAIM | Một kết luận gốc của nguồn. Không viết rộng hơn phạm vi paper | Nhiều lỗi multi-agent không nằm ở “model kém” đơn thuần mà thuộc các nhóm như giao việc, lệch mục tiêu, hoặc thiếu kiểm chứng trước khi hành động |
| BOUNDARY | Model / dataset / setup giới hạn kết luận đó | Lab dùng MockLLM cố định theo seed, 14 claim public, giá `config/pricing.yaml`, evaluator `lab8.metrics`; không phải production LLM |
| HYPOTHESIS | Nếu đổi shape A→B (hoặc bật/tắt một thành phần), metric X sẽ đổi theo hướng nào, khoảng bao nhiêu | Nếu bỏ hành vi tin `delay_days` trong ticket (đổi topology swarm → workflow, giữ seed/corpus/tool/giá), pass rate tăng từ ≈0.986 lên 1.0 và seed variance giảm về 0 vì hết fail C-002 seed 55 |
| METHOD | Biến giữ cố định: model, tools, seed, corpus, giá, evaluator. Biến duy nhất được thay đổi: … | Topology shape: `swarm` (có `REFERENCE_WEAKNESS`) so với `workflow` (không tin ticket). Cùng seeds `11,22,33,44,55`, cùng `data/public_claims.jsonl` |
| FAILURE / METRIC | Một lỗi dự kiến và cách đo nó: pass rate, pass^5, variance, cost/success, leak, bypass | Lỗi kiểm chứng: swarm trả `resolved` thay vì `denied` ở C-002 seed 55; đo bằng pass rate, pass^5, `seed_pass_rates`, `seed_variance` trong `benchmark.json` |

## Kết quả ablation

- Lệnh đã chạy (copy nguyên văn): `python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json`
- Run id / file report: fail chứng minh = `R-swarm-C-002-s55`; đối chứng đúng = `R-workflow-C-002-s55`; file `src/reports/benchmark.json`
- Kết quả so với hypothesis (**đúng**): swarm `pass_rate=0.985714`, `pass_5=0.928571`, `seed_variance≈0.000816`, seed 55 = 0.928571 với đúng 1 lỗi `status resolved != denied` tại C-002; workflow `pass_rate=1.0`, `pass_5=1.0`, `seed_variance=0.0`. Thuộc nhóm **lỗi kiểm chứng** (verification): tin chữ ticket hơn cột `delay_days` có cấu trúc, không phải lỗi giao việc handoff.
- Giới hạn: MockLLM không phải model sản xuất, latency là `unavailable`.
