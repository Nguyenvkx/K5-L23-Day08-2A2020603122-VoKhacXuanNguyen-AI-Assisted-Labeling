# Báo cáo Day 08 — 2A202603122 - Võ Khắc Xuân Nguyên

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Ngưỡng gợi ý | Lý do |
|---|---|---|
| person | 0.5 | recall 90% (>= 90%), precision 41% (10 vật gold) |
| bicycle | (mặc định) | chỉ 0 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định |
| motorbike | 0.1 | recall 100% (>= 90%), precision 26% (8 vật gold) |
| car | (mặc định) | không ngưỡng nào <= 0.5 đạt recall 90% (tối đa 83%, 6 vật): dùng ngưỡng mặc định, nói rõ trong báo cáo |
| bus | (mặc định) | chỉ 0 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định |
| truck | (mặc định) | chỉ 0 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định |
| three_wheeler | 0.15 | recall 100% (>= 90%), precision 78% (7 vật gold) |
- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): `--thr "person=0.5,motorbike=0.1,three_wheeler=0.15"`
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: sam2.1_b.pt), mask bao phủ khá đầy đủ thân và mái tuk-tuk nhưng còn dính người/nền nên cần sửa. Đã chạy được trên CPU với trọng số tải trực tiếp; chưa đo riêng tốc độ SAM và RAM.
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? Tuk-tuk bị gán car, box lệch hoặc mask dính người. Cần đổi lớp thành three_wheeler, chỉnh box và thêm điểm âm/dương hoặc sửa polygon thủ công.
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): sam2.1_b.pt), mask bao phủ khá đầy đủ thân và mái tuk-tuk nhưng còn dính người/nền nên cần sửa. Đã chạy được trên CPU với trọng số tải trực tiếp; chưa đo riêng tốc độ SAM và RAM.
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: Chọn ngưỡng trên chính 4 ảnh gold gây thiên lệch đánh giá; Precision 39.4%, Recall 83.9% chưa đại diện cho ảnh mới. Cần kiểm tra trên tập độc lập và đọc kèm khoảng tin cậy.
## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 399 | 399/435 = 91.7% | 435 box máy |
| edited (sửa) | 9 | 9/435 = 2.1% | 435 box máy |
| deleted (xoá) | 27 | 27/435 = 6.2% | 435 box máy |
| added (vẽ thêm) | 22 | 22/430 = 5.1% | 430 đối tượng sau duyệt |


- Lớp nào máy tệ nhất? Vì sao (dẫn ảnh)? three_wheeler: 5 box phải sửa và 11 box bị xoá trên 26 box máy (61.5%). Máy dễ nhầm tuk-tuk với car, bỏ sót hoặc vẽ box chưa sát; ví dụ img_004.jpg, img_013.jpg
- So với gold: Precision của máy 39.4% = 26/66 box dự đoán; Recall 83.9% = 26/31 đối tượng gold. Tập gold gồm 4 ảnh, 31 đối tượng; TP = 26, FP = 40, FN = 5.

## 3. Thiên kiến tự động hóa và ảnh bẫy
- Bắt bẫy: 8/12; loại nào lọt: Lọt 1 sai lớp (img_025.jpg), 2 bỏ sót (img_009.jpg, img_006.jpg), 1 box lỏng (img_003.jpg); vì sao (bạn đã bỏ bước nào của checklist)? Có thể chưa quét toàn ảnh khi ẩn nhãn máy, kiểm tra lớp và rà đủ bốn cạnh box; cần đối chiếu lại thao tác thực tế.
- Accepted cao mà bắt bẫy thấp (hoặc ngược lại) nói gì về cách bạn duyệt? Accepted 91.7% nhưng bắt bẫy chỉ 66.7% cho thấy việc giữ nguyên nhiều nhãn chưa chứng minh duyệt tốt; có dấu hiệu quá tin nhãn AI. Cần tìm vật bị bỏ sót trước khi xem nhãn máy, rồi kiểm tra lớp và độ sát box.

## 4. Máy có đáng dùng không?

Thời gian: Gán tay 3 phút/ảnh; duyệt 30/24 = 1,25 phút/ảnh (75 giây/ảnh). Duyệt nhanh hơn 2,4 lần, giảm khoảng 58,3% thời gian. Có thiên lệch do gán gold trước nên đã quen cảnh.

Kết luận: AI có hỗ trợ tiết kiệm thời gian, nhưng cần duyệt lại chất lượng: accepted 91,7%, bắt bẫy 8/12 = 66,7%, chưa đạt ngưỡng 80%. Đặc biệt cần kiểm tra kỹ lớp three_wheeler.

