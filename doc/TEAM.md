# Nhóm thực hiện K4 Track 3A

Tên nhóm: 12DoiDep

Repo chung: https://github.com/gianghoang0810/K4-L3-DAY24-12DoiDep-Eval-Guard

Trưởng nhóm/người nộp: Từ Hoàng Giang – 2A202602363

Ngày lab/deadline áp dụng: 12/10/2026, hạn nộp 23:59 — Asia/Ho_Chi_Minh (UTC+07:00)

Commit dùng để chấm: 80a7a45 (commit nội dung cuối; commit kế tiếp chỉ ghi dòng này)

Mỗi nhóm nộp một repo. Điền đủ thành viên và một mục INDIVIDUAL cho từng người.
Thêm/xóa rows để khớp nhóm thật; mẫu ba rows không ép nhóm phải có ba người.

## Thành viên và phân công

| STT | Họ và tên | MSSV | Vai trò / công việc thật | Bằng chứng |
|---|---|---|---|---|
| 1 | Từ Hoàng Giang | 2A202602363 | Trưởng nhóm: tích hợp m1–m5, môi trường, sinh 50 answers, chạy batch A/B/C, chỉnh prompt input rail, blueprint, kiểm tra và nộp | Commit f6fc6e1, cc791c8 và 5c4a3d8; bốn file trong artifacts/; doc/reports/blueprint.md; merge pull request 3, 4, 5, 6, 7 |
| 2 | Nguyễn Đức Thắng | 2A202602605 | Phase A: Tasks 1–4 trong src/phase_a_ragas.py và báo cáo failure_clusters | Commit d08f61a (pull request 1) và 98a4465 (pull request 5); doc/reports/failure_clusters.md |
| 3 | Trần Anh Quân | 2A202602598 | Phase B: Tasks 5–8 trong src/phase_b_judge.py và báo cáo bias_report | Commit cdb36d9, 71b14f5 (pull request 3) và ee3ec08 (pull request 7); doc/reports/bias_report.md |
| 4 | Đặng Quốc Cường | 2A202602466 | Phase C: triển khai Tasks 9a, 9b, 10, 11, 12 trong src/phase_c_guard.py; mục latency và phân tích bottleneck trong blueprint | Commit 7597ac6 (pull request 4) và 4bb0ee0 (pull request 6); mục latency trong doc/reports/blueprint.md |

Người tích hợp/chạy batch/nộp link: Từ Hoàng Giang, chạy setup_answers và ba runner trên máy của mình, bằng chứng là bốn file trong artifacts/ và commit cc791c8.

## Môi trường và chuẩn bị trước lớp

OS/Python/compiler: Windows 10 Home Single Language 10.0.19045 64-bit, RAM 7,9 GB; Python 3.11.9; Microsoft C++ Build Tools 2022 với MSVC 14.44.35207 và Windows SDK 10.0.22621.0.
Virtualenv/dependencies/imports: tạo `.venv` bằng `py -3.11 -m venv .venv`, cài bằng `pip install -r requirements/requirements.txt -c requirements/constraints-core.txt`; annoy 1.17.3 build được bằng MSVC và `pip check` in `No broken requirements found.`. Tuy vậy `import ragas` lỗi `ImportError: pyarrow requires NumPy 2.0 or newer, found 1.26.4` vì pip chọn pyarrow 26.0.0 trong khi constraints khoá numpy 1.26.4. Thử pyarrow 17.0.0 thì torch 2.14.1 lỗi `WinError 1114` khi pyarrow được nạp trước. Lệnh bổ sung nhóm đã dùng: `pip install "pyarrow==25.0.1" -c requirements/constraints-core.txt`; sau đó `pip check` sạch và lệnh `import ragas, nemoguardrails, presidio_analyzer, spacy` in `imports OK`. Các file trong requirements/ không bị sửa, nên cài lại trên máy khác cần lệnh bổ sung này. `python -m spacy download en_core_web_lg` cài bản 3.8.0. Ba thành viên còn lại chỉ dùng `.venv-tests` theo requirements-test.lock.txt.
Models/Qdrant/50 answers đã chuẩn bị: BAAI/bge-m3 và BAAI/bge-reranker-v2-m3 tải về cache Hugging Face, khoảng 6,5 GB vì transformers 5.19.0 tải thêm bản safetensors của bge-m3. Lần nạp hai model đầu tiên lỗi `The paging file is too small for this operation to complete (os error 1455)` do thiếu bộ nhớ; đóng bớt ứng dụng thì nạp được. Qdrant server 1.9.7 chạy bằng `docker compose -f infra/docker-compose.yml up -d`; qdrant-client 1.9.2 không có query_points nên m2 dùng client.search. `scripts/setup_answers.py --live` chạy một lần qua OpenRouter: 26 tài liệu, 102 chunk, 50 câu trả lời đều có generation_mode llm bằng openai/gpt-4o-mini, 0 extractive_fallback; 11 câu trả lời là "Không tìm thấy.". Hai file PDF scan trong data/ (BCTC.pdf và Nghi_dinh_so_13-2023_ve_bao_ve_du_lieu_ca_nhan_508ee.pdf) không có lớp text nên không được lập chỉ mục. NeMo tự tải model embedding Qdrant/all-MiniLM-L6-v2-onnx cho chỉ mục của nó. Năm module m1–m5 lấy từ repo bài Day 18 của Từ Hoàng Giang và được chỉnh adapter cho hợp đồng Day 24.
Nếu cài full NeMo chưa thành công, ghi lỗi thực tế; không dùng unit test pass
hoặc pip resolve để khẳng định runtime đã chạy.

