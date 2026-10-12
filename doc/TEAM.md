# Nhóm thực hiện K4 Track 3A

Tên nhóm: 12DoiDep

Repo chung: https://github.com/gianghoang0810/K4-L3-DAY24-12DoiDep-Eval-Guard

Trưởng nhóm/người nộp: Từ Hoàng Giang – 2A202602363

Ngày lab/deadline áp dụng: 12/10/2026, hạn nộp 23:59 — Asia/Ho_Chi_Minh (UTC+07:00)

Commit dùng để chấm: trưởng nhóm ghi SHA sau lần commit nội dung cuối

Mỗi nhóm nộp một repo. Điền đủ thành viên và một mục INDIVIDUAL cho từng người.
Thêm/xóa rows để khớp nhóm thật; mẫu ba rows không ép nhóm phải có ba người.

## Thành viên và phân công

| STT | Họ và tên | MSSV | Vai trò / công việc thật | Bằng chứng |
|---|---|---|---|---|
| 1 | Từ Hoàng Giang | 2A202602363 | Trưởng nhóm: tích hợp m1–m5, môi trường, sinh 50 answers, chạy batch A/B/C, chỉnh prompt input rail, blueprint, kiểm tra và nộp | Commit f6fc6e1 và cc791c8; bốn file trong artifacts/; doc/reports/blueprint.md |
| 2 | Nguyễn Đức Thắng | 2A202602605 | Phase A: Tasks 1–4 trong src/phase_a_ragas.py và báo cáo failure_clusters | Commit d08f61a (pull request 1); doc/reports/failure_clusters.md |
| 3 | Trần Anh Quân | 2A202602598 | Phase B: Tasks 5–8 trong src/phase_b_judge.py và báo cáo bias_report | Commit cdb36d9 và 71b14f5 (pull request 3); doc/reports/bias_report.md |
| 4 | Đặng Quốc Cường | 2A202602466 | Phase C: triển khai Tasks 9a, 9b, 10, 11, 12 trong src/phase_c_guard.py; phần báo cáo latency và rà soát guard chờ kết quả chạy thật | Commit 7597ac6; mục INDIVIDUAL DangQuocCuong-2A202602466 |

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

- Phần được giao và phần thực tế đã làm: Trưởng nhóm. Đưa năm module m1–m5 từ bài Day 18 vào src/ và chỉnh cho hợp đồng Day 24: m2 dùng client.search với qdrant-client 1.9.2, m4 trả metadata.evaluator_models và chấm lại theo dòng khi có điểm không hợp lệ, m5 gọi enrich song song. Đổi model ID sang openai/gpt-4o-mini cho OpenRouter trong config.py và guardrails/config.yml, thêm .gitattributes để file JSON dùng LF. Cài môi trường đầy đủ, sinh 50 answers, chạy ba runner A, B, C, chỉnh câu chữ prompt self_check_input qua ba lần chạy Phase C, viết blueprint trừ mục latency, tổng hợp TEAM.md, duyệt và merge pull request.
- Bằng chứng: commit f6fc6e1 (m1–m5, model ID, answers_50q.json) và cc791c8 (ba file kết quả và prompt đã chỉnh); bốn file trong artifacts/; doc/reports/blueprint.md; pull request 3 và 4.
- Cách kiểm tra: `pytest tests/ -q` offline; các hàm validate trong src/report_contracts.py trên bốn artifact; `python scripts/check_lab.py --with-tests` ở lần kiểm cuối; tính lại mean, matrix, κ và percentile từ các file JSON để đối chiếu.
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

### TranAnhQuan-2A202602598

- **Phần được giao và phần thực tế đã làm:** Phụ trách Phase B, Tasks 5–8 trong `src/phase_b_judge.py`: triển khai pairwise judge theo reference, hoán đổi thứ tự và quy đổi winner/scores, tính Cohen’s κ và thống kê position/verbosity bias. Báo cáo kỹ thuật đang chờ kết quả thực nghiệm.
- **Bằng chứng:** Commit `cdb36d9` triển khai Tasks 5–8; commit `71b14f5` chuyển helper/import vào hàm và tách lời gọi API khỏi xử lý validation. Báo cáo dự kiến tại `doc/reports/bias_report.md`, chưa có số liệu thật.
- **Cách kiểm tra:** Đã rà soát tĩnh mã nguồn và xác nhận không còn `# TODO`. Chưa xác minh kết quả unit tests. Khi môi trường sẵn sàng, chạy `.\.venv-tests\Scripts\python.exe -m pytest tests/test_phase_b.py -q`; bổ sung kết quả thực tế sau.
- **Điều đã học:** Phân biệt winner A/B/tie với nhãn chất lượng 0/1; cần quy đổi lượt swap về thứ tự gốc trước khi đối chiếu. κ xét cả đồng thuận quan sát và đồng thuận do ngẫu nhiên; tỷ lệ winner dài hơn chưa chứng minh nguyên nhân thiên lệch.
- **Giới hạn/cách xử lý:** Chưa có `artifacts/judge_results.json`, nên chưa kết luận κ hoặc bias. Chờ Giang chạy batch thật; dùng kết quả JSON để hoàn thiện báo cáo, không tự điền số hoặc chạy lại nhằm tăng κ.
- **Nguồn/AI hỗ trợ nếu có:** Sử dụng Codex hỗ trợ lập kế hoạch, rà soát tiến độ và hợp đồng hàm theo tài liệu phân công, đề bài và tests được cấp. Kiểm chứng hiện tại bằng đối chiếu tĩnh; kiểm thử mock và đối chiếu kết quả thật còn chờ thực hiện.

### NguyenDucThang-2A202602605

- Phần được giao: Phase A, Tasks 1–4 trong src/phase_a_ragas.py và báo cáo doc/reports/failure_clusters.md. Các mục còn lại do thành viên tự ghi sau khi hoàn thành phần của mình.

## Cập nhật sau khi nộp

Chưa nộp; trưởng nhóm cập nhật sau khi gửi link.
