# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.21 khớp có v > 0 mỗi người
- Tổng: v=2 359 | v=1 111 | v=0 23

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 20 | 9 | 0 | 31% |
| 1 | left_eye | 20 | 9 | 0 | 31% |
| 2 | right_eye | 20 | 9 | 0 | 31% |
| 3 | left_ear | 19 | 10 | 0 | 34% |
| 4 | right_ear | 23 | 6 | 0 | 21% |
| 5 | left_shoulder | 27 | 2 | 0 | 7% |
| 6 | right_shoulder | 27 | 2 | 0 | 7% |
| 7 | left_elbow | 26 | 3 | 0 | 10% |
| 8 | right_elbow | 25 | 4 | 0 | 14% |
| 9 | left_wrist | 23 | 6 | 0 | 21% |
| 10 | right_wrist | 20 | 8 | 1 | 28% |
| 11 | left_hip | 24 | 5 | 0 | 17% |
| 12 | right_hip | 21 | 8 | 0 | 28% |
| 13 | left_knee | 21 | 5 | 3 | 17% |
| 14 | right_knee | 17 | 9 | 3 | 31% |
| 15 | left_ankle | 15 | 6 | 8 | 21% |
| 16 | right_ankle | 11 | 10 | 8 | 34% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
