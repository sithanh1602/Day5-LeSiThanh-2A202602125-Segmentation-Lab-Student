# Báo cáo Day 5 — Segmentation Data Lab

- Mã học viên: 2A202602125.
- Ngày / CVAT local: 17/09/2026 / http://localhost:8080.
- Công cụ: CVAT, GitHub Actions, Google Colab và scorer của repo. Có dùng trợ lý AI để giải thích quy tắc, hỗ trợ chạy kiểm tra và tổng hợp báo cáo.
- Công cụ vẽ: Polygon/Mask Shape.

## 1. Bài đã nộp

Đã có ZIP cho cả chín task trong submissions/. Số ảnh trong export không có nghĩa mọi vật/vùng đã được tô đầy đủ.

| Task | File ZIP đúng tên | Ảnh trong export | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1, còn lỗi label | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1, còn lỗi label | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Tự kiểm báo OK cho Easy, Medium, Hard, cp1, cp2, cp5 và cp6. OK chỉ xác nhận cấu trú. cp3 và cp4 đã đọc được ZIP nhưng còn lỗi label.

## 2. Một quyết định trước khi dùng gợi ý

- Object Medium đầu tiên tự vẽ: người nữ trong ảnh `000000181542.jpg`, class `person`, vẽ bằng Mask/Polygon.
- Object khác đã xác nhận: người nam trong ảnh `000000458325.jpg`, class `person`, vẽ bằng Mask/Polygon.
- Class: `person`.
- Quy tắc đã tìm hiểu: mỗi vật một instance, chỉ lấy phần nhìn thấy, không vẽ xuyên phần bị che.
- Quyết định biên thực tế của object đầu: vẽ sát phần nhìn thấy của người nữ trong ảnh `000000181542.jpg`, dừng ở ranh quần áo/cơ thể và không tô phần bị che. Tôi chọn biên này để mask chỉ bao gồm phần người quan sát được, không lấn sang nền hoặc đoán phần khuất.
- Gợi ý tự động: không dùng. Tôi vẽ thủ công bằng Mask/Polygon trong suốt bài, không sử dụng SAM hoặc đề xuất mask tự động.

## 3. Một lỗi tôi tìm thấy và sửa

**Lỗi đã sửa: tên ZIP của cp3 và cp4.** Summary ban đầu báo thiếu cp3_thin.zip, cp4_curb.zip và liệt kê hai tên không thuộc task là 3-seg.zip, 4-seg.zip. Đã đổi tên tương ứng, commit và cập nhật repo trong Colab. Lần kiểm tiếp theo đã nhận được ảnh của hai task. Đây là sửa tên file bên ngoài, không phải sửa mask; chưa ghi nhận Save/export lại annotation sau thao tác này.

**Lỗi còn chưa sửa:**

- cp3_thin, ảnh 839f7736-abe28069.jpg: label ngoài classes.json là sidewalk.
- cp4_curb, ảnh 7d83710e-4697c3b2.jpg: label ngoài classes.json là pole, sky, traffic sign.
- Chưa sửa bộ label và export lại hai task này. Chưa có minh chứng về một lỗi annotation đã sửa trong CVAT để bổ sung vào báo cáo.

**Tự đánh giá trên Colab với ground truth ba tier thầy cung cấp:**

| Task | Metric | Điểm tự đánh giá |
| --- | ---: | ---: |
| easy_semantic | 0.845 | 19.8 / 20 |
| medium_instance | 0.300 | 0.0 / 32 |
| hard_panoptic | 0.304 | 7.0 / 30 |
| **Tổng ba tier** | | **26.8 / 82** |

Medium: mean matched IoU 0.761; P@0.5 = 0.67; R@0.5 = 0.39; TP 28, FP 14, FN 43. Có 42 mask nộp so với 71 vật trong reference. Những mask ghép được có độ khớp tương đối tốt nhưng recall thấp. Chưa đối chiếu từng ảnh để xác định nguyên nhân cụ thể của các mask không ghép được; chưa sửa và chấm lại sau kết quả này.


## 4. Ba ca chưa chắc hoặc đã cân nhắc

Ba tình huống dưới đây đã được cân nhắc và hỏi trong quá trình làm. Quy tắc đã hiểu không đồng nghĩa tất cả mask đã được sửa theo quy tắc.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| Easy — `7ee6d192-89e2408b.jpg`, bụi cây trên núi | Mỗi bụi một label hay nhiều mask cùng label vegetation? | Semantic xét lớp pixel; các mask khác ID có thể cùng label vegetation. | Dùng vegetation cho các vùng cây; kiểm biên theo phần nhìn thấy. |
| Medium — `000000373353.jpg`, nhóm ba người | Gộp ba người thành một mask hay tách ba instance? | Mỗi người là một vật riêng dù đứng sát hoặc che nhau. | Quy tắc là ba ID riêng cùng label person; cần xác nhận export đã tách đúng chưa. |
| Medium — `000000373353.jpg`, các xe đậu sát nhau | Tô cả hàng thành một vùng hay tách từng xe? | Mỗi xe một instance, chỉ lấy phần nhìn thấy. | Tách từng xe; hỏi coach khi xe quá xa hoặc bị che khó xác định ranh. Chưa kết luận mọi xe trong export đã đúng. |




