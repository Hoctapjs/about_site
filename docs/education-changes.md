# Ghi chú chỉnh sửa phần Education (nhánh `Fix_About`)

Tài liệu này giải thích **tất cả thay đổi** trong 2 commit dành cho người mới học HTML/CSS.
Mục tiêu chung: phần **Education** trên trang `About.html` hiển thị đẹp ở cả **máy tính (desktop)** và **điện thoại (mobile)**.

- **Desktop**: logo nằm **ngang hàng (bên cạnh)** nội dung, và chiếm **hết bề ngang** màn hình.
- **Mobile**: logo nằm **bên trên** nội dung, và **nằm gọn trong cùng một khung trắng**.

---

## Khái niệm nền tảng cần biết trước

Để hiểu các thay đổi bên dưới, bạn cần nắm vài ý:

- **HTML** là "khung xương": các thẻ `<div>`, `<h3>`, `<p>`, `<img>`... tạo ra nội dung.
- **CSS** là "trang điểm": quyết định màu sắc, vị trí, kích thước.
- **`class`**: cái tên gắn vào thẻ HTML để CSS biết "tô" cho thẻ nào. Ví dụ `<div class="education-row">`.
- **Grid** (`display: grid`): cách xếp các phần tử thành **cột/hàng** (giống bảng). Dùng cho desktop để logo nằm cạnh nội dung.
- **Flexbox** (`display: flex`): cách xếp phần tử thành **một hàng hoặc một cột**. Dùng cho mobile để xếp dọc.
- **Responsive** (`@media`): viết CSS riêng cho màn hình nhỏ. `@media (max-width: 768px)` nghĩa là "chỉ áp dụng khi màn hình rộng tối đa 768px" (điện thoại).

Cấu trúc HTML của phần Education:

```html
<div class="education">
    <h3>Education</h3>

    <div class="education-row">           <!-- 1 trường học = 1 hàng -->
        <div class="education-content">   <!-- khung trắng chứa chữ -->
            ...tên trường, ngành, năm...
        </div>
        <div class="education-logo">      <!-- logo trường -->
            <img src="...">
        </div>
    </div>

    <div class="education-row reverse">   <!-- trường thứ 2, đảo bên -->
        ...
    </div>
</div>
```

---

## Commit 1 — `9102642`
**"Fix About education layout: horizontal on desktop, stacked on mobile"**
(Sửa bố cục: ngang trên desktop, xếp dọc trên mobile)

File sửa: `css/style.css`

### 1.1. Thêm khoảng cách phía trên phần Education
```css
.education {
    margin-top: 30px;
}
```
- `margin-top: 30px;` tạo khoảng trống 30px phía trên, để phần Education không bị dính sát phần phía trên.

### 1.2. Sửa lỗi quan trọng: bật chế độ lưới (grid) cho desktop
**Trước đây:**
```css
.education-row {
    grid-template-columns: minmax(900px, 1fr) 220px;
}
```
**Sau khi sửa:**
```css
.education-row {
    display: grid;                       /* <-- DÒNG QUAN TRỌNG NHẤT */
    grid-template-columns: 1fr 160px;
    gap: 24px;
    align-items: center;
    margin-bottom: 24px;
}
```
Giải thích:
- **Lỗi cũ**: code có khai báo các cột (`grid-template-columns`) nhưng **quên `display: grid;`**. Thiếu dòng này thì trình duyệt **không** hiểu đây là lưới → logo và nội dung bị xếp chồng lên nhau thay vì nằm cạnh nhau.
- `display: grid;` → bật chế độ lưới, giờ các phần tử mới xếp thành cột.
- `grid-template-columns: 1fr 160px;` → tạo **2 cột**: cột 1 rộng linh hoạt (`1fr` = chiếm hết phần còn lại), cột 2 cố định `160px` cho logo.
- `minmax(900px, 1fr)` cũ bị bỏ vì `900px` quá lớn → làm tràn (vỡ) bố cục.
- `gap: 24px;` → khoảng cách 24px giữa nội dung và logo.
- `align-items: center;` → căn giữa theo chiều dọc (logo và chữ thẳng hàng giữa).
- `margin-bottom: 24px;` → khoảng cách 24px giữa trường này và trường kế tiếp.

### 1.3. Hàng "đảo bên" (`.reverse`) — đổi logo sang trái
```css
.education-row.reverse {
    grid-template-columns: 160px 1fr;     /* cột logo (160px) ra trước */
}

.education-row.reverse .education-content {
    grid-column: 2;                       /* đẩy nội dung sang cột 2 (bên phải) */
}

.education-row.reverse .education-logo {
    grid-column: 1;                       /* đẩy logo sang cột 1 (bên trái) */
    grid-row: 1;
}
```
Giải thích:
- Trường thứ 2 dùng thêm class `reverse` để **đổi chỗ**: logo bên trái, nội dung bên phải (tạo hiệu ứng xen kẽ cho đẹp).
- `grid-column: 1` / `grid-column: 2` → chỉ định phần tử nằm ở cột số mấy.
- Nhờ vậy không cần đổi thứ tự trong HTML, chỉ dùng CSS để hoán đổi vị trí.

