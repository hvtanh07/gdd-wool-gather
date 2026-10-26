# GDD template

`gdd-template.html` là khung GDD cho các game sort/puzzle tiếp theo. Nó được dựng từ GDD của Caps Sorting (`index.html`). Bản template giữ nguyên các phần có logic dùng chung. Các phần riêng của từng game được thay bằng placeholder.

Mở file trong trình duyệt là xem được ngay. Mỗi placeholder hiện thành một ô viền nét đứt, chữ nghiêng, nên dễ thấy chỗ nào chưa điền.

## Phần nào giữ, phần nào thay

| Mục | Loại | Khi làm game mới |
|---|---|---|
| Hero (tên game, câu giới thiệu, hình màn chơi A–E) | **Theo game** | Viết lại toàn bộ |
| 1.1 Gameplay overview | **Theo game** | Cách chơi, thắng/thua |
| 1.2 Main game mechanics | **Theo game** | Một `ent` cho mỗi đối tượng chính |
| 1.3 Additional mechanics | Cố định | Giữ logic (tim và tim vô hạn, thanh mở khóa yếu tố, thưởng thêm ở màn hình thắng, RV Slot, lặp lại màn chơi, hồi sinh), chỉ đổi số. Đổi `GBASE` cho khớp `level_win_coin` |
| 1.4 Elements | **Theo game** | Một `ent` cho mỗi yếu tố, cộng mục Fail-safe |
| 1.5 Boosters | **Theo game** | Một `ent` cho mỗi công cụ |
| 2.1 FTUE logic | Cố định | Giữ nguyên |
| 2.2–2.4 FTUE steps / tutorials | **Theo game** | Giữ dòng đầu và dòng cuối của 2.2, thay các dòng giữa |
| 3 Object set & animation | **Theo game** | Concept, bảng màu, chuyển động |
| 4 SFX & haptics | Một phần | Giữ các dòng UI, thay dòng core/element/booster |
| 5 Remote config | Cố định | Giữ mọi key, chỉ đổi giá trị |
| 6 IAP & offers | Cố định | Giữ logic. Đổi `const PID='game.name'` thành tiền tố product ID của game (ví dụ `sushi.sort` → `sushi.sort.starter_pack`), đổi giá/số lượng nếu khác. 6.1 là danh sách gói, 7.1 là danh sách popup: thêm hoặc sửa gói, popup ở đây trước |
| 7 Live ops | Cố định | Giữ logic, chỉ đổi số |
| 8 Change note | **Theo game** | Bắt đầu bằng một mục ngày tạo tài liệu |

Trong script, các chỗ cần sửa đều có ghi chú `/* PER GAME ... */`. Các chỗ cần giữ có ghi chú `/* FIXED ... */`.

## Các bước dựng GDD cho game mới

1. Đặt tên game ở 4 chỗ:
   - `const GAME='[Game name]'` (dùng cho hero và tiêu đề tab)
   - `const PID='game.name'` (tiền tố product ID của mọi gói IAP)
   - `<title>`
   - chữ `[Game name]` trong thanh bên (`.brand`)
2. Đổi tên yếu tố và công cụ trong `N` và `P`, ở đầu hàm `view()`. Thêm hoặc bớt key cho đúng số lượng. Mọi mục 1.4, 1.5, 2.3, 2.4, 3.3, 3.4 và dòng thứ tự mở khóa đều đọc tên từ đây.
3. Điền các mục **Theo game** theo bảng trên. Mỗi đoạn `ph('[…]','[…]')` thay bằng `t('English','Tiếng Việt')`.
4. Kiểm tra lại các con số ở những mục cố định:
   - giá trị remote config
   - level mở khóa ưu đãi (12 / 15 / 10)
   - giá và nội dung gói IAP
   - `GBASE`
5. Thay các hình động. Template đã có sẵn hình động mẫu lấy từ Caps Sorting:
   - **Hình màn chơi** ở đầu trang: băng chuyền chạy vòng, có nút dừng chuyển động, rê chuột vào chú thích A–E thì phần tương ứng được làm nổi bật. Muốn đổi, viết lại `fieldSVG()` theo màn chơi của game mới. Bọc mỗi phần trong `<g class="part part-A">` … `part-E`.
   - **Hình động cho yếu tố và công cụ** ở mục 1.4 và 1.5:
     - Yếu tố: `[Element 1]` đang dùng demo `lockedBox`, `[Element 2]` dùng `hidden`.
     - Công cụ: `[Booster 1]` dùng `hand`, `[Booster 2]` dùng `shuffle`, `[Booster 3]` dùng `magnet`. Demo công cụ có vòng tròn chỉ chỗ người chơi chạm (`tap()`) và hiệu ứng khói khi hộp biến mất (`puff()`).
     - Có sẵn 10 demo dùng lại được. Yếu tố: `hidden`, `locked`, `linked`, `door`, `lockedBox`, `blockQueue`, `lockedSlot`. Công cụ: `hand`, `shuffle`, `magnet`.
     - Gọi `demo('key')` để dùng chú thích có sẵn trong `DCAP`, hoặc `demo('key', t('…','…'))` để tự viết chú thích.
     - Muốn làm demo mới: thêm một mục vào `DEMOS` (thời lượng `D`, hàm `svg`, hàm `tracks`) và một chú thích vào `DCAP` cùng key.
     - Hình được vẽ bằng các hàm `mbox` (hộp), `dslots` (ô), `cdBadge` (con số đếm ngược), `padlock` (ổ khóa).
     - `tracks` là danh sách `[selector, thuộc tính CSS, [[giây, giá trị], …]]`. Bộ chạy demo tự lặp và tự dừng khi demo ra khỏi màn hình.
6. Tìm các chỗ còn sót: trong file không còn `ph(` nào và không còn chữ `[` dạng placeholder.

## Quy ước khi viết nội dung

- Mỗi đoạn chữ hiện ra trên trang có hai bản, viết bằng `t('English','Tiếng Việt')`. Tên riêng của yếu tố hoặc công cụ viết bằng `nm()`, để bản tiếng Việt hiện thêm tên tiếng Anh nhỏ bên dưới.
- Các hàm dựng sẵn trong `view()`:
  - `ent` (một mục có tiêu đề)
  - `ul`, `ol`, `params`, `defs`, `table`
  - `sys`, `panel`, `tiles`, `kv`, `lead` (dạng khối như mục 1.3)
  - `popup` (chữ trong popup hướng dẫn)
  - `lvl`, `unlock`, `unlockB`, `hap`
- Khi thêm hoặc đổi mục trong mục lục, sửa mảng `SECTIONS`. Mục nào có `wip:true` sẽ hiện nhãn "In progress".
- Màu `c1`…`c8` dùng chung cho bảng màu và huy hiệu số mục.
