<div align="center">
  <img src="CabinTranslator_banner.png" alt="Cabin Translator Banner" width="100%" />
  <h1>Cabin Translator</h1>
  <p>Ứng dụng dịch nói thành văn bản và dịch thời gian thực trên desktop.</p>
</div>
# TÀI LIỆU THIẾT KẾ HỆ THỐNG: CABIN TRANSLATOR

## 1. TỔNG QUAN DỰ ÁN (INTRODUCTION)
Cabin Translator là một ứng dụng máy tính hỗ trợ dịch giọng nói theo thời gian thực (real-time speech translation) được phát triển dựa trên nền tảng Tauri [1]. Ứng dụng này có khả năng thu âm thanh trực tiếp từ hệ thống hoặc micro, chuyển đổi giọng nói thành văn bản, và hiển thị bản dịch trên một giao diện tối giản [1].

Điểm nổi bật của kiến trúc hệ thống là tính phi tập trung: ứng dụng không sử dụng bất kỳ máy chủ trung gian (server) nào của nhà phát triển, thay vào đó kết nối trực tiếp với các dịch vụ API thông qua khóa cá nhân của người dùng, hoặc chạy hoàn toàn ngoại tuyến [1, 2].

## 2. SƠ ĐỒ KIẾN TRÚC HỆ THỐNG (ARCHITECTURE DIAGRAM)
Hệ thống được thiết kế theo mô hình Client-Side hoàn toàn, kết hợp giữa Frontend (Giao diện web) và Backend (Rust) chạy trực tiếp trên máy tính người dùng [2].

+-----------------------------------------------------------------+
|                  GIAO DIỆN NGƯỜI DÙNG (FRONTEND)                |
|               (JavaScript, HTML, CSS, WebView)                  |
| - Hiển thị bản dịch (Chế độ Single/Dual, Smart Scroll)          |
| - Điều khiển kích thước chữ, hiển thị thuật ngữ chuyên ngành    |
+-----------------------------------------------------------------+
                                |
                   (Giao tiếp qua Tauri IPC)
                                |
+-----------------------------------------------------------------+
|                    CORE BACKEND (RUST - TAURI 2)                |
+-----------------------------------------------------------------+
        |                       |                       |
+---------------+       +---------------+       +---------------+
| MODULE AUDIO  |       | MODULE DỊCH   |       | MODULE TTS    |
| (Thu âm thanh)|       | (Xử lý ngôn   |       | (Phát giọng   |
|               |       |  ngữ)         |       |  nói)         |
+---------------+       +---------------+       +---------------+
| - ScreenCap-  |       | ONLINE:       |       | - Edge TTS    |
|   tureKit     |       | - API Soniox  |       | - Google      |
|   (macOS)     |       |               |       |   Chirp 3 HD  |
| - WASAPI      |       | OFFLINE:      |       | - ElevenLabs  |
|   (Windows)   |       | - MLX, Whisper|       |               |
| - cpal (Mic)  |       |   Gemma (Mac) |       |               |
+---------------+       +---------------+       +---------------+
                                |
+-----------------------------------------------------------------+
|                       LƯU TRỮ CỤC BỘ (LOCAL)                    |
| - Tệp tin văn bản lưu lịch sử hội thoại (.md)                   |
| - Khóa API và cấu hình người dùng                               |
+-----------------------------------------------------------------+

## 3. CÁC CA SỬ DỤNG (USE CASES)

### Use Case 1: Dịch hội thoại một chiều (One-way Translation)
- Tác nhân: Người dùng cá nhân.
- Mô tả: Người dùng nói hoặc nghe một ngôn ngữ (hỗ trợ hơn 70 ngôn ngữ gốc) và hệ thống dịch sang một ngôn ngữ đích [1, 3].
- Quy trình: Hệ thống thu âm từ nguồn đã chọn, gửi đến API xử lý, và hiển thị văn bản dịch lên màn hình với độ trễ từ 2-3 giây [1].

