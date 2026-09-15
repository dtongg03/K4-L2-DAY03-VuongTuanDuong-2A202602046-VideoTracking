# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: Vương Tuấn Dương — MSSV: 2A202602046

Ngày: 15/09/2026 (ngày lập báo cáo)

Tôi tổng hợp báo cáo từ các file đánh giá và cấu hình hiện có trong `outputs/`. Các chỉ số được giữ nguyên theo JSON, trình bày đến bốn chữ số thập phân. Những mục không có nhật ký hoặc evidence được ghi là chưa có dữ liệu; các phát hiện từ output không được coi là thao tác sửa đã thực hiện.

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | Chưa có dữ liệu xác nhận công cụ thực tế đã sử dụng |
| Thời gian gán `clip_02` (warm-up) | Chưa có dữ liệu |
| Thời gian gán `clip_01` | Chưa có dữ liệu |
| Số track đã vẽ trong `clip_01` | Bản annotation hiện tại có 8 track, ID 1–8, với 583 box trên 190 frame |
| Số keyframe trung bình mỗi track | Chưa có dữ liệu; file MOT không ghi trạng thái keyframe |

Ba tình huống khó nhất khi gán clip này, và tôi xử lý thế nào:

1. Chưa có ghi chép xác nhận tình huống thứ nhất và cách xử lý thực tế.
2. Chưa có ghi chép xác nhận tình huống thứ hai và cách xử lý thực tế.
3. Chưa có ghi chép xác nhận tình huống thứ ba và cách xử lý thực tế.

Tôi trình bày các lỗi có frame và ID trong output tại mục 3 và mục 5; dữ liệu này không xác định được đâu là ba tình huống khó nhất trong quá trình gán nhãn.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: Chưa có nhật ký tự kiểm ID. Kết quả đánh giá hiện tại ghi nhận IDSW = 0, nhưng không chứng minh đã thực hiện lượt tua này.
- Lượt 2: Chưa có nhật ký tự kiểm frame đầu/cuối.
- Lượt 3: Chưa có nhật ký tự kiểm frame giữa.

Kiểm chéo với: Chưa có dữ liệu. Chưa có file `reports/review_partner.md` trong repo.

Số lỗi tôi tìm được trong bản của người kiểm chéo: Chưa có dữ liệu. Số lỗi người kiểm chéo tìm được trong bản của tôi: Chưa có dữ liệu.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

Chưa có biên bản để xác định bất đồng khi kiểm chéo. `GUIDELINE_MINI.md` hiện còn các mục chưa điền, gồm quy tắc xử lý một số tình huống ID, bbox và ba ca mơ hồ thực tế.

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | Chưa có file manifest |
| Thời điểm khóa | Chưa có dữ liệu |
| Số row / frame / track trước khi mở reference | Chưa có snapshot pre-gold để xác định |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | — | — | — | — | — | — | — | — | — | — |
| Sau rework | — | — | — | — | — | — | — | — | — | — |

Dấu “—” nghĩa là chưa có dữ liệu xác định cho mốc tương ứng. Tôi chưa có đủ evidence để gán lần đánh giá hiện tại cho mốc pre-gold hay sau rework, hoặc tính mức cải thiện trước/sau. Kết quả hiện tại trong `outputs/eval_vs_gold.json` được trình bày ở mục 4.

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có, đối với bản annotation được đánh giá trong `eval_vs_gold.json`**. Các giá trị lần lượt là **0.9654**, **0.9302** và **0.8587**.

Sau khi đọc danh sách lỗi, tôi đã sửa cụ thể những gì? Ghi theo frame và ID:

Tôi chưa có change log để xác nhận các thao tác sửa. Bảng dưới ghi các lỗi còn được output chỉ ra và trạng thái evidence, không phải danh sách sửa đã hoàn tất.

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Bbox có trước thời điểm track gold xuất hiện | 51–53 | Annotation 4 / gold 4 | Chưa có dữ liệu xác nhận đã sửa; output ghi nhận 3 box xuất hiện sớm |
| Bbox khớp chưa sát với gold | 84, 91, 92, 93, 94, 96 | Annotation 5 / gold 5 | Chưa có dữ liệu xác nhận đã sửa; tại frame 94, output ghi IoU = 0.506 |
| Bbox khớp chưa sát với gold | 111, 112, 113, 119 | Annotation 6 / gold 6 | Chưa có dữ liệu xác nhận đã sửa; tại frame 112, output ghi IoU = 0.537 |

