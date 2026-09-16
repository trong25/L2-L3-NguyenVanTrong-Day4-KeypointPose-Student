# Visibility report

- Thư mục nhãn: `outputs\temp_student_labels`
- 20 ảnh, 28 skeleton, trung bình 13.25 khớp có v > 0 mỗi người
- Tổng: v=2 342 | v=1 29 | v=0 105

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 0 | 6 | 0% |
| 1 | left_eye | 21 | 0 | 7 | 0% |
| 2 | right_eye | 20 | 0 | 8 | 0% |
| 3 | left_ear | 17 | 1 | 10 | 4% |
| 4 | right_ear | 18 | 3 | 7 | 11% |
| 5 | left_shoulder | 26 | 1 | 1 | 4% |
| 6 | right_shoulder | 28 | 0 | 0 | 0% |
| 7 | left_elbow | 22 | 2 | 4 | 7% |
| 8 | right_elbow | 26 | 0 | 2 | 0% |
| 9 | left_wrist | 22 | 1 | 5 | 4% |
| 10 | right_wrist | 19 | 4 | 5 | 14% |
| 11 | left_hip | 20 | 3 | 5 | 11% |
| 12 | right_hip | 23 | 3 | 2 | 11% |
| 13 | left_knee | 17 | 1 | 10 | 4% |
| 14 | right_knee | 18 | 2 | 8 | 7% |
| 15 | left_ankle | 11 | 4 | 13 | 14% |
| 16 | right_ankle | 12 | 4 | 12 | 14% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
