# Báo cáo Day 13 — Đinh Hữu Minh, 2A202603129

## 1. Kết luận trước

Task `D13-2A202603129`, Windows, trình duyệt Chrome và trình duyệt trong GPT, CVAT 2.81.0 tại localhost:8090. SDK 2.83.0 có cảnh báo khác phiên bản nhưng đã xuất được số liệu. Đã kiểm chéo các ca gán nhãn với MSSV `2A202603058`; kết quả trao đổi ghi ở mục 5.

Bản báo cáo chốt theo nhãn học viên đã sửa và Save ngày 26/11/2026, xuất lại bằng script lab. Repo: `K5-L2-DAY13-DinhHuuMinh-2A202603129-PointCloudCuboid`.

Bản đồng bộ sau rà soát có **100 cuboid**, gồm Car: 81; Bus: 3; Cyclist: 10; Pedestrian: 2; Truck: 4. Phân bố: s01f17: 8; s01f42: 9; s02f07: 11; s02f52: 7; s03f09: 11; s03f25: 5; s04f20: 19; s04f51: 7; s05f08: 15; s05f32: 8. Ít nhất là `s03f25` với 5 hộp: ngoài hai xe phía trước đã bổ sung ba xe bị che ở hai bên/phía sau. `s01f17` có 8 hộp sau khi bổ sung xe bên trái qua dải phân cách. Cả 10 frame đạt số hộp tối thiểu theo đề. Tự kiểm còn **3/100 hộp có cảnh báo**. Điều này chỉ xác nhận các ngưỡng tự kiểm, không chứng minh đã tìm hết vật hoặc cuboid chính xác tuyệt đối. Các xe gần có nhiều điểm là phần có cơ sở hình học tốt hơn; vật xa, che khuất và mặt đường ở vị trí cao/thấp khác nhau vẫn cần thận trọng.

Thao tác CVAT: xoay/kéo khung 3D và cuộn để đổi góc, dời hoặc phóng cảnh; chọn đối tượng rồi dùng Fit views để nhìn gần. Cuboid dùng **Shape**; chỉnh tâm, dài/rộng và yaw ở Top, kiểm đáy và chiều cao ở Side/Front, không xoay nghiêng hộp. Bật **Cuboid orientation**, kiểm đầu xe với camera; `preview` của lab chiếu hộp lên ảnh để đối chiếu.

## 2. Đọc frame (Bước 2)

Ảnh đã lưu: `t1-s01f17-3d.png`, `t1-s01f17-top.png`, `t1-s01f42-3d.png`, `t1-s01f42-top.png`. Hai ảnh top đã được thêm chú thích chữ và mũi tên bằng công cụ vẽ cục bộ, đối chiếu với các trục có sẵn trên ảnh: +y/phía sau hướng lên, +x/trái xe hướng sang phải màn hình, −y/phía trước hướng xuống. Chú thích chỉ bổ sung cách đọc hướng; ảnh chụp CVAT gốc đã được sao lưu, không thay đổi point cloud hoặc nhãn.

| Frame | Hướng trước | Mặt đường | Phạm vi nhãn xa nhất | Loại vật đã gán | Phía sau/hai bên và cụm chưa chắc |
|---|---|---|---|---|---|
| s01f17 | −y | Gần gốc khoảng −1,7 m; mặt đường tại hai xe xa cao hơn mốc này | 46,68 m | Car, Bus | Có Car ở y dương; xe xa #4, #5 thưa điểm, đã chỉnh đáy theo mặt đường cục bộ |
| s01f42 | −y | Gần gốc khoảng −1,7 m; không coi mọi vị trí cùng cao độ | 28.22 m | Car, Cyclist | Có xe phía sau và xe hai bánh bên phải; phần bị che cần đối chiếu nhiều camera |

Camera cung cấp màu thân xe, dấu hiệu đầu/đuôi và dấu hiệu phân biệt người lái với người đi bộ. LiDAR cung cấp khoảng cách theo mét, cấu trúc cao/thấp và vị trí 360° kể cả vật ngoài cam01. Không dùng một ảnh camera phía trước để kết luận không có vật phía sau.