Nguồn: `diagnostics` trong [eval_vs_gold.json](../outputs/eval_vs_gold.json). Output không ghi nhận track gold bị bỏ sót hoàn toàn, track bị tách hoặc ID switch; vẫn có 25 FP và 15 FN ở cấp box.

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ [outputs/model_run_config.json](../outputs/model_run_config.json):

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.15` / `8.4.145` / `2.11.0+cu128` / `0.5.13` |
| weights / hai tracker | `yolo26n.pt` / `bytetrack.yaml` / `/content/Day3-Lab/configs/trackers/botsort-reid.yaml` |
| conf / IoU / imgsz / classes | `0.25` / `0.7` / `960` / `[2, 5, 7]` |
| device | `0` |

Cấu hình cũng ghi `persist = true` và `clip_frames = 190`. IoU `0.7` ở trên là tham số chạy model; các file đánh giá ghi `iou_threshold = 0.5`. Theo `tools/motlib.py`, HOTA, DetA, AssA và LocA được lấy trung bình trên 19 ngưỡng từ 0.05 đến 0.95.

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.8200 | 0.8089 | 0.8323 | 0.8696 | 0.9654 | 0.9302 | 0.8587 | 25 | 15 | 0 |
| ByteTrack control vs gold | 0.7085 | 0.6487 | 0.7761 | 0.8463 | 0.8746 | 0.7487 | 0.8226 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.7635 | 0.7110 | 0.8204 | 0.8721 | 0.9001 | 0.7923 | 0.8595 | 91 | 26 | 2 |
| ReID vs bạn | 0.7644 | 0.7071 | 0.8301 | 0.8711 | 0.9091 | 0.8148 | 0.8532 | 80 | 25 | 3 |

Nguồn theo thứ tự các dòng: [eval_vs_gold.json](../outputs/eval_vs_gold.json), [eval_bytetrack_vs_gold.json](../outputs/eval_bytetrack_vs_gold.json), [eval_reid_vs_gold.json](../outputs/eval_reid_vs_gold.json), [eval_reid_vs_me.json](../outputs/eval_reid_vs_me.json).

Tôi đã đối chiếu số lượng trong output với các file MOT: annotation có 583 box và 8 track; gold có 573 box và 8 track; ByteTrack có 607 box và 16 track; BoT-SORT + ReID có 638 box và 16 track. Bản `eval_reid_vs_me.json` hiện tại ghi đúng 583 box và 8 track tham chiếu, nhất quán về số lượng với annotation hiện tại.

Dòng “ReID vs bạn” dùng annotation của tôi làm tham chiếu, còn ba dòng đầu dùng gold. Vì vậy, FP/FN của dòng này thể hiện bất đồng với annotation của tôi, không trực tiếp tương đương lỗi so với gold.

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của tôi là 0.9302, thấp hơn IDF1 = 0.9654. Output ghi IDSW = 0, FP = 25 và FN = 15. Theo công thức trong bộ chấm, `MOTA = 1 - (FP + FN + IDSW) / GT_boxes`, kết quả là `1 - (25 + 15 + 0) / 573 ≈ 0.9302`. Phần giảm MOTA của lần đánh giá này đến từ FP và FN.

Nếu MOTA cao nhưng IDF1 thấp, kết quả có thể phát hiện đúng nhiều box nhưng không giữ identity nhất quán trên toàn chuỗi. MOTA cộng mỗi lần đổi ID như một lỗi trong tổng FP, FN và IDSW; nó không trực tiếp phạt toàn bộ các frame mang identity sai sau lần đổi đó. IDF1 đánh giá độ đúng của identity qua phép ghép track toàn cục, nên phản ánh dạng lỗi này khác MOTA.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

So với ByteTrack, BoT-SORT + ReID tăng IDF1 từ 0.8746 lên 0.9001, chênh lệch 0.0255; AssA tăng từ 0.7761 lên 0.8204, chênh lệch 0.0443. IDSW giữ nguyên ở 2. Như vậy, chất lượng liên kết tổng thể tăng nhưng số lần đổi ID không giảm.

Tại chuỗi frame 57–59 của gold track 4, đối chiếu các file MOT cho thấy ByteTrack khớp với ID 14 ở frame 57, không có box khớp đạt IoU 0.5 ở frame 58, rồi khớp với ID 15 ở frame 59. `eval_bytetrack_vs_gold.json` ghi nhận switch 14 → 15 tại frame 59. BoT-SORT + ReID giữ ID 9 khớp với gold track 4 ở cả ba frame. Đây là một đoạn treatment liên kết ổn định hơn control.

Tuy nhiên, treatment vẫn có hai switch: 17 → 18 tại frame 87 trên gold track 5 và 24 → 31 tại frame 113 trên gold track 6. Hai switch của control nằm tại frame 59 trên gold track 4 và frame 94 trên gold track 5. Kết quả này **không cô lập causal effect của ReID**, vì hai tracker implementation khác nhau: ByteTrack và BoT-SORT + ReID.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

Từ control sang treatment, DetA tăng từ 0.6487 lên 0.7110, tức tăng 0.0623. FP tăng từ 88 lên 91, trong khi FN giảm từ 54 xuống 26. Treatment bỏ sót ít hơn nhưng tạo thêm 3 FP; MOTA tăng từ 0.7487 lên 0.7923.

Ở treatment, output còn 91 FP, 26 FN và 2 IDSW. Lỗi box thừa/thiếu vẫn nhiều hơn số lần đổi ID; đồng thời, hai switch cho thấy association chưa hoàn toàn ổn định. Ví dụ, model track 27 có 16 box trong frame 106–121 không khớp track gold nào; gold track 6 chỉ được phủ 44/56 frame theo diagnostics.

Tôi chưa thể quy toàn bộ FP/FN cho detector chỉ từ output tracking: lọc detection, duy trì track và độ khớp bbox cũng có thể ảnh hưởng đến box được chấm. Kết luận được dữ liệu hỗ trợ là còn cả lỗi box thừa/thiếu và lỗi liên kết ID; chưa đủ evidence để tách chính xác nguyên nhân theo từng thành phần.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Trên gold track 5, ở frame 85 và 87, annotation của tôi giữ ID 5 và có box khớp gold ở ngưỡng IoU 0.5. ReID khớp bằng ID 17 tại frame 85, không có box khớp đạt ngưỡng tại frame 86, rồi khớp bằng ID 18 tại frame 87. File `eval_reid_vs_gold.json` ghi nhận ID switch 17 → 18 tại frame 87; `eval_vs_gold.json` không ghi nhận switch trong annotation của tôi.

Trong đoạn này, tôi giữ đúng identity theo gold, còn ReID bị đổi identity. Nhận xét “đúng” ở đây nói về liên kết ID; không có nghĩa bbox của tôi hoàn toàn trùng gold. Chẳng hạn, diagnostics của annotation vẫn ghi bbox chưa sát ở frame 84 và 91–96 trên track 5.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Tôi chọn trường hợp evidence cho thấy model sai so với tham chiếu: model track 27 tại frame 106–121 có 16 box và được ghi là “không khớp track tham chiếu nào” trong cả `eval_reid_vs_gold.json` lẫn `eval_reid_vs_me.json`.

Như vậy, track này không được cả gold lẫn annotation của tôi hỗ trợ theo tiêu chí ghép của bộ chấm. Đây là căn cứ để xem nó là track thừa so với tham chiếu, thay vì bổ sung annotation chỉ vì model tạo ra track. Output không cho biết nguyên nhân hình ảnh cụ thể, nên tôi không kết luận đó là xe máy, bóng, quảng cáo hay vật thể nào khác.

## 6. Nếu phải gán thêm 10 clip nữa

Từ các lỗi hiện có, tôi sẽ làm rõ trong `GUIDELINE_MINI.md` cách xác định frame bắt đầu/kết thúc track, cách đặt bbox theo phần nhìn thấy và cách giữ ID qua các đoạn mất quan sát. Trọng tâm là tránh bbox xuất hiện sớm như ID 4 tại frame 51–53, đồng thời kiểm tra kỹ những đoạn bbox chưa sát như ID 5 và ID 6 được nêu ở mục 3.

Trong quy trình, tôi sẽ ghi thời gian và các ca mơ hồ ngay khi gán nhãn; lưu kết quả ba lượt tự kiểm và kiểm chéo theo frame–ID; tạo snapshot pre-gold cùng manifest trước khi mở reference; lưu riêng đánh giá trước/sau và nhật ký sửa. Sau mỗi lần thay annotation, tôi sẽ cập nhật các phép đánh giá sử dụng annotation đó để bảng so sánh dùng cùng phiên bản dữ liệu. Đây là kế hoạch cho các clip tiếp theo, không phải xác nhận những bước này đã được hoàn tất ở bài hiện tại.

## 7. Tệp đã nộp

Tôi đánh dấu theo tình trạng file trong repo tại thời điểm lập báo cáo; danh sách này không xác nhận việc nộp trên VLearn.

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [ ] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json` — chưa có
- [ ] `GUIDELINE_MINI.md` đã điền — file còn các mục chưa điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/review_partner.md` — chưa có
- [x] `reports/report.md` (file này)
