# Phân tích lỗi Phase A

Nhóm: 12DoiDep

Người phụ trách: Nguyễn Đức Thắng – 2A202602605

Nguồn: `artifacts/ragas_50q.json`; run timestamp: 2026-10-12T09:43:58.990991+00:00.
Generation mode/fallback và điều kiện chạy: 50 câu `llm`, 0 câu `extractive_fallback` (đếm từ `artifacts/answers_50q.json`); một lần chạy live qua OpenRouter, mỗi câu 3 context sau rerank, không câu nào phải chấm lại.
Models generation/evaluator: generation `openai/gpt-4o-mini`; evaluator `openai/gpt-4o-mini`, `openai/text-embedding-3-small`.

## Điểm theo distribution

| Metric | factual (20) | multi_hop (20) | adversarial (10) |
|---|---|---|---|
| faithfulness | 0.950 | 0.348 | 0.700 |
| answer_relevancy | 0.731 | 0.436 | 0.440 |
| context_precision | 0.962 | 0.958 | 0.958 |
| context_recall | 0.933 | 0.825 | 0.683 |
| avg_score | 0.894 | 0.642 | 0.695 |

## Bottom 10 và nguyên nhân

| rank | id | question | distribution | avg_score | worst_metric | diagnosis | suggested_fix |
|---|---|---|---|---|---|---|---|
| 1 | 21 | Một nhân viên Senior có 9 năm thâm niên được nghỉ bao nhiêu ngày phép năm và lương trong khoảng nào? | multi_hop | 0.333333 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=0.833, context_recall=0.500); câu multi_hop (cần ghép thông tin từ nhiều đoạn). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Multi_hop: yêu cầu trả lời từng bước, gắn đoạn nguồn cho từng ý trước khi tổng hợp. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 2 | 39 | So sánh yêu cầu mật khẩu giữa policy v1.0 và v2.0 về độ dài tối thiểu, thời hạn đổi và MFA. | multi_hop | 0.375 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=0.500); câu multi_hop (cần ghép thông tin từ nhiều đoạn). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Multi_hop: yêu cầu trả lời từng bước, gắn đoạn nguồn cho từng ý trước khi tổng hợp. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 3 | 33 | Nhân viên Manager có thâm niên 12 năm: tổng phụ cấp hàng tháng và số ngày phép năm theo v2024 là bao nhiêu? | multi_hop | 0.375 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=0.500); câu multi_hop (cần ghép thông tin từ nhiều đoạn). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Multi_hop: yêu cầu trả lời từng bước, gắn đoạn nguồn cho từng ý trước khi tổng hợp. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 4 | 44 | Bao lâu phải đổi mật khẩu một lần? | adversarial | 0.416667 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=0.667); câu adversarial (bẫy phiên bản/phủ định). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Adversarial: thêm chỉ dẫn kiểm tra phiên bản và phủ định trong câu hỏi, nói rõ khi context mâu thuẫn. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 5 | 46 | Nhân viên thử việc có được nghỉ phép năm không? | adversarial | 0.416667 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=0.667); câu adversarial (bẫy phiên bản/phủ định). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Adversarial: thêm chỉ dẫn kiểm tra phiên bản và phủ định trong câu hỏi, nói rõ khi context mâu thuẫn. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 6 | 50 | Nhân viên Manager có thể dùng VPN cá nhân (như NordVPN) khi WFH để tăng bảo mật thêm không? | adversarial | 0.416667 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=0.667); câu adversarial (bẫy phiên bản/phủ định). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Adversarial: thêm chỉ dẫn kiểm tra phiên bản và phủ định trong câu hỏi, nói rõ khi context mâu thuẫn. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 7 | 25 | Lương thử việc của nhân viên Junior mức cao nhất là bao nhiêu? | multi_hop | 0.458333 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=0.833, context_recall=1.000); câu multi_hop (cần ghép thông tin từ nhiều đoạn). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Multi_hop: yêu cầu trả lời từng bước, gắn đoạn nguồn cho từng ý trước khi tổng hợp. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 8 | 31 | Nhân viên đi công tác trong nước, ở khách sạn 2 đêm với giá 1.500.000 VNĐ/đêm. Công ty thanh toán tối đa bao nhiêu cho tiền khách sạn? | multi_hop | 0.497179 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.489, context_precision=0.833, context_recall=0.667); câu multi_hop (cần ghép thông tin từ nhiều đoạn). | Tighten system prompt, lower temperature. Multi_hop: yêu cầu trả lời từng bước, gắn đoạn nguồn cho từng ý trước khi tổng hợp. |
| 9 | 9 | Nam nhân viên được nghỉ bao nhiêu ngày khi vợ sinh con? | factual | 0.5 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=1.000); câu factual (tra cứu trực tiếp một đoạn). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Factual: buộc trả lời ngắn, trích nguyên văn số liệu/điều khoản từ context. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |
| 10 | 29 | Nhân viên tạm ứng 8 triệu, chưa thanh toán sau 30 ngày (quá hạn 15 ngày). Ai phê duyệt khoản này và phí phạt là bao nhiêu? | multi_hop | 0.5 | faithfulness | LLM hallucinating: faithfulness=0.000 thấp nhất (còn lại answer_relevancy=0.000, context_precision=1.000, context_recall=1.000); câu multi_hop (cần ghép thông tin từ nhiều đoạn). Answer chứa 'Không tìm thấy': pipeline từ chối vì context không có thông tin cần. | Tighten system prompt, lower temperature. Multi_hop: yêu cầu trả lời từng bước, gắn đoạn nguồn cho từng ý trước khi tổng hợp. Sửa retrieval trước khi chỉnh prompt vì model đã từ chối do thiếu context. |

