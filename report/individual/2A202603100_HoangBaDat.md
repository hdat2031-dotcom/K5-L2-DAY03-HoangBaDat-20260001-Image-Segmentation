# Báo cáo thực hành Day 3 — Image Segmentation

## 1. Thông tin học viên và Tiến độ hoàn thành

* **Họ và tên:** Hoàng Bá Đạt
* **Mã học viên / MSSV:** 20260001
* **Repository nộp bài:** `https://github.com/hdat2031-dotcom/K5-L2-DAY03-HoangBaDat-20260001-Image-Segmentation`
* **Trạng thái hoàn thành các task và file ZIP tương ứng (đã lưu trong `submissions/`):**

| STT | Mã Task | Loại Segmentation | Số ảnh hoàn thành | Định dạng Export | File ZIP nộp bài | Trạng thái |
| :--- | :--- | :--- | :---: | :--- | :--- | :--- |
| 1 | `easy_semantic` | Semantic | 3/3 | Segmentation mask 1.1 | `submissions/easy_semantic.zip` | Đã hoàn thành & Save |
| 2 | `medium_instance` | Instance | 3/3 | COCO 1.0 | `submissions/medium_instance.zip` | Đã hoàn thành & Save |
| 3 | `hard_panoptic` | Panoptic | 2/2 | COCO 1.0 | `submissions/hard_panoptic.zip` | Đã hoàn thành & Save |
| 4 | `cp1_holes` | Instance | 1/1 | COCO 1.0 | `submissions/cp1_holes.zip` | Đã hoàn thành & Save |
| 5 | `cp2_slice` | Instance | 1/1 | COCO 1.0 | `submissions/cp2_slice.zip` | Đã hoàn thành & Save |
| 6 | `cp3_thin` | Semantic | 1/1 | Segmentation mask 1.1 | `submissions/cp3_thin.zip` | Đã hoàn thành & Save |
| 7 | `cp4_curb` | Semantic | 1/1 | Segmentation mask 1.1 | `submissions/cp4_curb.zip` | Đã hoàn thành & Save |
| 8 | `cp5_occlusion` | Instance | 1/1 | COCO 1.0 | `submissions/cp5_occlusion.zip` | Đã hoàn thành & Save |
| 9 | `cp6_coverage` | Semantic | 1/1 | Segmentation mask 1.1 | `submissions/cp6_coverage.zip` | Đã hoàn thành & Save |

---

## 2. Object Medium đầu tiên tự làm & Quyết định gán nhãn

* **Task & Ảnh:** `medium_instance` — Ảnh đầu tiên trong thư mục `data/tiers/medium_instance/images/`.
* **Vị trí & Class:** Chiếc ô tô nằm ở làn đường chính phía trước bên trái ảnh, gán nhãn class `car` (đối chiếu đúng schema trong `classes.json`).
* **Quy tắc chọn biên đã áp dụng:**
  * Sử dụng công cụ **Polygon** kết hợp **Brush** thủ công trong CVAT để bám sát đường viền thực tế nhìn thấy của thân xe (bao gồm kính xe, gương chiếu hậu, bánh xe).
  * **Loại trừ vùng nhiễu:** Không bao gồm phần bóng đổ của xe trên mặt đường vào mask.
  * **Không tự suy đoán phần bị che:** Tại vị trí đầu/đuôi xe tiếp xúc hoặc bị che một phần bởi phương tiện khác, chỉ vẽ đúng phần pixel nhìn thấy của chiếc xe, không kéo dài đa giác xuyên qua vật che khuất.
* **Quyết định kiểm soát chất lượng (QC):** Sau khi hoàn thành object đầu tiên và tiếp tục với các đối tượng còn lại trong ảnh, tôi kiểm tra lại danh sách **Objects** bên thanh công cụ phải của CVAT để đảm bảo mỗi phương tiện (`car`, `motorcycle`, `person`) đều là một instance độc lập có ID riêng biệt, không bị gộp chung nhiều xe cạnh nhau thành một mask.

---

## 3. Một lỗi thật đã phát hiện và khắc phục

* **Vị trí phát hiện lỗi:** Task `cp2_slice` (và kiểm tra chéo trên `medium_instance`) — Vùng hai chiếc xe đỗ/di chuyển sát nhau ở khu vực trung tâm ảnh.
* **Loại lỗi:** Lỗi gộp nhầm hai đối tượng (Merged Instances) và tràn biên nhẹ tại ranh giới tiếp xúc giữa hai xe.
* **Dấu hiệu quan sát:** Khi bật chế độ hiển thị màu theo từng object trong CVAT, hai phương tiện đứng cạnh nhau có cùng màu hiển thị và chỉ xuất hiện 1 mục duy nhất trong danh sách **Objects** (thay vì 2 instance riêng biệt theo quy tắc bài lab).
* **Hành động sửa lỗi:**
  1. Xóa mask bị gộp chung.
  2. Phóng to khu vực ranh giới tiếp xúc giữa hai xe, dùng công cụ **Polygon** vẽ lại từng đối tượng một cách độc lập, đảm bảo hai polygon bám sát đường phân tách thực tế của từng thân xe và không chồng lấn pixel lên nhau.
  3. Kiểm tra lại số lượng trong bảng **Objects** đã hiển thị đủ 2 instance `car` riêng biệt.