Ba quy tắc giảm thiên kiến:
1. Ẩn nhãn AI và quét 100% ảnh để tìm vật bị bỏ sót trước khi xem dự đoán.
2. Kiểm tra lớp và bốn cạnh của từng box, kể cả box có điểm tin cậy cao.
3. Nếu bắt bẫy dưới 80% hoặc QC có lỗi đối tượng trên 2%, duyệt lại lô và chạy lại báo cáo QC.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | Phiên bản | Click dương; click âm | Diện tích mask / số polygon | Nhận xét quan sát | File |
|---|---|---|---|---|---|
| img_013 | SAM 2.1 base | (400,480), (600,110); (720,300) | 317.255 px; 45,4% ảnh; 1 polygon | Bao được mái/thân/bánh xe; còn dính đáng kể vùng người lái và một phần người/nền nhìn xuyên qua khung xe. Điểm âm chưa loại sạch người lái. | `masks/tuktuk_img013_sam2.jpg` |
| img_013 | MobileSAM (SAM 1) | (400,480), (600,110); (720,300) | 245.541 px; 35,2% ảnh; 2 polygon | Loại được nhiều vùng thân người lái hơn ảnh SAM 2.1, nhưng còn dính phần người và có vùng sàn/bánh xe bị thiếu. Không kết luận tốt hơn chỉ vì diện tích nhỏ hơn. | `masks/tuktuk_img013_sam1.jpg` |
| img_004 | SAM 2.1 base | (620,480), (700,560); (465,450) | 93.190 px; 13,3% ảnh; 1 polygon | Chọn được tuk-tuk tiền cảnh bên phải, phần lớn người đứng bên phải được tách khỏi mask; vùng gầm/biên dưới còn lỗ hoặc nham nhở cần kiểm. | `masks/tuktuk_img004_sam2.jpg` |
| img_017 | SAM 2.1 base | (560,420), (680,500); (645,355) | 84.532 px; 12,1% ảnh; 1 polygon | Bao được phần lớn xe bên phải và tách khỏi hai người đi bộ bên trái; điểm âm nằm trên mái xe mục tiêu, cần đặt lại nếu mục tiêu là giữ toàn bộ mái. | `masks/tuktuk_img017_sam2.jpg` |

Đối chiếu guideline mục 4.2: Quy tắc bám biên phần nhìn thấy, loại bóng đổ và tách từng vật giúp sửa mask dính người/nền ở img_013.jpg. Còn thiếu hướng dẫn cụ thể phân biệt kính xe kín với khoảng hở và xử lý người nhìn qua kính; cần bổ sung ví dụ vào v1.1.

Interactor SAM trong CVAT: Chưa chạy thành công; container SAM3 báo Exited (2), chưa có log xác định nguyên nhân. Đã chạy SAM 2.1 offline bằng điểm dương/âm và xuất được mask. Interactor thao tác trực tiếp trong CVAT; offline chạy bằng script rồi đưa kết quả vào công cụ gán nhãn.

## 6. Calibration (Part 6)

Số quyết định: 6 (CAL-01–`CAL-06`). Quan trọng nhất: CAL-05, tách người lái và vùng hở khỏi mask xe ở img_013.jpg.

Quy tắc mới đã vào guideline v1.1: Phân biệt giỏ gắn cố định với hàng rời; giữ kính kín nhưng loại người và vùng hở khỏi mask xe; kiểm mọi box dù confidence cao. Bổ sung bước kiểm bốn cạnh box và quét ảnh khi ẩn nhãn AI.

Nguyên nhân gốc: Guideline còn mơ hồ về hàng rời và mask kính/vùng hở; máy nhầm lớp, bỏ sót, tạo mask dính người; bước duyệt chưa phát hiện hết lỗi. Chưa đủ bằng chứng kết luận người duyệt chưa nắm quy tắc.

## 7. QC và quyết định (Part 6)

Mẫu: 5 ảnh (0 ảnh can_review + 5 ảnh ngẫu nhiên, seed 42). Người kiểm: bạn; hạn chế là vừa duyệt vừa tự kiểm. Cần xác nhận trực tiếp các lỗi trong phiếu QC do AI hỗ trợ.
Lỗi theo đối tượng: 5/90 = 5,6% · Theo ảnh: 4/5 = 80%. Chưa đạt ngưỡng Release ≤ 2%.
Top lỗi: Sai vị trí/kích thước: 3 lỗi, ví dụ img_003.jpg có box thiếu mái xe. Bỏ sót: 2 lỗi, ví dụ img_006.jpg thiếu người quay lưng mặc đồ hoa vàng tím.
Giới hạn mẫu nhỏ: Cận trên 95% của tỷ lệ ảnh lỗi khoảng 96%. Mẫu chỉ có 5 ảnh nên độ bất định lớn, chưa đủ để kết luận chất lượng toàn lô.
Script kiểm được / không kiểm được: Kiểm cấu trúc nhãn, thống kê lỗi đã ghi và áp dụng ngưỡng QC. Không tự xác nhận box ôm sát, phát hiện vật mà cả máy lẫn người cùng bỏ sót hoặc chứng minh người duyệt thực hiện đúng checklist.
Quyết định đề xuất: Rework, ngày 2026-11-17. Xét theo thứ tự: Hold nếu còn quy tắc chưa rõ ảnh hưởng nhãn; khi đã chốt quy tắc, chọn Rework vì lỗi vượt 2%, có lỗi hình học lặp lại và bắt bẫy chỉ 66,7% < 80%. Chưa đủ điều kiện Release.
Việc tiếp theo: Xác nhận phiếu QC, sửa lỗi đã ghi và duyệt lại toàn lô theo checklist; xuất qc_fixed.json, tạo lại release và chạy lại QC.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): ChatGPT/Codex hỗ trợ xử lý lỗi cài đặt, phân tích số liệu
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? Không
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: Windows, Python 3.12, chạy model bằng CPU, CVAT v2.81.0.
- Dùng cache `data/cache/` thay vì tự chạy? Đã tự chạy YOLO26, YOLOE và SAM 2.1
- Lỗi / bug script đã gặp (nếu có): ModuleNotFoundError do thiếu thư viện
- Một điều bạn làm khác lần sau: Quét toàn ảnh khi ẩn nhãn AI trước, sau đó mới kiểm từng box để giảm bỏ sót và thiên kiến tin máy.
