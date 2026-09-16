# Mini guideline - lượt rà soát 20 ảnh | 2026-09-16

Các quyết định dưới đây được ghi trong lượt kiểm ảnh phủ của 20 ảnh train. Màu xanh
là bên trái cơ thể, màu cam là bên phải, màu vàng là khớp bị che (`v=1`).

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Ước lượng khớp ở ranh thân và chân; dùng `v=1` nếu chỉ có thể suy ra từ tư thế, `v=2` nếu vị trí khớp nhận ra rõ. | Quần áo che mốc giải phẫu nhưng không làm khớp biến mất. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Phần tai còn nhìn thấy rõ thì `v=2`; tai không nhìn thấy thì ước lượng `v=1`. | Visibility mô tả khớp thật, không mô tả đường viền mũ. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp nằm ngoài khung là `(0,0,0)`; khớp bị vật che nhưng còn trong khung là `v=1` và có tọa độ. | Phân biệt ra ngoài ảnh với bị che. |
| Cổ tay nằm sau tay lái / sau thân mình | Đặt `v=1` gần vị trí giải phẫu suy từ khuỷu tay và bàn tay; không đặt điểm lên xe. | Tránh khớp trôi sang vật thể khác. |
| Hai người chồng lên nhau | Hoàn tất từng skeleton theo một người; khớp bị người khác che là `v=1`. | Tránh kéo xương sang cơ thể bên cạnh. |
| Người nhỏ đến mức nào thì không gán nữa | Gán tất cả người được nhận diện trong 20 ảnh này; không đặt một ngưỡng kích thước mới. | README xác định 20 ảnh đã được chọn để mọi người đủ lớn để gán. |

Ảnh mẫu từ lượt kiểm ảnh phủ: [người đội mũ quay lưng](reports/examples/train_06_overlay.jpg),
[người bị mèo và bàn che](reports/examples/train_11_overlay.jpg),
[người quay lưng dưới ô](reports/examples/train_14_overlay.jpg).
Đây là ảnh phủ từ `visualize_pose.py`, không phải screenshot CVAT.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_06`, người thứ 1, tay phải và đầu

- Mơ hồ ở chỗ nào: người lái quay lưng, mũ che mặt và cánh tay phải nằm sau thân/xe.
- Bạn quyết thế nào: chấm mặt và tay phải với `v=1`, đưa vai/khuỷu/cổ tay phải về trên cơ thể; đảo lại ước lượng mắt/tai và hông cho đúng trái/phải theo cơ thể.
- Vì sao: điểm cũ của tay phải nằm trên thùng xe, còn hông nối chéo sang chân đối diện.
- Nếu người khác quyết ngược lại thì model học sai cái gì: tay dính vào thùng xe và chân trái/phải bị đảo.

### Ca 2 - ảnh `train_11`, người thứ 1, hai đầu gối

- Mơ hồ ở chỗ nào: thân dưới bị mèo và bàn che, nhưng đầu gối dự kiến vẫn nằm trong khung.
- Bạn quyết thế nào: ước lượng hai đầu gối với `v=1`; mắt cá vẫn `v=0` vì nằm ngoài khung.
- Vì sao: `v=0` ở đầu gối sẽ xóa khớp bị che khỏi nhãn.
- Nếu người khác quyết ngược lại thì model học sai cái gì: mô hình không học vị trí đầu gối khi có vật che phía trước.

### Ca 3 - ảnh `train_14`, người thứ 2 (người cao), mặt

- Mơ hồ ở chỗ nào: người cao quay lưng; các chấm mặt ban đầu được đặt như người nhìn vào máy ảnh.
- Bạn quyết thế nào: ước lượng mặt với `v=1`, đổi trái/phải của mắt và tai theo hướng cơ thể.
- Vì sao: không nhìn thấy các khớp mặt nhưng chúng vẫn nằm trong khung.
- Nếu người khác quyết ngược lại thì model học sai cái gì: mặt được xem là nhìn thấy rõ và trái/phải không nhất quán với vai/hông.

## 4. Trạng thái kiểm chéo

- Không thực hiện kiểm tra chéo với bạn cùng nhóm (theo người gán cung cấp).
- Không có bảng thứ hai để chạy `--compare`, nên không tính tỷ lệ chênh lệch hoặc bổ sung luật dựa trên một cuộc thống nhất không diễn ra.
- Báo cáo visibility của lượt này nằm ở `reports/visibility_report.md`.