| Tám frame lướt | Hướng trước | Ghi nhận |
|---|---|---|
| s02f07 | −y; hướng trên màn hình phụ thuộc góc xoay | 11 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s02f52 | −y; hướng trên màn hình phụ thuộc góc xoay | 7 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s03f09 | −y; hướng trên màn hình phụ thuộc góc xoay | 11 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s03f25 | −y; hướng trên màn hình phụ thuộc góc xoay | 5 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s04f20 | −y; hướng trên màn hình phụ thuộc góc xoay | 19 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s04f51 | −y; hướng trên màn hình phụ thuộc góc xoay | 7 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s05f08 | −y; hướng trên màn hình phụ thuộc góc xoay | 15 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |
| s05f32 | −y; hướng trên màn hình phụ thuộc góc xoay | 8 cuboid; chưa kết luận riêng về hiện tượng từ số hộp |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh chụp trực tiếp CVAT: `t2-a-car.jpg`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`. ID hiển thị trên CVAT là ID toàn job; **idx dưới đây là số thứ tự trong CSV**, không phải ID toàn job.

Ba ảnh chụp lại đã thay vào gói ảnh và học viên đã Save, chạy lại sync. CSV mới ghi xe buýt rộng 3.276 m (ảnh hiển thị làm tròn 3,28 m); Car ở ảnh ít điểm có l/w/h=4.540/1.790/1.163 m (ảnh hiển thị làm tròn 4,54/1,79/1,16 m). Số liệu bên dưới lấy từ CSV mới do script lab xuất, không sửa tay theo ảnh.

| Vật | Frame, idx | Class | Yaw | Đáy | Kiểm hình học/kích thước |
|---|---|---|---|---|---|
| Xe con gần | s01f17 #1 | Car | 268,1° | −1,690 m | Có Top/Side/Front trong ảnh; 4,40 × 1,94 × 1,54 m, rộng hơn tham chiếu sedan 0,04 m, cần đối chiếu thân/gương |
| Xe buýt | s01f17 #7 | Bus | 273.2° | -2.090 m | 10.430 × 3.276 × 3.420 m; rộng vượt tham chiếu xe buýt 2,50–2,60 m và bị cờ tự kiểm. Giữ bản học viên Save, chưa kết luận độ rộng chính xác |
| Xe hai bánh có người | s04f51 #1 | Cyclist | 269.9° | -1.840 m | 1.750 × 0.980 × 1.720 m; ảnh mới có đủ Top/Side/Front. Vật xa, điểm thưa; kích thước cần đối chiếu cả người và xe, không chỉ dựa bảng tham chiếu |

Chưa có kết quả vòng hiệu chuẩn/đối chiếu với cả lớp để báo cáo. Các cụm cây, hàng rào và nhà chờ không gán thành xe. Ở s03f25, xe buýt phía trước khoảng 56 m và xe đỏ phía sau khoảng 54 m nằm ngoài phạm vi 50 m nên bỏ qua (UNCERTAIN_SCOPE đã đối chiếu khoảng cách). Xe trắng xa hơn hai xe chính chỉ có vài điểm nên không thêm khi chưa đủ cơ sở hình học (LOW_VISIBILITY). Các xe mới ở s03f25 #3–#5 đều có ảnh camera xác nhận; bật occluded cho phần thân bị hàng cây/tường che, với #4 phải dùng cao độ khu đỗ xe cao hơn mặt đường chính.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Tất cả ảnh dưới lấy từ task cá nhân `D13-2A202603129`, không mượn task khác. Các nguyên nhân là **giả thuyết**, chưa xác định bằng một frame.

| Hiện tượng | Frame / ảnh | Quan sát và giả thuyết | Hệ quả khi gán |
|---|---|---|---|
| Bóng che | s01f17 / `t4-1-bong-che.jpg` | Vùng ngoài xe buýt có khoảng trống điểm; giả thuyết thân xe cản tia. Ảnh này là ví dụ ứng viên, cần phân biệt với chỗ thưa do góc quét | Vùng không quan sát được không đồng nghĩa không có vật; chỉ suy luận hình hộp khi đủ cơ sở, bật occluded cho vật bị che |
| Thưa ở xa | s01f17 #4 / `t4-2-thua-o-xa.jpg` | Car cách 34,47 m có 32 điểm trong hộp; giả thuyết khoảng cách và che khuất làm giảm số điểm | Không bám theo vài điểm để bóp hộp; đối chiếu camera, đánh dấu LOW_VISIBILITY khi thiếu cơ sở |
| Ít điểm bất thường | s04f20 #11 / `t4-3-phan-xa-thap.png` | Car cách 14.44 m có 455 điểm, so với #9 cách 14.32 m có 1594 điểm. Số điểm đã tính lại sau sync. Có thể do bề mặt, góc tới hoặc che khuất; chưa chứng minh phản xạ thấp | Giữ lớp và kích thước theo hình học/camera; không khẳng định màu tối là nguyên nhân, không nới hộp để tăng số điểm |
| Điểm lạ | s02f07 / `t4-4-diem-la.jpg`; thêm `t4-4-diem-la-phan-tich.png` | Ảnh CVAT đã phóng sát hai điểm. Tọa độ LiDAR gốc: (16,352; 22,132; −12,817) m và (16,494; 22,162; −12,867) m, sâu khoảng 11 m dưới đường. Hình phân tích bổ sung làm rõ mặt đường và độ sâu, không phải ảnh chụp CVAT. Sai trả về/đa đường chỉ là giả thuyết | Không gán hai điểm lẻ thành vật; không thay đổi tọa độ/intensity dữ liệu |

## 5. Số từ cuboid của tôi (Bước 5)

Nguồn: `submission/D13-2A202603129-bangA.csv` và `submission/D13-2A202603129-tukiem.csv` sau đồng bộ. `dist_m=√(x²+y²)`; r dưới đây tính thêm z. Mốc −1,7 m chỉ là gần đúng; so đáy với mặt đường cục bộ trước khi kết luận chìm/lơ lửng.

| Cuboid | z | h | z đáy = z − h/2 | dist_m | r | yaw | Nhận xét |
|---|---|---|---|---|---|---|---|
| s01f17 #6 Car | -1.060 m | 1.560 m | -1.840 m | 8.07 m | 8.14 m | 267.0° | gần mặt đường cục bộ; chênh cục bộ +0.05 m |
| s02f07 #2 Car | -0.850 m | 2.200 m | -1.950 m | 37.16 m | 37.17 m | 84.2° | gần mặt đường cục bộ; chênh cục bộ +0.07 m |
| s04f20 #16 Cyclist | -0.990 m | 1.850 m | -1.915 m | 20.62 m | 20.65 m | 269.9° | gần mặt đường cục bộ; chênh cục bộ +0.04 m |

Gần nhất: s04f20 #19 Car, dist_m=3.64 m, r=3.78 m. Xa nhất: s01f17 #5 Car, dist_m=46.68 m, r=46.69 m. Car `s01f17 #6` có l/w/h=4,400/1,750/1,560 m, trong tham chiếu sedan. Cyclist `s04f20 #16` có 2,000/1,000/1,850 m; so với xe máy có người, rộng vượt cận 0,95 m một khoảng 0,05 m, cao vượt cận 1,80 m một khoảng 0,05 m. Giữ kích thước theo phần người và xe quan sát được; đây vẫn là ca che khuất có suy luận. Xe cùng chiều phía trước `s01f17 #6` có yaw=267,0°, gần 270°; xe ngược chiều `s02f52 #1` có yaw=90,5°, gần 90°; hướng cần tiếp tục đối chiếu đầu/đuôi trên camera.