### Use Case 2: Dịch hội thoại hai chiều (Two-way Translation) cho họp trực tuyến
- Tác nhân: Người dùng tham gia video call (Zoom, Google Meet, MS Teams).
- Mô tả: Hệ thống tự động phát hiện người đang nói thuộc ngôn ngữ nào trong hai ngôn ngữ được thiết lập (ví dụ: tiếng Việt và tiếng Nhật) và tự động dịch chéo qua lại [3].
- Quy trình: Thu âm thanh từ cả Hệ thống và Micro. Tính năng đọc văn bản (TTS) tự động bị vô hiệu hóa để tránh hiện tượng dội âm thanh (feedback loop) [3].

### Use Case 3: Đọc bản dịch (TTS Narration)
- Tác nhân: Người dùng cần nghe phát âm bản dịch.
- Mô tả: Hệ thống đọc to văn bản đã dịch trong chế độ một chiều [4].
- Quy trình: Người dùng chọn 1 trong 3 nhà cung cấp TTS (Edge, Google, hoặc ElevenLabs), có thể điều chỉnh tốc độ đọc (với Edge và Google) [4].

### Use Case 4: Áp dụng thuật ngữ tùy chỉnh (Custom Translation Terms)
- Tác nhân: Chuyên gia trong lĩnh vực đặc thù (y tế, tôn giáo, kỹ thuật).
- Mô tả: Định nghĩa sẵn cách hệ thống dịch một số từ vựng chuyên ngành cụ thể [4].
- Quy trình: Người dùng thêm thuật ngữ trong phần Cài đặt, hệ thống sẽ tự động đối chiếu và áp dụng trong quá trình dịch thời gian thực [4].

### Use Case 5: Dịch thuật ngoại tuyến (Local Mode)
- Tác nhân: Người dùng sử dụng máy Mac sử dụng chip Apple Silicon không có kết nối mạng.
- Mô tả: Dịch các ngôn ngữ Nhật, Anh, Trung, Hàn sang Việt, Anh ngay trên thiết bị [2].
- Quy trình: Kích hoạt Local Mode, hệ thống sử dụng sức mạnh tính toán cục bộ (MLX, Whisper, Gemma) mà không cần gọi API bên ngoài [2].

## 4. NGĂN XẾP CÔNG NGHỆ (TECH STACK)
Dự án được cấu thành từ các ngôn ngữ lập trình chính bao gồm JavaScript (46.7%), Rust (19.8%), CSS (12.8%), HTML (12.8%) và Python (7.9%) [5]. Chi tiết các thành phần:

- Nền tảng lõi: Tauri 2 (Frontend dùng WebView, Backend dùng Rust) [2].
- Xử lý âm thanh đầu vào:
  - ScreenCaptureKit: Thu âm thanh hệ thống trên macOS [2].
  - WASAPI: Thu âm thanh hệ thống trên Windows [2].
  - cpal: API thu âm Micro đa nền tảng [2].
- Xử lý nhận dạng và dịch thuật (STT & Translation): 
  - Đám mây: API Soniox [2].
  - Cục bộ: Mô hình MLX, Whisper, Gemma [2].
- Chuyển văn bản thành giọng nói (TTS): Edge TTS, Google Cloud TTS, ElevenLabs [2].

## 5. BẢO MẬT VÀ QUYỀN RIÊNG TƯ (SECURITY & PRIVACY)
Hệ thống được thiết kế với ưu tiên cao nhất về quyền riêng tư dữ liệu:
- Kiến trúc không máy chủ (Zero Server): Ứng dụng kết nối trực tiếp đến các API mà người dùng cấu hình, không qua bất kỳ máy chủ trung gian nào [2].
- Lưu trữ cục bộ: Khóa API cá nhân và toàn bộ bản tóm tắt phiên dịch (lưu dưới dạng tệp .md) đều chỉ tồn tại trên thiết bị của người dùng [2].
- Không theo dõi: Hệ thống không yêu cầu tạo tài khoản, không thu thập dữ liệu sử 