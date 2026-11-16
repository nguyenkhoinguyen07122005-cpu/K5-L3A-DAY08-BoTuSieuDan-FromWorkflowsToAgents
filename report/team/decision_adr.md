# Decision record

Điền sau dấu `:` trên cùng dòng, không xóa nhãn. Giảng viên và `python -m lab8.check_submission` (chạy trong `src/`) đọc các dòng này bằng máy. Không đổi tên hay dời file này khỏi `report/team/`.
`Shape chọn` phải là đúng một trong: `workflow`, `single`, `orchestrator`, `swarm`. Đây là shape được chấm cổng hidden.

- Shape chọn: workflow
- Commit hash: __COMMIT__ (thay bằng hash thật sau khi commit, xem bước 8 trong hướng dẫn nộp)
- Run id dẫn chứng: R-workflow-C-002-s55, R-workflow-C-013-s11, R-workflow-C-014-s33 (src/reports/benchmark.json); so sánh R-swarm-C-002-s55 (fail)
- Pass rate / pass^5 / seed min-max / variance: 1.000 / 1.000 / 1.000–1.000 / 0.000000 (14 claim × 5 seed = 70 trial, src/reports/benchmark.json)
- Cost per success (mô phỏng, VND): 28.83 (workflow) — single 39.89, orchestrator 56.66, swarm 49.57
- Crash safety: 20/20 (src/reports/crash.json)
- Secret leak / approval bypass: 0 / 0 (70/70 trial reason=ok; không trial nào forbidden_substring hay refund khi approval sai)

Ví dụ một dòng đã điền: `- Shape chọn: workflow`, `- Crash safety: 20/20 (src/reports/crash.json)`.

## Vì sao shape này

Cùng 14 claim public × 5 seed (11, 22, 33, 44, 55), cùng MockLLM, cùng bảng giá `config/pricing.yaml`, mỗi trial một SQLite riêng: ba shape nhóm viết (workflow, single, orchestrator) đều đạt pass 1.000, pass^5 1.000, variance 0. Khi chất lượng ngang nhau thì quyết định nằm ở chi phí, độ phức tạp và bề mặt rủi ro. Workflow rẻ nhất (28.83 VND/success, 1 model call/trial, p95 7 steps) so với single 39.89 (5.6 model call, p95 13), orchestrator 56.66 (5.5 tool call) và swarm 49.57 (pass 0.986, fail T02 ở seed 55 vì tin số ngày trễ trong ticket). Bài toán claim là chuỗi tuần tự cố định: đọc claim → đọc đơn → tính tiền theo chính sách → host ghi sổ. Không có bước nào cần model tự chọn hướng, nên workflow cho ít quyền tự quyết nhất mà vẫn đạt kết quả tốt nhất. Mọi đường tiền đi qua `compute_compensation` và `durability.issue_refund`, model chỉ viết bản nháp trả lời.

## Có đáng đổi lấy multi-agent không

Trên bộ public thì không: orchestrator có cùng pass nhưng tốn gần gấp đôi (56.66 so với 28.83 VND/success). Ablation R1 (research_card.md) thêm một investigator nữa: pass vẫn 1.000 nhưng cost lên 91.58 VND/success (+62%). Swarm vừa đắt hơn vừa có variance > 0. Nếu một shape multi-agent chỉ hơn pass một chút mà cost và variance cao hơn, nhóm vẫn giữ workflow, vì pass^5 và việc không bao giờ trả sai tiền quan trọng hơn vài phần trăm pass. Quyết định sẽ đảo lại khi: (1) claim cần đọc nguồn không có cấu trúc (ảnh hàng hỏng, email nhà vận chuyển) mà workflow cố định không xử lý được; (2) các bước đọc độc lập nhau và latency thật trở thành ràng buộc (song song hóa investigator); hoặc (3) bộ hidden cho thấy workflow fail những ca mà single hay orchestrator xử lý được, tính trên cùng claim × seed.

## Trace và bằng chứng

Mỗi run ghi `traces/<run_id>.json` (`lab8.trace.Trace`, đúng trace contract v1) trên mọi nhánh trả về, kể cả nhánh sớm (`failed`, `needs_info`, `escalated`) và nhánh crash (ghi event `crash` rồi raise lại `InjectedCrash`). Event của workflow: `start`, `tool` (get_claim_view: found/has_order_id; lookup_order: visible + order view đã allowlist), `quote` (amount, reason, clause_ids, parts), `host_call`, `host_result` (ok/reason/conflict), `decision`. Single thêm `act`/`observe`/`loop_end` (số step, lý do dừng); orchestrator thêm `handoff` (bản handoff.v1 đã sanitize, `facts` rỗng) và `investigator`.

Cố ý **không** ghi: nội dung ticket (khách có thể chèn số giả, prompt injection hoặc PII), `parent_secret`, `raw_headers`, principal row đầy đủ, các cột PII của đơn (phone, address, item_name, internal_note) — `lookup_order` chỉ trả ORDER_FIELDS nên các cột này không bao giờ đi vào bộ nhớ của shape. Trace nằm đúng chỗ scanner quét (`trace_path` + file `<run_id>*` cùng thư mục); test `tests/test_group_shapes.py::test_trace_written_and_clean_on_every_path` kiểm tra thêm việc không có `CANARY_SECRET`, `Bearer `, `parent_secret`, `raw_headers` và câu chữ ticket. Nếu trace phải nằm ngoài chỗ scanner quét, đội security cần chứng minh bằng code chứ không bằng log: chỉ đọc qua tool allowlist, handoff đi qua `sanitize()` (từ chối canary), và chạy cùng bộ scanner trên nơi lưu đó.

## Giới hạn

MockLLM không phải model sản xuất, nên "single" và "orchestrator" ở đây đo chi phí điều phối chứ không đo khả năng suy luận thật. Latency để `unavailable`. Kết quả chỉ trên 14 claim public; bộ hidden 40 × 5 có thể có ca workflow cố định chưa lường trước. Không sửa JSON cho đủ 0.75: mọi số liệu lấy từ `src/reports/benchmark.json` và `src/reports/crash.json` do script sinh ra.
