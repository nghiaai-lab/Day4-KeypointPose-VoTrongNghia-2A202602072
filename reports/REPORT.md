# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Võ Trọng Nghĩa | Nhóm: chưa có thông tin | Ngày: 2026-09-16

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 359 / 111 / 23 |
| Thời gian trung bình mỗi ảnh | 10 phút/ảnh (theo người gán cung cấp) |

Với 20 ảnh, tổng thời gian tương ứng là **200 phút** (3 giờ 20 phút).

Ba khớp có tỷ lệ `v=1` cao nhất theo [bảng visibility](visibility_report.md):

1. `left_ear`: 10/29, 34%.
2. `right_ankle`: 10/29, 34%.
3. `right_knee`: 9/29, 31% (đồng hạng với `nose`, `left_eye`, `right_eye`).

`v=1` cao phản ánh tần suất bị che, chưa đủ để kết luận khớp nào khó xác định vị trí giải phẫu nhất. Tai trái thường khuất do góc quay đầu hoặc mũ; đầu gối và mắt cá phải thường bị xe, bàn, trang phục che. Ví dụ `train_11` người thứ 1: phần chân dưới bị mèo/bàn che, nên đầu gối còn trong ảnh được ước lượng bằng `v=1`. Với ảnh rất nhỏ như `train_13`, nhận đúng bên trái/phải khó ngay cả khi khớp không bị che.

## 2. Chấm với gold

Nguồn số: [trước rework](../outputs/eval_vs_gold_before.json) và [sau rework](../outputs/eval_vs_gold.json). Cả hai lượt đều ghép được 29/29 người, không thiếu hoặc thừa skeleton.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.8131 | 0.8504 |
| OKS@0.50 | 1.0000 | 1.0000 |
| OKS@0.75 | 0.7586 | 0.8966 |
| Lỗi `dao_trai_phai` | 1 | 0 |
| Lỗi `nham_nguoi` | 4 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |
| Lỗi `truot_han` | 2 | 0 |

Đã sửa trong bản COCO, rồi chuyển lại toàn bộ 20 file YOLO Pose:

- `train_01.jpg`, người thứ 1: chuyển `left_wrist` từ vùng người bên cạnh về cổ tay của người này; chỉnh `right_elbow`, `left_hip`, `right_hip` và bounding box theo phần cơ thể nhìn thấy.
- `train_03.jpg`, người thứ 2: chuyển `right_wrist`, `right_knee`, `right_ankle` khỏi vị trí trôi/nhầm người; chỉnh `right_elbow`, hai hông, `left_ankle` và bounding box theo dáng người.
- `train_13.jpg`, người thứ 3 (người nhỏ ở mép trái): đổi lại danh tính toàn bộ cặp keypoint trái/phải. Gold ghép người này với người thứ 1 trong file gold.
- `train_15.jpg`, người thứ 1 (đội mũ bên trái): đưa `left_elbow`, `left_wrist` từ vùng xe về tay của người; chỉnh `left_shoulder`, `right_elbow`, `right_wrist`, hai hông, hai đầu gối, hai mắt cá và bounding box.

Lỗi đảo trái/phải nằm ở `train_13.jpg`, người thứ 3. Người quá nhỏ, đường vai và chân chỉ cách nhau vài chục pixel nên nhãn ban đầu nhìn thoáng qua vẫn có vẻ nối đúng khớp; đối chiếu từng bên với gold mới thấy danh tính trái/phải bị đảo. Sau rework vẫn có 40 cảnh báo lệch nhẹ và 40 cờ visibility khác gold. Có 78 khớp gold để `v=0` nhưng nhãn của tôi có điểm; các khớp này không bị tính vào OKS, và tôi không xoá chỉ để giống gold khi điểm vẫn có căn cứ trong ảnh.

## 3. Kiểm chéo

Bạn cùng nhóm: **Không có kiểm tra chéo** (theo người gán cung cấp).

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| Không áp dụng | — | — | — | Không có bảng visibility của người thứ hai để đối chiếu. |

