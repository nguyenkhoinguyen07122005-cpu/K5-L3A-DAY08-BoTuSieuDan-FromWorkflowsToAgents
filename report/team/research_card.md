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
| CÂU | R1 / R2 / R3 / R4 | R1 · Scaling |
| SOURCE | Paper/blog, năm, URL, loại bằng chứng (benchmark, production report, blog kinh nghiệm) | Kim et al., Google Research / Google DeepMind / MIT, *Towards a Science of Scaling Agent Systems*, 2025–2026, https://arxiv.org/abs/2512.08296 (blog: https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/). Loại bằng chứng: benchmark có kiểm soát (180 cấu hình agent) |
| CLAIM | Một kết luận gốc của nguồn. Không viết rộng hơn phạm vi paper | Phối hợp nhiều agent cải thiện rõ trên task song song hóa được nhưng có thể làm giảm hiệu năng trên task tuần tự; cách tiếp cận "thêm agent" thường chạm trần |
| BOUNDARY | Model / dataset / setup giới hạn kết luận đó | Kết luận đo trên các agentic benchmark của paper, với model thật thuộc 3 họ (OpenAI, Google, Anthropic) và ngân sách token cố định; không đo trên tác vụ có host ghi tiền như claim hoàn tiền, không dùng MockLLM |
| HYPOTHESIS | Nếu đổi shape A→B (hoặc bật/tắt một thành phần), metric X sẽ đổi theo hướng nào, khoảng bao nhiêu | Claim LogiX là chuỗi tuần tự (đọc claim → đọc đơn → quote → host). Tăng số investigator của orchestrator từ 1 lên 2: pass rate và pass^5 giữ nguyên 1.000 (không tăng), cost/success tăng khoảng 40–70%, mean steps tăng 1–2 |
| METHOD | Biến giữ cố định: model, tools, seed, corpus, giá, evaluator. Biến duy nhất được thay đổi: … | Giữ cố định: MockLLM seeded, tool manifest, seed 11/22/33/44/55, 14 claim public, `config/pricing.yaml`, evaluator `lab8.metrics.trial_pass`, SQLite riêng mỗi trial. Biến duy nhất được thay đổi: số investigator read-only (1 → 2; investigator thứ 2 đọc lại đơn + policy và quote độc lập, lệch thì `failed`). Code: `src/bonus/orchestrator_two_investigators.py` |
| FAILURE / METRIC | Một lỗi dự kiến và cách đo nó: pass rate, pass^5, variance, cost/success, leak, bypass | Lỗi dự kiến: investigator thêm vào làm tăng chi phí mà không tăng chất lượng; nếu hai investigator lệch nhau thì shape trả `failed`, làm giảm pass. Đo: pass rate, pass^5, variance, cost/success, mean steps; safety (leak, bypass) phải giữ 0 |

## Kết quả ablation

- Lệnh đã chạy (copy nguyên văn): `python -m lab8.benchmark --shapes orchestrator,orchestrator_x2 --extra-shape orchestrator_x2=bonus.orchestrator_two_investigators:run_claim --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/bonus_benchmark.json` (chạy trong `src/`)
- Run id / file report: `src/reports/bonus_benchmark.json`; ví dụ `R-orchestrator-C-003-s11` so với `R-orchestrator_x2-C-003-s11`
- Kết quả so với hypothesis (đúng / sai / không kết luận được), kèm số liệu: **Đúng.** orchestrator: pass 1.000, pass^5 1.000, variance 0, cost/success 56.66 VND, mean steps 7.00. orchestrator_x2: pass 1.000, pass^5 1.000, variance 0, cost/success 91.58 VND (+61.6%), mean steps 8.71 (+1.71). Thêm investigator không tăng chất lượng trên task tuần tự, chỉ tăng chi phí, khớp với claim của paper trong phạm vi lab này.
- Giới hạn: MockLLM không phải model sản xuất, latency là `unavailable`. Pass đã bão hòa ở 1.000 nên ablation không đo được phần "giảm hiệu năng" của claim; chỉ đo được phần chi phí. Chỉ có 14 claim public.

Muốn đi xa hơn một ablation để lấy điểm cộng thì xem [docs/bonus_topics.md](../../docs/bonus_topics.md).
