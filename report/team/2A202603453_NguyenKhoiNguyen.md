# Báo cáo cá nhân — 2A202603453_NguyenKhoiNguyen

## 1. Thông tin thành viên

- Họ tên: Nguyễn Khôi Nguyên
- MSSV: 2A202603453
- Email: nguyenkhoinguyen07122005@gmail.com
- GitHub: nguyenkhoinguyen07122005-cpu

## 2. Phần việc phụ trách

- Ba shape: `src/lab8/shapes/workflow.py`, `single.py`, `orchestrator.py`.
- Ablation R1: `src/bonus/orchestrator_two_investigators.py`.
- Chạy benchmark, crash suite; viết `decision_adr.md`, `research_card.md`, `group_report.md`.

## 3. Đóng góp cụ thể

- Commit chính: 25ec330
- Test thêm: `tests/test_group_shapes.py` (41 case: không tin số ngày trễ trong ticket ở 5 seed, trace sạch trên mọi nhánh kể cả claim không tồn tại, single không vượt 8 step, handoff orchestrator chỉ có fact allowlist và tool chỉ đọc, crash ở 3 điểm rồi chạy lại vẫn chỉ 1 refund 35000); `tests/test_bonus_orchestrator_x2.py` (2 case).
- Quyết định kỹ thuật: shape luôn gửi toàn bộ quote cho host (host tự trừ phần đã hoàn và kiểm approval); không đọc ticket để lấy order id hay số ngày; trace không ghi ticket; orchestrator tính lại quote và đối chiếu với investigator; chọn workflow vì cùng pass nhưng rẻ nhất.

## 4. Bằng chứng

- `src/reports/benchmark.json`: workflow pass 1.000, pass^5 1.000, variance 0, cost/success 28.83 VND; run id `R-workflow-C-002-s55` (T02 deny đúng, trong khi `R-swarm-C-002-s55` fail).
- `src/reports/crash.json`: crash_safety 20/20.
- `src/reports/bonus_benchmark.json`: orchestrator 56.66 → orchestrator_x2 91.58 VND/success, pass giữ 1.000.
- Lệnh kiểm: `python -m pytest -q` (97 passed), `python -m pytest -q -m student -o addopts=` (248 passed), `python -m lab8.check_submission` (0 FAIL).

## 5. Công cụ AI đã dùng

- Công cụ: Claude (trợ lý AI).
- Dùng vào việc: giải thích tài liệu lab và ranh giới host/model, gợi ý cấu trúc code cho ba shape và test, gợi ý bản nháp cho các phần báo cáo.
- Tự kiểm tra: đọc lại từng shape đối chiếu với `docs/business_rules.md` và `swarm_reference.py`; tự chạy `pytest` (host + student), benchmark, crash suite và `check_submission` trên máy; số liệu trong báo cáo lấy từ file JSON do script sinh ra, không chép tay.

## 6. Tự đánh giá

- Đã hiểu vì sao tiền phải do code tính và host ghi sổ, còn model chỉ đọc/viết nháp; vì sao không được tin số trong ticket.
- Hiểu cách so sánh công bằng: cùng claim × seed × giá, đo pass^5 và variance chứ không chỉ một lần chạy.
- Hạn chế: kết luận dựa trên MockLLM và 14 claim public; chưa đo latency thật.
