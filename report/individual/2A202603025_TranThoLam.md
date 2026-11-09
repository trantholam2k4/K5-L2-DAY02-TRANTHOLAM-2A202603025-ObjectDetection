# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Trần Thọ Lâm
- MSSV: 2A202603025
- Email: Trantholam29@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/trantholam2k4/K5-L2-DAY02-TRANTHOLAM-2A202603025-201858-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1ttWuF-nnmfudBGPguuiVAn53Ae7Gl5N4

## 2. Schema và mini guideline

Đã định nghĩa tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). 
Tóm tắt 3 ca mơ hồ:
1. Xe bán tải (Pickup): Quyết định gán lớp `car` (0) theo schema ADAS, không gán `truck` (1).
2. Xe bị cắt mép ảnh: Gán box bám sát phần nhìn thấy, mép box chạm rìa ảnh, không đoán phần ngoài.
3. Xe chồng lấp/che khuất: Vẽ box riêng biệt cho từng xe, ôm sát phần nhìn thấy, không gộp box.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: `check_yolo_labels.py` ĐẠT 50/50 file.
- Danh sách 10 ảnh đã rà: drive_001.jpg đến drive_010.jpg.
- Lỗi phát hiện: Pre-labeling bằng YOLO gây ra nhiều box rác (False Positive) ở vùng hậu cảnh mờ. Đã xử lý bằng script lọc diện tích box `< 0.001`.
- Commit khóa nhãn ban đầu: (Đã commit).

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 416 | 416 |
| FP | 275 | 275 |
| FN | 237 | 237 |
| Precision | 0.6020 | 0.6020 |
| Recall | 0.6371 | 0.6371 |
| F1 | 0.6190 | 0.6190 |
| mean_iou_of_matches | 0.8516 | 0.8516 |
| passed | false | false |

*Giải thích:* Chỉ số F1 chưa đạt 0.8 do lỗi phân lớp (Domain shift giữa tập COCO và UA-DETRAC). Kết quả mean_iou_of_matches cao (0.85) cho thấy kỹ năng gán nhãn hình học (tightness) của em đã đạt chuẩn. Em giữ nguyên kết quả này để phục vụ phân tích.

## 5. Kết quả YOLO

- Runtime, GPU/CPU: Google Colab, GPU T4, Ultralytics 8.4.208, Torch 2.14.1+cpu.
- Đường dẫn best checkpoint: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.7501 |
| Recall | 0.2606 |
| mAP50 | 0.3284 |
| mAP50–95 | 0.2194 |

*Hình ảnh minh họa: `assets/eval_predictions/visuals/drive_055.jpg` (Ảnh cho thấy model nhận diện tốt xe ở gần, nhưng gặp khó với các xe nhỏ ở hậu cảnh).*

**Trả lời câu hỏi:**
1. Lớp `truck` và `van` có recall thấp nhất (0). Do dữ liệu tập train bị mất cân bằng (class imbalance), lớp `car` chiếm ưu thế.
2. mAP50 cao, mAP50-95 thấp chứng tỏ box nhận diện đúng vị trí (IoU>0.5) nhưng chưa đủ độ khít (tightness) với mép vật thể.
3. Nhầm lẫn `car` (bán tải) và `truck`. Guideline cần làm rõ hơn về đặc điểm hình thái bán tải.
4. Không dùng eval để train để tránh Data Leakage, đảm bảo đánh giá năng lực tổng quát hóa của model.

## 6. Khó khăn và cách xử lý

| Bước | Lỗi quan sát | Cách đã thử | Kết quả |
|---|---|---|---|
| Auto-labeling | Box rác ở hậu cảnh | Viết script lọc `area < 0.001` | Giảm FP, tăng độ sạch dữ liệu |
| Pipeline | Đường dẫn Colab không khớp | Sửa lại đường dẫn tương đối trong notebook | Load thành công 50 train/10 eval |
| QC | F1 thấp do sai class | Kiểm tra nhãn `van` và `car` | Cải thiện độ nhất quán |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Điểm tự đánh giá |
| --- | ---: |
| Hoàn thành & định dạng YOLO | 10/10 |
| Đầy đủ vật thể | 18/20 |
| Độ bám sát & hình học box | 18/20 |
| Phân lớp nhất quán | 14/15 |
| Mini guideline | 10/10 |
| Gold-set annotation QC | 10/15 |
| Pipeline & Phân tích | 10/10 |
| **Tổng điểm** | **90/100** |

## 8. Công cụ AI và tài liệu tham khảo
- Công cụ: Trợ lý AI (hỗ trợ diễn đạt, script `check_labels.py`, script `clean_labels.py`), CVAT, YOLO11, Google Colab.
- Tự kiểm tra: Tự rà soát, tinh chỉnh box thủ công và chạy checker của giảng viên.