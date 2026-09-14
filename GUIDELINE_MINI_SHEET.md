# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Phùng Thảo An
**MSSV:** 2A202602331
**Hình thức:** Cá nhân
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: Ảnh 3, object 46
- Dấu hiệu nhìn thấy: Xe có tỷ lệ thân rất dài, dọc bên hông xe có một dãy nhiều cửa sổ kính liên tiếp (hoặc nhìn thấy rõ nhiều hàng ghế hành khách bên trong).
- Quy tắc áp dụng: Xe khách thân dài, có nhiều cửa sổ hoặc nhiều hàng ghế được xếp vào lớp bus.
- Quyết định: Bus
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Sẽ gán nhãn theo dự đoán khả thi nhất, nhưng bắt buộc chuyển review_state sang needs_review để đánh dấu cần rà soát lại.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: Ảnh 0, object 11
- Dấu hiệu nhìn thấy: Phần đuôi xe có thùng chở hàng (hoặc bạt/sàn phẳng) tách biệt hoàn toàn với khoang cabin lái ở phía trước, không phải dạng thân hộp kín liền mạch.
- Quy tắc áp dụng: Xe có thùng, sàn chở hàng hoặc thiết bị công vụ rõ ràng được phân loại là truck.
- Quyết định: truck
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì: Sẽ gán nhãn dựa trên phần hình dáng chiếm ưu thế nhất, thiết lập visibility = unclear (nếu hình ảnh mờ/bị che) và đổi review_state = needs_review.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: Ảnh 2, object 37
- Dấu hiệu nhìn thấy khi phóng 100%: Đuôi xe hình hộp, dài, có nhiều ô kính
- Giá trị `visibility`:  occluded
- Giá trị `boundary`: truncated
- Trạng thái `review_state`:  confident
- Lý do:  bị che những vẫn nhìn nhận được là xe bus

## 6. Xác nhận tự kiểm tra

- [x ] Đã rà đủ bốn ảnh.
- [ x] Đã kiểm vật thể thiếu và trùng.
- [x ] Đã kiểm lớp và hình học từng hộp.
- [ x] Mỗi hộp có đủ ba thuộc tính.
- [ x] Đã xử lý mọi hộp `needs_review`.
- [x ] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x ] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x ] Số vật thể thực tế: 50 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
