# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phạm Tiến Dũng
- MSSV: 2A202603061
- Lớp: 2A
- Link repo bài làm: https://github.com/lamsondongbua/K5-L2A-DAY02-PhamTienDung-2A202603061-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1ybFjP8f8hcbJkwy6lcMXV4FICB47dYaF

## 2. Schema và mini guideline

Xem [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).
- **Thứ tự ID lớp:** nhãn dùng thứ tự của bộ gold (`car=0, van=1, truck=2, bus=3`), đã được lab coach xác nhận là lệch so với bảng schema trong `README.md` (`car=0, truck=1, bus=2, van=3`). `data.yaml` đã cập nhật theo thứ tự này.
- **Ba ca mơ hồ đã quyết định:**
    + `drive_002` xe van màu sáng ở sát rìa phải, bị cắt khung; gần đó có xe buýt và nhiều xe con.
    + `drive_026` nhiều xe buýt nối sát nhau, một phần thân xe bị xe phía trước che khuất; xe con ở ngay cạnh.
    + `drive_041` xe buýt lớn ở góc trái bị cắt bởi cạnh ảnh trong cảnh mưa, các xe phía sau nhỏ và dễ lẫn.
    
- **Phần cần giảng viên làm rõ:** ca `drive_002` trong mini guideline — xe van màu sáng bị cắt ở mép phải, gần xe buýt và xe con. Guideline hiện quyết định gán `van` và vẽ box theo phần thân nhìn thấy; cần xác nhận cách phân biệt `van` với `bus` trong ca này.

## 3. Annotation và self-QC