Cả 10 câu có worst_metric là `faithfulness`, nhưng đọc answer và contexts cho thấy **không câu nào là model bịa nội dung**. 9/10 câu (trừ id 31) có answer nguyên văn `Không tìm thấy.`; tổng cộng 11/50 câu như vậy (id 9, 21, 25, 29, 33, 34, 39, 44, 46, 48, 50). Một lời từ chối không chứa mệnh đề nào để context hỗ trợ nên RAGAS chấm faithfulness = 0 và answer_relevancy = 0. Vì vậy chẩn đoán "LLM hallucinating" trong cột diagnosis (lấy từ `DIAGNOSTIC_TREE` theo tên metric) và chuỗi `insight` tự sinh **không khớp** với nội dung thật. Ghi chú tự sinh "context không có thông tin cần" cũng không đúng cho mọi câu: dưới đây đối chiếu từng nhóm.

Nguyên nhân chung của nhóm từ chối nằm ở system prompt sinh câu trả lời trong `scripts/setup_answers.py`: "Trả lời CHỈ dựa trên context. Nếu không có → nói 'Không tìm thấy.'". Prompt chỉ cho hai lựa chọn là trả lời đủ hoặc từ chối cả câu, không cho trả lời phần có căn cứ, không nói được phép tính toán từ số trong context, và không hướng dẫn chọn văn bản phiên bản mới.

**Câu đại diện 1 — id 9 (factual), lỗi generation thuần.** Answer: "Không tìm thấy.". Context 1 chứa nguyên văn "**Sinh con** (nam giới): 5 ngày làm việc liên tục"; reference: "Nam nhân viên được nghỉ 5 ngày làm việc liên tục khi vợ sinh con". Retrieval đúng (context_precision = context_recall = 1.00), đáp án nằm sẵn trong đoạn đứng đầu, nhưng model vẫn từ chối. Đây là lỗi generation: câu hỏi nói "khi vợ sinh con" còn context viết "Sinh con (nam giới)", và prompt cứng khiến model không dám nối hai cách diễn đạt.

