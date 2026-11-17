# Bonus report: B7
<!-- Bonus report: B1 .. B7 audit framework compatibility -->

- **Topic + nguồn (tên, năm, slide):** B7 · Kiểm toán grader. Nguồn: *Rethinking Evaluation of MAS Efficiency* (EMNLP 2026), slide 44, 59.
- **CLAIM của nguồn (1 câu):** Nếu thước đo (evaluator/grader) bị sai lệch, mọi so sánh hiệu năng giữa các kiến trúc agent đều vô nghĩa, bất kể bản thân agent có hoạt động đúng hay không.
- **BOUNDARY (model / dataset / setup):** Đánh giá benchmark trên hệ thống xử lý khiếu nại LogiX-VN với MockLLM, bộ fixtures kiểm thử biến hình (metamorphic testing) gồm các cặp trial có lỗi hạ tầng, nhân bản dữ liệu, phân vùng đa shape và biến động thời gian trễ.
- **HYPOTHESIS trên LogiX:** Một grader đúng chuẩn phải thỏa mãn tính bất biến biến hình (*metamorphic invariants*): lỗi hạ tầng (`infra_error: true`) không được tính vào tỷ lệ quyết định sai của agent; việc nhân đôi dữ liệu phải giữ nguyên tỷ lệ hiệu quả và nhân đôi tổng chi phí; thứ tự các trial và sự xuất hiện của shape khác không làm thay đổi chỉ số thống kê.
- **Biến cố định / biến thay đổi:**
  - Biến cố định: Bộ fixture (`metamorphic_infra.json`, `metamorphic_duplicate.json`, `metamorphic_claim_delay.json`), chính sách bồi thường `POL-2026-11`, hàm cơ sở `lab8.metrics.summarize`.
  - Biến thay đổi: Thuật toán tiền xử lý và tổng hợp của 3 ứng viên grader (`grader_a`, `grader_b`, `grader_c`), tỷ lệ lỗi hạ tầng, thứ tự trial và sự kết hợp đa kiến trúc.
- **Lệnh tái lập (copy-paste được):**
  - Chạy toàn bộ test suite kiểm toán: `python -m pytest -q tests/test_bonus_grader_audit.py`
  - Tái lập xuất file báo cáo số liệu: `python -m bonus.grader_audit --out reports/bonus_grader_audit.json`
- **File report + run id:** `src/reports/bonus_grader_audit.json` (Run ID: `audit-1794888367`).

---

## Kết quả

Số liệu định lượng được trích xuất trực tiếp từ `src/reports/bonus_grader_audit.json` do script sinh tự động:

### 1. Bảng ma trận đối sánh Grader × Quan hệ Metamorphic

| Grader | MR Hạ tầng (`infra`) | MR Nhân bản (`duplicate`) | MR Cách ly Shape (Mới) | MR Bất biến Thứ tự (Mới) | Kết luận |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **`grader_a`** | **PASS** (diff: 0.0000) | **FAIL** (cost ratio: 1.6684; diff cost/succ: -5.2222) | **PASS** (diff: 0.0000) | **FAIL** (lệch chi phí theo thứ tự duyệt) | **DEFECTIVE** |
| **`grader_b`** | **PASS** (diff: 0.0000) | **PASS** (cost ratio: 2.0000; diff cost/succ: 0.0000) | **PASS** (diff: 0.0000) | **PASS** (diff: 0.0000) | **SOUND / CORRECT** |
| **`grader_c`** | **FAIL** (pass rate: 0.75 $\to$ 0.5625, diff: -0.1875) | **PASS** (cost ratio: 2.0000; diff cost/succ: 0.0000) | **PASS** (diff: 0.0000) | **PASS** (diff: 0.0000) | **DEFECTIVE** |

### 2. Kiểm tra quyết định nghiệp vụ trên `metamorphic_claim_delay.json`
- **Quan hệ lật quyết định (`delay_days_2_vs_3`):** **PASS**
  - Khi `delay_days = 2` (Input `a`): Đơn trễ dưới ngưỡng tối thiểu (`min_delay_days = 3` của điều khoản `SHIP-LATE-03`), `compute_compensation` trả về đúng `0 VND` (`reason: no_entitlement`).
  - Khi `delay_days = 3` (Input `b`): Đơn thỏa mãn điều kiện trễ hẹn, `compute_compensation` trả về đúng mức phí vận chuyển `35,000 VND` (`reason: entitled`).
  - Logic nghiệp vụ tuân thủ 100% hợp đồng chính sách.

### 3. Chi tiết hành vi sai lệch của từng Grader
1. **`grader_c` — Đếm lỗi hạ tầng vào lỗi quyết định:**
   - Trong `grader_c.py`: Dòng `rows = [dict(t, passed=False) if t.get("infra_error") else dict(t) for t in trials]` ép các trial bị lỗi hạ tầng thành `passed=False`.
   - Hậu quả: Pass rate của `workflow` bị tụt oan từ `75.00%` xuống `56.25%` (-18.75%). Grader này trừng phạt kiến trúc agent vì lỗi mạng/timeout của môi trường máy chủ.