* **Trạng thái lưu & xuất file:** Đã bấm **Save** trên CVAT, chuyển sang frame khác rồi quay lại xác nhận dữ liệu được lưu ổn định, sau đó xuất lại định dạng `COCO 1.0` và cập nhật file `submissions/cp2_slice.zip`.

---

## 4. Ba ca chưa chắc hoặc đã cân nhắc (Edge Cases)

### Ca 1: Ranh giới giữa lòng đường (`road`) và vỉa hè (`sidewalk`) tại đoạn bó vỉa thấp
* **Vị trí:** Task `cp4_curb` (và các ảnh có vỉa hè trong `easy_semantic`) — Dải tiếp giáp dọc theo mép đường.
* **Hai cách hiểu:**
  * *Cách hiểu 1:* Phân chia theo màu sắc bề mặt nhựa đường/bê tông trên ảnh.
  * *Cách hiểu 2:* Phân chia theo ranh giới chức năng giao thông và đường bó vỉa (curb) thực tế.
* **Quy tắc / Bằng chứng áp dụng:** Theo phiếu quy tắc của chặng Easy và `cp4_curb`, ranh giới `road` – `sidewalk` phải xác định theo **chức năng sử dụng và đường bó vỉa**, không chỉ dựa vào sự tương đồng màu sắc của vật liệu.
* **Quyết định cuối cùng:** Phóng to khu vực mép đường, dùng đường gờ bó vỉa làm ranh giới chuẩn để tách bạch phần mặt đường dành cho xe chạy (`road`) và phần lề/vỉa hè dành cho người đi bộ (`sidewalk`), không để khoảng trống pixel ở giữa.

### Ca 2: Cột đèn và biển báo giao thông có kích thước mảnh trên nền trời (`sky`)
* **Vị trí:** Task `cp3_thin` — Khu vực cột trụ (`pole`) và biển báo (`traffic sign`) vươn lên nền trời.
* **Hai cách hiểu:**
  * *Cách hiểu 1:* Vẽ nét lớn bao trùm cả phần rìa mờ để đảm bảo không bị đứt đoạn mask.
  * *Cách hiểu 2:* Thu nhỏ đầu cọ/đỉnh đa giác để bám sát đúng độ dày thực tế của thân cột.
* **Quy tắc / Bằng chứng áp dụng:** Quy tắc `cp3_thin` yêu cầu phóng to tối đa và dùng brush nhỏ để giữ đúng hình dạng nét mảnh, tránh làm phình to đối tượng gây sai lệch chỉ số mIoU với lớp nền `sky` xung quanh.
* **Quyết định cuối cùng:** Phóng to vùng cột và biển báo, dùng công cụ **Polygon** đặt các điểm sát hai mép cột kết hợp **Brush** kích thước nhỏ nhất để tô liền mạch thân cột (`pole`) và mặt biển (`traffic sign`), phần nền xung quanh gán đầy đủ cho `sky`.

### Ca 3: Phương tiện bị vật thể phía trước che khuất chia thành hai mảng nhìn thấy rời nhau
* **Vị trí:** Task `cp5_occlusion` — Vị trí chiếc xe ở lớp sau bị cột/vật cản phía trước chắn ngang thân xe.
* **Hai cách hiểu:**
  * *Cách hiểu 1:* Tạo 2 object riêng biệt cho 2 phần thân xe nhìn thấy ở hai bên vật che.
  * *Cách hiểu 2:* Gộp cả 2 mảng pixel nhìn thấy vào chung 1 object duy nhất nhưng bỏ qua phần bị vật cản che khuất.
* **Quy tắc / Bằng chứng áp dụng:** Theo quy tắc chung của Instance Segmentation và trạm `cp5_occlusion`, một vật thể thực tế bị che khuất thành nhiều mảng rời nhau vẫn chỉ được tính là **một instance duy nhất** (để đếm đúng số lượng đối tượng), đồng thời chỉ tô các pixel thực sự nhìn thấy chứ không vẽ đè lên vật che phía trước.
* **Quyết định cuối cùng:** Tạo một instance duy nhất cho chiếc xe, vẽ phần mảng thân xe bên trái vật cản, sau đó giữ nguyên ID object đó trong CVAT để vẽ tiếp phần mảng thân xe bên phải vật cản, đảm bảo tổng số lượng object đếm đúng 1 xe và không lấn pixel sang vật che.
