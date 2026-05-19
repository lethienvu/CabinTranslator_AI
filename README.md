<div align="center">
  <img src="CabinTranslator_Logo.svg" alt="Cabin Translator Logo" width="100%" />
  <h1>Cabin Translator</h1>
  <p>Ứng dụng dịch nói thành văn bản và dịch thời gian thực trên desktop.</p>
</div>

# TÀI LIỆU THIẾT KẾ HỆ THỐNG: CABIN TRANSLATOR

## 1. Tổng quan
Cabin Translator là ứng dụng desktop hỗ trợ nhận dạng giọng nói, dịch thời gian thực và hiển thị văn bản trên giao diện overlay. Ứng dụng chạy hoàn toàn phía client (Tauri 2), kết hợp frontend (HTML/CSS/JS) và backend Rust, không có máy chủ trung gian. Khi cần dịch online, ứng dụng kết nối trực tiếp tới Soniox WebSocket bằng khóa API của người dùng. Khi dịch offline, ứng dụng chạy pipeline Python sử dụng MLX/Whisper/Gemma trên macOS Apple Silicon.

## 2. Mục tiêu và phạm vi
Mục tiêu
- Đối thoại một chiều hoặc hai chiều, độ trễ thấp.
- Hỗ trợ nhiều nguồn âm thanh: system, microphone, hoặc cả hai.
- TTS đọc bản dịch theo nhiều nhà cung cấp.
- Có thể chạy offline trên macOS Apple Silicon.

Không phạm vi
- Không có backend server lưu trữ dữ liệu người dùng.
- Không có tài khoản đăng nhập hoặc đồng bộ cloud.

## 3. Sơ đồ kiến trúc hệ thống

+---------------------------------------------------------------+
|                       Frontend (WebView)                      |
|  - UI overlay, transcript, settings, TTS control              |
|  - Soniox WebSocket client                                    |
|  - Audio playback (TTS)                                       |
+---------------------------+-----------------------------------+
                            | Tauri IPC (invoke, Channel)
+---------------------------+-----------------------------------+
|                       Backend Rust (Tauri)                    |
|  - Audio capture: system, microphone, both                    |
|  - Commands: settings, transcript, audio, local pipeline      |
|  - Edge TTS via Rust command                                  |
+---------------------------+-----------------------------------+
            |                               |
            |                               |
+-----------+-----------+         +---------+-------------------+
| Local Python pipeline |         | Soniox WebSocket (cloud)    |
| - local_pipeline.py   |         | wss://stt-rt.soniox.com      |
| - MLX/Whisper/Gemma   |         | stt-rt-v4, translate         |
+-----------------------+         +-----------------------------+

## 4. Thành phần chính
Frontend (src)
- index.html: giao diện overlay và settings.
- js/app.js: điều phối ứng dụng, bật tắt ghi âm, kết nối dịch.
- js/soniox.js: WebSocket client, gửi config, nhận kết quả.
- js/ui.js: hiển thị transcript, chế độ single/dual, placeholder.
- js/settings.js: đọc ghi settings qua Tauri IPC.
- js/edge-tts.js, google-tts.js, elevenlabs-tts.js: TTS.
- js/audio-player.js: phát audio TTS.

Backend Rust (src-tauri)
- src/lib.rs: đăng ký commands, khởi tạo state.
- commands/audio.rs: bắt đầu và dừng audio capture, forward IPC.
- commands/transcript.rs: lưu transcript ra file .md và mở thư mục.
- commands/settings.rs + settings.rs: lưu tải cấu hình JSON.
- commands/local_pipeline.rs: khởi động pipeline Python, gửi audio, nhận JSON.
- commands/edge_tts.rs: gọi Edge TTS.
- audio/*: system audio (ScreenCaptureKit/WASAPI), microphone (cpal).

Local pipeline (scripts)
- scripts/local_pipeline.py: nhận audio PCM, trả về JSON transcribe/dịch.
- setup_mlx.py: cài đặt MLX và các phụ thuộc.

## 5. Luồng dữ liệu
Luồng online (Soniox)
1) Frontend bắt đầu ghi âm qua command start_capture.
2) Backend Rust forward audio PCM sang frontend qua Channel.
3) Frontend gửi audio lên Soniox WebSocket.
4) Soniox trả về kết quả original/translation/provisional.
5) UI cập nhật transcript, TTS tùy chọn đọc bản dịch.

Luồng offline (Local)
1) Frontend bắt đầu ghi âm qua command start_capture.
2) Backend Rust khởi động local_pipeline.py và gửi audio.
3) Pipeline trả về JSON qua stdout và channel.
4) UI cập nhật transcript và TTS.

## 6. Use cases
Use Case 1: Dịch một chiều
- Tác nhân: người dùng cá nhân.
- Mô tả: dịch âm thanh từ system hoặc micro sang ngôn ngữ đích.
- Kết quả: hiển thị bản dịch và tùy chọn đọc TTS.

Use Case 2: Dịch hai chiều khi họp trực tuyến
- Tác nhân: người dùng tham gia Zoom/Meet/Teams.
- Mô tả: thu cả âm thanh hệ thống và micro, tự động dịch qua lại.
- Ghi chú: có thể tắt TTS để tránh vang âm.

Use Case 3: Đọc bản dịch bằng TTS
- Tác nhân: người dùng cần nghe phát âm.
- Mô tả: chọn Edge/Google/ElevenLabs, đọc bản dịch theo tốc độ.

Use Case 4: Thuật ngữ chuyên ngành tùy chỉnh
- Tác nhân: người dùng chuyên ngành.
- Mô tả: khai báo translation_terms để Soniox dịch chính xác.

Use Case 5: Dịch offline trên macOS Apple Silicon
- Tác nhân: người dùng macOS không có internet.
- Mô tả: dùng pipeline MLX/Whisper/Gemma để dịch tại máy.

## 7. Lưu trữ và cấu hình
- settings.json: lưu cấu hình tại ~/Library/Application Support/com.personal.translator/
- transcripts: lưu file .md theo thời gian trong thư mục app data.
- Khóa API lưu cục bộ, không gửi về server trung gian.

## 8. Bảo mật và quyền riêng tư
- Không có server trung gian, không yêu cầu tài khoản.
- Kết nối trực tiếp tới Soniox bằng khóa API của người dùng.
- Dữ liệu và khóa API lưu cục bộ.

## 9. Công nghệ sử dụng
- Tauri 2 (Rust + WebView)
- JavaScript, HTML, CSS
- Soniox WebSocket STT + Translation
- TTS: Edge, Google, ElevenLabs
- Local: Python, MLX, Whisper, Gemma (macOS Apple Silicon)

## 10. Giới hạn hiện tại
- Local mode chỉ hỗ trợ macOS Apple Silicon.
- Độ trễ phụ thuộc vào mạng và nhà cung cấp Soniox.

## 11. Cấu trúc thư mục
- src/ (frontend)
- src-tauri/ (backend Rust)
- scripts/ (local pipeline)
- CabinTranslator_Logo.svg (logo)

Bản quyền
Dự án này là sở hữu độc quyền. All rights reserved.
