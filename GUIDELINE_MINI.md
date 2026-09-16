# Mini guideline - nhóm: Nhóm 2  |  người gán: Nguyễn Văn Trọng  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt `v = 1` tại vị trí ước lượng tâm khớp chậu (giao điểm giữa trục thắt lưng và nếp gấp đùi). Không dùng `v = 0` nếu người còn trọn vẹn trong ảnh. | Hông là khớp nội tại của xương chậu, luôn có thể ước lượng giải phẫu khi nhìn thấy thân và chân. Dùng `v = 0` sẽ bị coi là xoá khớp bị che và làm mất điểm OKS. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu nhìn thấy một phần vành tai hoặc mép tóc/mũ ôm sát tai thì đặt `v = 1` tại tâm lỗ tai ước lượng. Chỉ dùng `v = 0` nếu đầu quay nghiêng đi hoàn toàn (không thuộc bán cầu nhìn thấy). | Giữ tính liên tục của cấu trúc ngũ quan và giúp model nhận diện đúng góc quay hướng mặt của đầu. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp từ đầu gối và cổ chân bị mép ảnh cắt ra ngoài thì đặt cờ Outside `v = 0` và không đặt chấm. Các khớp còn trong ảnh đánh `v = 1` hoặc `v = 2`. | Khớp đã ra ngoài mép ảnh thì không tồn tại tọa độ thực tế trên canvas. Tránh dạy model dự đoán điểm giả định ngoài biên ảnh. |
| Cổ tay nằm sau tay lái / sau thân mình | Đặt cờ `v = 1` và chấm tại vị trí khớp tiếp nối giữa cẳng tay và bàn tay theo hướng kéo dài của xương cẳng tay. | Vị trí này hoàn toàn suy đoán được từ hướng cẳng tay và vật thể cầm nắm (tay lái xe, túi xách). |
| Hai người chồng lên nhau | Hoàn thành dứt điểm từng skeleton một trước khi chuyển sang người thứ hai. Điểm của người bị che vẫn đặt `v = 1` theo đúng cơ thể của họ, không để điểm nhảy sang cơ thể người đứng trước. | Tránh lỗi nhầm người (xương kéo sang cơ thể bên cạnh), đảm bảo topology từng cá thể độc lập. |
| Người nhỏ đến mức nào thì không gán nữa | Bounding box có chiều cao < 50px hoặc quá mờ không phân biệt được các bộ phận thì không gán. Bộ ảnh core 20 ảnh đều đủ kích thước gán. | Đối tượng quá nhỏ khiến bán kính OKS dung sai siêu hẹp, model không thể học được đặc trưng keypoint. |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_06.jpg`, người thứ `1`, khớp `right_hip`

- Mơ hồ ở chỗ nào: Người mặc áo khoác dài trùm qua hông, không nhìn thấy đường cong cơ thể trực tiếp ở vùng eo và hông phải.
- Bạn quyết thế nào: Đặt cờ `v = 1` và chấm ước lượng tại vị trí khớp háng phải dựa trên đường trục cột sống và hướng cẳng chân phải.
- Vì sao: Người đứng hoàn toàn trong khung hình; hông không thể nằm ngoài ảnh. Khớp bị che nhưng vị trí có thể suy ra từ giải phẫu.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu đánh `v = 0`, model sẽ học thói quen bỏ qua khớp hông mỗi khi gặp áo khoác dài, làm giảm khả năng trích xuất bộ khung người hoàn chỉnh.

### Ca 2 - ảnh `train_15.jpg`, người thứ `2`, khớp các cặp trái/phải

- Mơ hồ ở chỗ nào: Người đứng hướng mặt thẳng về phía máy ảnh, hai tay buông xuôi, dễ bị nhầm phối cảnh trái/phải theo góc nhìn của người quan sát.
- Bạn quyết thế nào: Xác định trái/phải theo hệ quy chiếu cơ thể người trong ảnh: phần bên phải của ảnh là tay/chân trái (`left_*`), phần bên trái của ảnh là tay/chân phải (`right_*`).
- Vì sao: Tuân thủ tuyệt đối quy ước giải phẫu COCO.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Khi huấn luyện với augmentation lật ảnh ngang (`fliplr=0.5`), model sẽ bị dạy sai hai lần và đảo lộn hoàn toàn khái niệm đối xứng cơ thể, gây hỏng vĩnh viễn pose estimation.

### Ca 3 - ảnh `train_12.jpg`, người thứ `1`, khớp `left_knee`

- Mơ hồ ở chỗ nào: Người ngồi tại bàn làm việc, toàn bộ cẳng chân và đầu gối trái nằm khuất sau mép bàn gỗ dày.
- Bạn quyết thế nào: Chọn `v = 1` (Occluded), chấm tọa độ ước lượng của đầu gối trái dựa theo độ dốc xương đùi từ hông trái xuống mặt ghế.
- Vì sao: Cả thân người và mép bàn đều nằm trọn trong ảnh, cách mép dưới ảnh hơn 60px nên đầu gối chắc chắn nằm bên trong khung hình, không bị cắt ra ngoài.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu chọn `v = 0` (Outside), model sẽ học sai rằng đầu gối không tồn tại hoặc đã bị cắt khỏi ảnh mỗi khi có vật cản che ngang đùi.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_hip` / `right_hip` (bạn `28%` / họ `10%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Cả hai nguyên nhân: một phần guideline ban đầu chưa ghi rõ cách xử lý hông khi mặc quần dài, dẫn đến bạn cùng nhóm dùng `v=0` ở các ca không nhìn thấy rõ; một phần bạn cùng nhóm gán sai quy tắc (lỗi xoá khớp bị che).
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Với mọi người đứng hoặc ngồi trọn trong khung hình, bắt buộc gán đủ 2 khớp hông với cờ `v = 1` tại vị trí giao thoa giải phẫu (ngang xương chậu), nghiêm cấm dùng `v = 0`.
