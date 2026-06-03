# Workshop — Mổ App AI Thật
**Sản phẩm:** MoMo — Moni  
**Ngày test:** 03/06/2026  
**Hình thức:** Cá nhân  
**Output:** Finding note + sketch as-is / to-be

---

## 1. Sản phẩm đã chọn

**MoMo — Moni** — Trợ thủ tài chính AI, phân tích chi tiêu, chatbot tư vấn tài chính cá nhân.  
Truy cập: App MoMo → tab Tài chính → Moni.

---

## 2. Dùng thử: promise vs reality

**Product hứa gì?**  
Moni tự giới thiệu là "trợ thủ tài chính thông minh" — có thể phân tích chi tiêu cá nhân, đưa ra gợi ý tài chính, và dự báo dựa trên thói quen của user. Promise ngầm: AI biết dữ liệu tài chính của tôi và tư vấn cá nhân hóa.

**User nào được hứa sẽ được giúp?**  
User MoMo có lịch sử giao dịch, muốn hiểu và kiểm soát chi tiêu cá nhân tốt hơn.

**Kỳ vọng AI làm được task nào?**  
- Phân tích chi tiêu: tiêu nhiều nhất vào đâu, danh mục nào tốn kém nhất
- Tư vấn mua sắm: dựa trên tình hình tài chính thực để khuyên có nên mua không
- Dự báo: ước tính chi tiêu tháng sau dựa trên pattern thực tế

**Điểm gãy xuất hiện ở đâu?**

| Query đã thử | Kỳ vọng | Thực tế | Trạng thái |
|---|---|---|---|
| "Tháng này tôi tiêu nhiều nhất vào đâu?" | Phân tích danh mục chi tiêu | Chỉ có 1 giao dịch, không đủ để phân loại. Moni thành thật nhưng không giải quyết được câu hỏi. | 😐 Low-conf |
| "Tôi có nên mua điện thoại mới không?" | Tư vấn dựa trên số dư + lịch sử chi tiêu thực | Trả lời hoàn toàn generic: "nếu máy cũ vẫn chạy được thì chưa cần mua" — không dùng bất kỳ data tài chính nào của user | 😐 Low-conf |
| "Dự đoán tháng sau tôi tiêu bao nhiêu?" | Dự báo có cơ sở từ pattern nhiều tháng | Đưa ra 2.512.300đ (= 3 ngày × 30) nhưng không cảnh báo rằng sample size chỉ có 3 ngày — độ tin cậy rất thấp | 😐 Low-conf ẩn |

**Evidence:**
- Screenshot Q1: Moni trả lời "chỉ có 1 giao dịch, nên chưa đủ để gọi là tiêu nhiều nhất vào đâu"
- Screenshot Q2: Moni "bóc trần sự thật" bằng lời khuyên chung chung, không đề cập số dư hay chi tiêu thực của user
- Screenshot Q3: Moni đưa con số 2.512.300đ với cách tính lộ rõ: trung bình/ngày × 30, không có disclaimer về độ tin cậy

---

## 3. Vẽ 4 paths

| Path | Quan sát thực tế |
|---|---|
| **Happy** | Không xuất hiện trong 3 query đã test. Về lý thuyết: khi user hỏi tổng chi tiêu tháng → Moni trả lời được con số và ngày. Nhưng ngay cả path này cũng chỉ là thông tin thô, chưa có insight. |
| **Low-confidence** | Chiếm 100% query đã test. Moni không hỏi lại, không show options, không chuyển sang chế độ khác. Chỉ trả lời rồi kết thúc — user không biết câu trả lời đó đáng tin bao nhiêu. |
| **Failure** | Không xảy ra hard failure (AI không từ chối hay báo lỗi). Nhưng Q2 là failure ẩn: AI trả lời được nhưng hoàn toàn không giải quyết nhu cầu thực của user. |
| **Correction** | Không có cơ chế correction nào. Khi AI trả lời sai/mơ hồ, user chỉ có thể gõ lại câu hỏi khác. Không có "câu trả lời này không đúng", không log feedback, không học lại. |