## INDIVIDUAL

Sao chép mẫu cho mỗi thành viên; tiêu đề họ tên không dấu-MSSV.

### TuHoangGiang-2A202602363

- Phần được giao và phần thực tế đã làm: Trưởng nhóm. Đưa năm module m1–m5 từ bài Day 18 vào src/ và chỉnh cho hợp đồng Day 24: m2 dùng client.search với qdrant-client 1.9.2, m4 trả metadata.evaluator_models và chấm lại theo dòng khi có điểm không hợp lệ, m5 gọi enrich song song. Đổi model ID sang openai/gpt-4o-mini cho OpenRouter trong config.py và guardrails/config.yml, thêm .gitattributes để file JSON dùng LF. Cài môi trường đầy đủ, sinh 50 answers, chạy ba runner A, B, C, chỉnh câu chữ prompt self_check_input qua ba lần chạy Phase C, viết blueprint trừ mục latency, tổng hợp TEAM.md, duyệt và merge pull request. Ghi thay mục INDIVIDUAL của Thắng và dòng cập nhật cuối mục của Cường, Quân, dựa trên commit, mã nguồn và báo cáo của từng bạn.
- Bằng chứng: commit f6fc6e1 (m1–m5, model ID, answers_50q.json) và cc791c8 (ba file kết quả và prompt đã chỉnh); commit 5c4a3d8 (blueprint phần trưởng nhóm và TEAM.md); bốn file trong artifacts/; doc/reports/blueprint.md; merge pull request 3, 4, 5, 6, 7.
- Cách kiểm tra: `pytest tests/ -q` offline; các hàm validate trong src/report_contracts.py trên bốn artifact; `python scripts/check_lab.py --with-tests` ở lần kiểm cuối; tính lại mean, matrix, κ và percentile từ các file JSON để đối chiếu. Lần kiểm cuối: check_lab.py --with-tests cho 0 failed checks; pytest 86 passed.
- Điều đã học: constraints không khoá hết dependency nên một gói gián tiếp (pyarrow) vẫn có thể làm hỏng môi trường dù pip check sạch; kết quả của input rail thay đổi mạnh theo câu chữ prompt, nên phải đo cả attack lẫn câu sạch sau mỗi lần sửa; điểm faithfulness thấp cần được đọc cùng nội dung câu trả lời vì câu từ chối cũng bị chấm 0.
- Giới hạn/cách xử lý: prompt self_check_input được chỉnh sau khi xem kết quả trên chính 28 input của bộ kiểm, nên kết quả 18/20 và 8/8 chưa nói gì về input ngoài bộ; chỉ lần chạy thứ ba có trong repo. P95 của hai tầng input là 1621.1 ms, vượt mức 500 ms. Pipeline từ chối trả lời 11/50 câu. Máy 7,9 GB RAM phải đóng bớt ứng dụng mới nạp được hai model cùng lúc.
- Nguồn/AI hỗ trợ nếu có: Claude Code hỗ trợ lập kế hoạch, chỉnh adapter m1–m5, chạy các lệnh cài đặt và batch, và soạn nháp blueprint cùng mục này; kiểm chứng bằng pytest offline, scripts/check_lab.py và đối chiếu số trong các file JSON. Mã m1–m5 lấy từ repo bài Day 18 của chính tôi.

