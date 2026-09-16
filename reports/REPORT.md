# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Văn Trọng   Nhóm: Nhóm 2   Ngày: 16/09/2026

> Báo cáo được tổng hợp và đối chiếu tự động từ các công cụ `tools/check_pose_labels.py`, `tools/visibility_report.py` và `tools/evaluate_pose_annotations.py`.

---

## 1. Nhãn của tôi

<!-- Số liệu lấy từ reports/visibility_report.md và outputs/visibility_report.json -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 335 / 57 / 101 |
| Thời gian trung bình mỗi ảnh | ~4.5 phút / ảnh (~90 phút tổng) |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. **left_hip** (28% - 8/29 skeleton gán v=1)
2. **right_ankle** (24% - 7/29 skeleton gán v=1)
3. **right_wrist** / **right_hip** / **left_knee** (21% - mỗi khớp 6/29 skeleton gán v=1)

**Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích:**

Các khớp này đúng là những vị trí gây nhiều khó khăn và mất thời gian nhất khi gán nhãn, nhưng cần phân biệt rõ giữa việc **"hay bị che" (occluded)** và **"khó xác định vị trí giải phẫu"**:
- **Khớp hông (`left_hip`, `right_hip`):** Đây là khớp **khó xác định vị trí giải phẫu nhất**. Người trong ảnh mặc trang phục dài (quần âu, váy, áo khoác thụng) làm ẩn hoàn toàn mấu chuyển lớn xương đùi và khớp háng. Ta không có điểm tựa thị giác trực tiếp mà phải ước lượng vị trí đối xứng qua thắt lưng và nếp gấp đùi.
- **Khớp cổ tay (`wrist`) và cổ chân (`ankle`):** Đây là các khớp **hay bị che khuất vật lý nhất** do bị che bởi bàn ghế, túi xách, tay lái xe hoặc do cơ thể xoay nghiêng tự che khuất. Tuy nhiên, chúng không quá khó xác định tọa độ vì ta có thể lần theo trục xương cẳng tay và cẳng chân để suy ra điểm tâm khớp.

---

## 2. Chấm với gold

<!-- Lấy từ outputs/eval_vs_gold.json trước và sau rework (đối chiếu qua công cụ evaluate_pose_annotations.py) -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.843 | 1.000 |
| OKS@0.50 | 0.966 | 1.000 |
| OKS@0.75 | 0.793 | 1.000 |
| Lỗi `dao_trai_phai` | 1 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 19 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: tên ảnh, người thứ mấy, keypoint, thao tác sửa):

- `train_15.jpg` người #2: Đảo ngược toàn bộ các cặp khớp trái/phải (`left_shoulder` ↔ `right_shoulder`, `left_elbow` ↔ `right_elbow`, `left_wrist` ↔ `right_wrist`, `left_hip` ↔ `right_hip`, `left_knee` ↔ `right_knee`, `left_ankle` ↔ `right_ankle`); bổ sung các khớp mắt/tai trái (`left_eye`, `left_ear`) và hông trái (`left_hip`) do nhầm hướng cơ thể.
- `train_13.jpg` người #1: Bổ sung 1 skeleton bị bỏ sót hoàn chỉnh (17 keypoints) khi trong ảnh có nhiều người đứng chen chúc.
- `train_13.jpg` người #2: Đổi cờ từ `v=0` sang `v=1` và ước lượng vị trí cho các khớp bị che khuất: `left_shoulder`, `left_hip`, `left_knee`.
- Sửa lỗi xoá khớp bị che (`xoa_khop_bi_che`) từ `v=0` sang `v=1` kèm đặt chấm ước lượng tọa độ giải phẫu tại 19 vị trí:
  - `train_01.jpg` người #1 (`left_wrist`) và người #2 (`right_wrist`).
  - `train_06.jpg` người #1 (`right_hip`).
  - `train_08.jpg` người #1 (`left_ankle`).
  - `train_11.jpg` người #1 (`left_hip`, `right_hip`).
  - `train_12.jpg` người #1 (`left_hip`, `left_knee`, `left_ankle`).
  - `train_15.jpg` người #1 (`left_ankle`).
  - `train_16.jpg` người #2 (`nose`).
  - `train_18.jpg` người #1 (`right_ankle`).
  - `train_19.jpg` người #2 (`left_ear`, `right_wrist`).
  - `train_20.jpg` người #1 (`left_elbow`, `left_hip`).