**Câu đại diện 2 — id 25 và id 29, 34 (multi_hop): model từ chối khi phải tính hoặc ghép, cộng thêm lỗi cắt chunk.** Id 25: context 3 có "Junior (P1-P2) / 12.000.000 - 20.000.000", context 1 và 2 có "Nhân viên thử việc được nhận **85% mức lương**"; reference là 85% × 20.000.000 = 17.000.000 VNĐ. Đủ dữ kiện nhưng model không thực hiện phép tính mà trả "Không tìm thấy." → lỗi generation. Id 34 (cùng cơ chế với id 29): context 2 dừng giữa câu ở "Tạm ứng dưới 5.000.000 VNĐ: trưởng phòng phê duyệt. Từ 5.000.000"; phần "từ 5 triệu trở lên cần thêm Kế toán trưởng" của reference bị chunk cắt mất. Ở đây lỗi gốc là **chunking/retrieval** (ranh giới chunk cắt đôi điều khoản), cộng với generation không trả lời nửa có căn cứ (trường hợp 4 triệu). RAGAS vẫn chấm context_recall = 1.00 cho id 29 và 34, nên context_recall của evaluator ở đây chấm rộng tay hơn thực tế.

**Câu đại diện 3 — id 21, 33, 39 (multi_hop, context_recall 0.50): retrieval thiếu một vế.** Id 21 hỏi phép năm và lương của Senior 9 năm: context 1 có sẵn ví dụ "nhân viên 9 năm thâm niên được 18 ngày phép (15 + 3)", nhưng không đoạn nào chứa khung lương Senior "20-35 triệu" của reference; thay vào đó context 2 là bản v2023 đã hết hiệu lực. Id 33 tương tự: có đủ phần phép năm (15 + 12÷3), thiếu đoạn phụ cấp ăn trưa và điện thoại Manager. Id 39 so sánh v1.0 và v2.0: cả 3 context đều thuộc v1.0 hoặc thông báo thay thế, không có đoạn nội dung v2.0 (12 ký tự, 120 ngày, MFA). Ba câu này là lỗi **retrieval** thật: với chỉ 3 context sau rerank, câu hỏi hai vế chỉ lấy được một vế, rồi prompt khiến model bỏ luôn vế đã có.

**Câu đại diện 4 — id 31 (multi_hop), lỗi của thước đo, không phải của pipeline.** Answer: "Công ty thanh toán tối đa cho tiền khách sạn là 2.400.000 VNĐ (1.200.000 VNĐ/đêm x 2 đêm)." Context 1: "**Khách sạn**: tối đa **1.200.000 VNĐ/đêm**"; reference: "tối đa 2 × 1.200.000 = 2.400.000 VNĐ". Answer đúng hoàn toàn nhưng faithfulness = 0.00, vì con số 2.400.000 là kết quả suy ra, không xuất hiện nguyên văn trong context. Cùng hiện tượng ở các câu trả lời đúng có phép tính ngoài bottom 10: id 38 (600 USD, faithfulness 0.00), id 27 (17 ngày, 0.33), id 28 (1.300.000 VNĐ, 0.33). Ngược lại id 24 tính sai thật (300.000 VNĐ, reference ≈ 50.000 VNĐ do bỏ qua 15 ngày ân hạn và cách tính pro-rata) nhưng faithfulness 0.25, không thấp hơn các câu đúng. Faithfulness của RAGAS vì vậy không phân biệt được suy luận đúng với sai trên nhóm multi_hop.

## Matrix và nhóm yếu nhất

| worst_metric | factual | multi_hop | adversarial |
|---|---|---|---|
| faithfulness | 1 | 16 | 3 |
| answer_relevancy | 13 | 3 | 3 |
| context_precision | 3 | 0 | 1 |
| context_recall | 3 | 1 | 3 |

Nhóm yếu theo mean thấp nhất: multi_hop; mean: 0.642.
Worst metric xuất hiện nhiều nhất: faithfulness; số câu: 20.

