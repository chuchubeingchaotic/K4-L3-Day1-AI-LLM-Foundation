# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature 0.0, phản hồi thường ổn định và dễ lặp lại nhất; khi tăng lên 0.5 rồi 1.0, cách diễn đạt và các chi tiết được chọn đa dạng hơn. Ở 1.5, độ sáng tạo/ngẫu nhiên tăng rõ hơn nhưng câu trả lời cũng có thể kém tập trung hoặc kém nhất quán hơn. Temperature không thay đổi kiến thức nền của model, mà thay đổi cách model lấy mẫu token để diễn đạt câu trả lời.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature khoảng 0.2 (có thể trong khoảng 0.1–0.3). Chatbot hỗ trợ khách hàng cần câu trả lời nhất quán, rõ ràng và bám sát chính sách hơn là sáng tạo; temperature thấp giúp giảm biến thiên giữa các lần trả lời cùng một vấn đề.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Workload có 10.000 × 3 × 350 = 10.500.000 output token/ngày. Theo bảng giá của lab, GPT-4o tốn khoảng $105/ngày (10.500 × $0,010), còn GPT-4o-mini khoảng $6,30/ngày (10.500 × $0,0006), nên GPT-4o đắt hơn khoảng 16,7 lần cho phần output này. GPT-4o đáng chi phí khi cần suy luận/chất lượng cao cho tư vấn phức tạp hoặc nội dung quan trọng; mini phù hợp cho FAQ, phân loại, trích xuất hay các phản hồi ngắn với lưu lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với persona giáo viên tiểu học, câu trả lời thường ngắn hơn, dùng từ quen thuộc, ví dụ gần gũi như “cuốn sổ chung” và tránh thuật ngữ. Persona chuyên gia tài chính có xu hướng dài, chính xác về thuật ngữ hơn, có thể nhắc đến sổ cái phân tán, cơ chế đồng thuận, chữ ký số và tính bất biến. System prompt định hướng đối tượng, giọng điệu, mức chi tiết và cấu trúc câu trả lời, nên cùng một câu hỏi có thể cho trải nghiệm rất khác.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Tôi dùng đoạn 87 từ về khí hậu Việt Nam: `count_tokens(..., "gpt-4o")` cho 109 token, còn ước lượng theo số từ là 87 / 0,75 ≈ 116 token. Chênh lệch là khoảng 6% so với ước lượng (7 / 116), cho thấy công thức số từ chỉ mang tính gần đúng. Tiếng Việt thường tốn nhiều token hơn tiếng Anh khi diễn đạt cùng ý vì bộ mã hóa có thể tách riêng các âm tiết, dấu thanh/ký tự có dấu và các mảnh từ ít phổ biến hơn.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất với câu trả lời dài hoặc tác vụ có thời gian sinh đáng kể, như chatbot tư vấn, viết nội dung hay hỗ trợ lập trình: người dùng thấy phần đầu ngay và biết hệ thống vẫn đang phản hồi. Non-streaming phù hợp hơn khi ứng dụng cần nhận toàn bộ kết quả trước khi xử lý, kiểm duyệt, định dạng thành JSON hoặc hiển thị một câu trả lời ngắn đã hoàn chỉnh.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff làm các lần thử sau thưa dần, cho server thời gian hồi phục và giảm số request đổ thêm vào lúc quá tải. Nếu hàng nghìn client đều retry cố định sau 1 giây, chúng sẽ đồng loạt gửi lại request (thundering herd), gây một đợt quá tải mới lặp lại. Trong thực tế nên thêm jitter ngẫu nhiên vào thời gian chờ để các lần retry lệch nhau hơn.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona tôi chọn: “Bạn là trợ giảng AI thân thiện cho sinh viên VinUni. Trả lời bằng tiếng Việt rõ ràng, ngắn gọn; giải thích từng bước khi có khái niệm mới, nêu ví dụ Python nhỏ khi hữu ích, và nói rõ khi không chắc chắn.” Cụm “ngắn gọn” giúp câu trả lời dễ đọc và kiểm soát token/chi phí; “nói rõ khi không chắc chắn” tránh tạo cảm giác khẳng định sai. Chỉ định tiếng Việt giúp ngôn ngữ phản hồi nhất quán với người học.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn là history chỉ giữ ba lượt gần nhất nên trợ lý quên thông tin quan trọng ở đầu cuộc trò chuyện; đồng thời nó chưa có bộ nhớ dài hạn hay nguồn tài liệu để tra cứu. Một cải thiện cụ thể là lưu các lượt chat và tài liệu liên quan dưới dạng embedding trong vector database; trước mỗi lượt, tìm các đoạn liên quan nhất rồi đưa chúng vào messages cùng history gần. Cần kèm giới hạn token và chỉ lấy vài kết quả tốt nhất để không làm chi phí input tăng quá nhiều.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
