# Mini annotation guideline — Ngày 3 (tracking)

Nhóm / tên: Vương Tuấn Dương — MSSV: 2A202602046

Clip: `clip_01`, `clip_02`

Tôi hoàn thiện hướng dẫn này dựa trên `GUIDE.md`, `CVAT_TASK_SPEC.md` và kết quả đánh giá hiện có. Các quy tắc dưới đây hướng dẫn gán nhãn thủ công; ba ca ở mục 4 được tổng hợp từ output, không phải nhật ký thao tác hay xác nhận đã sửa annotation.

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Tôi chỉ gán **một nhãn duy nhất: `vehicle`**. Tất cả track được gán đều dùng nhãn này; không tạo nhãn riêng theo loại xe.

| Đối tượng | Cách gán |
| --- | --- |
| Xe bốn bánh thuộc phạm vi `vehicle` của bài | Gán nhãn `vehicle` bằng Rectangle Track |
| Mọi đối tượng còn lại | Không gán nhãn |

Người đi bộ, xe đạp, xe máy/mô tô, biển báo và hình ảnh xe trong quảng cáo, gương hoặc phản chiếu đều không gán. Xe bốn bánh đứng yên vẫn dùng nhãn `vehicle`. Khi vật thể quá nhỏ hoặc mờ, cần xem các frame lân cận; chỉ bắt đầu gán khi xác định được vật thể thuộc phạm vi `vehicle`.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Quy tắc áp dụng | Vì sao |
| --- | --- | --- |
| Xe bị che một phần | Giữ cùng ID, bbox ôm phần nhìn thấy; bật Occluded khi phù hợp | Che khuất không làm thay đổi identity của vật thể |
| Xe bị che hoàn toàn rồi hiện lại dưới 25 frame | Giữ ID cũ khi xác định được cùng xe; dùng Outside trong đoạn hoàn toàn vắng mặt | Áp dụng ngưỡng dưới 2 giây của lab tại 12.5 fps; không vẽ box vô hình |
| Xe bị che từ 25 frame trở lên | Hướng dẫn này lấy 25 frame làm ranh giới: tạo track mới khi hiện lại | Làm rõ trường hợp đúng ngưỡng, tránh nối track qua khoảng mất quan sát dài |
| Xe rời khung hình rồi quay lại | Kết thúc track cũ và tạo track mới | Theo quy tắc mặc định của lab, ra khỏi khung là kết thúc track |
| Hai xe cắt nhau / chồng lên nhau | Theo dõi từng xe qua các frame trước và sau giao nhau; không đổi ID theo vị trí trái/phải | Tránh hoán đổi ID khi vị trí tương đối của hai xe thay đổi |

Không gộp hai xe vào cùng ID. Không tạo ID mới chỉ vì thay đổi kích thước, hướng di chuyển hoặc bị che một phần. Nếu cần sửa ID, sửa trong công cụ gán nhãn rồi export lại; không đổi ID trực tiếp trong file MOT.

## 3. Luật bbox

| Tình huống | Quy tắc áp dụng |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | Bbox chạm đúng rìa ảnh, không đoán phần ngoài khung |
| Xe bị xe khác che một phần | Bbox ôm phần nhìn thấy được, không bao cả phần bị che |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | Bắt đầu ở frame đầu xác định được đó là xe bốn bánh. Dùng tiêu chí nhận diện loại vật thể, không tự đặt ngưỡng pixel khi bài không quy định |
| Xe đang đỗ, không di chuyển | Vẫn gán nhãn và giữ ID khi xe còn hiện diện; kiểm tra bbox nếu mức che khuất thay đổi |
| Xe hoàn toàn không còn nhìn thấy | Đặt Outside tại frame đầu vắng mặt, tránh để nội suy kéo dài box |
| Keyframe đặt dày ở đâu | Quanh lúc vào/ra khung, bắt đầu/kết thúc che khuất, giao nhau hoặc thay đổi nhanh về vị trí/kích thước; kiểm tra các frame nội suy giữa hai keyframe |