| Cuboid | dist_m | points_in |
|---|---|---|
| s01f17 #6 Car | 8.07 m | 1713 |
| s01f17 #2 Car | 14.40 m | 763 |
| s01f17 #3 Car | 22.24 m | 288 |
| s01f17 #4 Car | 34.47 m | 32 |

Trong bốn ví dụ này, xa từ 8,07 m lên 34,47 m (khoảng 4,27 lần) thì số điểm từ 1713 xuống 32 (còn khoảng 1.87%). Đây không phải quy luật chính xác cho mọi xe: kích thước, che khuất và góc tới cũng khác. Xu hướng giảm phù hợp slide (xe con khoảng 1968, 718, 214, 72 điểm theo các dải 0–10, 10–20, 20–30, 30–40 m), nhưng ví dụ xa này thấp hơn trung bình dải 30–40 m.

Sau preview/tự kiểm đã sửa bốn hộp, idx dưới đây theo **bản CSV mới**: `s02f07 #2` thu hộp quá lớn từ 8,86 × 4,59 × 3,76 m về 5,20 × 1,90 × 2,20 m và chỉnh tâm/yaw; `s02f52 #1` hạ đáy, thu rộng về 1,85 m và chỉnh tâm; xe phía sau `s02f52` thu rộng 2,60 m về 1,85 m; `s04f20 #16` tách hộp Cyclist quá rộng, đưa về 1,90 × 0,85 × 1,65 m và bật occluded. Đây là sửa theo ảnh/điểm, không phải chỉ tắt cờ. Các ID cũ đã đổi sau import.

