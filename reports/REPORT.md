# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: `TRẦN THẨM ANH TOÀN — MSSV 2A202602232 — theo cặp — mã cặp 21902232`
Ngày: `15/09/2026`

---

## 1. Quá trình gán nhãn

| Mục                               | Giá trị             |
| --------------------------------- | ------------------- |
| Công cụ                           | CVAT                |
| Thời gian gán `clip_02` (warm-up) | `chưa ghi lại` phút |
| Thời gian gán `clip_01`           | `chưa ghi lại` phút |
| Số track đã vẽ trong `clip_01`    | `8`                 |
| Số keyframe trung bình mỗi track  | `chưa ghi lại`      |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Xe bị che hoặc thay đổi vị trí giữa các frame. Tôi kiểm tra lại các frame xung quanh và giữ cùng ID khi vẫn xác định được đó là cùng xe.
2. Xe đi ra/vào vùng biên của ảnh. Tôi kiểm tra frame đầu và cuối của track để tránh kéo track quá sớm hoặc quá muộn.
3. Một số bbox nằm sát hoặc lệch khỏi xe. Tôi xem lại vị trí bbox và chỉnh lại theo xe thay vì chỉ dựa vào vị trí của frame trước.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

* Lượt 1: kiểm tra ID của các xe và sự liên tục của track.
* Lượt 2: kiểm tra frame bắt đầu và kết thúc của từng track.
* Lượt 3: kiểm tra các frame giữa, đặc biệt các đoạn xe di chuyển hoặc bị che.

Kiểm chéo với: `chưa ghi lại`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `chưa ghi lại`. Số lỗi bạn ấy tìm được trong bản của bạn: `chưa ghi lại`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`Chưa có thông tin kiểm chéo để ghi cụ thể.`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence                                             | Giá trị                               |
| ---------------------------------------------------- | ------------------------------------- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `chưa có trong kết quả được cung cấp` |
| Thời điểm khóa                                       | `chưa ghi lại`                        |
| Số row / frame / track trước khi mở reference        | `610 / 190 / 8`                       |

|              |                    HOTA |  DetA |  AssA |  LocA |  IDF1 |  MOTA |  MOTP | FP | FN | IDSW |
| ------------ | ----------------------: | ----: | ----: | ----: | ----: | ----: | ----: | -: | -: | ---: |
| Bản pre-gold | `chưa có số liệu riêng` |       |       |       |       |       |       |    |    |      |
| Sau rework   |                   0.822 | 0.804 | 0.842 | 0.883 | 0.955 | 0.908 | 0.874 | 45 |  8 |    0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi      | Frame | ID | Đã sửa thế nào                    |
| ------------- | ----- | -- | --------------------------------- |
| Bbox hơi lệch | 54    | 4  | Kiểm tra và chỉnh bbox sát xe hơn |
| Bbox hơi lệch | 80    | 5  | Kiểm tra và chỉnh bbox sát xe hơn |
| Bbox hơi lệch | 110   | 6  | Kiểm tra và chỉnh bbox sát xe hơn |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục                                | Giá trị                                    |
| ---------------------------------- | ------------------------------------------ |
| Python / ultralytics / torch / lap | `3.13.15 / 8.4.145 / 2.11.0+cpu / 0.5.13`  |
| weights / hai tracker              | `yolo26n.pt / ByteTrack / BoT-SORT + ReID` |
| conf / IoU / imgsz / classes       | `0.25 / 0.70 / 960 / [2, 5, 7]`            |
| device                             | `cpu`                                      |

| So sánh                   |  HOTA |  DetA |  AssA |  LocA |  IDF1 |  MOTA |  MOTP | FP | FN | IDSW |
| ------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | ----: | -: | -: | ---: |
| bạn vs gold               | 0.822 | 0.804 | 0.842 | 0.883 | 0.955 | 0.908 | 0.874 | 45 |  8 |    0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 |    2 |
| BoT-SORT + ReID vs gold   | 0.764 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 |    2 |
| ReID vs bạn               | 0.797 | 0.744 | 0.856 | 0.924 | 0.888 | 0.775 | 0.918 | 81 | 53 |    3 |

Các số liệu bạn vs gold cho thấy bản gán nhãn có IDF1 0.9552, MOTA 0.9075 và không có ID switch. ByteTrack không qua gate vì MOTA = 0.7487, thấp hơn 0.75 một chút. ReID qua gate với IDF1 = 0.9001 và MOTA = 0.7923.

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của tôi là 0.908, thấp hơn IDF1 là 0.955. Bản gán nhãn có ít lỗi detection và không có IDSW. MOTA chủ yếu bị ảnh hưởng bởi FP, FN và IDSW nên không phản ánh đầy đủ độ ổn định của identity như IDF1.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

ReID tốt hơn ByteTrack ở IDF1: `0.900 > 0.875`, AssA: `0.820 > 0.776`. Cả hai đều có 2 IDSW khi so với gold. ReID có association tốt hơn trên clip này.

Một đoạn đáng chú ý là GT track 6. ReID bị tách thành các track `24 → 28 → 31`, trong đó các lỗi ID được ghi nhận ở frame 107 và 113.

Tuy nhiên, không thể kết luận toàn bộ chênh lệch chỉ do ReID vì ByteTrack và BoT-SORT là hai tracker implementation khác nhau.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

ByteTrack có DetA 0.649, FP 88, FN 54. ReID có DetA 0.711, FP 91, FN 26. Như vậy ReID giảm FN khá nhiều nhưng FP tăng nhẹ.

Vì vậy lỗi còn lại không chỉ là association. ReID có association khá tốt nhưng vẫn có lỗi detection/coverage và một số ID fragmentation. ReID vẫn có 2 IDSW khi so với gold.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

ReID có ghost track `ID 7` trong khoảng frame `16–116`, không khớp với track tham chiếu nào. Đây là dấu hiệu model tạo thêm một track không có trong reference.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao):**

Ở frame `108`, ReID track `29` khớp với GT track `7` nhưng IoU chỉ `0.570`. Đây là một điểm tôi sẽ xem lại bbox và ID trong vùng frame khoảng `104–115`, thay vì sửa annotation ngay theo model.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

Tôi sẽ bổ sung rõ hơn quy tắc về:

* Khi nào bắt đầu một track mới và khi nào tiếp tục ID cũ.
* Kiểm tra kỹ frame đầu/cuối khi xe đi vào hoặc ra khỏi ảnh.
* Với xe bị che, ưu tiên kiểm tra các frame trước và sau khi bị che.
* Không đổi ID chỉ vì model hoặc ReID dự đoán khác.
* Sau khi gán xong nên tua lại một lượt riêng để kiểm tra ID và một lượt để kiểm tra bbox.

## 7. Tệp đã nộp

* [x] `annotations/clip_01/gt.txt`
* [x] `annotations/clip_02/gt.txt`
* [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
* [x] `GUIDELINE_MINI.md` đã điền
* [x] `outputs/eval_vs_gold.json`
* [x] `outputs/model_bytetrack_clip_01.txt`
* [x] `outputs/model_reid_clip_01.txt`
* [x] `outputs/model_run_config.json`
* [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
* [x] `reports/review_partner.md`
* [x] `reports/REPORT.md` (file này)
