# Label contract — Phố châu Á — BỔ SUNG v1.1 — [2A202603122 - Võ Khắc Xuân Nguyên]

> Bổ sung cho vòng REWORK, dựa trên lỗi đã quan sát. Học viên xác nhận trước khi áp dụng. Viết guideline chưa đồng nghĩa đã sửa nhãn hoặc đạt QC; giữ nguyên các ngưỡng trong taxonomy.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định cho vòng sửa | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 3 | Box/mask xe có gồm giỏ và hàng nhô ra không? | Box gồm giỏ, mâm và giá gắn cố định. Giữ quy tắc v1.0: hàng rời nhô hơn khoảng 20% chiều dài xe không mở rộng box xe. Mask giữ bộ phận xe/giỏ nhìn thấy, loại hàng rời phân biệt được; không đoán phần xe bị hàng che. | [img_031](../../data/images/img_031.jpg) | CAL-04 |
| 5 | Kính, vùng hở và người lái có thuộc mask xe không? | Mặt kính kín thuộc xe; vùng hở nhìn xuyên và người lái không thuộc mask xe. Tách người thành đối tượng riêng; giữ quy tắc v1.0 về bỏ lỗ lớn từ khoảng 5% diện tích. | [img_013](../../data/images/img_013.jpg), [mask SAM 2.1](../masks/tuktuk_img013_sam2.jpg) | CAL-05 |
| 7 | Có ngưỡng confidence để chấp nhận mà không kiểm không? | Không. Mọi box đều phải kiểm lớp, vị trí/kích thước và đối chiếu vật thật; quét ảnh để tìm vật chưa có box. Confidence chỉ hỗ trợ sắp thứ tự duyệt. | [img_032](../../data/images/img_032.jpg) | CAL-06 |

**Câu 1, 2, 4, 6 chưa chốt:** chưa có đối chiếu đủ rõ để đặt quy tắc mới về xe điện, cụm xe dày gần camera, thay ngưỡng che khuất 20% hoặc gộp box ảnh đêm. Tạm giữ v1.0; ca không chắc quy tắc phải tick `can_review` và ghi lại. Nếu ca chưa chốt ảnh hưởng nhãn cần phát hành, chọn HOLD để làm rõ.

## 2. Ca khó mới (từ lỗi của máy, bẫy lọt, box `can_review`)

| Ảnh | Tình huống | Máy/nhãn hiện tại | Quyết định | Bổ sung hoặc quy tắc áp dụng |
|---|---|---|---|---|
| [img_004](../../data/images/img_004.jpg), [img_013](../../data/images/img_013.jpg) | Tuk-tuk có mui | YOLO26 gán `car`; SAM vẫn giữ lớp đó | Sửa thành `three_wheeler` sau khi kiểm cấu trúc xe; người lái là `person` riêng. | CAL-01: áp dụng mục 2 v1.0, không đổi định nghĩa lớp. |
| [img_003](../../data/images/img_003.jpg), [img_035](../../data/images/img_035.jpg) | Box xe thiếu mái | QC: ID 1 và 435 cắt phần mái/khung trên | Mở box đủ phần mái, khung và bánh nhìn thấy; không bao bóng đổ. | CAL-02: áp dụng mục 2, 4.1 v1.0; thêm bước kiểm bốn cạnh box. |
| [img_006](../../data/images/img_006.jpg) | Người ngồi quay lưng bị bỏ sót | QC chưa có box cho người mặc áo/váy hoa vàng tím giữa người áo cam và người áo xanh lá | Thêm `person` ôm phần nhìn thấy, kiểm tiếp các người ngồi sát nhau. | CAL-03: áp dụng mục 1, 2, 4.1 v1.0; thêm lượt quét ảnh khi ẩn nhãn. |

CAL-01–03 là **lỗi áp dụng quy tắc/checklist**, không phải lý do đổi taxonomy. CAL-04–06 trả lời các câu hỏi mở ở mục 1. Box ID lấy từ release đã kiểm; nếu tạo lại release cần đối chiếu ID mới.

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do / ca |
|---|---|---|
| v1.0 | Giữ nguyên bản giảng viên. | Quy tắc nền và ngưỡng QC. |
| v1.1 | Làm rõ giỏ/hàng rời, mask kính/vùng hở/người lái và việc duyệt theo confidence; bổ sung checklist lớp xe, mái và người bị sót. | CAL-01–06; 8/12 bẫy được bắt, QC có 3 lỗi hình học và 2 lỗi bỏ sót. |

**Sau khi áp dụng:** sửa nhãn trong CVAT, xuất `qc_fixed.json`, tạo lại release và kiểm QC. Chưa có kết quả vòng sửa thì vẫn giữ số đo hiện tại và đề xuất REWORK; không tự ghi đã đạt Release.