Lịch sử rà soát trước lượt chốt của học viên: nâng đáy s01f17 #4 từ −1,70 m lên −1,40 m và #5 từ −1,70 m lên −1,25 m theo mặt đường xa; dịch tâm s03f09 #6 sang x=3,37 m để bao mặt xe gần cảm biến; nâng đáy s04f20 #11 lên −1,25 m và bật occluded; mở Cyclist s04f20 #16 thành 2,00 × 1,00 × 1,85 m, giữ đáy −1,915 m; đổi s05f32 #4 từ Bus sang Truck vì camera cho thấy xe tải ben, chỉnh kích thước về 8,00 × 2,50 × 3,40 m. Đã bỏ một hộp trùng hoàn toàn mặt bằng và kích thước ở s05f32; giữ hộp gốc và có bản sao lưu trước sửa.

Ở lượt chốt cuối, học viên sửa tiếp ba hộp, giữ nguyên tổng số: `s01f17 #8` có tâm (21.566; 3.580; -1.105) m, đáy -1.880 m; `s02f07 #9` có yaw 311.3°; `s05f32 #4` có tâm (11.068; -29.708; -0.644) m, đáy -2.344 m. Số liệu này lấy từ CSV sau đồng bộ lần cuối, không ghi đè lại bằng bản AI trước đó.

Sau khi chụp lại ảnh, học viên tiếp tục Save các chỉnh sửa của Bus s01f17 #7 và Car s04f20 #11. Bản mới của hai hộp: Bus có yaw 273.2°, rộng 3.276 m; Car có yaw 269.5°, cao 1.163 m và đáy -1.487 m. Các số liệu hiện tại thay cho số liệu lịch sử ở trên.

Kết quả tự kiểm hiện tại:

- s01f17 #7: width=3.28 ngoài khoảng 2.0–3.2 của Bus.
- s02f07 #9: 53 điểm sát ngay ngoài mép (quá chật, hay vật bên cạnh?).
- s04f20 #11: height=1.16 ngoài khoảng 1.2–2.3 của Car.

Giữ nguyên ba cảnh báo theo bản học viên đã Save: chiều rộng Bus s01f17 #7 vượt khoảng tham chiếu; chiều cao Car s04f20 #11 thấp hơn khoảng tham chiếu; Cyclist s02f07 #9 có nhiều điểm ngoài mép. Chưa đủ cơ sở để khẳng định các cờ sai, nên cần đối chiếu hình học và camera, không tự đổi kích thước hoặc yaw chỉ để tắt cờ. Cảnh báo không tính điểm theo hướng dẫn tự kiểm.

**Kiểm chéo với MSSV 2A202603058.** Tôi đã cùng bạn kiểm chéo các trường hợp bị che khuất trên màn hình CVAT. Qua trao đổi, chúng tôi ghi nhận các khác biệt và thống nhất như sau:

| Frame / trường hợp | Khác biệt khi kiểm chéo | Kết luận sau trao đổi |
|---|---|---|
| s01f17 — xe bị che một phần | Tôi và bạn phát hiện khác biệt về việc có gán cuboid cho xe bị mất một phần tầm nhìn hay không | Thống nhất gán cuboid cho xe bị che khi có đủ cơ sở nhận dạng và suy luận phần thân xe; không mặc định mọi vùng bị che đều có vật |
| s01f17 — ô tô bị che ở làn còn lại | Ban đầu tôi chưa gán, bạn có gán | Sau khi trao đổi, thống nhất gán nhãn Car cho ô tô này |
| s04f20 — vật thể bị che | Ban đầu tôi gán Bus, bạn gán Car | Sau khi thảo luận, thống nhất nhãn Car |

Các ca được ghi theo frame và vị trí tương đối như nội dung kiểm chéo đã cung cấp; chưa có idx CSV được xác nhận nên không tự điền idx từ ID trên thanh Objects. Việc hai người thống nhất giúp cách gán nhất quán, không thay thế việc đối chiếu guideline và bằng chứng LiDAR/camera. Phần mô tả này ghi nhận kết quả thảo luận, không tự xác nhận mọi kết luận đã được Save và đồng bộ vào CSV.

**Đối chiếu số liệu — tôi kiểm bài của partner 2A202603058.** Tôi đã kiểm cuboid `s01f14 #1` trong bài của partner: chiều dài **4,416 m**, chiều rộng **1,764 m**, chiều cao **1,480 m**. Cả ba giá trị đều khớp bảng A của partner; không ghi nhận sai khác ở ba số đã đối chiếu. Đây là số liệu của bài partner, không phải cuboid trong 10 frame của tôi và không được đưa vào bảng A của tôi.

**Chiều kiểm ngược lại — partner 2A202603058 kiểm bài của tôi.** Partner đã kiểm cuboid `s01f17 #1 Car`: chiều dài **4,400 m**, chiều rộng **1,940 m**, chiều cao **1,540 m**. Cả ba giá trị đều khớp bảng A của tôi; không ghi nhận sai khác ở ba số đã đối chiếu. Như vậy hai chiều kiểm chéo số liệu đều đã được thực hiện và có kết quả xác nhận.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét để mô tả kích thước và vị trí vật trong không gian, độc lập độ phân giải ảnh. Vật thưa điểm cần quy tắc suy luận và ngưỡng đủ cơ sở để model không học hộp quá nhỏ hoặc học nhiễu thành vật.

- Công cụ: ChatGPT hỏi đáp chạy script, Python phân tích cục bộ và CVAT localhost.
- Câu hỏi chính: thiết lập CVAT, hỏi đáp script và soạn báo cáo từ số liệu xuất.
- Cách dùng: học viên gán nhãn, rà soát lại các ảnh chéo với thành viên khác, bổ sung vật bị bỏ sót. Không dùng bảng A của người khác. Số liệu CSV do script lab sinh, không sửa tay. Hộp căn góc nhìn điểm lạ chỉ dùng tạm khi chụp, đã xóa trước Save và sync; hai điểm lạ không có nhãn cuboid trong bài nộp.

Điều còn chưa chắc: mức suy luận thân xe bị che ở s03f25, kích thước xe hai bánh so với bảng tham chiếu, giả thuyết phản xạ thấp. Tôi và MSSV 2A202603058 đã trao đổi ca gán nhãn, kiểm chéo số liệu theo cả hai chiều; các số đã đối chiếu đều khớp như ghi ở mục 5. Chưa có kết quả vòng hiệu chuẩn cả lớp. Cả hai frame trước đây thiếu số hộp tối thiểu đã được bổ sung. Bản chốt có 3/100 hộp bị cờ; không vì việc đạt số hộp tối thiểu hoặc ít cờ mà khẳng định không còn bỏ sót hay sai khác với nhãn chuẩn. `check_submission.py` kiểm tính đầy đủ và nhất quán, không có nhãn chuẩn và không cấp điểm chất lượng cuboid. Chưa xác nhận thời hạn/gia hạn hay việc nộp LMS; không kết luận bài đã nộp.
