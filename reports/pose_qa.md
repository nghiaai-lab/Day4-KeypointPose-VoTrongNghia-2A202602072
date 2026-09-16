# Rà soát ảnh phủ pose - 20 ảnh train

Ngày rà soát: 2026-09-16. Nguồn nhãn: `annotations/coco_keypoints/person_keypoints_default.json`.

Đã tạo ảnh phủ bằng `tools/visualize_pose.py` và mở cả 20 ảnh. Đã phóng to các ca
`train_06`, `train_07`, `train_09`, `train_11`, `train_14`, `train_15`, `train_16`,
`train_19` để kiểm tra các điểm khó. Màu xanh là bên trái cơ thể, cam là bên phải;
vàng biểu thị khớp bị che nhưng vẫn có tọa độ.

| Ảnh | Người | Kết quả lượt hình dáng |
| --- | ---: | --- |
| `train_01` | 2 | Hai đầu gối người thứ 1 nằm ngoài mép dưới: đổi sang `v=0`. |
| `train_02` | 1 | Không thấy đường nối lẫn sang người khác hoặc đảo bên rõ ràng. |
| `train_03` | 2 | Sau đối chiếu gold, sửa cổ tay phải và chân phải người thứ 2; điểm cũ trôi sang vùng người bên cạnh/xe đạp. |
| `train_04` | 2 | Mũ che mặt: các điểm mặt thành `v=1`; cổ tay phải người thứ 2 ra ngoài mép dưới: `v=0`. |
| `train_05` | 1 | Không thấy lỗi hình dáng nổi bật. |
| `train_06` | 1 | Sửa mắt/tai và hông trái-phải theo người quay lưng; đưa tay phải từ thùng xe về vị trí ước lượng trên người với `v=1`. |
| `train_07` | 1 | Hai mắt cá ở ngoài mép dưới ảnh, giữ `v=0`. |
| `train_08` | 1 | Các điểm bị xe che vẫn có tọa độ `v=1`; không thấy đường nối lẫn người. |
| `train_09` | 1 | Sửa vai/hông trái-phải của người quay lưng; cổ tay phải được ước lượng lại với `v=1`. |
| `train_10` | 1 | Nửa dưới cơ thể ra ngoài khung, giữ bốn khớp chân `v=0`. |
| `train_11` | 1 | Hai đầu gối sau mèo/bàn còn trong khung: ước lượng với `v=1`; mắt cá ngoài khung giữ `v=0`. |
| `train_12` | 1 | Điểm chân bị xe/hàng che vẫn có tọa độ; không thấy lỗi hình dáng nổi bật. |
| `train_13` | 3 | Đảo lại toàn bộ cặp trái/phải ở người nhỏ phía mép trái (người thứ 3). |
| `train_14` | 2 | Người cao quay lưng: mặt `v=1`, đảo ước lượng mắt/tai theo phía cơ thể. |
| `train_15` | 2 | Mặt người thứ 1 bị mũ kín che: đổi thành `v=1`. Sau đối chiếu gold, sửa tay trái đang trôi xuống xe và chỉnh lại hông/chân. |
| `train_16` | 2 | Hai người nhảy có xương tay/chân riêng; không thấy nhầm người ở vùng chồng lấn. |
| `train_17` | 1 | Không thấy lỗi hình dáng nổi bật. |
| `train_18` | 1 | Không thấy lỗi hình dáng nổi bật. |
| `train_19` | 2 | Hai người tách biệt; người áo trắng nhìn ngang, các bên khớp theo cơ thể. |
| `train_20` | 1 | Các điểm bị xe chắn giữ `v=1`; không thấy lỗi hình dáng nổi bật. |

Sau khi đối chiếu gold và tạo lại 20 file YOLO Pose từ bản COCO đã sửa,
`check_pose_labels.py` đạt định dạng: **20/20 ảnh, 29 skeleton, 359 điểm `v=2`,
111 điểm `v=1`, 23 điểm `v=0`**. `train_01` người thứ 1 cũng đã được sửa cổ tay
trái từ phía người thứ 2 về đúng tay và chỉnh lại khuỷu/hông.
Bảng theo từng khớp nằm trong `reports/visibility_report.md` và
`outputs/visibility_report.json`.

Script còn một cảnh báo tại `train_10`: bốn khớp chân `v=0` dù bounding box không
chạm mép ảnh. Đã mở ảnh kiểm tra: ảnh chỉ chứa thân trên của người đang cúi trên
xe, còn đầu gối và mắt cá nằm ngoài mép dưới. Đây là cảnh báo từ quy tắc suy đoán
theo bounding box, không phải lỗi visibility của bốn khớp đó.

Ảnh mẫu của ba ca mơ hồ: [train_06](examples/train_06_overlay.jpg),
[train_11](examples/train_11_overlay.jpg), [train_14](examples/train_14_overlay.jpg).

Lượt chấm gold sau rework: OKS trung bình **0.8500**, OKS@0.50 **1.0000**,
OKS@0.75 **0.8966**; không còn cảnh báo đảo trái/phải, nhầm người, trượt hẳn,
hoặc xoá khớp bị che. Các khớp gold đặt `v=0` không được tự động xoá khỏi nhãn:
chỉ dùng `v=0` khi khớp nằm ngoài khung ảnh hoặc không có căn cứ vị trí.
