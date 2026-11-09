# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con chở người, gồm sedan, SUV, taxi và xe bán tải. | Không xếp xe tải chở hàng, xe buýt hay mô tô vào lớp này. |
| `truck` | Xe tải chở hàng hoặc xe đầu kéo. | Phân biệt với xe bán tải (xếp `car`) và van chở hàng (xếp `van`) theo kiểu thân xe. |
| `bus` | Xe buýt và minibus chở khách. | Nhận diện theo kiểu xe buýt chở khách; không xếp van/minivan vào `bus` chỉ vì xe cao hoặc đang chở người. |
| `van` | Xe van/minivan dùng để chở người hoặc hàng. | Phân biệt với xe buýt/minibus theo kiểu thân xe; nếu ảnh không đủ rõ để phân biệt thì ghi lại, không tự đoán. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: ôm sát đường bao thân xe nhìn thấy được, không lấy thêm mặt đường/trời hay xe bên cạnh; mỗi xe có một box riêng.
- Vật thể bị che khuất: vẫn gán nếu phần nhìn thấy đủ nhận diện lớp; box theo phần thân nhìn thấy, không gộp với xe che khuất.
- Vật thể bị cắt bởi rìa ảnh: gán nếu vẫn nhận diện được; box dừng tại rìa ảnh ở phía bị cắt, không ước đoán phần nằm ngoài khung.
- Vật thể rất nhỏ/mơ hồ: chỉ gán khi còn đủ dấu hiệu nhận diện chắc chắn một trong bốn lớp; nếu không, không đoán lớp/box mà ghi lại ảnh để hỏi giảng viên. Không gán mô tô, người đi bộ hay vật thể ngoài schema.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_002` | Xe van màu sáng ở sát rìa phải, bị cắt khung; gần đó có xe buýt và nhiều xe con. | Gán `van` (ID 3), box theo phần xe nhìn thấy và dừng ở rìa phải. | Đây là kiểu thân van/minivan, không phải xe buýt/minibus; việc bị cắt khung không làm đổi lớp và không được đoán phần ngoài ảnh. |
| `drive_026` | Nhiều xe buýt nối sát nhau, một phần thân xe bị xe phía trước che khuất; xe con ở ngay cạnh. | Gán từng xe buýt thành box `bus` (ID 2) riêng; xe con vẫn là `car` (ID 0), không gộp box. | Schema phân lớp theo loại xe, không theo việc xe bị che hay đứng cạnh nhau; mỗi vật thể nhìn thấy đủ nhận diện có một box. |
| `drive_041` | Xe buýt lớn ở góc trái bị cắt bởi cạnh ảnh trong cảnh mưa, các xe phía sau nhỏ và dễ lẫn. | Gán xe lớn nhận diện được là `bus` (ID 2); box bám phần nhìn thấy và giới hạn tại cạnh trái, không suy rộng ra ngoài khung. | Xe buýt vẫn thuộc schema khi bị cắt khung; chỉ bỏ qua xe phía sau nếu kích thước/độ rõ không đủ để xác định chắc lớp. |
