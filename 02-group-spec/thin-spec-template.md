# Template — Thin SPEC Cuối Day 05

Thin SPEC không phải PRD đầy đủ. Đây là bản cam kết đủ rõ để sáng Day 06 nhóm build ngay.

## 1. Track, product/app và user

**Track:** FinTech & Personal Finance Management (PFM)  
**Product/app thật:** MoMo (Tính năng Trợ thủ tài chính Moni)  
**User cụ thể:** Chị Hoa (40 tuổi, nội trợ ở TP.HCM), hàng ngày đi chợ truyền thống mua thực phẩm cho gia đình. Chị thanh toán chủ yếu bằng tiền mặt hoặc quét mã VietQR cá nhân của tiểu thương (không phải mã QR MoMo Merchant chính thức).  
**Nhóm có phải user thật không? Nếu không, khác ở đâu?** Nhóm là sinh viên, có đi chợ sử dụng tiền mặt, tài khoản, hỗn hợp,... Nhóm là user thực tế.  

## 2. Evidence summary

| Evidence | Nguồn | User/pain nói lên điều gì? | SPEC phải đổi gì? |
|---|---|---|---|
| Chị Hoa đi chợ mua nhiều món, trả tiền mặt lẻ. Ghi chép tay lúc đang xách đồ thì bất khả thi, cuối ngày quên mất đã tiêu bao nhiêu cho món gì. | Phỏng vấn nhanh nội trợ (Self-observe / Interview) | Nhập liệu thủ công từng dòng cực kỳ phiền phức khi bận rộn. Cần cơ chế nhập rảnh tay | Chuyển từ App-first sang Chat-first thông qua Telegram để ghi chú nhanh bằng voice hoặc nhắn tin. |
| Người dùng nhắn tin vào các trang Facebook, nick TikTok phụ, hoặc tin nhắn Direct Message của người nổi tiếng/Idol chỉ để lưu nhanh số tiền vừa tiêu vì app chat luôn mở sẵn. | Khảo sát hành vi người dùng trên mạng xã hội | Người dùng cần sự tiện lợi tối đa (vừa mở app ra là nhắn/nói được ngay), nhưng cách note này vô cùng phân mảnh, tốn thời gian tự tổng hợp lại vào cuối tuần/cuối tháng. | Xây dựng Telegram Bot nhận tin nhắn chat nhanh và tự động gom nhóm, tổng hợp số liệu đồng bộ lên Web Hub thay vì bắt user làm tay. |
| Quét VietQR cho tiểu thương chỉ hiện tên người nhận và số tiền, không lưu thông tin chi tiết mua gì (rau, cá, thịt). | Lịch sử giao dịch thực tế | Việc thanh toán không chính thức khiến tính năng tự động phân loại chi tiêu mặc định của MoMo bị xếp hết vào nhóm "Chuyển tiền chung chung". | AI tự động bóc tách và phân loại các giao dịch từ nội dung text tự do của user chat trên Telegram (VD: "chuyển khoản 120k VietQR mua thịt"). |

## 3. Pain statement

```text
User [tiểu thương, lao động tự do, nội trợ truyền thống giao dịch lẻ tẻ] đang gặp khó ở [bước ghi chép và tổng hợp thu chi hàng ngày],
vì [các ứng dụng quản lý tài chính hiện tại đòi hỏi quá nhiều thao tác (mở app, điền số, chọn danh mục) và giao dịch VietQR lẻ không tự phân loại],
dẫn tới [hậu quả lười ghi chép, bỏ sót giao dịch, thất thoát tiền bạc hoặc phải dùng cách chắp vá như nhắn tin vào page/chat Idol để note tạm].
Bằng chứng chính là [việc người dùng mượn phần bình luận hoặc hộp chat riêng trên mạng xã hội của người nổi tiếng để note lại con số chi tiêu].
```

## 4. Build slice

```text
Cho [người dùng] đang [cần ghi chú nhanh khoản tiền ngay sau khi giao dịch],
prototype sẽ dùng AI để [bóc tách ý định (thu/chi), số tiền, và danh mục từ một tin nhắn văn bản tự do qua Telegram (VD: "bán 2 ly cafe sữa 50k")],
tạo ra [bản ghi dữ liệu chuẩn hóa trên Web Hub (Số tiền, Loại, Nhãn, Thời gian)],
và xử lý [trường hợp AI không nhận diện được số tiền] bằng [cách Bot tự động phản hồi bằng câu hỏi xác nhận ngắn gọn].
```