### DangQuocCuong-2A202602466

- Phần được giao và phần thực tế đã làm: Tôi phụ trách Phase C, gồm Tasks 9a, 9b, 10, 11, 12 trong `src/phase_c_guard.py`, mục latency của `doc/reports/blueprint.md` và rà soát kết quả guard. Tôi đã triển khai `pii_scan` để lọc và ẩn danh CCCD/CMND, điện thoại Việt Nam, email; `check_input_rail` và `check_output_rail` để lấy quyết định từ trạng thái `stop` của rail, giữ nguyên câu trả lời an toàn hoặc thay thế nội dung bị chặn; `run_adversarial_suite` để kiểm tra input theo thứ tự Presidio → NeMo và ghi nhận `blocked_by`; `measure_p95_latency` để đo hai tầng input tuần tự sau hai lượt warm-up. Phần báo cáo latency và rà soát kết quả chạy thật còn chờ dữ liệu của nhóm.
- Bằng chứng: Commit `7597ac6596dad164c24ec5faaf713a5b06532ca9` (`Phase C: Tasks 9a-12`), tác giả `cuongdqhe187188-glitch`, ngày 12/10/2026, chứa thay đổi trong `src/phase_c_guard.py`. Nhánh cá nhân là `dangquoccuong_2A202602466`; báo cáo đóng góp được ghi tại mục INDIVIDUAL này. Chưa có `artifacts/guard_results.json` trong bản repo hiện tại để đối chiếu số đo latency hoặc kết quả guard thực tế.
- Cách kiểm tra: Chạy `.\.venv\Scripts\python.exe -m pytest tests/test_phase_c.py -q` trên Python 3.12.10, kết quả `12 passed`. Bộ test dùng engine giả, kiểm tra phát hiện/ẩn danh PII, quyết định input/output rail, batch 28 input gồm 20 attacks và 8 clean controls, cùng số lượt warm-up và mẫu latency. Lệnh `Select-String -Path src\phase_c_guard.py -Pattern '# TODO'` không trả kết quả. Các kết quả này xác nhận logic với mock; số đo và tỷ lệ đạt khi chạy thật cần lấy từ artifacts của nhóm.
- Điều đã học: Tôi hiểu cách kết hợp bộ phát hiện PII với input/output rails trong pipeline RAG và cách dùng engine được truyền vào để kiểm thử độc lập với API. Quyết định chặn phải dựa vào rail thay vì dò câu từ chối; nhãn `expected` chỉ dùng để so sánh kết quả. Khi đo latency cần tái sử dụng engine, loại warm-up khỏi mẫu, dùng `time.perf_counter()` và tính percentile từ các mẫu thô; P95 tổng được tính từ tổng thời gian từng mẫu qua `summarize_latency`.
- Giới hạn/cách xử lý: Bản repo hiện tại chưa có artifacts chạy thật nên chưa kết luận mức chặn attacks, false positives, độ đúng của bốn output cases hoặc việc đạt ngân sách 500 ms. Phép đo Task 12 chỉ bao gồm Presidio và NeMo input chạy đầy đủ hai tầng; RAG, output rail và toàn bộ request còn chưa đo. Sau khi Giang cung cấp `guard_results.json`, tôi sẽ đối chiếu các dòng không đạt, `blocked_by`, P50/P95/P99 và `latency_budget_ok`, rồi điền mục latency trong blueprint. Lần kiểm tra cá nhân dùng Python 3.12.10; môi trường Python 3.11 theo yêu cầu lab và hành vi Presidio/NeMo thật cần được nhóm xác nhận khi chạy thực nghiệm.
- Nguồn/AI hỗ trợ nếu có: Tài liệu phân công `Goi-C_DangQuocCuong_PhaseC.md`, yêu cầu lab và bộ test trong repo là nguồn đối chiếu phạm vi công việc. ChatGPT/Codex hỗ trợ tổng hợp, diễn đạt báo cáo này và chạy lại kiểm tra; nội dung được đối chiếu với mã nguồn, tác giả/SHA commit và đầu ra pytest. Số liệu thực nghiệm phải lấy từ artifacts chạy thật của nhóm.
- Cập nhật sau khi có kết quả chạy thật: các câu "chưa có artifacts" ở trên mô tả thời điểm trước lần chạy thật. Sau khi nhóm có `artifacts/guard_results.json` (commit cc791c8), Cường điền mục latency của `doc/reports/blueprint.md` ở commit 4bb0ee0 (pull request 6): 20 mẫu sau 2 lượt warm-up, P50/P95/P99 của hai tầng input là 763.6 / 1621.1 / 1653.8 ms, `latency_budget_ok` False so với mức 500 ms; bottleneck là NeMo input (P95 1605.5 ms) vì mỗi lần kiểm tra gọi openai/gpt-4o-mini qua OpenRouter, còn Presidio chạy cục bộ (P95 20.8 ms). RAG, NeMo output và full guard vẫn ghi chưa đo. Lần chạy nộp chặn 18/20 attacks, cho qua 8/8 câu sạch và đúng 4/4 output case. `pytest tests/test_phase_c.py -q` cho `12 passed` khi trưởng nhóm chạy lại trên `.venv-tests` (Python 3.11.9).

