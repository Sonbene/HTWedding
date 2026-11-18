# 📋 HƯỚNG DẪN KẾT NỐI GOOGLE SHEETS (2 PHÚT)
### Nhận Lời Chúc & Xác Nhận Tham Dự (RSVP) — Đám Cưới Trung Hiếu & Hoài Thương

Tất cả lời chúc mừng trong **Sổ lưu bút** và thông tin khách đăng ký **Xác nhận tham dự (RSVP)** sẽ tự động bay thẳng vào file Google Trang Tính (Google Sheets) của bạn hoàn toàn tự động và miễn phí trọn đời.

---

## 📌 BƯỚC 1: TẠO TRANG TÍNH GOOGLE SHEETS
1. Mở trình duyệt và truy cập: [https://sheets.new](https://sheets.new) để tạo một bảng tính Google Sheets mới.
2. Đặt tên bảng tính ở góc trên bên trái: **"Khách Mời Đám Cưới Trung Hiếu & Hoài Thương"**.
3. Tại **Hàng 1 (Dòng tiêu đề)**, nhập tên cho 7 cột từ A đến G như sau:
   * Cột **A**: `Thời Gian`
   * Cột **B**: `Phân Loại` *(Lời chúc / Xác nhận RSVP)*
   * Cột **C**: `Họ Và Tên`
   * Cột **D**: `Lời Chúc / Lời Nhắn Gửi`
   * Cột **E**: `Sự Kiện Tham Dự` *(Tiệc cơm 28/11 / Vu Quy 29/11 / Cả hai)*
   * Cột **F**: `Số Lượng Khách`
   * Cột **G**: `Số Điện Thoại / Zalo`

---

## 📌 BƯỚC 2: MỞ APPS SCRIPT VÀ DÁN CODE
1. Trên thanh menu trên cùng của Google Sheets, chọn:  
   👉 **Tiện ích mở rộng (Extensions)** ➔ **Apps Script**.
2. Một tab mới sẽ mở ra. Xóa sạch mọi đoạn code mặc định có sẵn trong ô soạn thảo, sau đó **sao chép toàn bộ đoạn mã bên dưới và dán vào**:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data;
    
    if (e.postData && e.postData.contents) {
      data = JSON.parse(e.postData.contents);
    } else if (e.parameter) {
      data = e.parameter;
    } else {
      data = {};
    }
    
    var time = data.time || new Date().toLocaleString('vi-VN');
    var type = data.type || 'Không xác định';
    var name = data.name || '';
    
    if (type === 'Lời chúc') {
      // Ghi hàng mới cho Lời Chúc: [Thời gian, Phân loại, Tên, Lời chúc, '', '', '']
      sheet.appendRow([
        time,
        type,
        name,
        data.message || '',
        '',
        '',
        ''
      ]);
    } else if (type === 'Xác nhận RSVP') {
      // Ghi hàng mới cho RSVP: [Thời gian, Phân loại, Tên, Lời nhắn, Sự kiện, Số khách, Số điện thoại]
      sheet.appendRow([
        time,
        type,
        name,
        data.note || '',
        data.event || '',
        data.guests || '1',
        data.phone || ''
      ]);
    } else {
      sheet.appendRow([time, type, name, JSON.stringify(data), '', '', '']);
    }

    return ContentService.createTextOutput(JSON.stringify({ result: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService.createTextOutput(JSON.stringify({ result: 'error', error: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService.createTextOutput(JSON.stringify({ status: 'active', message: 'Wedding Webhook Ready' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

3. Bấm biểu tượng chiếc đĩa mềm **Lưu (Save 💾)** hoặc nhấn phím `Ctrl + S`.

---

## 📌 BƯỚC 3: TRIỂN KHAI (DEPLOY) LẤY LINK WEB APP
1. Ở góc trên cùng bên phải giao diện Apps Script, bấm nút **Triển khai (Deploy)** ➔ chọn **Tùy chọn triển khai mới (New deployment)**.
2. Bấm vào biểu tượng bánh răng ⚙️ ở góc trái (bên cạnh dòng "Chọn loại"), chọn **Ứng dụng web (Web app)**.
3. Điền các trường thông số:
   * **Mô tả (Description):** `Nhận phản hồi đám cưới Hiếu Thương`
   * **Thực thi dưới dạng (Execute as):** `Tôi (Email của bạn)`
   * **Người có quyền truy cập (Who has access):** **Bất kỳ ai (Anyone)**  
     *(⚠️ Cực kỳ quan trọng: Chọn "Bất kỳ ai" để khách mời vào trang web không cần phải đăng nhập tài khoản Google vẫn gửi được)*.
4. Bấm nút màu xanh **Triển khai (Deploy)**.
   * *Nếu Google hiển thị bảng cảnh báo cấp quyền: Bấm "Ủy quyền truy cập" (Authorize access) ➔ Chọn tài khoản Google của bạn ➔ Bấm vào chữ "Nâng cao" (Advanced) ➔ Bấm "Đi tới... (không an toàn)" ➔ Bấm "Cho phép" (Allow)*.
5. Google sẽ hiện hộp thoại thông báo thành công và cấp cho bạn một **URL ứng dụng web** có dạng:  
   `https://script.google.com/macros/s/AKfycbx...abc123.../exec`
6. Bấm nút **Sao chép (Copy)** đường link này.

---

## 📌 BƯỚC 4: DÁN ĐƯỜNG LINK VÀO WEBSITE CỦA BẠN
1. Mở file [script.js](file:///d:/Project/forfun/webwedding/script.js).
2. Tại dòng 14, dán đường link bạn vừa copy vào giữa hai dấu nháy đơn của biến `GOOGLE_SHEET_URL`:
   ```javascript
   const GOOGLE_SHEET_URL = 'https://script.google.com/macros/s/AKfycbx...abc123.../exec';
   ```
3. Lưu file `script.js` lại.

---

## 🎉 KIỂM TRA KẾT QUẢ
* Mở trang web [index.html](file:///d:/Project/forfun/webwedding/index.html).
* Thử nhập một lời chúc ở mục **Sổ Lưu Bút** và bấm gửi, hoặc điền form **Xác Nhận Tham Dự (RSVP)**.
* Mở lại file Google Sheet: Bạn sẽ thấy ngay lập tức xuất hiện một hàng mới với đầy đủ thông tin thời gian, tên khách, số điện thoại và nội dung!