- Tinh chỉnh các khớp bị lệch nhẹ (`lech_nhe`) về đúng tâm giải phẫu: `train_01.jpg` người #2 (`right_eye`), `train_03.jpg` người #1 (`left_hip`, `right_hip`), `train_08.jpg` người #1 (`left_knee`), `train_11.jpg` người #1 (`right_wrist`).

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ, bạn nghĩ vì sao mình vẫn sai?

- Lỗi đảo trái/phải xảy ra ở ảnh **`train_15.jpg`**, người thứ 2.
- Đây là một bức ảnh ở mức độ **trung bình - dễ**, người đứng hướng về phía máy ảnh, tư thế tương đối rõ ràng.
- **Nguyên nhân sai:** Trong quá trình gán nhãn với tốc độ nhanh, tôi đã bị đánh lừa bởi góc nhìn phối cảnh (egocentric perspective thay vì subject-centric): nhìn tay/chân ở nửa bên phải của bức ảnh và vô thức gán nhãn là `right_*`, trong khi xét theo hệ quy chiếu cơ thể của đối tượng thì đó lại là tay trái (`left_*`). Khi áp dụng đúng quy tắc *"tự đặt mình vào vị trí của người đó hướng mặt ra ngoài rồi giơ tay"*, tôi đã phát hiện và hoán đổi lại đúng toàn bộ các cặp khớp đối xứng.

---

## 3. Kiểm chéo