---

## 4. Finding — viết thành quyết định product

**Finding chính (path yếu nhất — Q2):**

```
Khi user hỏi câu có tính tài chính cá nhân như "tôi có nên mua X không?",
AI/product trả lời generic không dùng dữ liệu tài chính thực của user,
hậu quả là user không nhận được tư vấn có giá trị, mất tin tưởng vào promise "trợ thủ tài chính cá nhân".
Lỗi thuộc layer Promise + Intent + Data-tool.
Nên sửa bằng: (1) AI tra cứu số dư và chi tiêu gần nhất TRƯỚC khi trả lời,
(2) nếu chưa đủ data thì hỏi lại context cụ thể (ngân sách dự kiến, tình trạng máy cũ),
(3) không được trả lời tư vấn tài chính khi không có data — đó là misleading promise.
```

**Finding phụ (Q3 — dự báo thiếu disclaimer):**

```
Khi user hỏi dự báo chi tiêu tương lai với dữ liệu quá ít (3 ngày đầu tháng),
AI đưa ra con số cụ thể (2.512.300đ) mà không cảnh báo về độ tin cậy thấp,
hậu quả là user có thể tin vào con số không đáng tin và ra quyết định tài chính sai.
Lỗi thuộc layer Safety + UX Recovery.
Nên sửa bằng: gắn confidence label ("Dự báo này dựa trên 3 ngày — độ chính xác thấp"),
hoặc từ chối dự báo khi sample < 7 ngày và giải thích lý do.
```

---

## 5. Sketch as-is / to-be

**Path được chọn để sketch:** Q2 — Tư vấn mua sắm không dùng data

```
AS-IS                                    TO-BE
─────────────────────────────────        ─────────────────────────────────
[User] "Có nên mua ĐT mới không?"        [User] "Có nên mua ĐT mới không?"
              ↓                                         ↓
[Moni nhận câu hỏi]                      [Moni tra cứu: số dư + chi tiêu
              ↓                           tháng này của user]
[Trả lời chung chung:                                   ↓
 "Nếu máy cũ vẫn chạy được              [Moni hỏi lại:
  thì chưa cần mua..."] ❌               "Bạn dự kiến ngân sách bao nhiêu?
              ↓                           Moni thấy tháng này bạn tiêu 251k
[User không biết áp dụng                 — muốn Moni tính xem đủ không?"] ✓
 vào mình được không]                                   ↓
              ↓                          [Tư vấn cá nhân hóa:
[Bỏ cuộc / hỏi lại]                      "Với mức này, bạn có thể
              ↓                           để dành X tháng để mua..."] ✓
[Dead end ❌]                                           ↓
                                         [User có lối tiếp tục ✓]

Điểm gãy: AI không dùng data thực       Fix: data-first + clarify trước
```

**Ghi chú sketch:**
- Điểm gãy đánh dấu ❌ ở bước "Trả lời chung chung"
- Path to-be thêm 2 bước mới: tra cứu data + hỏi lại
- Kết quả: user không còn bị dead end

---

## 6. Tự kiểm trước khi nộp

- [x] Có ít nhất 1 screenshot hoặc observation cụ thể — **3 screenshot thực tế từ app Moni**
- [x] Có đủ 4 paths hoặc nói rõ path nào chưa có — **Đã phân tích cả 4, nêu rõ Happy và Correction chưa được implement**
- [x] Finding được viết thành product decision, không chỉ là nhận xét — **Đã dùng format: trigger / failure / impact / layer / fix**
- [x] Sketch có as-is và to-be — **Có sketch text + file HTML minh họa**
- [x] Có một câu nói rõ finding này sẽ đổi gì trong SPEC:

> **SPEC change:** Moni cần thêm yêu cầu "AI phải tra cứu và trích dẫn ít nhất 1 data point tài chính thực của user trước khi đưa ra tư vấn mua sắm bất kỳ. Nếu không có data, AI phải hỏi lại — không được tư vấn generic."

