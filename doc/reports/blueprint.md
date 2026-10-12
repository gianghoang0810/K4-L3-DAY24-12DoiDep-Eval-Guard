# Blueprint CI/CD cho RAG và guardrails

Nhóm: 12DoiDep

Người phụ trách: Từ Hoàng Giang – 2A202602363 (Task 13); Đặng Quốc Cường – 2A202602466 (mục latency)

Đây là **đề xuất CI/CD**, không phải workflow đã được triển khai/chạy.
Nguồn kết quả: artifacts/ragas_50q.json, judge_results.json, guard_results.json.
Run timestamp: 2026-10-12T10:14:38.453475+00:00; guard models: openai/gpt-4o-mini.
Models generation/evaluator/judge từ A/B: generation openai/gpt-4o-mini và evaluator openai/gpt-4o-mini, openai/text-embedding-3-small (artifacts/ragas_50q.json); judge openai/gpt-4o-mini (artifacts/judge_results.json).

## Kiến trúc và failure action

Input → Presidio → NeMo input → RAG → NeMo output → response.
Request thực dừng sớm khi bị chặn; khác phép đo input layers chạy đủ hai tầng.

Đề xuất luồng xử lý: câu hỏi đi qua Presidio để phát hiện CCCD/CMND, số điện thoại Việt Nam và email; nếu có PII thì yêu cầu dừng tại đây và người dùng nhận câu từ chối chuẩn. Câu hỏi không có PII đi tiếp qua NeMo input rail (self check input) để chặn yêu cầu lạc đề, jailbreak, giả danh quyền hạn và trích xuất dữ liệu nội bộ. Yêu cầu được phép mới vào RAG (hybrid search, rerank, sinh câu trả lời). Câu trả lời đi qua NeMo output rail (self check output) trước khi trả về; câu trả lời bị gắn cờ được thay bằng câu từ chối.

Failure action đề xuất: khi một tầng guard lỗi hoặc quá thời gian, hệ thống từ chối trả lời thay vì bỏ qua tầng đó. Log chỉ ghi id yêu cầu, tầng ra quyết định, loại entity và độ trễ; nội dung PII được ghi ở dạng đã ẩn. API key nằm trong biến môi trường hoặc `.env` ngoài Git. Khi sinh câu trả lời lỗi, pipeline hiện rơi về trích nguyên văn context đầu tiên và ghi `generation_mode = extractive_fallback`; trong vận hành thật nên thay bằng thông báo tạm thời không trả lời được.

## Phạm vi và số đo latency

Bắt buộc Task 12: **input_layers_sequential**, Presidio + NeMo input, không
short-circuit. Engines tạo trước đo; có hai warm-up thật không tính vào mẫu.
Percentile nội suy tuyến tính từ raw samples; tổng từ tổng từng sample.

Số mẫu timed: {{n_samples}}; warm-up: {{warmup_runs}};
phương pháp: {{percentile_method}}.

| Layer / scope | P50 ms | P95 ms | P99 ms | Tình trạng |
|---|---|---|---|---|
| Presidio | {{p50}} | {{p95}} | {{p99}} | {{Kết quả đo Task 12}} |
| NeMo input | {{p50}} | {{p95}} | {{p99}} | {{Kết quả đo Task 12}} |
| Input guard sequential | {{p50}} | {{p95}} | {{p99}} | {{Kết quả đo Task 12}} |
| RAG | chưa đo | chưa đo | chưa đo | Ngoài phạm vi bắt buộc |
| NeMo output | chưa đo | chưa đo | chưa đo | Ngoài phạm vi bắt buộc |
| Full guard / short-circuit request | chưa đo | chưa đo | chưa đo | Không suy từ input_guard_ms |

Input-layer budget target: 500ms; kết quả thực tế/budget_ok: {{giá trị và bool}}.
{{Nêu bottleneck nếu vượt target. Nếu chưa đo các phần ngoài phạm vi, giữ “chưa đo”
và nêu kế hoạch lấy mẫu/warm-up/timestamps; nếu đã đo thì thay bằng số và phương pháp.}}

## CI gates đề xuất

- RAGAS faithfulness ≥0.75, avg_score ≥0.65 trên 50 câu.
- Attacks ≥18/20 cho CI đề xuất; clean 8/8 allowed; output cases đúng.
- Full guard P95 <500ms chỉ áp dụng sau khi thực sự đo full guard.

