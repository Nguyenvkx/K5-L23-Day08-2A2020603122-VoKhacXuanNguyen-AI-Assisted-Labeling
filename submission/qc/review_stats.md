# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 435; đối tượng sau duyệt: 430; ngưỡng accepted: IoU >= 0.9
- Đã **loại 18 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 399 | 399/435 = 91.7% | 435 box máy |
| edited (sửa) | 9 | 9/435 = 2.1% | 435 box máy |
| deleted (xoá) | 27 | 27/435 = 6.2% | 435 box máy |
| added (vẽ thêm) | 22 | 22/430 = 5.1% | 430 đối tượng sau duyệt |

Trong số deleted + added, **8 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 14.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 244 | 2 | 4 | 5 |
| bicycle | 6 | 0 | 3 | 1 |
| motorbike | 88 | 1 | 0 | 5 |
| car | 46 | 1 | 4 | 3 |
| bus | 2 | 0 | 1 | 3 |
| truck | 3 | 0 | 4 | 1 |
| three_wheeler | 10 | 5 | 11 | 4 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 31 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 26 | 40 | 5 | 39.4% (KTC 95%: 29–51%) | 83.9% (KTC 95%: 67–93%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 8/12 = 66.7% (KTC 95%: 39–86%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - wrong_class: 2/3
  - extra: 3/3
  - missing: 1/3
  - loose: 2/3
  - lọt: img_025.jpg (wrong_class), img_009.jpg (missing), img_006.jpg (missing), img_003.jpg (loose)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
