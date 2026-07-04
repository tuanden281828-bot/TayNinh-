# Kết nối form đặt tour → Google Sheet

Trang `index.html` đã sẵn sàng gửi dữ liệu form (Họ tên, SĐT, số khách, ngày khởi hành, nguồn) lên Google Sheet qua Google Apps Script. Bạn cần tạo Sheet + Script 1 lần (khoảng 3 phút), vì bước này cần tài khoản Google của bạn nên mình không thể tạo thay được.

## Bước 1: Tạo Google Sheet
1. Mở Google Drive: https://drive.google.com/drive/u/0/home
2. Bấm **+ Mới** → **Google Trang tính** → **Trang tính trống**.
3. Đổi tên file thành: `V Holiday Tour - Dat Tour Tay Ninh`.
4. Ở dòng 1, nhập các cột tiêu đề (theo đúng thứ tự):

   ```
   thoi_gian | ho_ten | so_dien_thoai | so_khach | ngay_khoi_hanh | nguon | trang
   ```

## Bước 2: Tạo Apps Script
1. Trên thanh menu của Sheet: **Tiện ích mở rộng (Extensions) → Apps Script**.
2. Xoá hết code mẫu, dán đoạn code sau vào:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var p = e.parameter;
  sheet.appendRow([
    p.thoi_gian || new Date(),
    p.ho_ten || '',
    p.so_dien_thoai || '',
    p.so_khach || '',
    p.ngay_khoi_hanh || '',
    p.nguon || '',
    p.trang || ''
  ]);
  return ContentService
    .createTextOutput(JSON.stringify({ result: 'success' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

3. Bấm biểu tượng 💾 **Lưu** (đặt tên project tuỳ ý, ví dụ "Form Tay Ninh").

## Bước 3: Triển khai (Deploy) thành Web App
1. Bấm **Deploy (Triển khai) → New deployment (Bản triển khai mới)**.
2. Ở mục "Select type", chọn **Web app**.
3. Điền:
   - Description: `Form đặt tour Tây Ninh`
   - Execute as: **Me (bạn)**
   - Who has access: **Anyone (Bất kỳ ai)**
4. Bấm **Deploy**. Google sẽ yêu cầu cấp quyền lần đầu — bấm **Authorize access**, chọn tài khoản Google của bạn, bấm **Advanced → Go to ... (unsafe)** rồi **Allow** (đây là project riêng của bạn nên an toàn).
5. Sau khi deploy xong, copy **Web app URL** — có dạng:
   ```
   https://script.google.com/macros/s/XXXXXXXXXXXXXXXXXXXXXXXX/exec
   ```

## Bước 4: Gắn URL vào trang web
Mở file `index.html`, tìm dòng:

```javascript
var GOOGLE_SCRIPT_URL = '';
```

Dán URL vừa copy vào giữa hai dấu nháy đơn, ví dụ:

```javascript
var GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/XXXXXXXXXXXXXXXXXXXXXXXX/exec';
```

Lưu file, commit và push lại lên GitHub (hoặc gửi mình URL, mình sẽ gắn và đẩy lên giúp bạn).

## Kiểm tra
Mở trang, điền thử 1 form bất kỳ (Hero / form cuối trang / popup thoát trang) và bấm gửi. Sau vài giây, mở lại Google Sheet — sẽ thấy 1 dòng dữ liệu mới xuất hiện. Nếu chưa thấy, kiểm tra lại URL đã dán đúng và đã Deploy ở chế độ "Anyone" chưa.

> Lưu ý: mỗi lần sửa code trong Apps Script, bạn cần **Deploy → Manage deployments → Edit (biểu tượng bút chì) → New version → Deploy** thì thay đổi mới có hiệu lực trên URL cũ.
