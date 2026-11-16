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
|   2 | Dương Văn Công | 2A202603468 | __EMAIL_CONG__ | __GITHUB_CONG__ | Ranh giới agent (single + orchestrator): review vòng ReAct, handoff allowlist, test an toàn agent, review research card |
|   3 | Lê Quang Huy | 2A202603470 | lehuy20112002@gmail.com | huylq2002-art | Ledger / approval / crash + evaluation: test host trên 3 shape, chạy lại benchmark + crash, review ADR |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Nguyễn Khôi Nguyên (2A202603453) | workflow, single, orchestrator, ablation R1, benchmark/crash, ADR, research card, báo cáo nhóm | 25ec330 | `src/tests/test_group_shapes.py` (ticket delay không được tin, trace sạch mọi nhánh, step cap, handoff sanitize, crash rồi resume trả 1 lần), `src/tests/test_bonus_orchestrator_x2.py` |
| Dương Văn Công (2A202603468) | ranh giới agent single + orchestrator, review research card | __HASH_CONG__ | `src/tests/test_member_cong_agent_boundary.py` (dispatch từ chối write tool, handoff từ chối write tool + lọc secret, đơn của người khác → escalated không lộ PII, ticket prompt injection không ép được refund) |
| Lê Quang Huy (2A202603470) | ledger / approval / crash, chạy lại benchmark + crash, review ADR | eb5304e | `src/tests/test_member_huy_durability.py` (approval cũ → host từ chối + policy_denied, trần theo đơn T13, chỉ trả phần chênh T14, retry idempotent một dòng ledger) |

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

- Commit __HASH_CONG__; test `src/tests/test_member_cong_agent_boundary.py`.

### Bằng chứng

- Kết quả `pytest -q` của test trên; chi tiết trong `report/team/2A202603468_DuongVanCong.md`.

## 2A202603470_LeQuangHuy

### Phần việc phụ trách

- Ranh giới host: approval, trần theo đơn, idempotency, crash; chạy lại benchmark và crash suite; review ADR.

### Đóng góp cụ thể

- Commit eb5304e; test `src/tests/test_member_huy_durability.py`.

### Bằng chứng

- `src/reports/crash.json` (20/20), kết quả `pytest -q`; chi tiết trong `report/team/2A202603470_LeQuangHuy.md`.
