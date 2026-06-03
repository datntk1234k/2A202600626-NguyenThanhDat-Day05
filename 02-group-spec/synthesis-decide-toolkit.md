# Toolkit — Từ Evidence Đến Build Slice

## 1. Gom evidence thành cụm

### Cụm 1: Người dùng muốn ghi nhận chi tiêu càng nhanh càng tốt

**Evidence**

* Một số người có thói quen nhắn tin cho chính mình hoặc người khác để ghi nhớ các khoản thu chi.
* Trong self-use testing, việc chỉ cần gửi một tin nhắn ngắn được đánh giá là thuận tiện hơn nhiều so với việc điền form.
* Người dùng muốn ghi lại giao dịch ngay khi phát sinh rồi quay lại công việc đang làm.

### Cụm 2: Multi-step form tạo friction

**Evidence**

* Các ứng dụng quản lý chi tiêu thường yêu cầu nhập nhiều trường riêng biệt như số tiền, danh mục, ngày tháng và ghi chú.
* Mỗi bước xác nhận hoặc nhập thêm thông tin đều làm tăng khả năng người dùng bỏ qua việc ghi chép.
* Test user phản hồi rằng họ chỉ muốn nhập nhanh thay vì tương tác với nhiều form.

### Cụm 3: Người dùng đã quen với Telegram

**Evidence**

* Telegram là ứng dụng người dùng đã sử dụng hằng ngày.
* Việc mở thêm một ứng dụng khác chỉ để ghi nhận một khoản chi tạo thêm friction không cần thiết.
* Flow chat được đánh giá là tự nhiên hơn so với flow form truyền thống.

### Cụm 4: AI có thể xử lý phần việc người dùng không muốn làm

**Evidence**

* Người dùng không muốn tự phân loại từng khoản chi.
* Các sản phẩm hiện có vẫn yêu cầu người dùng xác nhận hoặc chỉnh sửa nhiều trường thông tin.
* AI có khả năng trích xuất amount, category và note từ ngôn ngữ tự nhiên.

---

## 2. Viết insight

Người dùng gặp khó khăn trong việc quản lý thu chi không chỉ vì họ không có công cụ phù hợp.

Họ thật sự cần một cách ghi nhận giao dịch mà gần như không phải suy nghĩ hoặc thực hiện thêm thao tác nào, vì mỗi bước nhập liệu hoặc xác nhận bổ sung đều làm tăng khả năng họ bỏ cuộc và ngừng ghi chép chi tiêu.

---

## 3. Viết opportunity

Cơ hội là sử dụng AI để tự động trích xuất và phân loại thông tin thu chi từ một tin nhắn Telegram đơn giản, giúp người dùng ghi nhận giao dịch ngay tại nơi họ đã quen sử dụng, đồng thời loại bỏ hầu hết các bước nhập liệu và xác nhận thủ công.

---

## 4. Chọn build slice

### User

Người dùng cá nhân thường xuyên phát sinh các khoản thu chi nhỏ lẻ và không muốn mất thời gian nhập liệu.

### Context

Người dùng vừa phát sinh một khoản thu hoặc chi và muốn ghi nhận nhanh nhất có thể.

### Task

Người dùng gửi một tin nhắn Telegram.

Ví dụ:

> Ăn trưa 50k

> Cafe 35k

> Nhận lương 8 triệu

> Mua sách 250k hôm qua

### AI Decision

AI tự động:

* Trích xuất số tiền.
* Trích xuất ngày giao dịch.
* Xác định đây là thu hay chi.
* Tự động phân loại danh mục giao dịch.
* Chuyển dữ liệu sang JSON chuẩn hóa.

### Output

Backend nhận JSON đã chuẩn hóa.

Ví dụ:

```json
{
  "type": "expense",
  "amount": 50000,
  "category": "Food",
  "date": "2026-06-03",
  "description": "Ăn trưa"
}
```

JSON được lưu trữ và hiển thị trên dashboard tổng hợp thu chi.

### Four Paths

#### Happy Path

Người dùng gửi:

> Ăn sáng 35k

AI nhận diện chính xác:

* Expense
* Food
* 35.000 VNĐ
* Ngày hiện tại

Hệ thống phản hồi:

> Đã nhận giao dịch.

Dữ liệu xuất hiện trên dashboard.

#### Low-Confidence Path

Người dùng gửi:

> Mua đồ 200k

AI nhận diện được số tiền nhưng không chắc danh mục.

Hệ thống:

* Gắn category = Other.
* Đánh dấu confidence thấp.
* Cho phép chỉnh sửa trên dashboard.

#### Failure Path

Người dùng gửi:

> Chuyển khoản 500k

AI không xác định được đây là:

* Chi tiêu,
* Thu nhập,
* Hay chuyển tiền giữa các tài khoản cá nhân.

Hệ thống yêu cầu người dùng bổ sung thông tin thay vì tự động lưu.

#### Correction Path

Người dùng gửi:

> Mua giáo trình 200k

AI phân loại thành Shopping.

Người dùng sửa thành Education.

Hệ thống cập nhật dữ liệu theo chỉnh sửa của người dùng.

### Failure Mode

#### Failure nguy hiểm nhất

AI xác định sai bản chất giao dịch.

Ví dụ:

> Được hoàn tiền 300k

AI phân loại thành Expense thay vì Income.

#### Tác động

* Tổng thu nhập bị sai.
* Tổng chi tiêu bị sai.
* Dashboard không phản ánh đúng tình hình tài chính.
* Người dùng mất niềm tin vào hệ thống.

#### Mitigation

* Hiển thị rõ kết quả AI vừa tạo.
* Cho phép chỉnh sửa sau khi lưu.
* Đánh dấu các giao dịch confidence thấp.
* Không thực hiện bất kỳ hành động tài chính tự động nào dựa trên dữ liệu AI.

---

## 5. Quyết định: giữ, giảm scope, hay đổi hướng?

### Quyết định

Giữ domain Finance nhưng giảm scope xuống một workflow duy nhất:

**Telegram message → AI extraction → Auto categorization → Dashboard.**

### Lý do

* Evidence cho thấy friction lớn nhất đến từ form multi-step.
* Telegram là nơi người dùng đã quen sử dụng.
* AI giải quyết trực tiếp pain point nhập liệu và phân loại thủ công.
* Có thể demo hoàn chỉnh trong phạm vi Day 06.

---

## 6. Câu chốt cuối

Dựa trên evidence từ self-use testing và quan sát hành vi người dùng, nhóm sẽ build một prototype ghi nhận thu chi qua Telegram dành cho người dùng cá nhân thường xuyên phát sinh các khoản thu chi nhỏ lẻ.

Prototype giải quyết pain point về friction từ các form nhập liệu nhiều bước bằng cách cho phép người dùng chỉ cần gửi một tin nhắn tự nhiên. AI sẽ tự động trích xuất thông tin giao dịch, phân loại danh mục và lưu dữ liệu để hiển thị trên dashboard, đồng thời nhóm sẽ kiểm thử failure path khi AI hiểu sai loại giao dịch hoặc phân loại sai danh mục.

---

## 7. Backlog

* Đồng bộ ngân hàng.
* OCR hóa đơn từ ảnh.
* Voice input.
* Hỗ trợ nhiều giao dịch trong một tin nhắn.
* Dự đoán chi tiêu tương lai.
* Phân tích tài chính nâng cao.
* Gợi ý tiết kiệm và đầu tư.
* Đồng bộ đa nền tảng ngoài Telegram.
