# Báo cáo nhóm — Lab 8 LogiX-VN

Tối đa 5 trang ngoài phụ lục. Số liệu lấy từ file trong `src/reports/`, không chép tay từ terminal.

## 1. Thông tin nhóm

- Tên nhóm: BoTuSieuDan
- Thành viên: xem `TEAM.md`
- Commit chốt (trùng `report/team/decision_adr.md`): __COMMIT__

## 2. Mục tiêu và cách tiếp cận

Bài toán: xử lý claim hoàn phí ship / bồi thường hàng hỏng của LogiX-VN bằng bốn "shape" có cùng chữ ký `run_claim(claim_id, *, seed, store_path, crash_at=None)`, rồi so sánh công bằng trên cùng claim × seed × giá để chọn một shape đưa vào cổng hidden. Câu hỏi chính không phải "agent nào thông minh hơn" mà là cần bao nhiêu quyền tự quyết thì vừa đủ, khi tiền luôn do code tính và host ghi sổ.

Thứ tự làm: (1) đọc `docs/business_rules.md`, `docs/contracts.md`, `swarm_reference.py` để nắm ranh giới host; (2) viết workflow trước làm baseline tất định; (3) single dùng `run_react` với tool chỉ đọc; (4) orchestrator dùng `make_handoff` + `run_readonly_investigators`; (5) chạy ma trận student, benchmark, crash suite; (6) ablation R1 và viết báo cáo.

## 3. Giải pháp

Ranh giới chung cho cả ba shape (theo `docs/architecture.md`): model (MockLLM) chỉ đọc và viết bản nháp; **code** quyết định trạng thái và số tiền; **host** (`durability.issue_refund`) kiểm approval của chính claim, trần theo đơn (trừ các lần đã hoàn), idempotency key `refund:{claim_id}` và ghi `refund_ledger` + audit. Shape luôn gửi **toàn bộ quote**, không tự trừ phần đã hoàn. `crash_at` được chuyển thẳng vào `issue_refund`; `InjectedCrash` không bị bắt (ghi event `crash`, flush trace rồi raise lại).

| Shape | Đọc gì | Quyết định gì | Gọi host |
| --- | --- | --- | --- |
| workflow | `get_claim_view` → `lookup_order` → `compute_compensation` | Các bước Python cố định: không tìm thấy → `failed`; thiếu order_id → `needs_info`; đơn không thuộc khách / không có → `escalated`; quote 0 → `denied` | Quote > 0 → `issue_refund(full quote)`; host từ chối → `denied`; xung đột số tiền → `failed`; commit → `resolved` |
| single | Vòng `run_react` (tối đa 8 step) chọn tool chỉ đọc: claim view → order → refund history → quote | Mỗi step một lần gọi MockLLM; `decide` bị giới hạn trong manifest đọc; `dispatch` từ chối write tool; sau vòng lặp code kiểm lại final với kết quả tool thật | Shape (không phải model) gọi `issue_refund`; hết step → `failed` (`step_cap`) |
| orchestrator | Parent chỉ đọc claim view; investigator đọc `lookup_order`, `lookup_refund_history`, `compute_compensation` qua handoff.v1 | Parent tính lại quote từ order view của investigator và đối chiếu với quote investigator; lệch → `failed` | Chỉ orchestrator ghi, qua host |

Handoff: `make_handoff(... facts={}, allowed_tools=[read-only])` — không đưa ticket, số ngày khách tự ghi, `parent_secret`, `raw_headers` hay row claim đầy đủ; `sanitize()` loại key ngoài allowlist và từ chối canary. Trace ghi bản handoff đã sanitize, không ghi parent context.

Trace (contract v1, `traces/<run_id>.json`, mọi nhánh): `start`, tool/observe (boolean + order view đã allowlist), `quote`, `handoff`/`investigator` (orchestrator), `act`/`loop_end` (single), `host_call`, `host_result`, `crash`, `decision`. Không bao giờ ghi ticket, secret, header, PII của đơn.

