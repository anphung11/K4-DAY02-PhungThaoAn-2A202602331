# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Phùng Thảo An
**MSSV:** 2A202602331git status
**Hình thức:** Cá nhân
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_008, drive_022, drive_033, drive_038
- Số vật thể thực tế: 50
- Mã SHA-256 của gói YOLO của bạn: 7dc4692e7a53def60a295598ce40478331cf244aef3fcee88d89c0a12cb4640b
- Mã SHA-256 của gói CVAT gốc của bạn: ed23476dfb7d695dd11b0000db7afaea270394fe202eda9c86c5957c23cd9a7e
- Nguồn đối chiếu: Bộ tham chiếu do người hướng dẫn thực hành cấp.
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: 15:53

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: Tôi đã hoàn thành việc rà soát dữ liệu, xuất hai gói định dạng (YOLO và CVAT 1.1) và chạy notebook để sinh mã băm SHA-256 chốt trạng thái dữ liệu trước khi nhận và mở gói dữ liệu đối chiếu từ Lab Coach. Mã băm này đảm bảo dữ liệu không bị thay đổi sau khi xem bài tham chiếu.

## 2. Quyết định phân lớp

Tình huống 1:

Ảnh/vật thể: [Ảnh 0, #11]

Lớp: truck

Dấu hiệu nhìn thấy: Phần đuôi có thùng hở tách biệt hoàn toàn với cabin lái phía trước.

Quy tắc áp dụng: Xe có thùng, sàn chở hàng hoặc thiết bị công vụ rõ ràng.

Tình huống 2:

Ảnh/vật thể: [Ảnh 0, #4]

Lớp: bus

Dấu hiệu nhìn thấy: Xe có tỷ lệ thân dài, dọc bên hông có nhiều cửa sổ kính liên tiếp.

Quy tắc áp dụng: Xe khách thân dài, có nhiều cửa sổ hoặc nhiều hàng ghế.

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:
Lớp (class) dùng để phân loại bản chất của phương tiện, trong khi thuộc tính (attribute) mô tả trạng thái vật lý của nó trên ảnh. Ví dụ: Một chiếc xe buýt bị lấp sau thân cây. Nó vẫn phải được gán nhãn lớp là bus, và sử dụng thuộc tính visibility = occluded để mô tả tình trạng bị lấp. Không được đổi lớp của nó thành loại khác chỉ vì nó bị che khuất.

## 3. Tự kiểm tra và sửa nhãn

- Trường hợp 1:

Trước khi sửa: Vẽ khung bao trùm cả bóng đổ của xe xuống mặt đường.

Loại lỗi: hình học

Cách phát hiện: Phóng to 100% rà soát lại các ranh giới Bounding Box.

Sau khi sửa và quy tắc: Co hẹp Bounding Box lại, chỉ bám sát phần khung kim loại và bánh xe thực tế nhìn thấy.

- Trường hợp 2:

Trước khi sửa: Xe nằm ở sát mép ảnh nhưng quên không gán thuộc tính.

Loại lỗi: thuộc tính

Cách phát hiện: Kiểm tra lại thanh menu bên phải trên CVAT thấy mục boundary chưa chọn.

Sau khi sửa và quy tắc: Cập nhật thuộc tính boundary = truncated (bị mép ảnh cắt).

- Trường hợp 3:

Trước khi sửa: Gán nhãn một chiếc SUV gầm cao thành lớp van.

Loại lỗi: lớp

Cách phát hiện: Đọc lại định nghĩa các lớp phương tiện trong lúc kiểm tra (Self-QC).

Sau khi sửa và quy tắc: Sửa lại thành lớp car (áp dụng quy tắc: xe SUV được xếp vào nhóm car).

Số hộp needs_review trước và sau khi kiểm: 4 trước / 1 sau

Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:
Gặp một phần đuôi xe nằm sát mép ảnh và bị che khuất phần lớn bởi chướng ngại vật, không đủ chi tiết để khẳng định là xe con (car) hay xe van. Quyết định: Gán nhãn dự đoán khả dĩ nhất là car, đặt visibility = unclear, chuyển review_state = needs_review và ghi chú tình huống trực tiếp vào phiếu quy tắc để chờ Lab Coach giải đáp.

## 4. Một dòng nhãn YOLO
- Dòng `class x_center y_center width height`: [3, 0.94332, 0.460758, 0.094547, 0.120641]
- Tên lớp và tọa độ điểm ảnh `xyxy`: [573.5, 256.3, 634.0, 333.5]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
Định dạng YOLO (file .txt) chỉ kiểm tra cú pháp kỹ thuật: đảm bảo số đầu tiên là số nguyên (mã ID lớp) và 4 số sau là số thập phân từ 0 đến 1 (tọa độ tương đối). Hệ thống không thể biết bằng mắt thường rằng mã ID đó đã phân đúng loại xe chưa, hay các tọa độ đó có thực sự bám sát ranh giới xe trên ảnh thực tế hay bị vẽ thừa ra ngoài.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: "drive_022", "drive_033", "drive_038"
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`:
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Cần kiểm tra lại các Bounding Box của lớp truck ở góc xa xem diện tích quá nhỏ hoặc bị thiếu thuộc tính needs_review khiến mô hình không học được đặc trưng.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu kiểm tra file nhãn gốc mà xe tải đó đã được gán nhãn hoàn hảo (box sát, đủ thuộc tính), điều đó chứng tỏ vấn đề nằm ở năng lực của mô hình (quá nhỏ, số epoch ít) chứ không phải do lỗi dữ liệu.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Tập dữ liệu 4 ảnh là quá nhỏ bé và thiếu tính đại diện (đa dạng về bối cảnh, góc chụp, ánh sáng). Đánh giá trên một tập dữ liệu validation không đủ lớn sẽ dẫn đến hiện tượng quá khớp (overfitting) hoặc sai lệch thống kê, không phản ánh đúng năng lực thật của mô hình trên môi trường thực tế.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 39
- IoU trung bình và trung vị: Trung bình: 0.7978, Trung vị: 0.8252
- Mức đồng thuận lớp: 0.6923 (đạt khoảng 69.23%)
- Số hộp phía bạn không ghép được: 12
- Số hộp phía đối chiếu không ghép được: 11
- Một điểm khác biệt cụ thể: Quan sát trực quan trên ảnh comparison_overlay.png (đặc biệt ở các ảnh drive_022 và drive_038), mặc dù thuật toán ghép được nhiều cặp hộp theo hình học, có sự lệch ranh giới nhẹ giữa khung xanh (của tôi) và khung đỏ (của tham chiếu), kèm theo độ đồng thuận lớp chỉ đạt 69.23%, cho thấy một số vật thể bị gán nhãn phân loại khác nhau giữa hai bên (ví dụ: nhầm lẫn ranh giới giữa xe van và xe tải/car ở các góc khuất).
- Quy tắc hoặc hành động sửa phát sinh: Rà soát lại các điểm bất đồng trên ảnh overlay, đối chiếu lại định nghĩa phân loại phương tiện để chuẩn hóa ranh giới và tên lớp cho các lượt chạy sau.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
Vì cả hai bên hoàn toàn có thể cùng mắc một lỗi hệ thống hoặc hiểu lầm một quy tắc gán nhãn giống nhau (ví dụ: cùng vẽ bao trùm bóng râm hoặc cùng nhầm lẫn thuộc tính phân loại một dòng xe). Khi đó, IoU hình học và mức đồng thuận vẫn có thể rất cao, nhưng bản chất nhãn dữ liệu vẫn chưa phản ánh đúng tiêu chuẩn thực tế.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:
Minh chứng mạnh nhất là tính nhất quán giữa mã băm SHA-256 của file YOLO và file CVAT gốc, chứng minh toàn bộ thuộc tính và hình học khớp nhau hoàn toàn tại một trạng thái dữ liệu. Câu hỏi: Với những chiếc xe bị lấp sau biển báo giao thông và bị chia làm 2 mảnh trên ảnh, ta nên vẽ 1 box lớn bao trùm qua biển báo (dùng occluded) hay vẽ 2 box nhỏ riêng biệt?