Không có luật mới được thống nhất qua kiểm chéo vì bước này không diễn ra. Quy tắc đã dùng được ghi trong [GUIDELINE_MINI.md](../GUIDELINE_MINI.md): khớp bị vật che nhưng vẫn nằm trong khung được đặt tọa độ ước lượng với `v=1`; chỉ dùng `v=0` khi khớp ra ngoài mép ảnh. Đây là quy tắc của lượt gán nhãn, không phải kết quả thống nhất với bạn cùng nhóm.

## 4. Model

Đã chạy [notebook Colab](https://colab.research.google.com/drive/1USqOiCsrkeYoMf7LKdGhssLjKv6J7j6A?hl=vi) với GPU Tesla T4. Tập train có 20 ảnh/29 người; tập test của `data.yaml` có 10 ảnh/13 người. Cấu hình đặt 80 epoch, `patience=30`, nên quá trình dừng sớm ở epoch 39 và chọn checkpoint tốt nhất tại epoch 9. Nguồn số: [outputs/eval_model.json](../outputs/eval_model.json).

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50 | 0.9785 | 0.9600 | -0.0185 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng **0.0055** trên 10 ảnh test, từ 0.6853 lên 0.6908. Đây là thay đổi nhỏ; 20 ảnh train chưa đủ để kết luận chất lượng model tăng ổn định. Trong cùng lượt đánh giá, `box_mAP50-95` giảm 0.0078, cho thấy cải thiện pose không đi cùng cải thiện phát hiện hộp người.

2. Sau fine-tune, `box_mAP50-95` cao hơn `pose_mAP50-95` **0.1133** (0.8041 so với 0.6908); ở model gốc, chênh **0.1266**. Trên tập test này, tìm đúng hộp người dễ hơn đặt đủ 17 khớp chính xác, đặc biệt khi chi bị vật thể che. `test_07.jpg` có người ngồi sau quầy và hộp bánh: hộp người vẫn được phát hiện, nhưng cổ tay trái bị model kéo lên mặt kính.

3. Trong ảnh dự đoán `test_07.jpg`, model đặt `left_wrist` tại khoảng **(365, 291)**, trên hộp bánh bằng kính, với confidence khoảng **0.253**. Đây là lỗi hình dáng kiểu **trượt hẳn**: điểm nằm trên vật thể che thay vì trên tay người. Nhãn test đặt khớp này `v=0`, nên ví dụ này là lỗi nhìn thấy khi mở ảnh dự đoán, không phải lỗi được cộng vào phép tính OKS của khớp đó.

4. Trên tập train, `train_13.jpg` người thứ **3** (người nhỏ ở mép trái) có OKS model so với nhãn thấp nhất: **0.296**. Nhãn sau rework của chính người đó đạt **0.8135** khi so gold. Ảnh phủ cho thấy model nén các điểm chi của người rất nhỏ vào một dải hẹp, còn nhãn đã tách hai bên theo thân và chân; bằng chứng gold và ảnh nghiêng về nhãn sau rework.

5. **Không.** Ảnh tôi gán kém nhất theo gold là `train_12.jpg`, người thứ 1, OKS **0.6313**, còn model bất đồng với nhãn nhiều nhất ở `train_13.jpg`, người thứ 3, OKS **0.296**. Điều này chỉ ra hai khó khăn khác nhau: `train_12` còn sai lệch vị trí so gold; `train_13` là người quá nhỏ khiến model dự đoán kém dù nhãn đã được gold xác nhận ở mức OKS 0.8135.

## 5. Một rule evidence đã dùng

Ở `train_11.jpg`, người thứ 1, `left_knee` nằm sau con mèo và mặt bàn nhưng vẫn trong khung ảnh. Vị trí hông và hướng của phần chân cho phép ước lượng đầu gối, dù không thấy trực tiếp khớp. Vì thế điểm được giữ ở tọa độ ước lượng với `v=1`. Hai mắt cá của người này ở ngoài mép dưới ảnh nên là `v=0`.
