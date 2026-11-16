# Team

## Thông tin nhóm

- Tên nhóm: BoTuSieuDan
- Lớp: L3A
- Repo nhóm: `K5-L3A-DAY08-TenNhom-FromWorkflowsToAgents` (xem [SUBMISSION.md](SUBMISSION.md))
- URL repo, nhánh nộp, commit chốt: https://github.com/nguyenkhoinguyen07122005-cpu/K5-L3A-DAY08-BoTuSieuDan-FromWorkflowsToAgents, nhánh `main`, commit chốt ghi trong `report/team/decision_adr.md`

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 | Nguyễn Khôi Nguyên | 2A202603453 | nguyenkhoinguyen07122005@gmail.com | nguyenkhoinguyen07122005-cpu | Integration + workflow + ledger/crash + evaluation/report |
|   2 | <Họ tên thành viên 2> | <MSSV> | nguyenkhoinguyen07122005@gmail.com | nguyenkhoinguyen07122005-cpu | <vai trò> |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Nguyễn Khôi Nguyên (2A202603453) | workflow, single, orchestrator, R1 ablation, benchmark/crash, report | __COMMIT__ | `tests/test_group_shapes.py` (ticket delay không được tin, trace sạch mọi nhánh, step cap, handoff sanitize, crash rồi resume trả 1 lần), `tests/test_bonus_orchestrator_x2.py` |

## Cá nhân

Mỗi thành viên đồng thời tạo một báo cáo riêng trong `report/` theo dạng:

```text
report/team/MSSV_HoTen.md
```

Ví dụ: `report/team/20241234_NguyenVanAn.md`.

Sao chép mục dưới đây cho từng thành viên.

## 2A202603453_NguyenKhoiNguyen

### Phần việc phụ trách

- Ba shape `src/lab8/shapes/workflow.py`, `single.py`, `orchestrator.py`; ablation R1 `src/bonus/orchestrator_two_investigators.py`; chạy benchmark/crash; viết ADR, research card, báo cáo nhóm.

### Đóng góp cụ thể

- Commit __COMMIT__; test `tests/test_group_shapes.py`, `tests/test_bonus_orchestrator_x2.py`.

### Bằng chứng

- `src/reports/benchmark.json`, `src/reports/crash.json` (20/20), `src/reports/bonus_benchmark.json`; chi tiết trong `report/team/2A202603453_NguyenKhoiNguyen.md`.