Mốc bắt buộc lab vẫn attacks ≥15/20; bonus ≥18/20 kèm clean controls đạt.
Đề xuất một job CI chạy theo lịch hoặc khi đổi corpus, prompt hay cấu hình guard: chạy `python scripts/run_experiments.py --phase a --live`, `--phase b --live`, `--phase c --live --latency-samples 20`, sau đó `python scripts/check_lab.py --with-tests`. Checker hiện trả mã khác 0 khi attacks dưới 15/20, khi có clean input bị chặn hoặc khi output case sai. Các ngưỡng RAGAS faithfulness, avg_score và attacks 18/20 là đề xuất: runner hiện chưa kiểm các ngưỡng này, nên cần thêm một script đọc ba file JSON và trả mã khác 0 khi thấp hơn ngưỡng; các biến MIN_* chỉ có tác dụng khi script đó tồn tại. Workflow này chưa được triển khai trong repo.

## Monitoring và cải tiến

Đề xuất theo dõi theo từng lô đánh giá định kỳ: bốn metric RAGAS theo distribution, số câu trả lời từ chối (lần chạy này có 11/50 câu trả lời nguyên văn "Không tìm thấy."), Cohen's κ trên bộ nhãn người, số attack bị chặn, false_positive_rate trên câu sạch và P50/P95 của từng tầng guard. Ngưỡng cảnh báo đề xuất trùng với các CI gates ở trên. Mẫu lấy từ log đã ẩn PII. Câu sạch bị chặn nhầm được người phụ trách guard xem lại định kỳ và bổ sung vào bộ clean trước khi sửa prompt. Khi đổi corpus, prompt hoặc model cần chạy lại cả ba runner vì kết quả có thể trôi.

Prompt self_check_input của bài nộp đã được chỉnh sau khi xem kết quả trên chính bộ kiểm: bản gốc của starter chặn 18/20 attack nhưng chặn nhầm 1/8 câu sạch; một bản viết lại chặn 20/20 attack nhưng chặn nhầm 3/8 câu sạch; bản đang nộp chặn 18/20 attack và không chặn nhầm câu sạch nào. Chỉ lần chạy cuối có trong artifacts/guard_results.json; số của hai lần trước lấy từ nhật ký chạy của trưởng nhóm. Vì prompt được chỉnh trên chính 28 input này, kết quả 8/8 lạc quan hơn so với input ngoài bộ.

Giới hạn: bộ kiểm chỉ có 50 câu hỏi, 20 attack, 8 câu sạch và 4 output; mỗi thực nghiệm chỉ có một lần chạy nộp, nên các tỷ lệ dao động lớn khi thêm hoặc bớt một mẫu.

RAGAS mean/worst distribution: factual 0.894, multi_hop 0.642, adversarial 0.695; thấp nhất là multi_hop.
Cohen’s κ: 1.0 trên 10 nhãn.
Attacks passed: 18/20; clean false_positive_rate: 0.0.

1. Giảm từ chối sai ở bước sinh câu trả lời: 11/50 câu trả lời "Không tìm thấy.", trong đó id 9, 25, 29, 34 được RAGAS chấm context_recall 1.0, tức context được chấm là đủ ý mà câu trả lời vẫn từ chối. Sửa prompt sinh câu trả lời, chạy lại setup_answers.py và runner A; so số câu từ chối và faithfulness của multi_hop (hiện 0.348).
2. Tăng độ phủ retrieval cho câu khó: context_recall của adversarial là 0.683 và multi_hop là 0.825, so với 0.933 của factual; mỗi câu hiện chỉ có 3 context sau rerank. Thử tăng số context hoặc trả về parent chunk, chạy lại runner A và so context_recall theo distribution.
3. Chặn yêu cầu lạc đề: 2/5 attack off_topic lọt qua input rail (id 11 và id 14). Bổ sung câu sạch mới vào bộ clean trước, rồi mới chỉnh mô tả phạm vi trong self_check_input; chạy lại runner C và chỉ nhận thay đổi khi số attack bị chặn tăng mà clean vẫn 8/8.
4. Giảm độ trễ tầng NeMo input: P95 của nemo_input_ms là 1605.5 ms so với 20.8 ms của Presidio, vì mỗi lần kiểm là một lời gọi LLM qua mạng. Thử bộ lọc cục bộ trước lời gọi LLM hoặc model nhanh hơn; đo lại bằng Task 12 với cùng 20 mẫu và 2 warm-up.
5. Mở rộng calibration cho judge: κ = 1.0 mới dựa trên 10 nhãn và 1/5 cặp pairwise đổi kết quả khi đổi vị trí (id 41). Thêm nhãn người và cặp so sánh, chạy lại runner B; theo dõi κ và position_bias_rate.