## 4. Kết quả

Bộ public: 14 claim × 5 seed (11, 22, 33, 44, 55) = 70 trial mỗi shape, SQLite riêng mỗi trial, `src/reports/benchmark.json`.

| Shape | Pass rate | pass^5 | Seed min–max | Variance | Cost/success (VND mô phỏng) | Mean / p95 steps |
| --- | --: | --: | --: | --: | --: | --: |
| workflow | 1.000 | 1.000 | 1.000–1.000 | 0.000000 | 28.83 | 6.07 / 7 |
| single | 1.000 | 1.000 | 1.000–1.000 | 0.000000 | 39.89 | 11.57 / 13 |
| orchestrator | 1.000 | 1.000 | 1.000–1.000 | 0.000000 | 56.66 | 7.00 / 8 |
| swarm (reference) | 0.986 | 0.929 | 0.929–1.000 | 0.000816 | 49.57 | 2.80 / 3 |

- Crash safety: **20/20** (`src/reports/crash.json`).
- Secret leak / approval bypass: 0 / 0 (70/70 trial mỗi shape nhóm viết có reason `ok`).
- Swarm fail đúng 1 trial `R-swarm-C-002-s55` (T02): ở seed % 5 == 0 nó tin số ngày trễ trong ticket (5) thay vì order row (2) nên trả 20000 khi đáng lẽ phải từ chối. Ba shape của nhóm không đọc số từ ticket nên pass T02 ở cả 5 seed.
- Ma trận student: `pytest -q -m student -o addopts=` → 248 passed (62 mỗi shape). Cổng host `pytest -q` → 97 passed.

Quyết định kiến trúc và lý do: xem `report/team/decision_adr.md` (chọn **workflow**). Phiếu nghiên cứu và ablation: xem `report/team/research_card.md` (R1: thêm investigator → cost/success 56.66 → 91.58, pass không đổi).

## 5. Khó khăn và cách xử lý

- `pytest -k single` khớp cả tên test `test_public_single_run_cases` của mọi shape nên lúc đầu tưởng single fail; lọc lại bằng `grep FAILED | grep -v orchestrator` thì thấy toàn bộ lỗi là của orchestrator chưa viết.
- `run_react` chỉ trả lại kết quả của tool gần nhất, nên single phải giữ trạng thái (claim, order, history, quote) trong một đối tượng agent riêng.
- `RunResult` không có field `steps` (số step nằm trong `Budget`), test step cap ban đầu báo `AttributeError`; sửa sang đọc event `loop_end` trong trace.
- Trên Windows, chạy với `$env:PYTHONUTF8 = "1"` để tránh lỗi mã hóa cp1252 khi đọc file tiếng Việt.

## 6. Tổng hợp đóng góp

Chi tiết đóng góp của từng thành viên nằm trong `TEAM.md` (bảng đóng góp) và báo cáo cá nhân `report/team/MSSV_HoTen.md`.

## 7. Hạn chế và hướng cải thiện

MockLLM không phải model sản xuất; latency là `unavailable`. Pass trên public đã bão hòa ở 1.000 nên bộ public không phân biệt được ba shape về chất lượng, chỉ về chi phí; bộ hidden 40 × 5 có thể có ca mà luồng cố định chưa lường trước (ví dụ dạng claim mới). Với model thật, single và orchestrator có thể có variance > 0 vì model tự chọn tool. Hướng cải thiện: chạy song song investigator độc lập (bonus B-parallel) và đo latency mô phỏng; kiểm thêm claim tổng hợp từ `student_drop/` khi có.

## 8. Tài liệu tham khảo

- Tài liệu lab: `README.md`, `RULES.md`, `docs/architecture.md`, `docs/business_rules.md`, `docs/contracts.md`, `docs/metrics.md`.
- Kim et al., *Towards a Science of Scaling Agent Systems*, Google Research / DeepMind / MIT, https://arxiv.org/abs/2512.08296.
