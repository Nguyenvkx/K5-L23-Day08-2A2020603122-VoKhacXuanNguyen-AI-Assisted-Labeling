# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 5 ảnh (0 ca khó + 5 ngẫu nhiên), 90 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 5/90 = 5.6% | 90 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 4/5 = 80.0% | 5 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 399/435 = 91.7% / 9/435 = 2.1% / 27/435 = 6.2% | 435 box máy |
| Máy: added | 22/430 = 5.1% | 430 đối tượng sau duyệt |
| Bắt lỗi bẫy | 8/12 = 66.7% | 12 bẫy |

## Top 3 loại lỗi
| Loại lỗi | Số lỗi | Ảnh ví dụ |
|---|---|---|
| sai_vi_tri_kich_thuoc | 3 | img_003.jpg, img_031.jpg, img_035.jpg |
| bo_sot | 2 | img_003.jpg, img_006.jpg |

**Lỗi hệ thống**: 'sai_vi_tri_kich_thuoc' lặp lại -> sửa quy tắc / recipe, không chỉ sửa từng ảnh.

## Giới hạn thống kê
- 4 ảnh lỗi trong 5 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **96%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**REWORK**

- ảnh có lỗi 80.0% > 2%
- lỗi hệ thống: 'sai_vi_tri_kich_thuoc' chiếm 3/5 lỗi
- bắt bẫy 67% < 80% -> lô duyệt chưa đáng tin

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