### TranAnhQuan-2A202602598

- **Phần được giao và phần thực tế đã làm:** Phụ trách Phase B, Tasks 5–8 trong `src/phase_b_judge.py`: triển khai pairwise judge theo reference, hoán đổi thứ tự và quy đổi winner/scores, tính Cohen’s κ và thống kê position/verbosity bias. Báo cáo kỹ thuật đang chờ kết quả thực nghiệm.
- **Bằng chứng:** Commit `cdb36d9` triển khai Tasks 5–8; commit `71b14f5` chuyển helper/import vào hàm và tách lời gọi API khỏi xử lý validation. Báo cáo dự kiến tại `doc/reports/bias_report.md`, chưa có số liệu thật.
- **Cách kiểm tra:** Đã rà soát tĩnh mã nguồn và xác nhận không còn `# TODO`. Chưa xác minh kết quả unit tests. Khi môi trường sẵn sàng, chạy `.\.venv-tests\Scripts\python.exe -m pytest tests/test_phase_b.py -q`; bổ sung kết quả thực tế sau.
- **Điều đã học:** Phân biệt winner A/B/tie với nhãn chất lượng 0/1; cần quy đổi lượt swap về thứ tự gốc trước khi đối chiếu. κ xét cả đồng thuận quan sát và đồng thuận do ngẫu nhiên; tỷ lệ winner dài hơn chưa chứng minh nguyên nhân thiên lệch.
- **Giới hạn/cách xử lý:** Chưa có `artifacts/judge_results.json`, nên chưa kết luận κ hoặc bias. Chờ Giang chạy batch thật; dùng kết quả JSON để hoàn thiện báo cáo, không tự điền số hoặc chạy lại nhằm tăng κ.
- **Nguồn/AI hỗ trợ nếu có:** Sử dụng Codex hỗ trợ lập kế hoạch, rà soát tiến độ và hợp đồng hàm theo tài liệu phân công, đề bài và tests được cấp. Kiểm chứng hiện tại bằng đối chiếu tĩnh; kiểm thử mock và đối chiếu kết quả thật còn chờ thực hiện.
- **Cập nhật sau khi có kết quả chạy thật:** các câu "chưa có số liệu thật" ở trên mô tả thời điểm trước lần chạy thật. Sau khi nhóm có `artifacts/judge_results.json` (commit cc791c8), Quân viết xong `doc/reports/bias_report.md` ở commit ee3ec08 (pull request 7) và gỡ file phân công nội bộ khỏi repo. Kết quả: Cohen's κ = 1.0 trên 10 nhãn (p_o = 1.0, p_e = 0.52); position bias 1/5 cặp, ở id 41; verbosity win rate 1.0 trên 4 cặp phân thắng bại. Báo cáo ghi rõ mẫu nhỏ và chưa chứng minh judge ưu tiên độ dài, đồng thời ghi Codex hỗ trợ soạn và kiểm tra. `pytest tests/test_phase_b.py -q` cho `8 passed` khi trưởng nhóm chạy lại trên `.venv-tests`.

