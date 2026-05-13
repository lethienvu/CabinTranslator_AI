<div align="center">
  <img src="CabinTranslator_Logo.png" alt="Cabin Translator Logo" width="100%" />
  <h1>Cabin Translator</h1>
  <p>Ứng dụng dịch nói thành văn bản và dịch thời gian thực trên desktop.</p>
</div>

Tài liệu thiết kế hệ thống

1. Mục tiêu và phạm vi
- Ứng dụng desktop chạy trên Tauri 2.0.
- Thu âm hệ thống và micro, chuyển thành văn bản, dịch theo thời gian thực.
- Hiển thị văn bản gốc và bản dịch trong giao diện trong suốt.
- Hỗ trợ đọc bản dịch bằng nhiều nhà cung cấp TTS.

2. Yêu cầu chức năng
- Thu âm hệ thống, micro hoặc cả hai.
- Kết nối Soniox WebSocket để STT và dịch theo thời gian thực.
- Hỗ trợ chế độ dịch một chiều và hai chiều.
- Hỗ trợ context cho Soniox (general, terms, text, translation_terms).
- Hỗ trợ TTS: Edge TTS (proxy qua Rust), ElevenLabs, Google TTS.
- Lưu settings và transcript theo phiên.
- Chế độ local pipeline với Python sidecar (tùy chọn).

3. Yêu cầu phi chức năng
- Độ trễ thấp, xử lý liên tục.
- Ổn định kết nối (reconnect và reset session).
- Giao diện nhẹ, luôn trên cùng, có chế độ compact.
- Lưu trữ an toàn trong thư mục cấu hình và dữ liệu ứng dụng.

4. Kiến trúc tổng thể
Frontend chạy trong WebView, backend Rust xử lý thu âm, settings, lưu transcript và proxy Edge TTS.

Sơ đồ kiến trúc hệ thống
```mermaid
flowchart LR
  A[Audio nguồn: System hoặc Microphone] --> B[Rust Audio Capture]
  B --> C[PCM s16le 16kHz mono]
  C --> D[Frontend JS - Soniox WebSocket]
  D --> E[Soniox STT và Dịch]
  E --> F[UI Transcript]
  F --> G[TTS Provider]
  G --> H[AudioPlayer]

  subgraph Backend Tauri (Rust)
    B
    I[Settings API]
    J[Transcript API]
    K[Edge TTS Proxy]
    L[Local Pipeline Manager]
  end

  subgraph Frontend (WebView)
    D
    F
    G
    H
  end

  I --> F
  J --> F
  K --> G
  L --> D
```

5. Luồng dữ liệu chính
5.1. Audio capture
- Nguồn âm thanh: system, microphone, hoặc cả hai.
- Rust thu âm, resample về PCM s16le 16kHz mono.
- Dữ liệu được gửi sang frontend qua Tauri IPC channel.

5.2. STT và dịch
- Frontend mở kết nối WebSocket tới Soniox.
- Gửi audio liên tục và nhận token, văn bản gốc, bản dịch.
- Hỗ trợ gợi ý ngôn ngữ, chế độ one-way và two-way.
- Có cơ chế reconnect và reset session để duy trì kết nối dài.

5.3. TTS
- Edge TTS: frontend gọi command edge_tts_speak trong Rust; Rust proxy WebSocket, trả về base64 MP3.
- ElevenLabs và Google TTS: frontend gọi API và phát lại bằng AudioPlayer.
- AudioPlayer quản lý hàng đợi để phát mượt.

5.4. Local pipeline (tùy chọn)
- Rust chạy Python sidecar (scripts/local_pipeline.py) khi chọn chế độ local.
- Audio được gửi sang pipeline và nhận kết quả qua IPC channel.
- Script setup MLX (scripts/setup_mlx.py) để cài môi trường.

6. Use case
6.1. Dịch thời gian thực từ âm thanh hệ thống
- Người dùng chọn nguồn system và nhấn Start.
- Ứng dụng thu âm, gửi tới Soniox, hiển thị văn bản gốc và bản dịch.

6.2. Dịch thời gian thực từ micro
- Người dùng chọn nguồn microphone và nhấn Start.
- Ứng dụng thu âm micro, gửi tới Soniox, hiển thị văn bản gốc và bản dịch.

6.3. Dịch song song hệ thống và micro
- Người dùng chọn nguồn both.
- Ứng dụng trộn dữ liệu thu âm, gửi tới Soniox, hiển thị kết quả theo luồng.

6.4. Dịch hai chiều
- Người dùng chọn chế độ two-way, cấu hình Language A và Language B.
- Soniox trả về văn bản gốc và bản dịch theo hướng phù hợp.

6.5. Đọc bản dịch bằng TTS
- Người dùng bật TTS và chọn nhà cung cấp.
- Ứng dụng phát âm thanh bản dịch theo thời gian thực.

6.6. Chạy local pipeline
- Người dùng chọn chế độ local và khởi động pipeline.
- Python sidecar xử lý và trả kết quả về giao diện.

7. Thành phần hệ thống theo source code
7.1. Frontend
- src/js/app.js: App controller, điều phối settings, UI, audio, STT, TTS.
- src/js/ui.js: hiển thị transcript và trạng thái.
- src/js/soniox.js: Soniox WebSocket client, quản lý session, keepalive.
- src/js/edge-tts.js: TTS Edge thông qua Rust.
- src/js/elevenlabs-tts.js, src/js/google-tts.js: TTS cloud.
- src/js/audio-player.js: phát audio dạng hàng đợi.
- src/js/settings.js: quản lý settings qua Tauri IPC.

7.2. Backend Tauri
- src-tauri/src/lib.rs: khởi tạo Tauri, đăng ký commands và state.
- src-tauri/src/commands/audio.rs: bật tắt thu âm, gom nhiều nguồn.
- src-tauri/src/audio/system_audio.rs: thu âm hệ thống (macOS).
- src-tauri/src/audio/microphone.rs: thu âm micro (cpal), resample.
- src-tauri/src/commands/edge_tts.rs: proxy Edge TTS.
- src-tauri/src/commands/local_pipeline.rs: quản lý Python sidecar.
- src-tauri/src/commands/transcript.rs: lưu và mở thư mục transcript.
- src-tauri/src/commands/settings.rs: đọc ghi settings.

8. Lưu trữ và cấu hình
- Settings lưu ở config dir: com.personal.translator/settings.json.
- Transcript lưu ở app data dir, theo timestamp, định dạng .md.

9. Cấu trúc thư mục
AI_CabinTranslator/
- src/
  - index.html
  - styles/main.css
  - js/
- src-tauri/
  - src/
  - tauri.conf.json
  - Cargo.toml
- scripts/
  - local_pipeline.py
  - setup_mlx.py
- docs/
- cabinTranslator_banner.png

Bản quyền
Dự án này là sở hữu độc quyền. All rights reserved.