Dùng Rectangle **Track**, không dùng các Shape rời cho cùng một xe. Các lỗi thủ công thường gặp là box lệch, quá rộng/hẹp, bỏ sót frame và kết thúc track muộn. Cách xử lý là xem từng frame quanh đoạn chuyển tiếp, chỉnh theo phần nhìn thấy và kiểm tra lại đoạn nội suy.

Frame trong phần evidence dưới đây là **MOT, bắt đầu từ 1**. Nếu CVAT hiển thị frame đầu là 0 thì đối chiếu đúng độ lệch khi tìm frame; không sửa chỉ số frame bằng tay sau export.

## 4. Ba ca cần rà soát từ kết quả đánh giá

Nguồn: `diagnostics` trong [outputs/eval_vs_gold.json](outputs/eval_vs_gold.json). Các ca này ghi nhận sai khác với gold; nguyên nhân hình ảnh và thao tác sửa cần được xác định bằng frame gốc.

### Ca 1 — Bắt đầu track sớm

- Clip / frame / ID: `clip_01`, frame **51–53**, annotation ID **4**, gold ID **4**.
- Tình huống: Output ghi nhận 3 bbox của annotation có trước khi track tham chiếu xuất hiện.
- Hướng xử lý: Rà lại frame bắt đầu; chỉ bắt đầu track khi xác định được xe bốn bánh theo hình ảnh. Kiểm tra Outside và nội suy quanh thời điểm xuất hiện.
- Lý do: Tránh tạo box sớm khi chưa có đủ căn cứ nhìn thấy vật thể; không tự xóa box chỉ để khớp gold.

### Ca 2 — Bbox của track 5 khớp chưa sát

- Clip / frame / ID: `clip_01`, frame **84, 91, 92, 93, 94, 96**, annotation ID **5**, gold ID **5**.
- Tình huống: Output liệt kê các bbox có IoU thấp; tại frame **94**, IoU là **0.506**.
- Hướng xử lý: Kiểm tra các cạnh bbox và các frame nội suy lân cận; điều chỉnh box theo phần xe nhìn thấy nếu hình ảnh xác nhận có sai lệch.
- Lý do: Bbox có thể vẫn đạt ngưỡng ghép 0.5 nhưng chưa sát. Không đổi ID chỉ vì bbox lệch.

### Ca 3 — Bbox của track 6 khớp chưa sát

- Clip / frame / ID: `clip_01`, frame **111, 112, 113, 119**, annotation ID **6**, gold ID **6**.
- Tình huống: Output ghi IoU **0.537** tại frame **112** và **0.563** tại frame **119**.
- Hướng xử lý: Rà các frame trong đoạn 111–119, kiểm tra thay đổi hình dạng phần nhìn thấy và vị trí box; bổ sung keyframe nếu nội suy không bám sát vật thể.
- Lý do: Cần kiểm tra cả đoạn thay vì chỉ một frame. Output không đủ để kết luận sai lệch do che khuất, thao tác kéo box hay nội suy.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Tôi bổ sung các quy tắc sau vào hướng dẫn từ những vấn đề được output chỉ ra:

- **Thời điểm bắt đầu/kết thúc:** kiểm tra từng frame quanh lúc xuất hiện và vắng mặt; đặt Outside đúng lúc, không để box kéo dài qua đoạn không nhìn thấy vật thể.
- **Độ sát của bbox:** rà cả frame keyframe và frame nội suy; ôm phần nhìn thấy, không suy đoán phần bị che hoặc ngoài ảnh.
- **Giữ ID:** kiểm tra chuỗi trước/sau đoạn giao nhau hoặc che khuất; phân biệt lỗi bbox với lỗi identity. Output annotation hiện tại ghi **IDSW = 0**, không có track bị tách theo diagnostics.
- **Theo dõi sửa đổi:** nếu sửa annotation, ghi frame–ID–nội dung sửa, export lại và cập nhật các phép đánh giá dùng annotation đó. Giữ nguyên snapshot đã lưu.

Đây là cập nhật quy tắc và hướng rà soát; chưa có nhật ký xác nhận các bbox nêu trên đã được sửa. Phần kiểm chéo không thực hiện trong báo cáo này.
