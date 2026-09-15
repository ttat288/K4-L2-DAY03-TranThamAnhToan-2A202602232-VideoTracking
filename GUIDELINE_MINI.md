# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `TRẦN THẨM ANH TOÀN — MSSV 2A202602232 — mã cặp 21902232`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán                           | Không gán                                           |
| ----------------------------- | --------------------------------------------------- |
| xe con, SUV, taxi, xe bán tải | người đi bộ                                         |
| van, minivan                  | xe đạp                                              |
| xe buýt, minibus              | **xe máy / mô tô**                                  |
| xe tải, xe đầu kéo            | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `không có`

## 2. Luật ID — phần quan trọng nhất

| Tình huống                       | Luật của nhóm                                                                   | Vì sao                                              |
| -------------------------------- | ------------------------------------------------------------------------------- | --------------------------------------------------- |
| Xe bị che một phần rồi hiện lại  | giữ nguyên ID nếu vẫn xác định được là cùng xe; mặc định ngưỡng lab là 25 frame | tránh đổi ID khi xe chỉ bị che trong thời gian ngắn |
| Xe bị che lâu hơn ngưỡng trên    | tạo track mới nếu không còn đủ cơ sở để xác định là cùng xe                     | tránh đoán ID                                       |
| Xe rời khung hình rồi quay lại   | mặc định: **track mới**                                                         | tránh nối nhầm hai lần xuất hiện                    |
| Hai xe cắt nhau / chồng lên nhau | theo dõi đặc điểm và vị trí của từng xe, giữ ID nếu vẫn xác định được từng xe   | tránh đổi ID khi hai xe đi gần hoặc chồng lên nhau  |

## 3. Luật bbox

| Tình huống                             | Luật của nhóm                                                                                               |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Xe bị cắt bởi rìa ảnh                  | bbox chạm đúng rìa, không đoán phần ngoài ảnh                                                               |
| Xe bị xe khác che một phần             | bbox ôm phần **nhìn thấy được**                                                                             |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `chưa đặt ngưỡng số cụ thể` |
| Xe đang đỗ, không di chuyển            | vẫn giữ track nếu xe thuộc phạm vi cần gán                                                                  |
| Keyframe đặt dày ở đâu                 | đặt dày hơn ở đoạn xe di chuyển nhanh, bị che hoặc bbox thay đổi rõ; đoạn ổn định có thể thưa hơn           |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1

* Clip / frame / ID: `clip_01 / frame 54 / ID 4`
* Tình huống: bbox của xe bị lệch, IoU với gold chỉ khoảng `0.591`
* Quyết định: xem lại và chỉnh bbox sát xe hơn
* Lý do: bbox cần bám đúng phần xe nhìn thấy được

### Ca 2

* Clip / frame / ID: `clip_01 / frame 80 / ID 5`
* Tình huống: bbox của xe bị lệch, IoU với gold chỉ khoảng `0.535`
* Quyết định: xem lại và chỉnh bbox
* Lý do: vị trí bbox cần được kiểm tra lại thay vì chỉ dựa vào frame trước

### Ca 3

* Clip / frame / ID: `clip_01 / frame 110 / ID 6`
* Tình huống: bbox của xe bị lệch, IoU với gold chỉ khoảng `0.521`
* Quyết định: xem lại và chỉnh bbox sát xe hơn
* Lý do: đây là một trong các frame có bbox khá lỏng nên cần kiểm tra lại

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

* `Cần nói rõ hơn trường hợp xe bị che lâu thì khi nào giữ ID và khi nào tạo ID mới.`
* `Cần kiểm tra riêng frame đầu/cuối khi xe đi vào hoặc ra khỏi ảnh để tránh track quá sớm hoặc quá muộn.`
* `Cần kiểm tra bbox ở các frame giữa, không chỉ dựa vào vị trí bbox của frame trước.`
* `Không đổi ID chỉ vì model/ReID dự đoán khác; annotation vẫn phải dựa trên đối tượng thực tế.`