Bạn cùng nhóm: Trần Tuấn Anh (Nhóm 2)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_hip` / `right_hip` | 28% | 10% | 18% | Guideline chưa thống nhất: Bạn gán `v=0` khi không thấy hông lộ ra; tôi gán `v=1` và ước lượng vị trí chậu theo xương đùi. |
| `right_wrist` | 21% | 7% | 14% | Gán sai quy tắc: Khớp cổ tay khuất sau lưng vẫn còn trong khung ảnh nhưng bạn đánh cờ Outside (`v=0`) thay vì Occluded (`v=1`). |
| `left_ankle` / `right_ankle` | 24% | 14% | 10% | Guideline chưa rõ đối với người bị che khuất chân bởi bàn ghế thấp. |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- **Quy tắc ước lượng hông (`hip`):** Mọi đối tượng có thân người nằm trọn trong khung hình bắt buộc phải có đủ 2 khớp hông (`v=1` nếu mặc quần áo dài che khuất), đặt chấm tại tâm khớp háng ước lượng nằm ngang mép dưới khóa quần/xương chậu, không được dùng `v=0`.
- **Phân biệt `v=1` và `v=0` cho các khớp bị che:** Chỉ đánh `v=0` (Outside) khi đường bao cơ thể của đối tượng bị mép ảnh cắt ngang và vị trí khớp chắc chắn nằm ngoài khung hình hiển thị. Nếu khớp nằm trong không gian ảnh nhưng bị vật thể hoặc bộ phận khác che khuất, bắt buộc đặt chấm ước lượng và tick `Occluded` (`v=1`).

---

## 4. Model

<!-- Số liệu đối chiếu từ notebook day4_pose_finetune_yolo26.ipynb (yolo26n-pose gốc và sau fine-tune trên 20 ảnh) -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.9024 | 0.8872 | -0.0152 |
| pose_mAP50-95 | 0.6518 | 0.6245 | -0.0273 |
| pose_precision | 0.8650 | 0.8412 | -0.0238 |
| pose_recall | 0.8120 | 0.8035 | -0.0085 |
| box_mAP50-95 | 0.7432 | 0.7280 | -0.0152 |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?**
   - `pose_mAP50-95` giảm nhẹ khoảng 0.0273 (từ 0.6518 xuống 0.6245).
   - *Model học được gì:* Tập 20 ảnh dạy cho model thói quen dự đoán các khớp bị che khuất (`v=1`) với tọa độ ước lượng kiên quyết hơn (đặc biệt là khớp hông và cổ tay vốn hay bị gán thiếu trên COCO gốc).
   - *Model bị hỏng điều gì:* 20 ảnh là tập dữ liệu quá nhỏ (under-sampling / domain shift hẹp), gây hiện tượng "catastrophic forgetting" nhẹ trên phân phối tư thế đa dạng của tập test chuẩn, dẫn tới giảm độ mượt mà và tổng quát hóa của các trọng số pretrained từ hàng chục nghìn ảnh COCO.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?**
   - `box_mAP50-95` (0.7280) cao hơn `pose_mAP50-95` (0.6245) khoảng 0.1035 (hơn 10%).
   - Model **tìm người (bounding box) dễ hơn tìm khớp (keypoints) rất nhiều**.
   - *Lý do:* Bounding box bao trùm toàn bộ cơ thể, có diện tích lớn, đường biên tương phản rõ rệt với nền và không nhạy cảm với sai số vài pixel. Trong khi đó, keypoint là các điểm đơn lẻ (độ phân giải 1 pixel) có không gian bậc tự do rất lớn (các khớp xoay nhiều góc độ), bán kính dung sai OKS cực kỳ khắt khe (đặc biệt khớp mắt, tai có $\sigma = 0.025$), và thường xuyên bị che khuất lẫn nhau khi người vận động.

3. **Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):**
   - Ở ảnh test `test_04.jpg`, người đi bộ bị chiếc xe đạp che khuất nửa dưới chân:
   - Model gặp lỗi **"trượt hẳn" (miss)** ở khớp cổ chân phải (`right_ankle`) do nhầm căm xe đạp với cấu trúc cẳng chân, và lỗi **"lệch nhẹ"** ở khớp hông trái (`left_hip`) do nếp nhăn của áo khoác thụng đánh lừa vị trí tâm xương chậu.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?**
   - Ảnh **`train_13.jpg`** (ảnh nhóm người ngồi chen chúc và che khuất chéo) có OKS thấp nhất giữa nhãn của tôi và model (~0.71).
   - **Nhãn của tôi (sau khi qua gold) đúng hơn model.**
   - *Căn cứ:* Con người có hiểu biết về giải phẫu học cơ thể và tính liên tục của cấu trúc xương khớp khi bị vật cản che một phần (contextual semantic reasoning). Model trong trường hợp bị che khuất dày đặc đã bị nhiễu bởi ranh giới bóng đổ và hoa văn quần áo, dẫn đến việc dự đoán chấm khớp tay của người này kéo sang cánh tay của người bên cạnh (gần như nhầm người).

5. **Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?**
   - **Có**, cả hai ảnh tôi gán gặp nhiều khó khăn nhất ban đầu là `train_13.jpg` và `train_15.jpg` cũng chính là hai ảnh model đạt OKS thấp nhất.
   - *Ý nghĩa:* Điều này chứng minh sự tương quan mạnh mẽ giữa độ khó của dữ liệu thị giác đối với con người và đối với mạng nơ-ron. Những bức ảnh này có mật độ đối tượng cao, mức độ che khuất phức tạp (occlusion) và góc chiếu sáng không thuận lợi. Đây là các trường hợp "hard cases" điển hình trong thị giác máy tính, đòi hỏi guideline gán nhãn phải cực kỳ chi tiết và mô hình cần lượng dữ liệu huấn luyện lớn hơn nhiều để có thể xử lý chính xác.

---

## 5. Một rule evidence bạn đã dùng

**Ảnh:** `train_12.jpg` | **Người thứ:** #1 | **Khớp:** `left_knee` (đầu gối trái)

- **Quyết định:** Chọn trạng thái **`v = 1`** (Occluded - bị che nhưng còn trong khung, đặt chấm ước lượng) thay vì `v = 0` (Outside - ra ngoài mép ảnh).
- **Căn cứ thị giác:** Đối tượng trong ảnh đang ngồi làm việc tại bàn, hướng nhìn chếch sang phải. Phần đùi trái hướng về phía trước và cẳng chân trái bị mặt bàn cùng thùng tài liệu che khuất hoàn toàn. Tuy nhiên, toàn bộ thân trên, mép ghế tựa và vị trí mặt bàn đều nằm gọn bên trong bức ảnh, cách biên dưới của khung hình hơn 60 pixel.
- **Lý do lựa chọn:** Dựa vào khoảng cách từ hông trái xuống mặt ghế và độ dốc của đùi trái, có thể xác định chắc chắn 100% vị trí giải phẫu của khớp đầu gối trái nằm ngay dưới góc bàn và hoàn toàn thuộc phạm vi tọa độ của khung hình. Do khớp chưa từng vượt ra khỏi mép ảnh nên theo đúng nguyên tắc chuẩn hóa: không được đánh `v=0` (sẽ bị coi là lỗi xoá khớp bị che và mất điểm OKS), mà bắt buộc phải đặt chấm tại vị trí giao thoa ước lượng giải phẫu với cờ `v=1`.
