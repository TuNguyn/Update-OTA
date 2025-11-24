ESP32 & ESP8266 PZEM-004T Power Monitor with OTA Update

Dự án giám sát điện năng tiêu thụ sử dụng cảm biến PZEM-004T v3.0, hỗ trợ cập nhật firmware từ xa (OTA) qua GitHub cho cả hai nền tảng ESP32 và ESP8266.

📌 Tính năng chính

Giám sát điện năng: Đo Vol, Ampe, Công suất, Tần số, Hệ số công suất, Điện năng tiêu thụ (kWh).

Lưu trữ thông minh: Tự động lưu dữ liệu khi mất điện, cộng dồn năng lượng chính xác.

Giao tiếp đa dạng:

Gửi dữ liệu chu kỳ (Cycle) qua HTTP POST.

Gửi dữ liệu thời gian thực (Realtime) qua Socket.IO.

OTA Update: Tự động cập nhật firmware mới từ GitHub Releases.

Đa nền tảng: Hỗ trợ code riêng biệt nhưng dùng chung cơ chế quản lý cho ESP32 và ESP8266.

3. Cấu hình Partition (Quan trọng cho OTA)

Để đảm bảo đủ bộ nhớ cho việc tải firmware OTA, bạn cần chọn Partition Scheme phù hợp:

Với ESP32: Tools -> Partition Scheme -> "Minimal SPIFFS (1.9MB APP with OTA/190KB SPIFFS)"

Với ESP8266: Tools -> Flash Size -> "4MB (FS:1MB OTA:~1019KB)"

☁️ Cấu hình GitHub cho OTA

Để tính năng tự động cập nhật hoạt động, bạn cần thiết lập một Repository trên GitHub như sau:

1. Tạo Repository

Tạo một Public Repository (ví dụ: Update-OTA).

2. Tạo file quản lý phiên bản

Tạo 2 file .json riêng biệt trên nhánh main để quản lý phiên bản cho từng loại chip:

File version.json (Cho ESP32):

{
  "version": "1.0.0",
  "url": "[https://github.com/USERNAME/REPO/releases/download/v1.0.0/firmware_esp32_v1.0.0.bin](https://github.com/USERNAME/REPO/releases/download/v1.0.0/firmware_esp32_v1.0.0.bin)"
}


File version_esp8266.json (Cho ESP8266):

{
  "version": "1.0.0",
  "url": "[https://github.com/USERNAME/REPO/releases/download/v1.0.0/firmware_esp8266_v1.0.0.bin](https://github.com/USERNAME/REPO/releases/download/v1.0.0/firmware_esp8266_v1.0.0.bin)"
}


(Thay USERNAME và REPO bằng thông tin thực tế của bạn)

🚀 Hướng dẫn Build & Phát hành bản cập nhật

Bước 1: Tăng phiên bản trong Code

Mở file ota_update.ino (hoặc file tương ứng), sửa dòng define version:

#define CURRENT_FIRMWARE_VERSION "1.0.1" // Tăng lên số mới


Bước 2: Xuất file Binary (.bin)

Trong Arduino IDE:

Chọn board (ESP32 hoặc ESP8266).

Vào menu Sketch -> Export Compiled Binary.

File .bin sẽ xuất hiện trong thư mục dự án.

Đổi tên file cho dễ nhớ (ví dụ: firmware_esp32_v1.0.1.bin).

Bước 3: Tạo Release trên GitHub

Vào tab Releases trên GitHub Repo.

Bấm Draft a new release.

Tag version: Đặt trùng với phiên bản (ví dụ v1.0.1).

Release title: Tiêu đề tùy ý.

Attach binaries: Kéo thả file .bin vừa tạo vào đây.

Bấm Publish release.

Bước 4: Cập nhật file JSON

Vào tab Code, mở file version.json (hoặc version_esp8266.json).

Sửa "version": "1.0.1".

Sửa "url" thành đường dẫn Download trực tiếp của file .bin trong Release vừa tạo (Chuột phải vào file trong Assets -> Copy link address).

Bấm Commit changes.

Bước 5: Tận hưởng

Sau khoảng 1-5 phút (do GitHub cache), khởi động lại thiết bị. Nó sẽ tự động tải bản mới về và cập nhật.

⚠️ Xử lý sự cố (Troubleshooting)

Lỗi

Nguyên nhân

Cách khắc phục

HTTP -104

Server trả về mã Redirect (302) nhưng Client không theo.

Đảm bảo code có dòng http.setFollowRedirects(...).

HTTP -102 (404)

Sai đường dẫn tải file.

Kiểm tra kỹ link .bin trong file version.json.

Space / Size Error

Không đủ bộ nhớ Flash.

Chọn lại Partition Scheme trong Tools (xem mục Cài đặt).