- Số file nhãn train: 50 (`dataset/labels/train/drive_001.txt` đến `drive_050.txt`)
- Kết quả checker và lỗi đã xử lý:  `python tools/check_yolo_labels.py` chạy  **"ĐẠT cấu trúc: 50/50 file nhãn hợp lệ"**.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001`, `drive_005`,  `drive_006`, `drive_012`, `drive_024`,
`drive_031`, `drive_039`, `drive_042`, `drive_044`, `drive_049`
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
    + Một số xe con bị gán `truck`, xe tải bị gán `bus`, xe buýt bị gán `truck`; đã sửa lớp.
    + Box thừa trên mái che bến xe buýt, lan can, ống cống, thùng rác và xe ba bánh; đã xóa.
    + Một xe bị sót ở `drive_042` (xe buýt lớn ở góc dưới trái); đã thêm box.
    + Cách sửa: không sửa, vì bộ nhãn đạt gold QC theo ngưỡng mới.
- Commit khóa nhãn ban đầu: cea6b57 

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 380 | |
| FP | 301 | |
| FN | 273 | |
| Precision | 0.5580 | |
| Recall | 0.5819 | |
| F1 | 0.5697 | |
| mean_iou_of_matches | 0.8579 | |
| passed | false | |

Kết quả nằm trong `outputs/annotation_evaluation.json`. `passed` vẫn là `false` so với ngưỡng gốc F1 ≥ 0,80, nhưng lab coach đã xác nhận F1 > 0,5 là chấp nhận được cho bài này.

## 5. Kết quả YOLO

- Runtime: Google Colab, GPU Tesla T4; Python 3.13.16; Ultralytics 8.4.208; torch 2.14.1+cu130.
- Tham số thay đổi so với notebook: không đổi (`yolo26n.pt`, 50 epoch, `imgsz=640`, batch tự động = 44, `seed=20260909`). Chỉ điền `REPO_URL` trong phiên Colab.
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không nộp file `.pt`).


| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4090 |
| Recall | 0.2339 |
| mAP50 | 0.2706 |
| mAP50–95 | 0.1995 |


- Ảnh dự đoán 10 ảnh eval: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/)
- Ảnh cụ thể: [assets/eval_predictions/visuals/drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg)
- Confusion matrix: [assets/yolo26_runs/vehicle_eval/confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png)
- Log train theo epoch: [assets/yolo26_runs/vehicle_annotation/results.csv](assets/yolo26_runs/vehicle_annotation/results.csv)

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
    - Lớp truck có recall thấp nhất, khoảng 0,20 (20%) theo ma trận nhầm lẫn chuẩn hóa. Ảnh minh họa: [`drive_052.jpg`](/report/individual/assets/eval_predictions/visuals/drive_052.jpg) — box truck nhỏ ở gần phía trên ảnh bị dự đoán thành car.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
    - Cần kiểm độ sát của box: tâm có lệch không, box có quá lỏng/quá chặt không, và cạnh box có ôm sát thân xe không. Kết quả của bài mình là mAP50 = 0,2706, mAP50–95 = 0,1995; chênh 0,0711, mAP50–95 bằng khoảng 73,7% mAP50. Cả hai điểm đều thấp, nên chưa thể nói đây là trường hợp mAP50 cao nhưng box không chính xác. Gold QC có mean_iou_of_matches = 0,8579, nhưng con số này tính trên các box khớp gold, không đại diện cho mọi box dự đoán của model.

3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
    - Có dấu hiệu nhầm van thành bus: ma trận nhầm lẫn cho thấy 11/12 box van thật bị dự đoán là bus. Điều này liên quan đến guideline phân biệt van/minivan với bus/minibus theo kiểu thân xe. Báo cáo ghi nhận đã sửa một số lỗi gán nhầm giữa car, truck và bus. Đây là dấu hiệu phù hợp với vấn đề guideline, nhưng chưa đủ để khẳng định đó là nguyên nhân duy nhất. Xem [ma trận nhầm lẫn](/report/individual/assets/yolo26_runs/vehicle_eval/confusion_matrix.png) và [GUIDELINE_MINI.md](/GUIDELINE_MINI.md).
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
    - Dùng chúng để train sẽ khiến model học trên ảnh dùng để đánh giá, làm kết quả kém khách quan. Trong notebook, 10 ảnh eval được dùng làm validation trong lúc train và để chọn best.pt; vì vậy chúng không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Trước và trong lúc gán nhãn | Ranh giới giữa `car`/`truck` và `van`/`bus` dễ nhầm; thứ tự class ID trong schema cần được đối chiếu. | Đối chiếu schema với gold và trao đổi với lab coach; ghi định nghĩa, mapping và các ca mơ hồ vào `GUIDELINE_MINI.md`. | Đã có quy tắc tham chiếu và ví dụ cho `drive_002`, `drive_026`, `drive_041`; trường hợp không rõ vẫn cần hỏi giảng viên thay vì tự đoán. |
| Tự rà soát nhãn train | Phát hiện một số xe bị gán sai lớp, box thừa trên vật không thuộc schema và một xe buýt bị bỏ sót ở `drive_042`. | Rà lại ảnh, sửa class/box và chạy `python tools/check_yolo_labels.py`. | Checker xác nhận 50/50 file hợp lệ. Gold QC ghi TP=380, FP=301, FN=273, F1=0,5697, mean IoU=0,8579; `passed=false` theo ngưỡng F1 gốc. |
| Train và đánh giá model | Kết quả trên 10 ảnh eval còn thấp (precision=0,4090; recall=0,2339; mAP50=0,2706; mAP50–95=0,1995). Ma trận nhầm lẫn cho thấy recall `truck` khoảng 0,20 và 11/12 box `van` bị dự đoán thành `bus`. | Xem confusion matrix và ảnh dự đoán; dùng các lỗi này để xác định lớp cần rà lại theo guideline. | Đã xác định được các cặp lớp cần chú ý; còn cần thêm dữ liệu đại diện cho lớp ít gặp và tiếp tục kiểm tra các ca `van`/`bus`. |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Kết quả bài làm |
|---|---|
| Hoàn thành và định dạng YOLO | Có đủ 50 file nhãn theo ảnh train; checker xác nhận 50/50 file hợp lệ, đúng cấu trúc YOLO và class ID nằm trong schema. |
| Đầy đủ vật thể | Đã rà ảnh và bổ sung xe buýt bị sót ở `drive_042`, nhưng gold QC còn FN=273, recall=0,5819; độ bao phủ còn hạn chế. |
| Độ bám sát và hình học box | mean IoU của các match với gold là 0,8579, vượt mốc 0,70; chỉ số này tính trên box đã match, nên chưa chứng minh mọi box đều sát. |
| Phân lớp nhất quán | Đã sửa một số nhầm lẫn class; tuy nhiên gold QC còn FP=301 và confusion matrix eval cho thấy truck recall khoảng 0,20, 11/12 van bị dự đoán thành bus. |
| Mini guideline và quyết định mơ hồ | Có định nghĩa bốn lớp, quy tắc box và ba ca mơ hồ kèm lý do trong `GUIDELINE_MINI.md`. |
| Gold-set annotation QC | Lab coach xác nhận F1 > 0,5 là đạt cho bài này. Kết quả có F1=0,5697 và mean IoU=0,8579, nên đạt ngưỡng đã thống nhất; output hiện tại chỉ lưu một lần đánh giá nên chưa đối chiếu được số trước/sau rework. |
| Pipeline YOLO và phân tích | Có log train, metrics và ảnh eval; notebook dùng eval làm validation, không dùng eval để train; đã nhận xét lỗi class và recall. |

## 8. Công cụ AI và tài liệu tham khảo

- Claude Code (Anthropic):
    + Hỏi một số trường hợp lỗi, hoặc khó xác định vẽ vật thể, phân loại class
- Codex (OpenAI):
    + Đối chiếu số liệu với các file trong repo như `data.yaml`, `GUIDELINE_MINI.md`, JSON đánh giá, confusion matrix và ảnh dự đoán cho chắc chắn với kết quả đã làm được.
- CVAT, Ultralytics YOLO26, Google Colab.
