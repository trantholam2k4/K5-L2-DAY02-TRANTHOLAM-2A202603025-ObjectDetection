# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Phương tiện giao thông cá nhân từ 4–7 chỗ và xe chở hàng nhỏ có buồng lái riêng. | Sedan, SUV, hatchback, xe taxi và **toàn bộ xe bán tải (pickup truck)**. Phân biệt với `truck`: xe bán tải có cabin nhỏ và thân liền khối. |
| `truck` | Xe cơ giới chuyên dụng dùng cho mục đích vận tải hàng hóa nặng hoặc đầu kéo. | Xe tải thùng, xe ben, xe container, xe bồn. Phân biệt với `van`: thùng xe tách rời buồng lái hoặc kích thước cồng kềnh hơn nhiều. |
| `bus` | Phương tiện vận tải hành khách công cộng cỡ lớn hoặc xe khách nhiều chỗ. | Xe buýt nội đô, xe khách 29–45 chỗ, xe buýt 2 tầng. Phân biệt với `van`: kích thước chiều dài và chiều cao vượt trội, chở nhiều người. |
| `van` | Phương tiện hình hộp chữ nhật tầm trung dùng chở người hoặc chở hàng hóa nhẹ. | Xe minivan gia đình, xe 16 chỗ (Ford Transit, Hyundai Solati), xe van giao hàng đóng kín thùng liền thân. |

## Quy tắc chung

- **Box bám sát vật thể nghĩa là:** Bounding box phải ôm sát các mép ngoài cùng của thân xe (vỏ xe, gương, cản trước/sau), không để lọt khoảng trống mặt đường, vỉa hè hoặc bầu trời vượt quá 5% diện tích box.
- **Vật thể bị che khuất:** Nếu xe bị che khuất bởi xe khác hoặc chướng ngại vật nhưng vẫn nhìn thấy một phần và nhận diện chắc chắn được lớp $\rightarrow$ Chỉ vẽ bounding box bao quanh **phần thân xe thực sự nhìn thấy được**, không đoán mò phần bị che.
- **Vật thể bị cắt bởi rìa ảnh:** Cạnh của bounding box phải áp sát đúng mép ảnh (tọa độ chạm biên 0.0 hoặc 1.0). Tuyệt đối không kéo box lòi ra ngoài ảnh để phỏng đoán phần thân xe bị mất.
- **Vật thể rất nhỏ/mơ hồ:** Các xe ở quá xa sát đường chân trời có kích thước dưới 15x15 pixel, hoặc xe bị che khuất trên 85% không thể phân biệt rõ là car, van hay truck $\rightarrow$ **Không gán nhãn** để tránh sinh lỗi False Positive. Không gán người đi bộ, xe máy, xe đạp.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001.jpg` | Xuất hiện nhiều phương tiện kích thước rất nhỏ (dưới 15-20 pixel) ở khu vực hậu cảnh phía sau sát đường chân trời. | Không gán nhãn các xe quá nhỏ ở xa. | Tránh sinh lỗi False Positive (báo động giả) do đối tượng quá nhỏ không phân biệt chắc chắn được lớp, bảo vệ chỉ số Precision và F1. |
| `drive_005.jpg` | Phương tiện bị mờ nhòe (motion blur) hoặc điều kiện ánh sáng kém, không nhìn rõ hình dáng chi tiết để phân biệt chắc chắn loại xe (`car` hay `truck`). | Bỏ qua, không gán nhãn phương tiện này. | Tuân thủ quy tắc hướng dẫn: "Nếu không thể xác định chắc lớp, tuyệt đối không tự đoán mò". |
| `drive_009.jpg` | Phương tiện bị che khuất phần lớn (trên 70-80%) bởi phương tiện hoặc vật cản khác phía trước. | Chỉ vẽ bounding box bao quanh phần thân xe thực sự nhìn thấy được (hoặc bỏ qua nếu chỉ còn một mảnh quá nhỏ không rõ dạng). | Quy tắc che khuất: Vật thể bị che một phần vẫn gán box quanh phần nhìn thấy nếu nhận diện được lớp, không đoán phần khuất. |
