# Báo cáo Day 13 — Trần Đình Quang Vinh, 2A202603041

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
2A202603041, task `D13-2A202603041`, job 27, 10 frame. Phiên rà soát dùng Windows, trình duyệt tích hợp Codex và CVAT Server/UI 2.74.1. MSSV bạn ngồi cạnh và trình duyệt dùng trong buổi học chưa được xác nhận. Task có **80 cuboid dạng Track**, trong đó `s03f34` ít nhất với 4 hộp (2 Car, 1 Cyclist, 1 Pedestrian). Cần quét lại vùng chưa gán để biết có bỏ sót vật. Phiếu tự kiểm ghi **0/80 cuboid có cờ**. Hộp Car gần có cụm điểm rõ. Phần cần rà nhất là đáy hộp ở nơi cao độ đường thay đổi, chiều rộng Bus và vật bị che ở xa. `s01f14` có 7/8 hộp và `s03f34` có 4/5 hộp theo mốc thực hành.

Trong Perspective, kéo chuột trái để xoay về góc nhìn từ trên xuống, kéo chuột phải để dời và lăn để chỉnh thu phóng.  
Chọn lớp và Shape, sau đó nháy đúp vào cụm điểm. Dùng Top để căn tâm, dài, rộng và yaw, rồi xem Side/Front để kiểm đáy và chiều cao.  
Bật **Cuboid orientation** để thấy vạch đỏ chỉ hướng hộp. Dùng `python scripts/lab.py preview` để xem hộp chiếu lên ảnh camera.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-<frame>-3d`, `t1-<frame>-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| pointcloud/s04f44.pcd | Cạnh trên là phía trước (−y), +x sang trái, +y xuống dưới theo BEV quy ước. | Dải giữa làn hơi thấp hơn về phía trước. Cao độ điển hình từ −1,78 đến −1,73 m. | Car idx 8 ở trước-phải, 47,29 m. | 7 Car và 1 Cyclist. | Hai Car ở sau-phải (idx 4 và 5). Nhiều vật đã gán ở trước-phải. | Car idx 8 chỉ có 62 điểm, phần khuất của thân xe khó đọc. |
| pointcloud/s01f14.pcd | Cạnh trên là phía trước (−y), +x sang trái, +y xuống dưới. | Gần phẳng ở dải giữa làn, khoảng −1,74 m. | Car idx 7 ở phía sau, 51,70 m, ngoài vùng lab 50 m. | 6 Car và 1 Bus. | Car idx 1 trước-trái, 3,50 m. Có nhiều Car phía sau. | Vùng sau Bus thưa điểm, khó xác định có vật bị che. |

**Chỉ camera** cho thấy vùng chiếu Car idx 1 ở `s04f44` sáng xám trên cam07, vùng Car idx 2 ở cùng frame tối và ngả đỏ trên cam01/cam03, và vùng Bus idx 3 ở `s01f14` sáng gần trắng trên cam01. Màu tại vùng chiếu có thể gồm nền hoặc phần vật che.

**Chỉ LiDAR** cho biết cao độ đường quanh −1,74 đến −1,78 m ở các dải khảo sát, cự ly ngang 47,29 m và vị trí trước-phải của Car idx 8 ở `s04f44`, cùng vùng thưa điểm sau Bus ở `s01f14`.

