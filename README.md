# Dashboard Nhiệt Ẩm QA (Dữ liệu hồi cứu)

Web app giao diện trực quan dành cho quản lý QA trong sản xuất/bảo quản dược phẩm:
- Đánh giá dữ liệu nhiệt độ - độ ẩm theo chu kỳ ghi 15 phút/lần.
- Dữ liệu mang tính **hồi cứu** (review hàng tuần), không phải realtime.
- Theo dõi KPI tuân thủ, xu hướng, và điểm vượt giới hạn (OOS/OOT).

## Tính năng chính
- KPI: tổng điểm đo, tỷ lệ đạt chuẩn, nhiệt độ/độ ẩm trung bình.
- Bộ lọc theo khu vực và khoảng ngày.
- Biểu đồ xu hướng (line chart) và biểu đồ tuân thủ (doughnut chart).
- Bảng cảnh báo các điểm vượt giới hạn.

## Gợi ý tích hợp Google Apps Script + Google Sheets
Bạn có thể xuất dữ liệu từ Sheet tổng hợp sang JSON bằng Web App của Apps Script, rồi gọi từ `index.html`.

Ví dụ `Code.gs`:

```javascript
function doGet() {
  const ss = SpreadsheetApp.openById('YOUR_SHEET_ID');
  const sh = ss.getSheetByName('TongHop_NhietAm');
  const values = sh.getDataRange().getValues();
  const headers = values.shift();

  const data = values.map(r => {
    const obj = {};
    headers.forEach((h, i) => obj[h] = r[i]);
    return {
      dt: Utilities.formatDate(new Date(obj.datetime), 'GMT+7', "yyyy-MM-dd'T'HH:mm:ss"),
      zone: obj.zone,
      temp: Number(obj.temp),
      hum: Number(obj.hum)
    };
  });

  return ContentService
    .createTextOutput(JSON.stringify({ data }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

Sau đó thay mảng `data` trong `index.html` bằng dữ liệu fetch từ URL Web App.

## Chạy local
Mở trực tiếp `index.html` bằng trình duyệt.