## 5. Auto/Aug decision

Chọn một:

- [ ] **Augmentation:** AI gợi ý/draft/phân loại, user quyết cuối.
- [x] **Conditional automation:** AI tự làm trong case hẹp; case mơ hồ/rủi ro chuyển người.
- [ ] **Automation:** AI tự quyết và tự hành động.

**Lý do chọn:** Với các câu lệnh rõ ràng ("chi 50k ăn sáng"), AI hoàn toàn có thể tự động lưu để đảm bảo tốc độ cực nhanh. Nhưng với câu mơ hồ ("hôm nay lỗ rồi", "đưa vợ 500"), AI cần hỏi lại để xác định chính xác đây là khoản thu, chi hay chuyển nội bộ.  
**Human role:** reviewer / corrector (Người dùng kiểm tra lại báo cáo trên Hub và sửa bằng lệnh chat nếu Bot nhận diện sai).  

## 6. Four paths

| Path | Prototype phải thể hiện gì? |
|---|---|
| Happy | User gõ "đổ xăng 50k". Bot phản hồi "Đã ghi nhận: Chi - 50.000đ (Di chuyển)". Dữ liệu bắn thẳng lên Web Hub. |
| Low-confidence | User gõ "tiền hàng 500k". Bot hỏi lại: "500k này là bạn thu hay chi vậy?". User gõ "thu". Bot ghi nhận và lưu dữ liệu lên Hub. |
| Failure | User gửi tin nhắn sai chính tả nặng hoặc không có số tiền. Bot báo: "Mình chưa tìm thấy số tiền, bạn ghi theo mẫu [Số tiền] + [Nội dung] nhé." |
| Correction | Bot nhận diện "nhận 50k" thành Chi. User chat "sai rồi, là thu". Bot tự động sửa bản ghi gần nhất trên Hub và báo "Đã cập nhật lại thành Thu". |

## 7. Failure mode nguy hiểm nhnhấiiất

```text
Nếu user [nhập thiếu/dư số 0 hoặc viết tắt (VD: "500" thay vì "500k")],
AI có thể [bóc tách sai hoàn toàn đơn vị tiền tệ (ví dụ 500đ thay vì 500.000đ)],
hậu quả là [báo cáo thu chi cuối tháng trên Hub bị sai lệch nghiêm trọng, làm mất niềm tin vào hệ thống].
Prototype sẽ xử lý bằng [hiển thị lại số tiền đã format (VD: 500,000 đ) kèm nút [Hoàn tác/Undo] dạng inline keyboard của Telegram].
Owner kiểm thử path này là [Nguyễn Viết Du].
```

## 8. Owner plan cho sáng Day 06

| Thành viên | Việc phụ trách | Bằng chứng cần có trong repo |
| :--- | :--- | :--- |
| **Phạm Triều Dương** | Phụ trách AI Telegram (Nhận diện & bóc tách thu chi từ text/voice) | Code Python/JS tích hợp Telegram API và LLM API trong folder `/bot-telegram`. |
| **Lê Sỹ Hân** | Triển khai Hub - Backend (BE) (Database, API xử lý và lưu trữ bản ghi) | Database schema (MySQL) và source code server trong folder `/backend`. |
| **Nguyễn Thành Đạt** | Triển khai Hub - Frontend (FE) (Giao diện web dashboard báo cáo thu chi) | Source code React/Vue/HTML hiển thị biểu đồ và bảng dữ liệu trong folder `/frontend`. |
| **Nguyễn Viết Du** | Triển khai Hub - Integration & Testing (Kết nối FE/BE, QA/Test kịch bản Happy/Failure) | Bộ test cases và video quay màn hình chạy thử các luồng Low-confidence/Correction trên Telegram. |
| **Nguyễn Ngọc Duy** | Viết báo cáo & Documentation (Hoàn thiện SPEC, Evidence Pack, Báo cáo) | Các file markdown [ke_hoach_phat_trien_hub_telegram.md](file:///c:/Users/PC/Desktop/Assigtment/Day05-2A202600982-NguyenNgocDuy/02-group-spec/ke_hoach_phat_trien_hub_telegram.md), [thin-spec-template.md](file:///c:/Users/PC/Desktop/Assigtment/Day05-2A202600982-NguyenNgocDuy/02-group-spec/thin-spec-template.md) hoàn chỉnh. |