Gói ảnh T1 hiện có `t1-s01f14-3d.jpg`, `t1-s01f14-top.jpg`, `t1-s01f59-3d.jpg` và `t1-s01f59-top.jpg`. Phần `s04f44` của mẫu được điền từ số liệu LiDAR và camera. Hai ảnh T1 riêng cho frame này chưa có trong ZIP.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s01f59` | Cạnh trên theo quy ước. | Car idx 8 ở trước-trái chỉ có 12 điểm trong hộp. Có ảnh T1 trong gói minh chứng. |
| `s02f15` | Cạnh trên theo quy ước. | Các hộp Car có hai nhóm yaw quanh 90° và 270°. |
| `s02f38` | Cạnh trên theo quy ước. | Có cụm điểm thấp bên trái ở khoảng 47 m. |
| `s03f34` | Cạnh trên theo quy ước. | Có Pedestrian và Cyclist về trước-phải. |
| `s03f52` | Cạnh trên theo quy ước. | Đáy Cyclist idx 4 cao hơn mặt đường cục bộ 0,42 m. |
| `s04f03` | Cạnh trên theo quy ước. | Cao độ đường quanh các hộp thay đổi theo vị trí. |
| `s05f20` | Cạnh trên theo quy ước. | Đáy Car idx 7 cao hơn mặt đường cục bộ 0,41 m. |
| `s05f44` | Cạnh trên theo quy ước. | Có 3 Truck và một cụm điểm nằm thấp bất thường. |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f14`, idx 1 | Car | 269,4°, gần hướng −y. | Gần mặt đường, đáy thấp hơn 0,09 m. | Có ảnh Top/Side/Front. Cần đối chiếu đầu xe trên ảnh camera. | 4,688 × 1,794 × 1,587 m, trong khoảng sedan/hatchback. |
| (b) xe buýt hoặc xe tải | `s01f14`, idx 3 | Bus | 282,5°, lệch 12,5° so với −y. | Gần mặt đường, đáy thấp hơn 0,06 m. | Có ảnh Top/Side/Front. Cần rà bề rộng trên Top và Front. | 17,510 × 2,963 × 3,287 m. Dài và cao phù hợp xe buýt nối toa. Rộng vượt mốc 2,60 m khoảng 0,36 m. |
| (c) người đi bộ hoặc xe hai bánh | `s03f34`, idx 3 | Pedestrian | 89,8°, gần hướng +y. Hướng đi thực tế chưa xác nhận. | Gần mặt đường, đáy thấp hơn 0,12 m. | Có ảnh Top/Side/Front. Cần đối chiếu hướng đi hoặc hướng mặt trên ảnh camera. | 0,792 × 0,793 × 1,625 m, trong khoảng người lớn. |

Chưa có ghi chép để xác nhận khác biệt giữa các cuboid trong vòng hiệu chuẩn hoặc kết luận của lớp. Danh sách vật đã bỏ qua cũng chưa được xác nhận. Vùng sau Bus ở `s01f14` là ca cần kiểm thêm. Nếu camera vẫn thiếu bằng chứng về vật thứ hai, ghi `LOW_VISIBILITY`.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s01f14` / `t4-1-bong-che.jpg` / task cá nhân | Thân Bus có thể chắn tia LiDAR. | Ở dải 34–48 m sau Bus, quạt 4–10° lệch trái gần như trống. Quạt liền kề 10–13° có 48 điểm. | Bật `occluded` khi nhận ra vật bị che một phần và còn đủ cơ sở suy ra hộp. |
| 2. Thưa ở xa | `s01f14` / `t4-2-thua-o-xa.jpg` / task cá nhân | Xe ở xa có thể nhận ít tia quét hơn. | Car idx 6 cách 34,71 m có 55 điểm. Car idx 1 cách 3,50 m có 3.067 điểm. | Chỉ gán khi thấy khoảng 15–20% thể tích hoặc có căn cứ đủ rõ. Ca thiếu căn cứ ghi `LOW_VISIBILITY`. |
| 3. Phản xạ thấp / ít điểm bất thường | `s01f59` / `t4-3-phan-xa-thap.jpg` / task cá nhân | Che khuất, góc tới hoặc phản xạ yếu đều có thể làm giảm số điểm. | Car idx 8 có 12 điểm ở 24,41 m. Car idx 7 có 128 điểm ở 26,29 m. Cả hai đã gắn `occluded`. | Đối chiếu ảnh camera để phân biệt vật với vùng bị che trước khi suy kích thước. |
| 4. Điểm lạ | `s05f44` / `t4-4-diem-la.jpg` / task cá nhân | Có thể do nhiễu hoặc phản xạ gián tiếp. | Bảy điểm ở trước-trái, cách ngang khoảng 25,47 m. Cao độ trung vị −11,86 m, thấp hơn mốc đường 10,16 m. | Bỏ qua cụm thấp bất thường khi chưa có bằng chứng đó là vật cần gán. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f14`, idx 1, Car | −1,058 m | 1,587 m | −1,852 m | 3,50 m | 3,66 m | 269,4° | Gần chạm đường, thấp hơn cao độ cục bộ 0,09 m. |
| `s03f52`, idx 4, Cyclist | −0,789 m | 1,101 m | −1,339 m | 9,63 m | 9,66 m | 269,1° | Có dấu hiệu lơ lửng, đáy cao hơn đường cục bộ 0,42 m. |
| `s05f44`, idx 13, Car | −1,629 m | 1,946 m | −2,602 m | 45,30 m | 45,33 m | 184,6° | Thấp hơn mốc chung 0,90 m. So với mặt đường cục bộ, đáy thấp hơn 0,25 m. |

