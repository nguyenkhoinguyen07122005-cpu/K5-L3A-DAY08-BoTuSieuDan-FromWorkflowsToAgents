# Báo cáo cá nhân — 2A202603470_LeQuangHuy

## 1. Thông tin thành viên

- Họ tên: Lê Quang Huy
- MSSV: 2A202603470
- Email: lehuy20112002@gmail.com
- GitHub: huylq2002-art

## 2. Phần việc phụ trách

- Ranh giới host cho cả 3 shape nhóm viết: approval của claim, trần theo đơn (trừ phần đã hoàn), idempotency `refund:{claim_id}`, crash/resume.
- Chạy lại benchmark và crash suite trên máy để kiểm tra kết quả tái lập được; review `report/team/decision_adr.md`.

## 3. Đóng góp cụ thể

- Commit: eb5304e
- Test thêm: `src/tests/test_member_huy_durability.py` (12 case, mỗi test chạy trên workflow, single, orchestrator):
  - `test_stale_approval_is_denied_by_host`: T07, approval cũ thì host từ chối, có audit `policy_denied`, proposal vẫn là full quote 35000, ledger rỗng.
  - `test_order_already_refunded_hits_cap`: T13, đơn đã hoàn đủ thì bị `denied` theo trần, không ghi thêm.
  - `test_partial_refund_pays_only_difference`: T14, shape gửi full quote 150000, host chỉ ghi 120000.
  - `test_retry_is_idempotent_and_reported_once`: chạy lại cùng claim vẫn chỉ có một dòng ledger 35000.
- Quyết định kỹ thuật đã thống nhất với nhóm: shape **không** tự trừ phần đã hoàn mà luôn gửi full quote, để host là nơi duy nhất áp trần và approval; không bắt `InjectedCrash` trong shape.

## 4. Bằng chứng

- Lệnh: `python -m pytest -q tests/test_member_huy_durability.py` (trong `src/`), kết quả 12 passed.
- `python -m lab8.crash_test --count 20 --out reports/crash.json` cho `crash_safety 20/20`.
- `python -m lab8.benchmark ... --out reports/benchmark.json`: workflow pass 1.000, pass^5 1.000, cost/success 28.83 VND, khớp số trong ADR.

## 5. Công cụ AI đã dùng

- Công cụ: Claude (trợ lý AI), dùng chung trong nhóm.
- Dùng vào việc: giải thích `durability.issue_refund` và các luật nghiệp vụ, gợi ý khung test và bản nháp báo cáo này.
- Tự kiểm tra: đối chiếu từng test với `docs/business_rules.md` và nhãn trong `data/public_claims.jsonl`; tự chạy test, benchmark, crash trên máy.

## 6. Tự đánh giá

- Hiểu idempotency key và vì sao crash sau khi ghi tiền nhưng trước checkpoint không được trả lần hai.
- Hiểu vì sao trần theo đơn đặt ở host chứ không ở shape.
- Hạn chế: chưa thử crash ở môi trường nhiều tiến trình ghi cùng lúc.
