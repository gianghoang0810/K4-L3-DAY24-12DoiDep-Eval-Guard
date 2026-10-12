# Đánh giá LLM Judge Phase B

Nhóm: 12DoiDep

Người phụ trách: Trần Anh Quân – 2A202602598

Nguồn: `artifacts/judge_results.json`; run timestamp: 2026-10-12T09:38:49.031793+00:00.
Models generation/judge: generation: `openai/gpt-4o-mini`; judge: `openai/gpt-4o-mini`.

## Năm cặp A/B và hai lượt

Nguồn cố định: A từ artifacts/answers_50q.json:answer; B từ
fixtures/human_labels_10q.json:model_answer; reference từ fixtures/test_set_50q.json:ground_truth.
Ids: 1, 5, 12, 21, 41. Không đổi nguồn sau khi xem kết quả judge.

| id | Candidate A (ký tự) | Candidate B (ký tự) | winner_pass1 | winner_pass2 | final_winner | position_consistent | Scores lượt 1 A/B | Scores lượt 2 A/B | Reasoning hai lượt (tóm tắt) |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Nhân viên được nghỉ 3 ngày làm việc khi kết hôn. (48) | Nhân viên được nghỉ 3 ngày làm việc có lương khi kết hôn. (57) | B | B | B | True | 0.8 / 1.0 | 0.8 / 1.0 | Lượt 1: B có thông tin có lương. Lượt 2: A ở vị trí đảo (B gốc) đầy đủ hơn vì có lương. |
| 5 | Cần Tổng Giám đốc (CEO) phê duyệt. (34) | Cần Giám đốc phòng ban phê duyệt. (33) | A | A | A | True | 1.0 / 0.0 | 1.0 / 0.0 | Lượt 1: A đúng người phê duyệt. Lượt 2: B ở vị trí đảo (A gốc) đúng reference. |
| 12 | Thưởng Tết tối thiểu cho nhân viên chính thức có thời gian làm việc từ 6 tháng trở lên là 1 tháng lương. (104) | Nhân viên được thưởng Tết tối thiểu 1 tháng lương. (50) | A | A | A | True | 1.0 / 0.5 | 1.0 / 0.7 | Lượt 1: A đủ điều kiện thời gian làm việc. Lượt 2: B ở vị trí đảo (A gốc) đầy đủ điều kiện hơn. |
| 21 | Không tìm thấy. (15) | Được nghỉ 18 ngày phép (15 cơ bản + 3 thâm niên). Lương Senior từ 20 đến 35 triệu. (82) | B | B | B | True | 0.0 / 1.0 | 0.0 / 1.0 | Lượt 1: B đủ phép và lương; A không trả lời. Lượt 2: A ở vị trí đảo (B gốc) đầy đủ hơn. |
| 41 | Mỗi nhân viên chính thức được hưởng 12 ngày phép năm có lương. (62) | Nhân viên được nghỉ 12 ngày phép năm. (37) | B | A | tie | False | 0.5 / 0.7 | 0.6 / 0.4 | Lượt 1: ưu tiên B ngắn gọn, dù dùng chính sách cũ. Lượt 2: ưu tiên B ở vị trí đảo (A gốc) vì thêm có lương, dù vẫn thiếu v2024. |

Position bias count/rate: 1/5 cặp; rate = 0.2 (20%).
Verbosity win rate: 1.0; decisive cases: 4 (A thắng và A dài hơn: 2; B thắng và B dài hơn: 2).

Winner và scores lượt 2 trong bảng đã quy về A/B gốc; reasoning lượt 2 trong JSON vẫn dùng tên ở vị trí đảo. Độ dài là len() chuỗi, gồm cả khoảng trắng trong dữ liệu gốc.

Cả 4 cặp decisive đều có winner dài hơn (4/4 = 1.0), nhưng nội dung cho thấy các yếu tố gây nhiễu:

- id 1: B dài 57 ký tự so với A 48, bổ sung “có lương”, đúng reference. Cả hai vẫn thiếu chi tiết không trừ vào phép năm; B chỉ đầy đủ hơn tương đối.
- id 5: A dài 34 ký tự, B 33, chỉ chênh 1 ký tự. Reference yêu cầu CEO phê duyệt đơn hàng trên 50 triệu; A đúng còn B nêu Giám đốc phòng ban sai. Khác biệt chính là tính chính xác.
- id 12: A dài 104 ký tự, B 50. A nêu rõ nhân viên chính thức và điều kiện từ 6 tháng, phù hợp reference. B trả lời đúng mức 1 tháng lương trong ngữ cảnh câu hỏi nhưng diễn đạt chung hơn; cả hai không nêu pro-rata dưới 6 tháng.
- id 21: B dài 82 ký tự, trả lời đúng 18 ngày phép (15 + 3) và lương Senior 20–35 triệu theo reference. A dài 15 ký tự chỉ nói “Không tìm thấy.”. B thắng vì có câu trả lời đúng, đầy đủ.

Do đó 4/4 là dấu hiệu thống kê trên bốn cặp, chưa chứng minh judge ưu tiên độ dài vì chính độ dài. Muốn tách nguyên nhân cần các cặp kiểm soát cùng nội dung, khác độ dài; không thay đổi hay chạy lại kết quả nộp này.

id 41 là cặp duy nhất không nhất quán: lượt 1 chọn B, lượt 2 chọn A gốc, kết quả cuối tie. Reference quy định v2024 hiện hành là 15 ngày cơ bản, cộng 1 ngày mỗi 3 năm thâm niên; 12 ngày thuộc v2023 đã bị thay thế. Cả hai candidate đều sai phiên bản. Lượt 1 judge ưu tiên sự ngắn gọn của B; lượt 2 lại ưu tiên chi tiết “có lương” của A gốc. Đây là sự đổi trọng tâm giữa súc tích và đầy đủ khi đảo vị trí trên hai đáp án đều sai; một cặp chưa đủ xác định nguyên nhân vị trí có hệ thống. Tie chỉ biểu thị bất đồng hai lượt, không có nghĩa hai câu trả lời đều đúng.

## Calibration đúng model_answer

Helper đánh giá đúng question/model_answer đang có trong fixtures/human_labels_10q.json;
không lấy answer mới của pipeline. Prompt chỉ nhận question, answer, reference.
Không gửi human_label, human_note hoặc phân bố nhãn human vào prompt.

| question_id | model_answer gốc | Nhãn judge | Reasoning judge (tóm tắt) | human_label | agree |
|---|---|---|---|---|---|
| 1 | Nhân viên được nghỉ 3 ngày làm việc có lương khi kết hôn. | 1 | Câu trả lời chính xác theo thông tin trong reference, đầy đủ các ý thiết yếu và không sai lệch. | 1 | có |
| 5 | Cần Giám đốc phòng ban phê duyệt. | 0 | Câu trả lời sai vì yêu cầu phê duyệt cho đơn hàng trên 50 triệu là từ Tổng Giám đốc (CEO), không phải Giám đốc phòng ban. | 0 | có |
| 12 | Nhân viên được thưởng Tết tối thiểu 1 tháng lương. | 1 | Câu trả lời chính xác theo thông tin trong reference, nêu rõ điều kiện và mức thưởng cho nhân viên chính thức có từ 6 tháng trở lên. | 1 | có |
| 21 | Được nghỉ 18 ngày phép (15 cơ bản + 3 thâm niên). Lương Senior từ 20 đến 35 triệu. | 1 | Câu trả lời chính xác theo reference, nêu rõ số ngày phép và khoảng lương cho nhân viên Senior có 9 năm thâm niên. | 1 | có |
| 23 | Hoàn trả 25 triệu vì chưa đủ 1 năm cam kết. | 1 | Câu trả lời chính xác theo reference, nêu rõ nhân viên phải hoàn trả 25 triệu do chưa đủ 1 năm cam kết. | 1 | có |
| 29 | Trưởng phòng phê duyệt. Phạt 2% tháng trên 8 triệu. | 0 | Câu trả lời sai vì không đề cập đến sự cần thiết có sự phê duyệt của Kế toán trưởng bên cạnh Trưởng phòng. Ngoài ra, phí phạt được tính cho quá hạn 15 ngày, không phải là 2% trên 8 triệu mà là 80.000 VNĐ. | 0 | có |
| 33 | Phép: 19 ngày. Phụ cấp: 1.500.000 VNĐ/tháng. | 1 | Câu trả lời cung cấp đúng số ngày phép và tổng phụ cấp hàng tháng theo thông tin trong tài liệu tham khảo. | 1 | có |
| 41 | Nhân viên được nghỉ 12 ngày phép năm. | 0 | Câu trả lời sai vì nó dựa trên chính sách cũ (v2023) thay vì chính sách hiện hành (v2024), trong đó quy định nhân viên được nghỉ 15 ngày phép năm. | 0 | có |
| 46 | Nhân viên thử việc không được nghỉ phép năm và phải xin nghỉ không lương nếu cần. | 1 | Câu trả lời chính xác theo reference, nêu rõ rằng nhân viên thử việc không được nghỉ phép năm và phải xin nghỉ không lương nếu cần, đồng thời không bỏ qua điều kiện cần có sự phê duyệt của trưởng phòng. | 1 | có |
| 50 | Được, miễn là đảm bảo kết nối an toàn. | 0 | Câu trả lời sai vì cho phép sử dụng VPN cá nhân trong khi chính sách rõ ràng cấm điều này. | 0 | có |

