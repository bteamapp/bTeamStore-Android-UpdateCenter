
### File: `update.json` (Đặt trên server)

Đây là file bạn sẽ chỉnh sửa thủ công mỗi khi có phiên bản mới.

```json
{
  "hasUpdate": true,
  "updateInfoUrl": "https://your-domain.com/path/to/update-info-v2.html",
  "title": "✨ Có bản cập nhật mới!",
  "message": "Một phiên bản mới với nhiều cải tiến đã sẵn sàng để bạn khám phá. Xem chi tiết bên dưới nhé!"
}
```

**Giải thích:**
*   `hasUpdate`: Đặt là `true` để kích hoạt popup, `false` để tắt.
*   `updateInfoUrl`: **Quan trọng nhất.** Đây là URL của trang thông tin cập nhật mà bạn muốn hiển thị trong iframe. Mỗi lần có version mới, bạn chỉ cần thay đổi URL này.
*   `title`, `message`: Tiêu đề và thông điệp ngắn gọn hiển thị phía trên iframe.

**Khi không có update, bạn có thể để file như sau:**
```json
{
  "hasUpdate": false
}
```


**Bước cuối: Kiểm tra**
1.  **Lần đầu chạy:** Đảm bảo file `update.json` trên server có `hasUpdate: true` và một `updateInfoUrl` hợp lệ. Mở app, bạn sẽ thấy một "viên thuốc" (pill) xuất hiện ở cạnh trên.
2.  **Tương tác:** Nhấn vào "viên thuốc" đó, nó sẽ mở rộng ra thành một popup lớn với hiệu ứng Glassmorphism, hiển thị iframe và nút đóng.
3.  **Đóng popup:** Nhấn nút "X" hoặc click ra ngoài vùng mờ. Popup sẽ đóng lại.
4.  **Mở lại app:** Sau khi đóng, hãy khởi động lại app. Popup sẽ không hiện ra nữa vì URL đã được lưu vào `localStorage`.
5.  **Để hiện lại (test version mới):** Vào server, thay đổi giá trị của `updateInfoUrl` trong file `update.json` thành một URL khác. Mở lại app, popup sẽ xuất hiện trở lại.

Chúc bạn thành công với app của mình! Giao diện này chắc chắn sẽ gây ấn tượng tốt với người dùng.
