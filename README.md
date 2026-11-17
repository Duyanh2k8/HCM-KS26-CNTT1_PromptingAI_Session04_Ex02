# [Phân tích] So sánh Trade-off các giải pháp ra lệnh cho Kỹ thuật Prompting Nâng cao (Logic & Chuỗi)
## 1. Bối cảnh bài toán
Một cửa hàng bán lẻ thường nhận đơn hàng qua tin nhắn của khách. Việc xử lý thủ công dễ bị nhầm lẫn về sản phẩm, số lượng và giá tiền.

Em đề xuất sử dụng AI để đọc thông tin đơn hàng, kiểm tra sản phẩm, tồn kho và tính tổng tiền.

## 2. Thiết kế Input / Output
**Input:**
- Tin nhắn đặt hàng của khách hàng.
- Danh sách sản phẩm, giá bán và tồn kho.

**Output:**
- Thông tin khách hàng.
- Danh sách sản phẩm và số lượng.
- Thành tiền và tổng tiền.
- Trạng thái đơn hàng.
## 3. Đề xuất 2 giải pháp Prompt
### Giải pháp 1: Prompt đơn giản
```text
Bạn là nhân viên xử lý đơn hàng.

Hãy đọc tin nhắn khách hàng, lấy thông tin
người mua và sản phẩm cần đặt.

Kiểm tra sản phẩm, giá bán và tồn kho.
Sau đó tính tổng tiền đơn hàng.

Nếu thiếu thông tin hoặc có lỗi thì thông báo.
Không tự ý thêm dữ liệu.

Trả kết quả dưới dạng JSON.

Tin nhắn khách hàng:
[Nhập tin nhắn]

Danh sách sản phẩm:
[Nhập danh sách]
```
### Giải pháp 2: Prompt chi tiết
```text
Bạn là nhân viên xử lý đơn hàng của cửa hàng.

Hãy thực hiện công việc theo 3 bước:

Bước 1: Trích xuất tên khách hàng, số điện thoại,
địa chỉ, sản phẩm và số lượng.

Bước 2: Kiểm tra sản phẩm, giá bán và tồn kho
theo danh sách được cung cấp.

Bước 3: Nếu dữ liệu hợp lệ, tính thành tiền
và tổng tiền đơn hàng.

Quy tắc:
- Không tự tạo thông tin hoặc giá bán.
- Thiếu thông tin thì trả về NEED_INFO.
- Sản phẩm không tồn tại thì trả về
  PRODUCT_NOT_FOUND.
- Số lượng vượt tồn kho thì trả về OUT_OF_STOCK.
- Dữ liệu trống thì trả về INVALID_INPUT.
- Nếu JSON sai cú pháp, thử sửa lại một lần.
- Nếu bước trước chưa hợp lệ thì không chuyển
  sang bước tiếp theo.
- Kiểm tra lại phép tính trước khi trả kết quả.

Chỉ trả về JSON, không giải thích thêm.

Tin nhắn khách hàng:
[Nhập tin nhắn]

Danh sách sản phẩm:
[Nhập danh sách]
```
## 4. Bảng so sánh Trade-off
|Tiêu chí|Giải pháp 1|Giải pháp 2|
|---|---|---|
|Độ dễ hiểu| Cao| Cao|
|Tốn token| Ít| Nhiều|
|Thời gian viết Prompt| Nhanh| Lâu hơn|
|Kiểm soát dữ liệu| Trung bình| Tốt|
|Chặn lỗi biên| Trung bình| Tốt|
|Xử lý đứt gãy| Cơ bản| Chi tiết|
|Khả năng mở rộng| Thấp| Cao|
## 5. Lựa chọn giải pháp tối ưu
Em chọn Giải pháp 2 vì có hướng dẫn cụ thể từng bước và quy định rõ cách xử lý khi dữ liệu bị thiếu hoặc không hợp lệ.

Mặc dù tốn nhiều token hơn nhưng giúp hạn chế sai sót và dễ kiểm soát quá trình xử lý đơn hàng.
## 6. Chạy thử và kết quả
**Dữ liệu đầu vào:**

Tin nhắn khách hàng:

"Chào shop, mình tên Minh, số điện thoại 0912345678. Mình muốn mua 2 áo thun basic và 1 bình nước inox."

Danh sách sản phẩm:
|Sản phẩm| Giá bán| Tồn kho|
|---|---|---|
|Áo thun basic| 150.000đ| 5|
|Bình nước inox| 90.000đ| 10|

**Kết quả AI trả về:**
```JSON
{
  "customer": {
    "name": "Minh",
    "phone": "0912345678",
    "address": null
  },
  "items": [
    {
      "product_name": "Áo thun basic",
      "quantity": 2,
      "unit_price": 150000,
      "total": 300000
    },
    {
      "product_name": "Bình nước inox",
      "quantity": 1,
      "unit_price": 90000,
      "total": 90000
    }
  ],
  "subtotal": 390000,
  "status": "NEED_INFO"
}
```
