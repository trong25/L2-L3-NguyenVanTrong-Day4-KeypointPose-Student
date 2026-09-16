# Báo cáo Kiểm chéo bài bạn cùng nhóm

Người gán: Trần Tuấn Anh (Nhóm 2)   Người kiểm: Nguyễn Văn Trọng   Ngày: 16/09/2026

## Reviewer Checklist

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | :---: | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ | Đạt 29 skeleton, đủ 17 điểm/người |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☑ | Đã kiểm tra qua `visualize_pose.py`, không còn đường xương chéo |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | Đạt, không có lỗi nhầm người |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☒ | Còn lỗi dùng `v = 0` ở hông và cổ tay |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☒ | Đã phát hiện một số khớp trong khung nhưng gán `v = 0` |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ | Đạt, không dùng phím tắt `h` |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ | Đạt chuẩn COCO Keypoints 1.0 |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | Đạt format YOLO Pose |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☑ | Đã so sánh đối chiếu visibility |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☑ | Đã đồng bộ vào `GUIDELINE_MINI.md` |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☑ | Định dạng chuẩn |

## Lỗi tìm được và kiến nghị sửa

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_02.jpg` | 1 | `left_ear` | Xoá khớp bị che / Thiếu khớp | Đổi cờ sang `v = 1` và đặt chấm ước lượng tại vị trí tai trái dưới tóc |
| `train_06.jpg` | 1 | `left_hip`, `right_hip` | Xoá khớp bị che (`v = 0`) | Chuyển sang `v = 1` và ước lượng tâm xương chậu ngang cạp quần |
| `train_11.jpg` | 1 | `right_wrist` | Lệch nhẹ khỏi khớp | Kéo chấm về đúng tâm khớp cổ tay tiếp giáp bàn tay |
| `train_12.jpg` | 1 | `left_knee`, `left_ankle` | Dùng `v = 0` khi khớp còn trong ảnh | Đổi sang `v = 1` do chân chỉ bị bàn che khuất chứ không ra ngoài khung ảnh |
| `train_15.jpg` | 2 | Các cặp chi trái / phải | Đảo trái/phải | Hoán đổi nhãn đối xứng theo hệ quy chiếu cơ thể người trong ảnh |

## Hai câu kết luận

- **Lỗi lặp đi lặp lại nhiều nhất của bài này:** Lỗi sử dụng cờ Outside (`v = 0`) thay vì Occluded (`v = 1`) cho các khớp bị che khuất nhưng vẫn còn nằm bên trong khung hình (đặc biệt ở khớp hông và các chi dưới bàn).
- **Nó là lỗi thao tác hay lỗi guideline chưa rõ:** Phần lớn là do **guideline ban đầu chưa nêu cụ thể cách xử lý trường hợp bị che bởi quần áo hoặc nội thất**, một phần do thói quen thao tác chọn nhanh phím tắt `o` thay vì `q`.