2. **`grader_a` — Tích luỹ chi phí ảo và phụ thuộc thứ tự:**
   - Trong `grader_a.py`: Vòng lặp cộng thêm `extra += float(row["cost_estimate"])` cho mỗi `claim_id` duy nhất gặp lần đầu.
   - Hậu quả 1 (Vi phạm tính nhân bản): Khi nhân đôi tập trial (từ 12 lên 24), chi phí cơ sở nhân đôi nhưng `extra` chỉ cộng 1 lần cho mỗi `claim_id` duy nhất $\to$ tổng chi phí chỉ tăng 1.6684 lần (từ 283.5 lên 473.0 VND thay vì 567.0 VND), khiến `cost_per_success_vnd` bị kéo giảm ảo 5.2222 VND.
   - Hậu quả 2 (Vi phạm tính bất biến thứ tự): Với cùng 1 `claim_id`, các seed khác nhau có chi phí khác nhau. Giá trị `extra` phụ thuộc vào việc seed nào xuất hiện trước trong mảng, làm thước đo mất tính tất định.
3. **`grader_b` — Bản chuẩn xác (Sound Grader):**
   - Đặt `passed=None` khi `infra_error=True`, loại bỏ trial lỗi hạ tầng khỏi mẫu số tính pass rate nhưng vẫn ghi nhận trạng thái.
   - Giữ nguyên tính tuyến tính của chi phí và độc lập thứ tự.

---

## Kết luận

- **Đánh giá hypothesis:** Kết quả thực nghiệm trên LogiX hoàn toàn **ỦNG HỘ** hypothesis của EMNLP 2026. Một thước đo bị lỗi (như `grader_a` làm méo mó chi phí, hay `grader_c` hạ thấp pass rate vì lỗi hạ tầng) sẽ dẫn tới kết luận sai lệch nghiêm trọng về hiệu năng của các shape.
- **Điều kiện đảo ngược:** Kết luận sẽ đảo ngược nếu mục tiêu benchmark là đo lường *Độ khả dụng toàn diện hệ thống (End-to-End System Availability)* bao gồm cả hạ tầng phần cứng/mạng, thay vì đánh giá *Năng lực ra quyết định nghiệp vụ của Agent (Agent Decision Quality)*.

---

## Trả lời các câu hỏi nghiên cứu bắt buộc

1. **Một lỗi thước đo có thể lọt qua mọi quan hệ nhóm đã viết không?**
   - **Có.** Nếu một lỗi xuất hiện mang tính hệ thống làm sai lệch Input $A$ và Input $B$ theo cùng một tỷ lệ hoặc cùng một dạng biến đổi (ví dụ: một lỗi cố định trừ 50% chi phí ở mọi lượt gọi hoặc một lỗi bỏ qua hoàn toàn việc kiểm tra secret leak trên tất cả các trial), thì tỷ lệ so sánh giữa $A$ và $B$ vẫn có thể giữ nguyên và lọt qua bài test biến hình.
2. **Quan hệ nào còn thiếu?**
   - Còn thiếu **Quan hệ Đơn điệu Chi phí theo Quy mô Tool (`Tool-Call Cost Monotonicity`)**: Nếu một trial thực hiện thêm $k$ lượt gọi tool/model mà không đổi kết quả, tổng chi phí bắt buộc phải tăng đơn điệu ngặt $\ge k \times c_{\min}$.
   - Còn thiếu **Quan hệ Kháng Rò rỉ Bí mật (`Secret Injection Invariance`)**: Bơm thêm các trường chứa `CANARY_SECRET_*` vào payload không được làm thay đổi quyết định số tiền hoàn trả nhưng bắt buộc phải kích hoạt cơ chế chặn rò rỉ.
3. **Vì sao quan hệ metamorphic hữu ích khi không có đáp án tuyệt đối cho số tiền hay pass rate?**
   - Trong các hệ thống Multi-Agent phức tạp, việc xây dựng một "Test Oracle" hoàn hảo (biết trước chính xác 100% số tiền hay pass rate của mọi tình huống ngẫu nhiên) là vô cùng tốn kém và bất khả thi. Kiểm thử biến hình giải quyết bài toán này bằng cách **kiểm tra mối quan hệ logic giữa các đầu vào và đầu ra liên quan** (thay vì kiểm tra giá trị tuyệt đối). Dù không biết pass rate thực tế là bao nhiêu, ta vẫn khẳng định chắc chắn rằng: *việc nhân đôi dữ liệu không thể làm thay đổi tỷ lệ thành công*, và *lỗi timeout hạ tầng không được phép làm giảm điểm nghiệp vụ của agent*.

---

## Giới hạn

1. **Giới hạn của MockLLM:** MockLLM phản hồi tất định theo seed và bảng tra cứu, không có độ trễ token thực tế, không có hiện tượng ảo giác (hallucination) đa dạng như LLM thương mại ngoài đời thực.
2. **Giới hạn về tập kịch bản:** Các fixture kiểm toán mới chỉ bao phủ các kịch bản mẫu của đơn hàng chậm giao và hư hỏng; chưa bao phủ các corner-case phức tạp như đồng thời trễ giao hàng, hỏng hàng và có khiếu nại trước đó chưa giải quyết.
3. **Giới hạn mô phỏng chi phí:** Mô hình chi phí tính dựa trên số bước và số lần gọi tool giả lập theo công thức cố định, chưa phản ánh đúng chi phí input/output token thực tế theo ngữ cảnh động.