Cohen’s κ: 1.0.
Observed/chance agreement và diễn giải: p_o = 10/10 = 1.0. Judge và người đều có 6 nhãn 1, 4 nhãn 0; p_e = 0.6 × 0.6 + 0.4 × 0.4 = 0.52. κ = (1.0 − 0.52) / (1 − 0.52) = 1.0, khớp calibration.cohen_kappa. Theo thang Landis–Koch dùng trong lab, đây là mức gần như hoàn toàn đồng thuận; vượt ngưỡng bonus 0.6..

## Giới hạn và cách dùng judge

Đây là một lần chạy live với generation và judge cùng model openai/gpt-4o-mini qua OpenRouter, temperature 0. Pairwise prompt ưu tiên chính xác theo reference, đầy đủ rồi súc tích, yêu cầu không ưu tiên vị trí hoặc độ dài; binary helper chỉ nhận question, answer và reference. Có reference giúp bài toán dễ hơn đánh giá không có đáp án chuẩn; không suy rộng độ tin cậy sang model, prompt hoặc dữ liệu khác.

Mẫu chỉ có 5 cặp và 10 nhãn. κ = 1.0 mô tả đúng lần đo này nhưng nhạy với một thay đổi nhãn: nếu một nhãn judge 1 đổi thành 0, p_o = 0.9, p_e = 0.5, κ = 0.8; nếu một nhãn 0 đổi thành 1, p_e = 0.54, κ ≈ 0.783. Các ví dụ này là phép tính độ nhạy, không phải kết quả chạy mới.

Không có case lệch nhãn với người (10/10 agree). Đối chiếu human_note cho các nhãn 0: id 5 cùng xác định sai người phê duyệt trên 50 triệu; id 29 cùng xác định thiếu Kế toán trưởng và cần tính phí pro-rata 15 ngày = 80.000 VNĐ; id 41 cùng xác định dùng v2023 thay vì 15 ngày v2024; id 50 cùng xác định VPN cá nhân bị cấm. Human_note id 50 còn nhắc phải dùng WireGuard của công ty, trong khi reasoning judge tập trung vào lệnh cấm.

Đồng thuận nhãn không bảo đảm mọi reasoning đều chuẩn: id 12, judge binary nói câu trả lời “nêu rõ điều kiện” dù model_answer chỉ nêu mức thưởng; điều kiện nằm trong câu hỏi. id 46, reasoning nói không bỏ qua phê duyệt của trưởng phòng dù model_answer không ghi chi tiết đó. Cần đọc cả câu hỏi, answer và reference khi kiểm chứng, tránh xem lời giải thích của judge là bằng chứng tuyệt đối.

Dùng judge làm tín hiệu hỗ trợ rà soát; ưu tiên người kiểm tra case đổi ý, sai phiên bản, phủ định hoặc thiếu điều kiện quan trọng. Giữ nguyên artifacts và κ của lần chạy thành công; không chạy lại để cải thiện điểm. Judge không phải đáp án tuyệt đối.

Báo cáo được hỗ trợ soạn và kiểm tra bằng Codex; số liệu đối chiếu trực tiếp JSON, nội dung đối chiếu ground_truth và human_note. Không cập nhật doc/TEAM.md theo yêu cầu riêng của người dùng.
