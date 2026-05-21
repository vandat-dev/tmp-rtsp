# recordRTSP

Dự án này cung cấp các cách để ghi lại luồng RTSP từ camera (ví dụ: Hikvision) vào file MP4 bằng cách sử dụng FFmpeg.

## Yêu cầu hệ thống

- **FFmpeg**: Phải được cài đặt trên hệ thống của bạn.
    - **Ubuntu/Debian**: `sudo apt update && sudo apt install ffmpeg`
    - **CentOS/RHEL**: `sudo dnf install ffmpeg`
    - **macOS (Homebrew)**: `brew install ffmpeg`
    - **Windows**: Tải bản build từ [ffmpeg.org](https://ffmpeg.org/download.html) và thêm vào PATH.
- **Python 3**: (Tùy chọn) Nếu bạn muốn chạy bằng script Python.

## Cách sử dụng

### 1. Sử dụng Script Python

File `main.py` sử dụng thư viện `subprocess` của Python để gọi lệnh FFmpeg.

**Chạy lệnh:**
```bash
python main.py
```

Mặc định, script sẽ tạo ra một file có tên theo định dạng `record_<timestamp>.mp4`.

### 2. Sử dụng lệnh FFmpeg trực tiếp

Bạn có thể chạy lệnh FFmpeg trực tiếp từ terminal để ghi lại stream với tên file chứa ngày giờ hiện tại:

```bash
ffmpeg -rtsp_transport tcp \
-i "rtsp://admin:Rsc*33344@10.2.7.120:554/Streaming/channels/101" \
-c copy \
-fflags +genpts \
-movflags +faststart \
"/home/dat/PycharmProjects/recordRTSP/record_$(date +%Y%m%d_%H%M%S).mp4"
```

**Giải thích các tham số:**
- `-rtsp_transport tcp`: Sử dụng giao thức TCP thay vì UDP để đảm bảo tính ổn định (tránh mất gói tin).
- `-i "rtsp://..."`: URL của luồng RTSP đầu vào.
- `-c copy`: Copy trực tiếp dữ liệu stream mà không cần encode lại (giảm tải cho CPU và giữ nguyên chất lượng).
- `-fflags +genpts`: Tạo lại timestamp nếu stream gốc không có hoặc bị lỗi.
- `-movflags +faststart`: Đưa thông tin metadata lên đầu file MP4 để có thể xem được ngay cả khi file chưa tải xong (hữu ích cho việc phát trực tuyến).

## Lưu ý bảo mật

URL RTSP chứa thông tin đăng nhập (`admin:password`). Hãy cẩn thận khi chia sẻ file hoặc đẩy lên các kho lưu trữ công khai như GitHub.