### 1.4. Chỉnh kích thước logo trên desktop
```css
.education-logo img {
    width: 140px;    /* trước là 180px -> thu nhỏ cho cân đối */
    height: auto;    /* chiều cao tự co theo chiều rộng, không méo ảnh */
    object-fit: contain;
}
```

### 1.5. Chỉnh logo trên mobile (trong khối `@media`)
```css
.education-logo img {
    width: 110px;    /* trước là 90px */
    height: auto;    /* trước là 90px cố định -> gây méo ảnh */
}
```
- Trước đây ép cả `width: 90px; height: 90px;` → ảnh logo không vuông sẽ bị **bóp méo**.
- Sửa thành `height: auto;` → ảnh giữ đúng tỉ lệ, không méo.

---

## Commit 2 — `acc490a`
**"Education: full-width on desktop, logo inside white card on mobile"**
(Desktop chiếm hết bề ngang; mobile logo nằm trong khung trắng)

Files sửa: `html/About.html` và `css/style.css`

### 2.1. (HTML) Đưa phần Education ra ngoài để chiếm hết bề ngang
**Vấn đề:** Trang About chia làm 2 cột: bên trái là chữ (`.about-text`), bên phải là hình (`.about-img`). Trước đây phần Education **nằm bên trong cột trái** → chỉ rộng được **một nửa** màn hình.

**Cách sửa:** Di chuyển cả khối `<div class="education">` **ra ngoài** khu vực 2 cột (`.about-container`), để nó trở thành phần riêng nằm dưới → tự động **trải hết chiều ngang**.

Trước (rút gọn):
```html
<div class="about-container">
    <div class="about-text">
        ...chữ...
        <div class="education"> ... </div>   <!-- bị kẹt trong cột trái -->
    </div>
    <div class="about-img"> ... </div>
</div>
```

Sau (rút gọn):
```html
<div class="about-container">
    <div class="about-text">
        ...chữ...
    </div>
    <div class="about-img"> ... </div>
</div>

<div class="education"> ... </div>   <!-- ra riêng, chiếm hết bề ngang -->
```
> Lưu ý: **nội dung chữ không đổi**, chỉ **đổi vị trí** của khối Education trong cấu trúc HTML.

### 2.2. (CSS) Trên mobile: gộp logo vào chung khung trắng với nội dung
**Mục tiêu:** Trên điện thoại, logo và phần chữ nằm trong **cùng một khung trắng** (thay vì logo nằm tách rời bên ngoài).

**Cách làm:** Biến **cả hàng** (`.education-row`) thành khung trắng, và **bỏ** khung trắng riêng của phần nội dung.

Cho cả hàng thành khung trắng:
```css
.education-row,
.education-row.reverse {
    display: flex;
    flex-direction: column;   /* xếp dọc: logo trên, chữ dưới */
    align-items: center;      /* căn giữa theo chiều ngang */
    gap: 16px;
    text-align: center;

    /* Biến cả hàng thành 1 thẻ trắng */
    background: #fff;                         /* nền trắng */
    padding: 28px 24px;                       /* đệm bên trong cho thoáng */
    border-radius: 20px;                      /* bo góc tròn */
    box-shadow: 0 10px 30px rgba(0,0,0,0.08); /* đổ bóng nhẹ */
}
```
Bỏ khung trắng riêng của phần nội dung (để không bị "khung trong khung"):
```css
.education-content {
    order: 2 !important;     /* nội dung xuống dưới logo */
    width: 100%;

    background: none;        /* bỏ nền trắng riêng */
    padding: 0;              /* bỏ đệm riêng */
    border-radius: 0;        /* bỏ bo góc riêng */
    box-shadow: none;        /* bỏ bóng riêng */
}
```
Giải thích thêm:
- `flex-direction: column;` → xếp các phần tử theo chiều dọc (trên xuống).
- `order` → quyết định thứ tự hiển thị: logo (`order: 1`) ở trên, nội dung (`order: 2`) ở dưới.
- Vì giờ **cả hàng** đã là khung trắng, nên phải **bỏ** nền/bóng/bo góc của riêng phần nội dung, tránh hiện tượng **1 khung trắng nằm trong 1 khung trắng** trông xấu.

---

## Tóm tắt nhanh

| Thiết bị | Logo nằm đâu | Bề ngang | Khung trắng |
|----------|--------------|----------|-------------|
| Desktop  | Bên cạnh nội dung (xen kẽ trái/phải) | Chiếm hết màn hình | Chỉ bọc phần chữ |
| Mobile   | Bên trên nội dung | Theo bề ngang điện thoại | Bọc cả logo + chữ |

**Kỹ thuật CSS chính đã dùng:**
- `display: grid` + `grid-template-columns` → xếp cạnh nhau (desktop).
- `display: flex` + `flex-direction: column` → xếp dọc (mobile).
- `@media (max-width: 768px)` → áp dụng riêng cho điện thoại.
- `grid-column` → hoán đổi vị trí logo/nội dung mà không sửa HTML.
- `background`, `padding`, `border-radius`, `box-shadow` → tạo hình "thẻ" (card) trắng.

**Bài học rút ra:** muốn dùng `grid-template-columns` thì **bắt buộc** phải có `display: grid;` trước đó — đây chính là lỗi đã được sửa.
