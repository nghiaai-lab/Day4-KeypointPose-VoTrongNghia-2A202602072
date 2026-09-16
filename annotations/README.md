# annotations/ - bản COCO Keypoints đã rà soát

`coco_keypoints/person_keypoints_default.json` được tạo từ bản export CVAT của
20 ảnh, sau đó đã sửa các lỗi quan sát được ở lượt ảnh phủ và sắp lại đúng thứ tự
17 điểm COCO. Chi tiết nằm trong [`reports/pose_qa.md`](../reports/pose_qa.md).
Bản export ban đầu vẫn có trong lịch sử Git (commit `34b2201`).

```text
annotations/
  coco_keypoints/     Export -> COCO Keypoints 1.0   (bộ 17 điểm thân, 20 ảnh core)
```

## Bộ 17 điểm thân

1. Trong CVAT: **Export -> COCO Keypoints 1.0**.
2. Giải nén, tìm file `.json` (thường là `annotations/person_keypoints_default.json`).
3. Chép vào `annotations/coco_keypoints/`.
4. Chuyển sang nhãn để train:

```bash
python3 tools/coco_kp_to_yolo_pose.py \
    --coco annotations/coco_keypoints/person_keypoints_default.json \
    --out dataset/labels/train
```

**Phép đếm 30 giây** (slide 40) — mở file `.json` ra và đếm:

- Mảng `keypoints` của **mỗi người** phải có đúng **51** số = 17 × (x, y, v).
- Mỗi dòng trong `dataset/labels/train/*.txt` phải có đúng **56** số = 5 (box) + 51.
- Đếm ra 34 hoặc 39 → **export lại**, đừng gán lại nhãn.

Nếu file không có mảng `keypoints` nào: bạn đã chọn nhầm **COCO 1.0** hoặc **YOLO 1.1**.
Hai định dạng đó chỉ xuất box — 17 điểm biến mất, không lỗi, không cảnh báo.
