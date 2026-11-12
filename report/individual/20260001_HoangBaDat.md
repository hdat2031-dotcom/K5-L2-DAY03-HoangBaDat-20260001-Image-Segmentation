# Báo cáo thực hành Day 3 — K5 · L2

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên [LMS](https://aithucchien.vinuni.edu.vn).

- Mã học viên theo lớp: 2A202603100
- Ngày / CVAT local: 2026-11-12 / localhost:8080
- Công cụ đã dùng: CVAT Brush, Polygon

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh đầu tiên trong medium_instance, vẽ xe ô tô ở giữa khung hình trước khi dùng gợi ý tự động.
- Class và quy tắc tôi dùng để chọn biên: Gán class "car", vẽ biên theo viền xe sát nhất có thể, bao gồm cả gương chiếu hậu nhưng không bao gồm bóng đổ dưới xe.
- Nếu dùng gợi ý sau đó: Gợi ý tự động bao rộng hơn thực tế, tôi đã kéo lại biên để sát với viền xe hơn ở phần đầu xe.
- Quyết định: Giữ biên theo viền vật lý thực của xe, không theo bóng hoặc phản chiếu.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài.

- Task/ảnh/vùng: easy_semantic, ảnh thứ nhất, vùng lề đường (sidewalk).
- Lỗi thuộc loại: sai lớp — ban đầu gán một phần sidewalk thành road do màu asphalt giống nhau.
- Bằng chứng tôi nhìn thấy: Phần lề đường có vạch phân cách và hơi lên cao so với mặt đường chính.
- Quy tắc và hành động sửa: Road vs sidewalk phân theo chức năng (xe chạy vs người đi bộ), không theo màu. Đã xóa vùng sai và vẽ lại bằng Brush đúng class sidewalk.
- Sau sửa đã Save và export lại chưa? Có, đã Save và export lại ZIP.

Chưa có điểm từ script (chưa chạy scorecard).

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp4_curb — ranh giới lề đường | Gộp chung road và sidewalk vì cùng màu asphalt, hoặc tách theo chức năng | Quy tắc task: curb là ranh giới chức năng | Tách theo chức năng; đường xe chạy = road, lề người đi = sidewalk |
| cp1_holes — cửa kính xe bus | Khoét lỗ cửa kính ra khỏi mask, hoặc giữ nguyên trong mask | Quy tắc task: holes/windows stay inside mask | Giữ kính trong mask, không khoét |
| cp5_occlusion — xe bị che khuất | Vẽ hai phần nhìn thấy riêng, hoặc nối thành một mask | Quy tắc task: vật bị che vẫn là một instance | Vẽ một mask liên tục cho toàn bộ xe, chỉ gán phần nhìn thấy |
