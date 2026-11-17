# Team

## Thông tin nhóm

- Tên nhóm: BoTuSieuDan
- Lớp: L3A
- Repo nhóm: `K5-L3A-DAY08-BoTuSieuDan-FromWorkflowsToAgents` (xem [SUBMISSION.md](SUBMISSION.md))
- URL repo, nhánh nộp, commit chốt: https://github.com/nguyenkhoinguyen07122005-cpu/K5-L3A-DAY08-BoTuSieuDan-FromWorkflowsToAgents, nhánh `main`, commit chốt ghi trong `report/team/decision_adr.md`

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 | Nguyễn Khôi Nguyên | 2A202603453 | nguyenkhoinguyen07122005@gmail.com | nguyenkhoinguyen07122005-cpu | Integration + 3 shape (workflow, single, orchestrator) + ablation R1 + báo cáo nhóm |
|   2 | Dương Văn Công | 2A202603468 | congvduong.work@gmail.com | dcongdev | Ranh giới agent (single + orchestrator): review vòng ReAct, handoff allowlist, test an toàn agent, review research card |
|   3 | Lê Quang Huy | 2A202603470 | lehuy20112002@gmail.com | huylq2002-art | Ledger / approval / crash + evaluation: test host trên 3 shape, chạy lại benchmark + crash, review ADR |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Nguyễn Khôi Nguyên (2A202603453) | workflow, single, orchestrator, ablation R1, benchmark/crash, ADR, research card, báo cáo nhóm | 25ec330 | `src/tests/test_group_shapes.py`: `test_ticket_delay_is_never_trusted`, `test_trace_written_and_clean_on_every_path`, `test_single_stays_under_step_cap`, `test_orchestrator_handoff_is_sanitized`, `test_crash_then_resume_pays_once`; `src/tests/test_bonus_orchestrator_x2.py`: `test_two_investigators_same_outcome_more_cost`, `test_two_investigators_still_forward_crash` |
| Dương Văn Công (2A202603468) | ranh giới agent single + orchestrator, review research card | 52cfa83 | `src/tests/test_member_cong_agent_boundary.py`: `test_react_dispatch_rejects_write_tools`, `test_handoff_refuses_write_tool_and_secret`, `test_foreign_order_escalates_without_pii`, `test_prompt_injection_ticket_cannot_force_refund` |
| Lê Quang Huy (2A202603470) | ledger / approval / crash, chạy lại benchmark + crash, review ADR | eb5304e | `src/tests/test_member_huy_durability.py`: `test_stale_approval_is_denied_by_host`, `test_order_already_refunded_hits_cap`, `test_partial_refund_pays_only_difference`, `test_retry_is_idempotent_and_reported_once` |

## Cá nhân

Mỗi thành viên có báo cáo riêng trong `report/team/`:

- `report/team/2A202603453_NguyenKhoiNguyen.md`
- `report/team/2A202603468_DuongVanCong.md`
- `report/team/2A202603470_LeQuangHuy.md`

## 2A202603453_NguyenKhoiNguyen

### Phần việc phụ trách

- Ba shape `src/lab8/shapes/workflow.py`, `single.py`, `orchestrator.py`; ablation R1 `src/bonus/orchestrator_two_investigators.py`; chạy benchmark/crash; viết ADR, research card, báo cáo nhóm; tích hợp repo.

### Đóng góp cụ thể

- Commit 25ec330; test `src/tests/test_group_shapes.py`, `src/tests/test_bonus_orchestrator_x2.py`.

### Bằng chứng

- `src/reports/benchmark.json`, `src/reports/crash.json` (20/20), `src/reports/bonus_benchmark.json`.

## 2A202603468_DuongVanCong

### Phần việc phụ trách

- Ranh giới agent của shape single và orchestrator: review vòng ReAct (step cap, tool chỉ đọc), handoff.v1 allowlist; review research card R1.

### Đóng góp cụ thể

- Commit 52cfa83; test `src/tests/test_member_cong_agent_boundary.py`.

### Bằng chứng

- Kết quả `pytest -q` của test trên; chi tiết trong `report/team/2A202603468_DuongVanCong.md`.

## 2A202603470_LeQuangHuy

### Phần việc phụ trách

- Ranh giới host: approval, trần theo đơn, idempotency, crash; chạy lại benchmark và crash suite; review ADR.

### Đóng góp cụ thể

- Commit eb5304e; test `src/tests/test_member_huy_durability.py`.

### Bằng chứng

- `src/reports/crash.json` (20/20), kết quả `pytest -q`; chi tiết trong `report/team/2A202603470_LeQuangHuy.md`.
