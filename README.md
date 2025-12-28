# Xiaozhi_NTC_wallpaper

# 🎵 XiaoZhi ESP32 – Bản tích hợp Nhạc Việt (Online/SD/Radio) + ⏰ Báo thức + 🖼️ Hình nền

👤 **Tác giả: Nguyễn Thọ Chung**

---

## 🧩 1) Tổng quan dự án

📦 Dự án **XiaoZhi** sử dụng nền tảng **ESP-IDF**, có các đặc điểm nổi bật:

- 🤖 Trợ lý ảo chatbot/voice điều khiển thiết bị qua **MCP**
- 🖥️ Giao diện hiển thị sử dụng **LVGL** (nếu bật `HAVE_LVGL`)
- 🎶 Khối giải trí mạnh mẽ:
  - Phát nhạc **online có lời karaoke**
  - Phát nhạc từ **thẻ SD**
  - Nghe **radio Internet**
- ⏰ Tiện ích:
  - **Báo thức thông minh**
  - **Tự động đổi hình nền**

> ⚠️ **Không** tìm thấy mã nguồn xử lý cảm biến nhiệt độ NTC hoặc ghi log nhiệt trong project này.

---

## 🎼 2) Các tính năng có trong mã nguồn

### 2.1 🌐 Phát nhạc Online + lời Karaoke

📁 `main/boards/common/esp32_music.cc`, `esp32_music.h`

- Tải và phát nhạc từ Internet (HTTP)
- Hỗ trợ lyrics dạng **LRC karaoke**:
  - Đồng bộ theo timestamp
  - Thread xử lý riêng cho hiển thị karaoke
- Tuỳ chọn **tự động lưu cache nhạc** vào SD (`auto_cache_to_sd_`)

---

### 2.2 💾 Phát nhạc từ thẻ SD

📁 `esp32_sd_music.cc`, `esp32_sd_music.h`, `sd_mount.cc`

- Quét nhạc và phát từ SD card
- Playlist hỗ trợ JSON (`playlist.json`)
- Khi dùng lệnh “phát bài X”, hệ thống sẽ:
  - 🔍 Tìm trong SD → phát offline
  - ❌ Không có → fallback sang phát online

---

### 2.3 📻 Nghe radio Internet

📁 `esp32_radio.cc`, `esp32_radio.h`

- Phát stream audio HTTP/HTTPS
- Danh sách 16 kênh sẵn có:
  - `VOV1`, `VOV2`, `VOV_FM89`, `JOY_FM989`, ...
- Hỗ trợ phát từ URL hoặc mã lệnh MCP

---

### 2.4 ⏰ Báo thức (Alarm)

📁 `alarm_manager.cc`, `alarm_manager.h`

- Tạo nhiều báo thức với:
  - Giờ/phút, tên chuông, chế độ **lặp lại hằng ngày**
- Chuông có sẵn:
  - `ga.ogg`, `alarm1.ogg`, `iphone.ogg`
- Có thể dừng báo thức qua lệnh hoặc MCP

---

### 2.5 🖼️ Tự đổi hình nền

📁 `wallpaper_manager.cc`, `wallpaper_manager.h`, `application.cc`

- Danh sách ảnh nền: `1.png` → `7.png`
- Vị trí ảnh: `main/spiffs/1.png` … `7.png`
- Thời gian đổi ảnh: mặc định **180 giây**
- Hiệu ứng chuyển cảnh: `FadeBlack`

---

### 2.6 😎 Custom Emoji

📁 `custom_emoji/`

- Các biểu tượng cảm xúc riêng: 🔔, 🤖, ...
- Dạng ảnh nhị phân vẽ tay

---

## 🧠 3) Hệ điều khiển qua MCP Tools

📁 `main/mcp_server.cc`

### 📅 Báo thức
- `self.alarm.set(hour, minute, ringtone, repeat_daily)`
- `self.alarm.list`, `self.alarm.clear`, `self.alarm.stop`

### 🖼️ Hình nền
- `self.wallpaper.apply(index)`
- `self.wallpaper.set_interval(seconds)`

### 📻 Radio
- `self.radio.get_stations`, `play_station`, `play_url`, `stop`
- `self.radio.set_display_mode`

### 🎶 Nhạc SD
- Điều khiển:
  - `self.sdmusic.playback` (play/pause/stop/next/prev)
- Tìm kiếm:
  - `self.sdmusic.search`, `suggest`, `track`
- Thư viện:
  - `self.sdmusic.library`, `directory`, `reload`
- Cài đặt:
  - `self.sdmusic.genre`, `mode`, `shuffle`, `progress`

---

## 🗂️ 4) Cấu trúc mã nguồn

📌 Dự án dùng **.cc và .h** là chính. Một vài file `.cpp` vẫn tồn tại:

| Tệp | Ghi chú |
|-----|--------|
| `*.cc`, `*.h` | Gần như toàn bộ mã |
| `blufi.cpp` | Giao thức Bluetooth |
| `image_to_jpeg.cpp` | Chuyển ảnh JPG trong LVGL |

---

## ⚙️ 5) Hướng dẫn build (ESP-IDF)

```bash
# Cài đặt môi trường ESP-IDF
idf.py set-target esp32s3
idf.py build
idf.py flash monitor