Multi_hop yếu nhất không phải vì retrieval kém trên diện rộng: context_precision của nhóm này 0.958, ngang factual 0.962, và context_recall 0.825. Điểm tụt nằm ở faithfulness 0.348 và answer_relevancy 0.436, với 16/20 câu có worst_metric là faithfulness. Theo phân tích ở trên, 16 câu đó gồm ba loại: 6 câu từ chối (21, 25, 29, 33, 34, 39), trong đó 3 câu thiếu một vế do retrieval và các câu còn lại có đủ hoặc gần đủ dữ kiện; các câu trả lời đúng nhưng có phép tính bị faithfulness phạt (23, 27, 28, 31, 38); và số ít lỗi suy luận thật (id 24 tính sai phí phạt, id 35 thiếu tổng 11.200.000). Ở factual, 13/20 câu có worst_metric là answer_relevancy dù đa số trả lời đúng và ngắn (ví dụ id 3 "1.000.000 VNĐ" chỉ được 0.41), nên metric này cũng nhạy với độ ngắn của câu trả lời. Không dùng tổng cột 20/20/10 để chọn nhóm yếu; nhóm yếu được chọn theo mean avg_score.

Đề xuất sửa, theo thứ tự ưu tiên:

1. Sửa prompt sinh câu trả lời: cho phép trả lời phần có căn cứ và nêu rõ phần thiếu thay vì từ chối cả câu; cho phép tính toán từ số liệu trong context và ghi phép tính; khi các đoạn mâu thuẫn, ưu tiên văn bản có phiên bản/ngày hiệu lực mới nhất. Kiểm chứng: số câu `Không tìm thấy.` giảm từ 11 về các câu thật sự thiếu dữ kiện, đặc biệt id 9, 25, 44, 46, 48, 50 phải có câu trả lời đúng reference.
2. Sửa chunking: không cắt giữa một điều khoản hoặc một danh sách (chunk theo heading Markdown, hoặc mở rộng parent chunk), để "Từ 5.000.000 VNĐ trở lên…" và "Nhân viên thử việc **KHÔNG được** nghỉ phép năm" nằm trọn trong một chunk. Kiểm chứng ở id 29, 34, 46.
3. Tăng số context cho câu nhiều vế (ví dụ `RERANK_TOP_K` từ 3 lên 5, hoặc tách câu hỏi thành các truy vấn con) để id 21, 33, 39 lấy được cả hai vế. Kiểm chứng: context_recall multi_hop tăng khỏi 0.825 mà context_precision không giảm đáng kể.

Cách chạy lại để biết sửa có hiệu quả: sau mỗi thay đổi chạy `python scripts/setup_answers.py --live` rồi `python scripts/run_experiments.py --phase a --live`; so `per_distribution.multi_hop.avg_score` (mốc 0.642), faithfulness và answer_relevancy multi_hop (mốc 0.348 và 0.436), số câu `Không tìm thấy.` (mốc 11) và hàng faithfulness của matrix (mốc 20). Vì faithfulness phạt cả câu tính đúng, phải đọc lại answer của bottom 10 mới chứ không chỉ nhìn điểm; nên đổi từng yếu tố một để biết thay đổi nào có tác dụng.

## Bẫy adversarial và giới hạn

Mean adversarial 0.695 **thấp hơn** factual 0.894 (chênh 0.199), nhưng **cao hơn** multi_hop 0.642. Adversarial yếu ở answer_relevancy 0.440 và context_recall 0.683 (thấp nhất trong ba nhóm), trong khi context_precision vẫn 0.958.

Ba câu adversarial trong bottom 10 đều trả lời `Không tìm thấy.`:

- Id 44 (bẫy phiên bản): reference "Theo v2.0 HIỆN HÀNH: mỗi 120 ngày. Chính sách v1.0 (đã hết hiệu lực) là 90 ngày". Context 1 có "Mật khẩu phải được thay đổi **mỗi 120 ngày**", context 2 có "**mỗi 90 ngày**", context 3 nói v1.0 đã được thay thế bởi v2.0. Hai đoạn chứa con số không mang nhãn phiên bản nên model không biết đoạn nào hiện hành và từ chối. Retrieval đã đưa về đáp án đúng; lỗi ở việc chunk thiếu metadata phiên bản và prompt không hướng dẫn xử lý mâu thuẫn.
- Id 46 (bẫy phủ định): reference "KHÔNG. Nhân viên thử việc không được nghỉ phép năm". Context 1 bắt đầu bằng "nghỉ phép năm**. Trường hợp cần nghỉ việc riêng, nhân viên thử việc phải xin nghỉ không lương": chữ phủ định "KHÔNG được" nằm ở chunk trước và bị cắt mất. Đây là lỗi chunking làm mất chính từ phủ định của bẫy.
- Id 50 (bẫy phủ định/mâu thuẫn chính sách): reference "KHÔNG. Chính sách VPN v1.3 cấm sử dụng VPN cá nhân". Context 1 có nguyên văn "Không sử dụng VPN cá nhân (NordVPN, ExpressVPN) trong mạng công ty". Đủ dữ kiện để trả lời "không" nhưng model vẫn từ chối, có thể vì câu hỏi đặt khung "Manager", "WFH", "để tăng bảo mật" khác câu chữ của quy định → lỗi generation.

Id 48 (phủ định, ngoài bottom 10) cũng trả lời `Không tìm thấy.` dù context 1 ghi rõ "chưa được hưởng gói bảo hiểm sức khỏe PVI"; evaluator lại chấm faithfulness 1.00 cho lời từ chối này trong khi chấm 0.00 cho mười lời từ chối giống hệt, cho thấy cách chấm câu từ chối không nhất quán.

Điểm thấp của adversarial **không tự chứng minh pipeline đã nhận ra bẫy**. Ở id 44, 46, 50 pipeline từ chối trả lời nên không rơi vào bẫy, nhưng cũng không đưa ra đáp án đúng và không nêu được lý do (phiên bản cũ hay phủ định). Ngược lại, id 41 cho thấy pipeline **có rơi vào bẫy phiên bản**: hỏi số ngày phép năm, answer "Mỗi nhân viên chính thức được hưởng 12 ngày phép năm có lương" là con số của v2023 đã bị thay thế, trong khi reference là 15 ngày theo v2024. Context 1 (v2023) được rerank lên trước context 2 (v2024). Câu này vẫn được faithfulness 1.00 vì câu trả lời đúng với context đã lấy, nên không nằm trong bottom 10: faithfulness đo bám context chứ không đo đúng phiên bản. Các câu bẫy còn lại được trả lời đúng: id 43 (12 ký tự), 45 (MFA bắt buộc), 47 (không tự xử lý malware), 49 (phân biệt v2023 và v2024); id 42 nêu đúng mốc 3 năm của v2024 nhưng thiếu "mỗi 3 năm thêm 1 ngày".

Giới hạn:

- 50 câu (adversarial chỉ 10 câu), một lần chạy, không lặp lại nên chưa ước lượng được độ dao động của điểm.
- Evaluator là `openai/gpt-4o-mini` qua OpenRouter, cùng model với generation; các bất thường đã thấy (faithfulness 0 cho câu tính đúng, context_recall 1.00 khi chunk bị cắt, chấm lời từ chối không nhất quán) cho thấy điểm RAGAS cần đối chiếu với nội dung trước khi kết luận.
- Không có fallback: cả 50 câu sinh bằng `llm`.
- Mỗi câu chỉ có 3 context sau rerank, bất lợi cho câu nhiều vế.
- Hai file PDF scan trong corpus (`BCTC.pdf`, `Nghi_dinh_so_13-2023_ve_bao_ve_du_lieu_ca_nhan_508ee.pdf`) không có lớp text nên không được lập chỉ mục; tìm theo từ khoá không thấy câu nào trong 50 câu liên quan tới hai file này.
- Chẩn đoán trong cột diagnosis/suggested_fix là heuristic theo worst_metric và distribution; phần phân tích bằng chữ ở trên mới là kết luận sau khi đọc nội dung.