### NguyenDucThang-2A202602605

- Phần được giao và phần thực tế đã làm: Phase A, Tasks 1–4 trong `src/phase_a_ragas.py` và báo cáo `doc/reports/failure_clusters.md`. Đã triển khai `group_by_distribution` (chia 50 câu thành factual, multi_hop, adversarial và báo lỗi khi gặp distribution lạ); `run_ragas_50q` (gọi `evaluate_ragas` của m4, kiểm từng điểm phải hữu hạn và nằm trong khoảng 0 đến 1, lấy danh sách evaluator model từ metadata của m4); `bottom_10` (sắp theo avg_score rồi question_id, ghi worst_metric, chẩn đoán và gợi ý sửa theo distribution, đánh dấu câu trả lời "Không tìm thấy"); `cluster_analysis` (matrix worst_metric × distribution, nhóm yếu nhất tính theo mean thấp nhất). Sau khi nhóm có `artifacts/ragas_50q.json`, viết báo cáo failure_clusters gồm bảng điểm theo distribution, bottom 10, bốn nhóm câu đại diện, matrix, ba đề xuất sửa và phần bẫy adversarial.
- Bằng chứng: commit d08f61a (`Phase A: Tasks 1-4`, pull request 1) sửa `src/phase_a_ragas.py`; commit 98a4465 (pull request 5) viết `doc/reports/failure_clusters.md`; nhánh cá nhân `nguyenducthang_2A202602605`.
- Cách kiểm tra: `pytest tests/test_phase_a.py -q` cho `6 passed` khi trưởng nhóm chạy lại trên `.venv-tests` ngày 12/10/2026; `src/phase_a_ragas.py` không còn `# TODO`; `scripts/check_lab.py` báo PASS cho `phase_a_ragas.py`, `Phase A artifact` và `doc/reports/failure_clusters.md`. Ba mean 0.894, 0.642, 0.695, matrix và danh sách bottom 10 trong báo cáo khớp `artifacts/ragas_50q.json`.
- Điều đã học (rút từ báo cáo của Thắng): cả 10 câu bottom có worst_metric là faithfulness, nhưng 9/10 câu là lời từ chối "Không tìm thấy." chứ không phải model bịa, nên điểm RAGAS phải đọc cùng answer và contexts. Multi_hop thấp nhất (0.642) vì bước sinh câu trả lời từ chối khi phải ghép hoặc tính, trong khi context_precision của nhóm này là 0.958, ngang factual 0.962. Matrix chỉ đếm metric thấp nhất của từng câu, không phải số câu lỗi.
- Giới hạn/cách xử lý: 50 câu, một lần chạy, adversarial chỉ 10 câu nên chưa ước lượng được độ dao động. Evaluator cùng model với generation (openai/gpt-4o-mini) và chấm lời từ chối không nhất quán: id 48 được faithfulness 1.00, mười câu từ chối còn lại 0.00. Cột diagnosis và suggested_fix là heuristic sinh từ code; kết luận nằm ở phần phân tích bằng chữ. Ba đề xuất sửa prompt, chunking và số context chưa được chạy lại để kiểm chứng.
- Nguồn/AI hỗ trợ nếu có: mục này do trưởng nhóm Từ Hoàng Giang ghi thay, có Claude Code hỗ trợ soạn, dựa trên commit, mã nguồn và báo cáo của Thắng. Khi làm phần của mình, Thắng dùng Antigravity IDE và Codex hỗ trợ.

## Cập nhật sau khi nộp

Chưa nộp; trưởng nhóm cập nhật sau khi gửi link.