(a) **Gần nhất** là Car `s01f59` idx 1, `dist_m` 2,99 m và r 3,16 m. **Xa nhất** là Car `s01f14` idx 7, `dist_m` 51,70 m và r 51,70 m sau khi làm tròn. Hộp xa vượt vùng quy định 50 m.

(b) Car `s01f14` idx 1 có dài, rộng và cao lần lượt là 4,688 m, 1,794 m và 1,587 m, trong khoảng sedan/hatchback. Pedestrian `s03f34` idx 3 có số đo 0,792 m, 0,793 m và 1,625 m, trong khoảng người lớn. Cyclist `s03f52` idx 4 có ba cạnh 1,182 m, 0,462 m và 1,101 m, đều dưới khoảng xe đạp có người. Hộp có thể chỉ bao phần nhìn rõ.

(c) Car `s01f59` idx 3 phía trước có yaw 268,9°, gần 270°. Car `s02f15` idx 1 có yaw 90,4°, gần 90°. Hai số lần lượt phù hợp với mẫu xe cùng chiều và ngược chiều.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f14`, idx 1, Car | 3,50 m | 3.067 |
| `s01f14`, idx 2, Car | 14,38 m | 672 |
| `s01f14`, idx 5, Car | 26,51 m | 207 |
| `s01f14`, idx 6, Car | 34,71 m | 55 |

Trong bốn mẫu Car, khi cự ly tăng khoảng 9,9 lần (3,50 m lên 34,71 m), số điểm giảm từ 3.067 còn 55, tức khoảng 1,8%. Xu hướng cùng chiều với bốn mốc trung vị trên slide là 1.968, 718, 214 và 72 điểm cho các dải 0–10 m, 10–20 m, 20–30 m và 30–40 m. Số điểm của từng xe còn phụ thuộc mức che, góc nhìn và bề mặt.

Phiếu tự kiểm ghi 0 cờ, nên không có cờ được giữ lại. Hai ca cần rà sau preview là Cyclist `s03f52` idx 4 vì đáy cao hơn đường 0,42 m và Bus `s01f14` idx 3 vì hộp rộng hơn mốc khoảng 0,36 m. Hồ sơ hiện có chưa xác nhận hai lần chỉnh hộp.

Đối chiếu kỹ thuật cho thấy tổng 80 hộp khớp giữa CVAT, Bảng A và phiếu tự kiểm. Với Car `s01f14` idx 1, khoảng cách ngang 3,50 m cũng khớp với kết quả tính từ tọa độ tâm. MSSV người kiểm chéo và kết quả đổi kiểm trong buổi học chưa được xác nhận.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Nhãn theo mét giúp mô hình học kích thước và vị trí thực của vật trong 3D dù kích thước ảnh thay đổi theo cự ly. Vật thưa điểm cần ngưỡng riêng để giữ những nhãn xa có đủ bằng chứng và giảm hộp suy đoán trong dữ liệu huấn luyện.

(b) **Công cụ** — ChatGPT trong Codex.  
**Câu hỏi chính** — Rà số liệu CVAT, đối chiếu Bảng A và điền báo cáo theo mẫu.  
**Cách dùng** — Dùng số liệu đã đối chiếu, thống kê LiDAR cục bộ và tên ảnh minh chứng để điền các mục.

(c) Chưa chắc nguyên nhân của cụm điểm thấp ở `s05f44` và phần kích thước có thể bị che của Cyclist `s03f52` idx 4. Cần thêm góc nhìn trước khi kết luận.
