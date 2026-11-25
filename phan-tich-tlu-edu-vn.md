# Giải phẫu bố cục tlu.edu.vn — Bài giảng Front-end theo 3 lớp song song

> **Nguồn khảo sát:** https://tlu.edu.vn/ (Trường Đại học Thủy lợi) · DOM thực tế trích xuất ngày 25/11/2026
> **Công nghệ gốc của trang:** Tailwind CSS utilities + Swiper · **Phương pháp:** phân tích từ ngoài vào trong, từ trên xuống dưới; mỗi component phân tích song song 3 lớp **A. Layout Box · B. CSS Layout Technique · C. Content + HTML Element**.
> **Quy ước bằng chứng:** mục ✓ là điều đọc trực tiếp từ DOM/HTML; mục ? là suy luận từ layout, cần kiểm tra HTML/interaction để xác nhận. CSS minh họa trong bài viết bằng CSS thuần cho dễ học; trang thật dùng Tailwind utility tương ứng.

**Công thức tư duy cốt lõi:**

```
Nhìn giao diện → Khoanh hộp → Đặt tên → Xác định cha/con
→ Chọn Flex / Grid / Block / Position → Nhận diện nội dung → Chọn HTML semantic
```

---

## 01. Bản đồ bố cục toàn trang (Bước 1)

Trang thật gồm `header` (2 tầng: brand bar + nav bar), `main` chứa 13 section, một `nav` mạng xã hội cố định, và `footer`.

```text
page
├── header                     [sticky top-0 z-30]
│   ├── header-inner           [container 1400px · FLEX justify-between]
│   └── navigation             [thanh xanh #0118d8 · ẩn < 1025px]
├── main
│   ├── hero                   [Swiper carousel 7 banner · tràn viền]
│   ├── section · Tin Tức      [GRID 3 cột, trái span 2 · trong 2fr 1fr]
│   ├── section · Sự Kiện      [FLEX slider + badge ngày absolute]
│   ├── section · Con Số Ấn Tượng [dl GRID 2 → 5 cột]
│   ├── section · banner xanh + Tuyển Sinh [GRID 2 cột · ảnh 4:3]
│   ├── section · Đào Tạo / Sinh Viên / Cựu SV [GRID 3 cột]
│   ├── section · Các Đơn Vị Đào Tạo, KHCN [Swiper dọc + panel]
│   ├── section ×3 · HTQT / Nghiên Cứu / Vì Cộng Đồng [GRID 3+1]
│   ├── section · Mạng Lưới Đối Tác [nền xanh · Swiper logo]
│   └── section · Khoảnh Khắc TLU [GRID mosaic 3→4→5 cột]
├── social-rail                [nav FIXED mép phải]
└── footer                     [GRID 1.35fr 1fr 1fr 1fr + 4 cột link]
```

**Quan sát trực tiếp từ DOM:** mọi section dùng chung một mẫu container: `mx-auto max-w-[1400px] px-6 lg:px-8` — tức `max-width: 1400px; margin-inline: auto; padding-inline: 24px`. Đây là "hộp căn giữa" lặp lại nhiều nhất trang.

| Hộp | Vai trò | Quan hệ cha | Container căn giữa? |
|---|---|---|---|
| `header` | Nhận diện thương hiệu + điều hướng, bám đỉnh khi cuộn | con trực tiếp của page | Có — 1400px |
| `navigation` | Menu chính 8 mục, dropdown 2 cấp | con của header | Có — 1400px |
| `hero` | Banner nổi bật, thu hút ánh nhìn đầu tiên | con đầu của main | Không — tràn viền |
| `main > section ×13` | Mỗi section một chủ đề nội dung | con của main | Có — 1400px (trừ hero, banner) |
| `social-rail` | Lối tắt mạng xã hội | neo vào viewport, ngoài flow | Không |
| `footer` | Liên hệ, địa chỉ, sitemap, bản quyền | con cuối của page | Có — 1400px |

> **Vì sao hero KHÔNG dùng container?** Banner được thiết kế tràn hết chiều ngang màn hình. Vùng nào cần "đóng khung đọc" thì có container; vùng nào là hình ảnh sân khấu thì để tràn. Đừng áp container một cách máy móc.

---

## 02. Phân tích từng component — song song 3 lớp (Bước 2)

Mỗi component đi qua đủ 7 mục: **① Layout Box → ② CSS Layout → ③ Nội dung + HTML → ④ bản chỉ-dùng-div → ⑤ bản semantic → ⑥ CSS minh họa → ⑦ kết luận tư duy**.

### 01. Header — Brand Bar — `.header-inner`

**Kỹ thuật chính:** Flexbox + Sticky

#### 1. Nhận diện Layout Box

Thanh nhận diện thương hiệu bám đỉnh màn hình. Cần hộp riêng vì nó là vùng cố định (sticky) tách khỏi dòng chảy nội dung, và bên trong là bài toán căn ngang kinh điển: logo trái — tiện ích phải.

```text
header                          ← sticky top-0 z-30
└── header-inner                ← container 1400px
    ├── brand                   ← a > img logo (flex-none)
    └── header-actions
        ├── lang-switch         ← button VN / EN
        └── menu-toggle         ← button hamburger (<1025px)
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Flexbox + Position sticky.** `header-inner` là Flex Container, hai items là `brand` và `header-actions`. Bản thân actions lại là flex container nhỏ (nested). Logo đặt `flex-none` để không bị co khi hẹp màn hình.

- `display: flex`
- `align-items: center`
- `justify-content: space-between`
- `gap: 16px`
- `position: sticky; top: 0`
- `max-width: 1400px; margin-inline: auto`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Logo trường | hình ảnh thương hiệu, bấm về trang chủ | `a > img` |
| Chuyển ngôn ngữ | hành vi tại chỗ | `button` |
| Mở menu mobile | hành vi tại chỗ | `button` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="header">
    <div class="header-inner">
        <div class="brand">
            <img src="logo.png" alt="">
        </div>
        <div class="header-actions">
            <div class="lang-switch">VN | EN</div>
            <div class="menu-toggle">☰</div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<header class="header">
    <div class="header-inner">
        <a class="brand" href="/">
            <img src="logo.png" alt="Đại học Thủy lợi">
        </a>
        <div class="header-actions">
            <button class="lang-switch" type="button">VN | EN</button>
            <button class="menu-toggle" type="button"
                    aria-label="Mở menu" aria-expanded="false">☰</button>
        </div>
    </div>
</header>
```

#### 6. CSS layout minh họa

```css
.header {
    position: sticky;   /* bám đỉnh khi cuộn */
    top: 0;
    z-index: 30;        /* đè lên nội dung */
    background: #fff;
}
.header-inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    max-width: 1400px;
    margin-inline: auto;
    height: 100px;      /* desktop ≥1025px */
}
.brand { flex: none; }  /* logo không bị co */
.header-actions {
    display: flex;
    align-items: center;
    gap: 12px;
}
```

#### 7. Kết luận tư duy

- **Layout:** `header-inner → brand + actions`
- **CSS:** `Flex row · justify-between · sticky`
- **HTML:** `header · a>img · button`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: sticky top-0 z-30
- ✓ Đã xác minh từ DOM: flex items-center justify-between
- ✓ Đã xác minh từ DOM: max-w-[1400px]
- ✓ Đã xác minh từ DOM: brand flex-none w-[350px]

---

### 02. Navigation — menu chính + dropdown — `.nav-list`

**Kỹ thuật chính:** Flexbox + Position

#### 1. Nhận diện Layout Box

Thanh menu ngang nền xanh #0118d8, 8 mục chia đều chiều rộng, mỗi mục có thể xổ dropdown 2 cấp. Cần hộp riêng vì đây là vùng điều hướng độc lập, ẩn hoàn toàn dưới 1025px.

```text
nav                             ← ẩn <1025px
└── nav-inner                   ← container 1400
    └── nav-list (ul)           ← flex, items-stretch
        └── nav-item (li) ×8    ← flex-auto, relative
            ├── nav-link (a)    ← flex center: span + svg
            └── dropdown (div)  ← absolute, hiện khi hover
                └── ul > li (group/sub) > a
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Flexbox 3 tầng lồng nhau + Position cho dropdown.** `ul` là flex container chia đều 8 `li` bằng `flex: auto`; mỗi `a` lại là flex container căn chữ + mũi tên. Dropdown là `absolute` neo vào `li relative`, hiện bằng opacity/translate khi `:hover` hoặc `:focus-within` (hỗ trợ bàn phím!).

- `ul: display:flex; align-items:stretch`
- `li: flex:auto; position:relative`
- `a: display:flex; justify-content:center; gap:6px`
- `dropdown: position:absolute; top:100%`
- `hiện: opacity 0→1 + translateY`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Vùng điều hướng | menu chính của trang | `nav` |
| Danh sách mục menu | các mục ngang hàng | `ul > li` |
| Mục menu | điều hướng tới trang khác | `a > span` |
| Mũi tên chỉ dropdown | icon trang trí | `svg` |
| Menu con 2 cấp | danh sách lồng | `div > ul > li > a` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="nav">
    <div class="nav-inner">
        <div class="nav-list">
            <div class="nav-item">
                <div class="nav-link">GIỚI THIỆU ▾</div>
                <div class="dropdown">
                    <div class="nav-sub-item">Tổng quan</div>
                    <div class="nav-sub-item">Sứ mạng</div>
                </div>
            </div>
            <!-- ... 7 item khác -->
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<nav class="nav" aria-label="Điều hướng chính">
    <div class="nav-inner">
        <ul class="nav-list">
            <li class="nav-item">
                <a class="nav-link" href="/gioi-thieu">
                    <span>GIỚI THIỆU</span>
                    <svg aria-hidden="true">…</svg>
                </a>
                <div class="dropdown">
                    <ul>
                        <li><a href="/tong-quan">Tổng quan</a></li>
                        <li><a href="/su-mang">Sứ mạng</a></li>
                    </ul>
                </div>
            </li>
        </ul>
    </div>
</nav>
```

#### 6. CSS layout minh họa

```css
.nav { background: #0118d8; }
.nav-list {
    display: flex;
    align-items: stretch;  /* item cao hết thanh */
}
.nav-item {
    flex: auto;            /* 8 mục chia đều */
    position: relative;    /* mốc cho dropdown */
}
.nav-link {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    height: 40px;
}
.dropdown {
    position: absolute;
    top: 100%; left: 0;
    opacity: 0;
    visibility: hidden;
    transform: translateY(8px);
    transition: .2s;
}
.nav-item:hover .dropdown,
.nav-item:focus-within .dropdown {
    opacity: 1;
    visibility: visible;
    transform: translateY(0);
}
```

#### 7. Kết luận tư duy

- **Layout:** `nav-list → nav-item → nav-link`
- **CSS:** `Flex 3 tầng · flex-auto · dropdown absolute`
- **HTML:** `nav · ul/li/a · svg`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: ul flex w-full items-stretch
- ✓ Đã xác minh từ DOM: li group relative flex flex-auto
- ✓ Đã xác minh từ DOM: dropdown absolute + group-hover:visible
- ✓ Đã xác minh từ DOM: group-focus-within (bàn phím)
- ✓ Đã xác minh từ DOM: hidden min-[1025px]:block

---

### 03. Mobile Drawer — menu ngăn kéo — `.mobile-drawer`

**Kỹ thuật chính:** Position + Flex column

#### 1. Nhận diện Layout Box

Menu mobile trượt từ trái ra, phủ lên nội dung. Cần hộp riêng vì nó sống ngoài dòng chảy (fixed), có vòng đời đóng/mở riêng và vùng cuộn riêng.

```text
aside#mobile-nav                ← fixed, w-340px, flex-col
├── drawer-head                 ← flex justify-between
│   ├── span (tiêu đề)
│   └── button (đóng menu)
├── drawer-search (form)        ← input + button
├── drawer-nav (nav)            ← flex-1, overflow-y-auto
│   └── ul > li > a
└── drawer-foot
(scrim — lớp phủ mờ sau drawer)
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Position fixed + Flexbox column.** Drawer neo vào viewport bằng `fixed; inset-block: 0; left: 0`, trượt bằng `translateX(-100%) → 0`. Bên trong là flex dọc: head và foot cao tự nhiên, vùng nav chiếm trọn phần còn lại bằng `flex: 1` và tự cuộn `overflow-y: auto` — mẫu "holy grail" của mọi sidebar.

- `position:fixed; inset-block:0; left:0`
- `width:340px; max-width:90vw`
- `display:flex; flex-direction:column`
- `transform:translateX(-100%) → 0`
- `nav: flex:1; overflow-y:auto`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Ngăn kéo menu | vùng điều hướng dialog | `aside[role=dialog]` |
| Nút đóng | hành vi tại chỗ | `button` |
| Ô tìm kiếm | form nhập liệu | `form > input + button` |
| Danh sách menu | điều hướng | `nav > ul > li > a` |
| Lớp phủ mờ | chặn tương tác nền | `div (scrim)` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="mobile-drawer">
    <div class="drawer-head">
        <div class="drawer-title">MENU</div>
        <div class="drawer-close">✕</div>
    </div>
    <div class="drawer-search">
        <div class="search-input">Tìm kiếm…</div>
    </div>
    <div class="drawer-nav">
        <div class="drawer-link">GIỚI THIỆU</div>
        <div class="drawer-link">TUYỂN SINH</div>
    </div>
</div>
<div class="scrim"></div>
```

#### 5. Phiên bản HTML semantic

```html
<aside id="mobile-nav" class="mobile-drawer"
       role="dialog" aria-modal="true"
       aria-label="Menu di động" aria-hidden="true">
    <div class="drawer-head">
        <span class="drawer-title">MENU</span>
        <button type="button" aria-label="Đóng menu">✕</button>
    </div>
    <form class="drawer-search" role="search">
        <input type="search" placeholder="Tìm kiếm…">
        <button type="submit">Tìm</button>
    </form>
    <nav class="drawer-nav" aria-label="Điều hướng di động">
        <ul>
            <li><a href="/gioi-thieu">GIỚI THIỆU</a></li>
            <li><a href="/tuyen-sinh">TUYỂN SINH</a></li>
        </ul>
    </nav>
</aside>
```

#### 6. CSS layout minh họa

```css
.mobile-drawer {
    position: fixed;
    inset-block: 0;         /* top:0; bottom:0 */
    left: 0;
    z-index: 50;
    width: 340px;
    max-width: 90vw;
    display: flex;
    flex-direction: column; /* xếp dọc 3 tầng */
    transform: translateX(-100%);
    visibility: hidden;
    transition: transform .3s, visibility .3s;
}
.mobile-drawer.open {
    transform: translateX(0);
    visibility: visible;
}
.drawer-head {
    display: flex;
    align-items: center;
    justify-content: space-between;
}
.drawer-nav {
    flex: 1;                /* ăn hết phần còn lại */
    overflow-y: auto;       /* tự cuộn */
}
```

#### 7. Kết luận tư duy

- **Layout:** `drawer → head + search + nav`
- **CSS:** `Fixed + Flex column · flex-1 scroll`
- **HTML:** `aside[dialog] · form · nav · button`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: aside fixed inset-y-0 left-0 w-[340px] flex-col
- ✓ Đã xác minh từ DOM: -translate-x-full + invisible
- ✓ Đã xác minh từ DOM: nav flex-1 overflow-y-auto
- ✓ Đã xác minh từ DOM: role=dialog aria-modal
- ? Suy luận — cần kiểm tra: Lớp scrim mờ phía sau — có div fixed riêng, cần kiểm tra interaction để xác nhận vai trò

---

### 04. Hero — carousel banner — `.hero-carousel`

**Kỹ thuật chính:** Flex + Position (Swiper)

#### 1. Nhận diện Layout Box

Sân khấu đầu trang: 7 banner quảng bá chạy ngang, tràn toàn bộ chiều rộng. Cần hộp riêng vì đây là một cỗ máy trượt (slider) có điều khiển riêng, không phải nội dung đọc.

```text
section[aria-roledescription=carousel]
└── hero-carousel (.swiper)     ← relative, overflow-hidden
    ├── hero-track (.swiper-wrapper)   ← flex row
    │   └── hero-slide ×7       ← flex-none, width 100%
    │       └── picture > img
    └── hero-dots (.swiper-pagination) ← absolute đáy
        └── button.dot ×7
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Flexbox hàng ngang + Position + JS transform.** Track là flex container giữ 7 slide trên một hàng, mỗi slide `flex-shrink: 0; width: 100%`. JS (Swiper) đẩy track bằng `translateX(-N×100%)`. Dots là absolute neo đáy giữa. **Đã xác minh là slider thật**: DOM có class `swiper-initialized`, 7 slide, aria "2/7…".

- `wrapper: display:flex`
- `slide: flex-shrink:0; width:100%`
- `track: transform translateX (JS)`
- `dots: position:absolute; bottom; left:50%`
- `container: overflow:hidden`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Vùng banner nổi bật | section có nhãn | `section[aria-label]` |
| Mỗi banner | hình quảng bá | `picture > img` |
| Chấm chuyển banner | hành vi tại chỗ | `button (Swiper render)` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="hero">
    <div class="hero-carousel">
        <div class="hero-track">
            <div class="hero-slide"><img src="banner-1.jpg" alt=""></div>
            <div class="hero-slide"><img src="banner-2.jpg" alt=""></div>
            <!-- ... 7 slide -->
        </div>
        <div class="hero-dots">
            <div class="dot active"></div>
            <div class="dot"></div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section class="hero" aria-label="Nội dung nổi bật trang chủ"
         aria-roledescription="carousel">
    <div class="hero-carousel">
        <div class="hero-track">
            <div class="hero-slide" role="group"
                 aria-roledescription="slide" aria-label="1 / 7">
                <picture>
                    <img src="banner-1.jpg" alt="Kỷ niệm ngày Nhà giáo Việt Nam 20/11">
                </picture>
            </div>
        </div>
        <div class="hero-dots">
            <button type="button" aria-label="Đi đến banner 1"></button>
            <button type="button" aria-label="Đi đến banner 2"></button>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.hero-carousel {
    position: relative;   /* mốc cho dots */
    overflow: hidden;     /* giấu slide chờ */
}
.hero-track {
    display: flex;        /* 7 slide một hàng */
    /* JS: transform: translateX(-100% * index) */
    transition: transform .5s ease;
}
.hero-slide {
    flex-shrink: 0;       /* không cho co */
    width: 100%;
}
.hero-slide img {
    width: 100%;
    display: block;
}
.hero-dots {
    position: absolute;
    bottom: 14px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 8px;
}
```

#### 7. Kết luận tư duy

- **Layout:** `carousel → track → slide ×7`
- **CSS:** `Flex row + translateX (JS) · dots absolute`
- **HTML:** `section[carousel] · picture/img · button`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: swiper-initialized, 7 slide
- ✓ Đã xác minh từ DOM: aria-roledescription=carousel
- ✓ Đã xác minh từ DOM: pagination bullets có aria-label
- ? Suy luận — cần kiểm tra: Tự động chuyển slide sau N giây — cần kiểm tra interaction để xác nhận autoplay

---

### 05. Khối Tin Tức — lưới 2:1 lồng 2:1 — `.news-grid`

**Kỹ thuật chính:** CSS Grid lồng nhau

#### 1. Nhận diện Layout Box

Khối tin phức tạp nhất trang: bên trái 2/3 là tin nổi bật + slider tin phụ, bên phải 1/3 là danh sách headlines. Đáng học vì có HAI tầng grid lồng nhau và tỷ lệ cột khai báo bằng fr.

```text
section
└── container
    └── news-grid               ← grid lg:3 cột, gap 40
        ├── news-main           ← col-span-2 (chiếm 2/3)
        │   ├── section-title (h2 > a)
        │   └── news-main-grid  ← grid 2fr 1fr, gap 30
        │       ├── news-featured (article)
        │       └── news-side-slider
        └── news-aside          ← 1/3 còn lại
            ├── section-title
            └── headline-list (ul)
                └── headline-item (li)
                    ├── a (line-clamp-2)
                    └── time
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid 2 tầng.** Tầng ngoài `grid-template-columns: repeat(3, 1fr)` với cột trái `grid-column: span 2` → tỷ lệ 2:1. Tầng trong `grid-template-columns: 2fr 1fr` → lại 2:1. Vì sao Grid mà không Flex? Vì tỷ lệ là của **cột trong hàng**, khai báo fr trực tiếp, không cần tính phần trăm. Dưới 1120px (lg) tất cả xếp dọc một cột.

- `news-grid: display:grid; grid-template-columns:repeat(3,1fr); gap:40px`
- `news-main: grid-column:span 2`
- `news-main-grid: grid-template-columns:2fr 1fr; gap:30px`
- `<lg: 1 cột (Normal Flow theo grid)`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Tiêu đề khối | heading cấp 2, kiêm link xem tất cả | `h2 > a` |
| Tin nổi bật | bài viết độc lập | `article (img, h3>a, p, time)` |
| Danh sách headline | các tin ngắn xếp dọc | `ul > li` |
| Tiêu đề tin ngắn | link bài viết | `a` |
| Ngày đăng | mốc thời gian | `time` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="news-section">
    <div class="container">
        <div class="news-grid">
            <div class="news-main">
                <div class="section-title">Tin Tức</div>
                <div class="news-main-grid">
                    <div class="news-featured">
                        <img src="tin-1.jpg" alt="">
                        <div class="news-title">Tiêu đề tin chính</div>
                        <div class="news-excerpt">Mô tả…</div>
                    </div>
                    <div class="news-side-slider">…</div>
                </div>
            </div>
            <div class="news-aside">
                <div class="section-title">Tin khác</div>
                <div class="headline-list">
                    <div class="headline-item">
                        <div class="headline-link">Tiêu đề…</div>
                        <div class="headline-date">13/11/2026</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section class="news-section" aria-label="Tin tức">
    <div class="container">
        <div class="news-grid">
            <div class="news-main">
                <h2 class="section-title"><a href="/tin-tuc">Tin Tức</a></h2>
                <div class="news-main-grid">
                    <article class="news-featured">
                        <a href="/tin/abc"><img src="tin-1.jpg" alt="…"></a>
                        <h3><a href="/tin/abc">Tiêu đề tin chính</a></h3>
                        <p>Mô tả…</p>
                        <time datetime="2026-11-20">20/11/2026</time>
                    </article>
                    <div class="news-side-slider">…</div>
                </div>
            </div>
            <div class="news-aside">
                <h2 class="section-title">Tin khác</h2>
                <ul class="headline-list">
                    <li class="headline-item">
                        <a href="/tin/xyz">Sinh viên Thủy lợi hưởng ứng
                           Ngày Văn hóa Việt Nam 24/11</a>
                        <time datetime="2026-11-13">13/11/2026</time>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.news-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 40px;
}
.news-main {
    grid-column: span 2;   /* 2/3 chiều rộng */
}
.news-main-grid {
    display: grid;
    grid-template-columns: 2fr 1fr;  /* lại 2:1 */
    gap: 30px;
}
.headline-list {
    /* KHÔNG cần flex/grid: li xếp dọc
       theo Normal Flow là đúng yêu cầu */
    list-style: none;
}
.headline-item + .headline-item {
    margin-top: 12px;      /* space-y */
}
@media (max-width: 1119px) {
    .news-grid { grid-template-columns: 1fr; }
}
```

#### 7. Kết luận tư duy

- **Layout:** `news-grid → main(span 2) + aside`
- **CSS:** `Grid 3 cột + grid lồng 2fr 1fr`
- **HTML:** `section · article · ul/li · a · time`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: grid gap-10 lg:grid-cols-3
- ✓ Đã xác minh từ DOM: lg:col-span-2
- ✓ Đã xác minh từ DOM: grid-cols-[2fr_1fr] gap-[30px]
- ✓ Đã xác minh từ DOM: li: a line-clamp-2 + time
- ? Suy luận — cần kiểm tra: news-side-slider là slider dọc tin phụ — có class group/slider, cần kiểm tra interaction

---

### 06. Event Card — badge ngày đè ảnh — `.event-card`

**Kỹ thuật chính:** Flex slider + Position

#### 1. Nhận diện Layout Box

Card sự kiện trong dải trượt ngang. Bài học vàng: badge ngày tháng là phần tử absolute đè lên ảnh, và ngày tháng phải là thẻ time chứ không phải div.

```text
event-slider                    ← relative
├── event-track                 ← flex row (slider)
│   └── event-card (article)
│       ├── event-thumb (a)     ← relative, aspect-video
│       │   ├── img
│       │   └── event-date (time) ← absolute, flex-col
│       └── event-body
│           └── h3 > a (line-clamp-2)
└── event-dots                  ← flex justify-center
    └── button ×8
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Flex (track slider) + Position (badge).** Track flex giữ các card trên một hàng để JS trượt. Bên trong card là Normal Flow dọc (ảnh trên, chữ dưới). Badge ngày: `thumb relative` làm mốc, `time absolute` đặt góc ảnh, bản thân badge là flex column căn giữa ngày/tháng. Ảnh giữ tỷ lệ bằng `aspect-ratio: 16/9` — không đặt height cứng.

- `track: display:flex`
- `thumb: position:relative; aspect-ratio:16/9`
- `date: position:absolute (góc ảnh)`
- `date: display:flex; flex-direction:column; align-items:center`
- `dots: display:flex; justify-content:center; gap:8px`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Card sự kiện | bài viết độc lập | `article` |
| Ảnh sự kiện | link tới bài | `a > img` |
| Ngày diễn ra | mốc thời gian, badge đè ảnh | `time` |
| Tiêu đề | heading cấp 3 + link | `h3 > a` |
| Chấm chuyển trang | hành vi tại chỗ | `button` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="event-slider">
    <div class="event-track">
        <div class="event-card">
            <div class="event-thumb">
                <img src="su-kien.jpg" alt="">
                <div class="event-date">
                    <div class="date-day">20</div>
                    <div class="date-month">11</div>
                </div>
            </div>
            <div class="event-body">
                <div class="event-title">Lễ kỷ niệm 20/11</div>
            </div>
        </div>
    </div>
    <div class="event-dots">
        <div class="dot"></div><div class="dot"></div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<div class="event-slider">
    <div class="event-track">
        <article class="event-card">
            <a class="event-thumb" href="/su-kien/le-20-11">
                <img src="su-kien.jpg" alt="Lễ kỷ niệm 20/11">
                <time class="event-date" datetime="2026-11-20">
                    <span class="date-day">20</span>
                    <span class="date-month">11</span>
                </time>
            </a>
            <div class="event-body">
                <h3 class="event-title">
                    <a href="/su-kien/le-20-11">Lễ kỷ niệm Ngày
                       Nhà giáo Việt Nam 20/11</a>
                </h3>
            </div>
        </article>
    </div>
    <div class="event-dots">
        <button type="button" aria-label="Hiện trang sự kiện 1"></button>
        <button type="button" aria-label="Hiện trang sự kiện 2"></button>
    </div>
</div>
```

#### 6. CSS layout minh họa

```css
.event-track { display: flex; }  /* slider */
.event-card { flex: none; width: 300px; }

.event-thumb {
    position: relative;      /* MỐC cho badge */
    aspect-ratio: 16 / 9;    /* khóa tỷ lệ ảnh */
    overflow: hidden;
    display: block;
}
.event-thumb img {
    width: 100%; height: 100%;
    object-fit: cover;
}
.event-date {
    position: absolute;      /* ĐÈ lên ảnh */
    top: 10px; left: 10px;
    display: flex;
    flex-direction: column;  /* ngày trên, tháng dưới */
    align-items: center;
}
.event-title {
    display: -webkit-box;
    -webkit-line-clamp: 2;   /* cắt 2 dòng */
    -webkit-box-orient: vertical;
    overflow: hidden;
}
.event-dots {
    display: flex;
    justify-content: center;
    gap: 8px;
}
```

#### 7. Kết luận tư duy

- **Layout:** `slider → track → card(thumb+body)`
- **CSS:** `Flex track · badge absolute trên thumb relative`
- **HTML:** `article · a>img · time · h3>a · button`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: article.group
- ✓ Đã xác minh từ DOM: a relative aspect-video
- ✓ Đã xác minh từ DOM: time absolute flex flex-col items-center
- ✓ Đã xác minh từ DOM: h3 line-clamp-2
- ✓ Đã xác minh từ DOM: dots: button aria-label 'Hiện trang sự kiện N'

---

### 07. Stats — chỉ số bằng dl/dt/dd — `.stats-grid`

**Kỹ thuật chính:** Grid + Flex column

#### 1. Nhận diện Layout Box

Dải 5 con số ấn tượng (người học, đối tác, việc làm, ngành học, giảng viên TS). Bài học semantic lớn nhất trang: dữ liệu cặp nhãn–giá trị phải dùng dl/dt/dd.

```text
section#stats
└── container
    ├── h2
    └── stats-grid (dl)         ← grid 2 cột → 5 cột (≥850)
        └── stat (div) ×5       ← flex-col items-center
            ├── dt (nhãn)
            └── dd (giá trị)
        (stat đầu: col-span-2 trên mobile)
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid ngoài + Flex column trong.** `dl` là grid container: mobile 2 cột (ô đầu span 2 cho cân), từ 850px thành 5 cột đều. Mỗi ô là flex column căn giữa nhãn và số. Grid lo hàng×cột, Flex lo căn giữa dọc — mỗi công cụ một việc.

- `dl: display:grid; grid-template-columns:repeat(2,1fr); gap-row:36px`
- `≥850px: repeat(5,1fr)`
- `stat: display:flex; flex-direction:column; align-items:center`
- `stat đầu: grid-column:span 2 (mobile)`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Khối chỉ số | vùng nội dung có nhãn | `section[aria-label]` |
| Danh sách cặp nhãn–giá trị | dữ liệu mô tả | `dl` |
| Nhãn (Người học…) | thuật ngữ | `dt` |
| Giá trị (30.000…) | mô tả của nhãn | `dd` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="stats-section">
    <div class="container">
        <div class="section-title">Những Con Số Ấn Tượng</div>
        <div class="stats-grid">
            <div class="stat">
                <div class="stat-label">Người học</div>
                <div class="stat-value">30.000+</div>
            </div>
            <!-- ... 4 stat khác -->
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section id="stats" aria-label="Những Con Số Ấn Tượng">
    <div class="container">
        <h2>Những Con Số Ấn Tượng</h2>
        <dl class="stats-grid">
            <div class="stat">
                <dt>Người học</dt>
                <dd>30.000+</dd>
            </div>
            <div class="stat">
                <dt>Đối tác trong &amp; ngoài nước</dt>
                <dd>200+</dd>
            </div>
        </dl>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.stats-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    row-gap: 36px;
}
.stat {
    display: flex;
    flex-direction: column;  /* nhãn trên, số dưới */
    align-items: center;
    min-width: 0;
}
.stat:first-child {
    grid-column: span 2;     /* mobile: ô đầu trọn hàng */
}
@media (min-width: 850px) {
    .stats-grid { grid-template-columns: repeat(5, 1fr); }
    .stat:first-child { grid-column: auto; }
}
```

#### 7. Kết luận tư duy

- **Layout:** `dl.stats-grid → div.stat → dt+dd`
- **CSS:** `Grid 2→5 cột · Flex column căn giữa`
- **HTML:** `dl · dt · dd (cặp nhãn–giá trị)`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: dl grid grid-cols-2 min-[850px]:grid-cols-5
- ✓ Đã xác minh từ DOM: div flex flex-col items-center
- ✓ Đã xác minh từ DOM: col-span-2 min-[850px]:col-span-1
- ✓ Đã xác minh từ DOM: dt + dd thật trong DOM

---

### 08. Tuyển Sinh — ảnh 4:3 + nội dung — `.admission-grid`

**Kỹ thuật chính:** CSS Grid 2 cột

#### 1. Nhận diện Layout Box

Khối giới thiệu tuyển sinh: trái ảnh tỷ lệ 4:3, phải tiêu đề + mô tả + danh sách link + CTA. Đáng chú ý: chính container căn giữa cũng là grid container — một hộp hai vai.

```text
section[aria-label="Tuyển Sinh"]
└── admission-grid            ← container 1400 + grid md:2
    ├── admission-media       ← relative, aspect 4/3
    │   └── img
    └── admission-body
        ├── h2
        ├── p (mô tả)
        ├── ul > li > a (ngành, đề án…)
        └── a.cta (xem thêm)
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid 2 cột + Normal Flow bên trong.** Container vừa `max-width:1400; margin:auto` vừa `display:grid; grid-template-columns:1fr 1fr; align-items:center`. Cột chữ bên trong KHÔNG cần flex — tiêu đề, đoạn văn, danh sách vốn xếp dọc đúng ý đồ. Mobile: 1 cột, ảnh trên chữ dưới.

- `display:grid; grid-template-columns:1fr 1fr (≥768px)`
- `align-items:center; gap:32px`
- `media: aspect-ratio:4/3; overflow:hidden`
- `body: Normal Flow`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Ảnh tuyển sinh | hình minh họa | `img (trong div)` |
| Tiêu đề khối | heading cấp 2 | `h2` |
| Mô tả | đoạn văn | `p` |
| Danh sách link tuyển sinh | các mục điều hướng | `ul > li > a` |
| Nút xem thêm | điều hướng (đi trang khác) | `a.cta` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="admission-section">
    <div class="admission-grid">
        <div class="admission-media">
            <img src="tuyen-sinh.jpg" alt="">
        </div>
        <div class="admission-body">
            <div class="section-title">Tuyển Sinh</div>
            <div class="admission-desc">Cung cấp thông tin…</div>
            <div class="admission-links">
                <div class="admission-link">Ngành tuyển sinh</div>
                <div class="admission-link">Đề án tuyển sinh</div>
            </div>
            <div class="admission-cta">Xem thêm</div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section aria-label="Tuyển Sinh">
    <div class="admission-grid">
        <div class="admission-media">
            <img src="tuyen-sinh.jpg" alt="Sinh viên Thủy lợi trong ngày nhập học">
        </div>
        <div class="admission-body">
            <h2>Tuyển Sinh</h2>
            <p>Cung cấp thông tin tuyển sinh, ngành tuyển sinh,
               đề án tuyển sinh… cho thí sinh.</p>
            <ul>
                <li><a href="/nganh">Ngành tuyển sinh</a></li>
                <li><a href="/de-an">Đề án tuyển sinh</a></li>
            </ul>
            <a class="cta" href="/tuyen-sinh">Xem thêm</a>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.admission-grid {
    /* một hộp, hai vai: căn giữa + grid */
    max-width: 1400px;
    margin-inline: auto;
    display: grid;
    grid-template-columns: 1fr;   /* mobile */
    gap: 32px;
    align-items: center;
}
@media (min-width: 768px) {
    .admission-grid { grid-template-columns: 1fr 1fr; }
}
.admission-media {
    aspect-ratio: 4 / 3;
    overflow: hidden;
}
.admission-media img {
    width: 100%; height: 100%;
    object-fit: cover;
}
/* admission-body: không cần flex/grid,
   h2 p ul a tự xếp dọc đúng ý */
```

#### 7. Kết luận tư duy

- **Layout:** `admission-grid → media + body`
- **CSS:** `Grid 2 cột items-center · body Normal Flow`
- **HTML:** `section · img · h2 · p · ul/li/a`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: container chính là grid md:grid-cols-2 items-center
- ✓ Đã xác minh từ DOM: aspect-[4/3] overflow-hidden
- ✓ Đã xác minh từ DOM: body: p + ul + div cta

---

### 09. Đào Tạo / Sinh Viên / Cựu SV — 3 cột — `.edu-grid`

**Kỹ thuật chính:** Grid 3 cột

#### 1. Nhận diện Layout Box

Ba mạch nội dung song song, mỗi mạch một tiêu đề riêng và các bài viết. Bài học: min-w-0 chống tràn và tư duy 'grid dựng khung, flow dựng cột'.

```text
section
└── container
    └── edu-grid                ← grid md:3 cột, gap
        └── edu-col ×3          ← min-w-0
            ├── h2 > a (Đào Tạo / Sinh Viên / Cựu SV)
            └── article
                ├── a.thumb (aspect 16/9) > img
                └── div
                    ├── a.title (line-clamp-2)
                    └── p (line-clamp-2)
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid 3 cột + Normal Flow trong cột.** Từ 768px: `repeat(3, minmax(0,1fr))`. Mỗi cột đặt `min-width: 0` để nội dung dài không đẩy cột phình; tiêu đề và mô tả cắt bằng line-clamp. Bên trong cột không cần flex — article xếp dọc tự nhiên. Mobile: 3 cột nối tiếp nhau.

- `display:grid; grid-template-columns:repeat(3,1fr) (≥768px)`
- `gap:24px (md) / 48px (mobile dọc)`
- `edu-col: min-width:0`
- `title/excerpt: line-clamp:2`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Tiêu đề mạch | heading cấp 2 + link chuyên mục | `h2 > a` |
| Bài viết | nội dung độc lập | `article` |
| Ảnh bài | link tới bài | `a > img` |
| Tiêu đề bài | link chữ | `a` |
| Tóm tắt | đoạn văn cắt dòng | `p` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="edu-section">
    <div class="container">
        <div class="edu-grid">
            <div class="edu-col">
                <div class="section-title">Đào Tạo</div>
                <div class="edu-card">
                    <div class="edu-thumb"><img src="dao-tao.jpg" alt=""></div>
                    <div class="edu-body">
                        <div class="edu-title">Bảo vệ luận án tiến sĩ…</div>
                        <div class="edu-excerpt">Trường Đại học Thủy lợi…</div>
                    </div>
                </div>
            </div>
            <!-- Sinh Viên, Cựu Sinh Viên: cùng cấu trúc -->
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section aria-label="Đào tạo, Sinh viên, Cựu sinh viên">
    <div class="container">
        <div class="edu-grid">
            <div class="edu-col">
                <h2><a href="/dao-tao">Đào Tạo</a></h2>
                <article>
                    <a class="thumb" href="/tin/luan-an">
                        <img src="dao-tao.jpg" alt="…">
                    </a>
                    <div>
                        <a class="title" href="/tin/luan-an">Bảo vệ luận án
                           tiến sĩ cấp Trường…</a>
                        <p>Trường Đại học Thủy lợi tổ chức…</p>
                    </div>
                </article>
            </div>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.edu-grid {
    display: grid;
    grid-template-columns: 1fr;      /* mobile dọc */
    gap: 48px;
}
@media (min-width: 768px) {
    .edu-grid {
        grid-template-columns: repeat(3, minmax(0, 1fr));
        gap: 24px;
    }
}
.edu-col {
    min-width: 0;   /* CHỐNG TRÀN: cho phép cột co,
                       chữ dài phải tự cắt dòng */
}
.edu-col .title,
.edu-col p {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
```

#### 7. Kết luận tư duy

- **Layout:** `edu-grid → edu-col ×3 → article`
- **CSS:** `Grid 3 cột minmax(0,1fr) · flow trong cột`
- **HTML:** `h2>a · article · a>img · p`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: grid md:grid-cols-3
- ✓ Đã xác minh từ DOM: min-w-0 từng cột
- ✓ Đã xác minh từ DOM: a aspect-[16/9]
- ✓ Đã xác minh từ DOM: line-clamp-2

---

### 10. Các Đơn Vị — danh sách dọc + panel — `.units-layout`

**Kỹ thuật chính:** Swiper dọc + Panel

#### 1. Nhận diện Layout Box

Khối giới thiệu khoa/viện/phân hiệu: một danh sách tên đơn vị chạy dọc (swiper vertical) và một panel chi tiết bên cạnh. Bố cục dạng 'master–detail'.

```text
section[aria-label="Các đơn vị đào tạo, KHCN"]
├── units-nav
│   ├── h2
│   └── units-list (.swiper-vertical)
│       └── unit-item ×N (tên khoa/viện)
└── units-detail
    └── unit-panel
        ├── p (mô tả đơn vị đang chọn)
        └── a ("Khám phá Phân hiệu")
```

#### 2. Tư duy dàn trang CSS

**Layout mode: hai vùng song song (master–detail).** Danh sách dùng Swiper chế độ dọc (`swiper-vertical` đã xác minh trong DOM) — về bản chất là flex column bị JS trượt theo chiều dọc. Panel chi tiết là Normal Flow. Hai vùng đặt cạnh nhau bằng bố cục hàng ngang ở desktop.

- `list: swiper-vertical (flex column + translateY)`
- `panel: Normal Flow`
- `hai vùng: hàng ngang desktop`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Tiêu đề khối | heading cấp 2 | `h2` |
| Tên đơn vị | mục chọn trong danh sách | `swiper slide (button/a)` |
| Mô tả đơn vị | đoạn văn | `p` |
| Link khám phá | điều hướng | `a` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="units-section">
    <div class="units-nav">
        <div class="section-title">Các đơn vị đào tạo, KHCN</div>
        <div class="units-list">
            <div class="unit-item">Khoa Công trình</div>
            <div class="unit-item">Phân hiệu Trường ĐH Thủy lợi</div>
        </div>
    </div>
    <div class="units-detail">
        <div class="unit-panel">
            <div class="unit-desc">Phân hiệu (mã TLS) là cơ sở…</div>
            <div class="unit-link">Khám phá Phân hiệu</div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section aria-label="Các đơn vị đào tạo, KHCN">
    <div class="units-nav">
        <h2>Các đơn vị đào tạo, KHCN</h2>
        <div class="units-list" aria-label="Danh sách đơn vị">
            <!-- swiper-vertical: mỗi slide một đơn vị -->
        </div>
    </div>
    <div class="units-detail">
        <div class="unit-panel">
            <p>Phân hiệu Trường Đại học Thủy lợi (mã trường TLS)
               là cơ sở đào tạo…</p>
            <a href="/phan-hieu">Khám phá Phân hiệu</a>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
/* Danh sách dọc kiểu swiper:
   bản chất flex column, JS trượt translateY */
.units-list .swiper-wrapper {
    display: flex;
    flex-direction: column;
}
.unit-panel p { /* Normal Flow, không cần flex */ }

/* Hai vùng master–detail cạnh nhau:
   grid 2 cột là lựa chọn gọn nhất */
.units-section {
    display: grid;
    grid-template-columns: 1fr 1.4fr;
    gap: 32px;
}
```

#### 7. Kết luận tư duy

- **Layout:** `section → units-nav + units-detail`
- **CSS:** `Swiper dọc (flex col) + Grid 2 vùng`
- **HTML:** `h2 · p · a · danh sách đơn vị`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: swiper-vertical trong DOM
- ✓ Đã xác minh từ DOM: panel: p mô tả + a 'Khám phá Phân hiệu'
- ? Suy luận — cần kiểm tra: Tỷ lệ và cách đặt 2 vùng cạnh nhau — suy luận từ layout, cần kiểm tra CSS thực tế
- ? Suy luận — cần kiểm tra: Danh sách tự trượt hay bấm chọn — cần kiểm tra interaction

---

### 11. Article Card + lưới 3+1 (HTQT / Nghiên Cứu / Cộng Đồng) — `.card-grid`

**Kỹ thuật chính:** Grid 3+1 · card pattern

#### 1. Nhận diện Layout Box

Pattern lặp 3 lần trên trang: 3 card tin + 1 cột dẫn link rộng hơn (sàn 230px). Card chuẩn: ảnh 16:9, category, tiêu đề 3 dòng, ngày. Đây là 'họ card' dùng chung đáng đặt tên nhất quán.

```text
section[aria-label="Hợp Tác Quốc Tế"]
└── container
    ├── h2
    ├── card-grid               ← hidden md:grid
    │   │                          repeat(3,minmax(0,1fr))
    │   │                          minmax(230px,1fr)
    │   ├── card (article) ×3
    │   │   ├── card-thumb (a)  ← aspect 16/9
    │   │   │   └── img (hover scale-105)
    │   │   └── card-body
    │   │       ├── p.card-cat
    │   │       ├── h3.card-title (line-clamp-3) > a
    │   │       └── time
    │   └── card-cta (div cột 4)
    └── card-mobile             ← md:hidden (bản slider)
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid hàng 4 cột không đều.** `repeat(3, minmax(0,1fr)) minmax(230px,1fr)` — 3 cột card co giãn đều, cột cuối không bao giờ hẹp hơn 230px. Trong card là Normal Flow dọc. Mobile: ẩn grid, hiện bản DOM thứ hai dạng slider (`hidden md:grid` + `md:hidden`). Hover ảnh phóng nhẹ bằng `transform: scale(1.05)` trong thumb overflow-hidden.

- `display:grid; grid-template-columns:repeat(3,minmax(0,1fr)) minmax(230px,1fr)`
- `gap:20px`
- `thumb: aspect-ratio:16/9; overflow:hidden`
- `title: line-clamp:3`
- `mobile: 2 bản DOM bật/tắt`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Card tin | bài viết độc lập | `article` |
| Ảnh tin | link tới bài | `a > img` |
| Chuyên mục | nhãn phân loại | `p` |
| Tiêu đề | heading cấp 3 + link | `h3 > a` |
| Ngày đăng | mốc thời gian | `time` |
| Cột dẫn link | khối điều hướng phụ | `div > a` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="article-section">
    <div class="container">
        <div class="section-title">Hợp Tác Quốc Tế</div>
        <div class="card-grid">
            <div class="card">
                <div class="card-thumb"><img src="htqt.jpg" alt=""></div>
                <div class="card-body">
                    <div class="card-cat">Hợp tác quốc tế</div>
                    <div class="card-title">Thúc đẩy hợp tác Việt – Hàn</div>
                    <div class="card-date">19/11/2026</div>
                </div>
            </div>
            <!-- 2 card nữa + cột cta -->
            <div class="card-cta">…</div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section aria-label="Hợp Tác Quốc Tế">
    <div class="container">
        <h2>Hợp Tác Quốc Tế</h2>
        <div class="card-grid">
            <article class="card">
                <a class="card-thumb" href="/tin/viet-han">
                    <img src="htqt.jpg" alt="…">
                </a>
                <div class="card-body">
                    <p class="card-cat">Hợp tác quốc tế</p>
                    <h3 class="card-title">
                        <a href="/tin/viet-han">Thúc đẩy hợp tác giáo dục
                           Việt Nam – Hàn Quốc</a>
                    </h3>
                    <time datetime="2026-11-19">19/11/2026</time>
                </div>
            </article>
            <div class="card-cta">…</div>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.card-grid {
    display: grid;
    grid-template-columns:
        repeat(3, minmax(0, 1fr))  /* 3 card đều */
        minmax(230px, 1fr);        /* cột CTA sàn 230px */
    gap: 20px;
}
.card-thumb {
    display: block;
    aspect-ratio: 16 / 9;
    overflow: hidden;      /* cắt phần ảnh phóng */
}
.card-thumb img {
    width: 100%; height: 100%;
    object-fit: cover;
    transition: transform .3s;
}
.card:hover .card-thumb img {
    transform: scale(1.05);
}
.card-title {
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
/* Mobile: trang thật dùng 2 bản DOM */
.card-grid   { display: none; }
.card-mobile { display: block; }
@media (min-width: 768px) {
    .card-grid   { display: grid; }
    .card-mobile { display: none; }
}
```

#### 7. Kết luận tư duy

- **Layout:** `card-grid → article ×3 + cta`
- **CSS:** `Grid repeat(3,minmax(0,1fr)) + minmax(230px,1fr)`
- **HTML:** `article · a>img · p · h3>a · time`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: grid-cols-[repeat(3,minmax(0,1fr))_minmax(230px,1fr)] gap-5
- ✓ Đã xác minh từ DOM: article.group > a aspect-[16/9]
- ✓ Đã xác minh từ DOM: p + h3 line-clamp-3 > a + time
- ✓ Đã xác minh từ DOM: hidden md:grid + md:hidden
- ? Suy luận — cần kiểm tra: Bản mobile là slider vuốt — có div md:hidden riêng, cần kiểm tra interaction để xác nhận

---

### 12. Mạng Lưới Đối Tác — dải logo — `.partner-marquee`

**Kỹ thuật chính:** Flex (Swiper virtual)

#### 1. Nhận diện Layout Box

Section nền xanh với tiêu đề, đoạn dẫn căn giữa và dải logo đối tác chạy ngang. Bài học: văn bản căn giữa bằng max-width + margin auto, không cần flex.

```text
section (nền #0118d8)
└── container
    ├── h2 (căn giữa)
    ├── p.intro                 ← max-w-3xl, mx-auto
    └── partner-slider (.swiper-virtual)
        └── logo-slide ×N       ← flex row, JS trượt
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Normal Flow + Flex slider.** Tiêu đề và đoạn dẫn chỉ cần `text-align:center; max-width:768px; margin-inline:auto` — không flex. Dải logo là Swiper chế độ `virtual` (đã xác minh class `swiper-virtual`): chỉ render các slide trong tầm nhìn, tiết kiệm DOM khi có hàng trăm logo.

- `intro: max-width:768px; margin-inline:auto; text-align:center`
- `slider: flex row + translateX (JS)`
- `virtual: chỉ render slide nhìn thấy`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Tiêu đề khối | heading cấp 2 | `h2` |
| Đoạn dẫn | văn bản giới thiệu | `p` |
| Logo đối tác | hình ảnh thương hiệu đối tác | `img (trong slide)` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="partner-section">
    <div class="container">
        <div class="section-title">Mạng Lưới Đối Tác</div>
        <div class="partner-intro">Trường Đại học Thủy lợi tự hào…</div>
        <div class="partner-slider">
            <div class="logo-slide"><img src="logo-dt-1.png" alt=""></div>
            <div class="logo-slide"><img src="logo-dt-2.png" alt=""></div>
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section class="partner-section" aria-label="Mạng Lưới Đối Tác">
    <div class="container">
        <h2>Mạng Lưới Đối Tác</h2>
        <p class="partner-intro">Trường Đại học Thủy lợi tự hào là
           đối tác của các đại học, viện nghiên cứu…</p>
        <div class="partner-slider" aria-label="Logo các đối tác">
            <div class="logo-slide">
                <img src="logo-dt-1.png" alt="Tên đối tác 1">
            </div>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.partner-section {
    background: #0118d8;
    color: #fff;
    text-align: center;
}
.partner-intro {
    max-width: 768px;       /* giới hạn bề ngang chữ */
    margin-inline: auto;    /* căn giữa — KHÔNG cần flex */
}
.partner-slider .swiper-wrapper {
    display: flex;          /* hàng logo để JS trượt */
    align-items: center;
}
```

#### 7. Kết luận tư duy

- **Layout:** `section → h2 + p + slider`
- **CSS:** `Flow căn giữa + Flex slider (virtual)`
- **HTML:** `h2 · p · img`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: swiper-virtual swiper-horizontal
- ✓ Đã xác minh từ DOM: p mx-auto max-w-3xl
- ✓ Đã xác minh từ DOM: nền bg-[#0118d8] text-white
- ? Suy luận — cần kiểm tra: Dải logo tự chạy liên tục kiểu marquee — cần kiểm tra interaction để xác nhận autoplay

---

### 13. Khoảnh Khắc TLU — mosaic ảnh — `.mosaic`

**Kỹ thuật chính:** Grid mosaic + span

#### 1. Nhận diện Layout Box

Lưới ảnh hoạt động sinh viên với một ô tâm điểm lớn gấp nhiều lần. Đây là vùng Grid 'nặng đô' nhất trang: span, auto-rows, đổi vị trí ô lớn theo breakpoint.

```text
section[aria-label="Khoảnh khắc TLU"]
└── container
    ├── h2
    └── mosaic                  ← grid 3→4→5 cột
        │                          auto-rows 120→140→160px
        ├── tile (button) ×14   ← relative, img phủ
        └── tile--big (button)  ← col-span-3 row-span-2
                                   ≥992px: col-start-2
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid mosaic.** `grid-template-columns: repeat(3→4→5, 1fr)` theo breakpoint, chiều cao hàng cố định bằng `grid-auto-rows`. Ô tâm điểm `grid-column: span 3; grid-row: span 2`; từ 992px thêm `grid-column-start: 2` để đổi vị trí. Các tile là `button` (mở lightbox tại chỗ) — không phải link. Flex không diễn đạt nổi bố cục này.

- `display:grid; grid-template-columns:repeat(3,1fr)`
- `gap:12px; grid-auto-rows:120px`
- `≥768: 4 cột/140px · ≥992: 5 cột/160px`
- `big: span 3 cột × 2 hàng; ≥992: col-start 2`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Tiêu đề khối | heading cấp 2 | `h2` |
| Ô ảnh | mở lightbox tại chỗ | `button[aria-label]` |
| Ảnh khoảnh khắc | hình hoạt động | `img` |
| Ô tâm điểm | ảnh nổi bật cỡ lớn | `button.tile--big` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="moments-section">
    <div class="container">
        <div class="section-title">Khoảnh Khắc TLU</div>
        <div class="mosaic">
            <div class="tile"><img src="anh-1.jpg" alt=""></div>
            <div class="tile tile-big"><img src="anh-hot.jpg" alt=""></div>
            <div class="tile"><img src="anh-2.jpg" alt=""></div>
            <!-- ... 15 tile -->
        </div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<section aria-label="Khoảnh khắc TLU">
    <div class="container">
        <h2>Khoảnh Khắc TLU</h2>
        <div class="mosaic">
            <button class="tile" type="button"
                    aria-label="500 nam sinh viên xác lập kỷ lục áo dài">
                <img src="anh-1.jpg" alt="">
            </button>
            <button class="tile tile--big" type="button"
                    aria-label="Lễ kỷ niệm Ngày Nhà giáo Việt Nam 20/11">
                <img src="anh-hot.jpg" alt="">
            </button>
        </div>
    </div>
</section>
```

#### 6. CSS layout minh họa

```css
.mosaic {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-auto-rows: 120px;   /* chiều cao MỖI HÀNG */
    gap: 12px;
}
.tile {
    position: relative;
    overflow: hidden;
    padding: 0; border: 0;   /* button trần */
    cursor: pointer;
}
.tile img {
    width: 100%; height: 100%;
    object-fit: cover;
}
.tile--big {
    grid-column: span 3;
    grid-row: span 2;
}
@media (min-width: 768px) {
    .mosaic { grid-template-columns: repeat(4, 1fr);
              grid-auto-rows: 140px; }
    .tile--big { grid-row: span 3; }
}
@media (min-width: 992px) {
    .mosaic { grid-template-columns: repeat(5, 1fr);
              grid-auto-rows: 160px; }
    .tile--big { grid-column: 2 / span 3;
                 grid-row: span 2; }
}
```

#### 7. Kết luận tư duy

- **Layout:** `mosaic → tile ×15 (1 ô span 3×2)`
- **CSS:** `Grid auto-rows + span + col-start đổi vị trí`
- **HTML:** `button[aria-label] > img`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: grid-cols-3 → min-[768px]:4 → min-[992px]:5
- ✓ Đã xác minh từ DOM: auto-rows 120/140/160px
- ✓ Đã xác minh từ DOM: col-span-3 row-span-2, min-[992px]:col-start-2
- ✓ Đã xác minh từ DOM: tile là button có aria-label
- ? Suy luận — cần kiểm tra: Bấm tile mở lightbox xem ảnh lớn — suy luận từ việc dùng button, cần kiểm tra interaction

---

### 14. Social Rail — nav cố định mép phải — `.social-rail`

**Kỹ thuật chính:** Position fixed + Flex column

#### 1. Nhận diện Layout Box

Dải icon mạng xã hội bám mép phải màn hình ở mọi vị trí cuộn. Nhỏ nhưng là ví dụ sạch nhất về fixed + flex column.

```text
nav[aria-label="Mạng xã hội"]   ← fixed right, top 24vh
└── a.social-link ×N             ← flex-col, gap 8
    └── svg / img icon
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Position fixed + Flex column.** Neo vào viewport: `position:fixed; right:16px; top:24vh`, các icon xếp dọc bằng flex column gap 8. Ẩn dưới 768px (`hidden md:flex`). Vì nằm ngoài flow, nó không đẩy bất kỳ hộp nào — đúng nhiệm vụ của phần tử 'trang trí điều hướng'.

- `position:fixed; right:16px; top:24vh`
- `display:flex; flex-direction:column; gap:8px`
- `hidden dưới 768px`
- `z-index:20`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Vùng link mạng xã hội | điều hướng phụ | `nav[aria-label]` |
| Từng mạng xã hội | link ra ngoài | `a[target=_blank]` |
| Icon | hình nhận diện | `svg / img` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="social-rail">
    <div class="social-link"><img src="fb.svg" alt=""></div>
    <div class="social-link"><img src="yt.svg" alt=""></div>
    <div class="social-link"><img src="zalo.svg" alt=""></div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<nav class="social-rail" aria-label="Mạng xã hội">
    <a href="https://facebook.com/…" target="_blank"
       rel="noopener" aria-label="Facebook">
        <svg aria-hidden="true">…</svg>
    </a>
    <a href="https://youtube.com/…" target="_blank"
       rel="noopener" aria-label="YouTube">
        <svg aria-hidden="true">…</svg>
    </a>
</nav>
```

#### 6. CSS layout minh họa

```css
.social-rail {
    position: fixed;      /* neo vào viewport */
    right: 16px;
    top: 24vh;
    z-index: 20;
    display: flex;
    flex-direction: column;
    gap: 8px;
}
@media (max-width: 767px) {
    .social-rail { display: none; }
}
```

#### 7. Kết luận tư duy

- **Layout:** `social-rail → a ×N`
- **CSS:** `Fixed + Flex column gap 8`
- **HTML:** `nav · a · svg`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: nav fixed right-4 top-[24vh]
- ✓ Đã xác minh từ DOM: hidden md:flex flex-col gap-2

---

### 15. Footer — liên hệ + sitemap — `.footer-top`

**Kỹ thuật chính:** Grid tỷ lệ lẻ + Flex

#### 1. Nhận diện Layout Box

Chân trang nền xanh đậm: hàng trên logo + 3 khối liên hệ (cột logo rộng 1.35), hàng dưới 4 cột link sitemap, cuối là bản quyền. Bài học: tỷ lệ fr lẻ và thẻ address.

```text
footer (nền #1907dc)
└── container
    ├── footer-top              ← grid lg:1.35fr 1fr 1fr 1fr
    │   ├── footer-brand (img)
    │   └── footer-contact ×3   ← flex items-start gap 8
    │       ├── svg icon
    │       └── address / p
    ├── footer-links            ← grid sm:2 lg:4 cột
    │   └── section ×4
    │       ├── h3
    │       └── ul > li > a
    └── p.copyright > a
```

#### 2. Tư duy dàn trang CSS

**Layout mode: Grid 2 tầng + Flex hàng icon-chữ.** Hàng trên: `grid-template-columns: 1.35fr 1fr 1fr 1fr` — cột logo rộng hơn 35%, tỷ lệ lẻ mà fr xử lý gọn. Mỗi khối liên hệ là flex row (icon + chữ, `align-items:start` để icon không bị kéo giữa khi chữ nhiều dòng). Hàng dưới: grid 4 cột, bên trong mỗi cột là ul Normal Flow với space-y. Mobile: 1 cột, tablet 2 cột.

- `footer-top: grid 1.35fr 1fr 1fr 1fr (lg); 2 cột (sm); 1 cột`
- `contact: display:flex; align-items:start; gap:8px`
- `footer-links: grid repeat(4,1fr) (lg)`
- `ul: space-y (margin giữa li)`

#### 3. Nhận diện nội dung bên trong

| Nội dung | Vai trò | HTML phù hợp |
|---|---|---|
| Logo cuối trang | nhận diện thương hiệu | `img` |
| Địa chỉ, điện thoại, email | thông tin liên hệ | `address (trong div flex)` |
| Icon liên hệ | hình minh họa | `svg` |
| Nhóm link sitemap | vùng điều hướng có tiêu đề | `section > h3 + ul` |
| Link chân trang | điều hướng | `a` |
| Bản quyền | đoạn văn cuối | `p` |

#### 4. Phiên bản chỉ dùng div

```html
<div class="footer">
    <div class="container">
        <div class="footer-top">
            <div class="footer-brand"><img src="logo-white.png" alt=""></div>
            <div class="footer-contact">
                <div class="contact-icon">📍</div>
                <div class="contact-text">175 Tây Sơn, Đống Đa, Hà Nội</div>
            </div>
            <!-- 2 contact nữa -->
        </div>
        <div class="footer-links">
            <div class="footer-col">
                <div class="footer-col-title">Giới thiệu</div>
                <div class="footer-link">Tổng quan</div>
                <div class="footer-link">Sứ mạng</div>
            </div>
            <!-- 3 cột nữa -->
        </div>
        <div class="copyright">© 2026 Đại học Thủy lợi</div>
    </div>
</div>
```

#### 5. Phiên bản HTML semantic

```html
<footer class="footer">
    <div class="container">
        <div class="footer-top">
            <div class="footer-brand">
                <img src="logo-white.png" alt="Đại học Thủy lợi">
            </div>
            <div class="footer-contact">
                <svg aria-hidden="true">…</svg>
                <address>175 Tây Sơn, Đống Đa, Hà Nội</address>
            </div>
        </div>
        <div class="footer-links">
            <section>
                <h3>Giới thiệu</h3>
                <ul>
                    <li><a href="/tong-quan">Tổng quan</a></li>
                    <li><a href="/su-mang">Sứ mạng</a></li>
                </ul>
            </section>
        </div>
        <p class="copyright">© 2026 <a href="/">Đại học Thủy lợi</a></p>
    </div>
</footer>
```

#### 6. CSS layout minh họa

```css
.footer-top {
    display: grid;
    grid-template-columns: 1fr;          /* mobile */
    gap: 32px 48px;
}
@media (min-width: 640px) {
    .footer-top { grid-template-columns: 1fr 1fr; }
}
@media (min-width: 1120px) {
    .footer-top {
        grid-template-columns: 1.35fr 1fr 1fr 1fr;
        align-items: center;
    }
}
.footer-contact {
    display: flex;
    align-items: start;   /* icon bám dòng đầu */
    gap: 8px;
    min-width: 0;
}
.footer-links {
    display: grid;
    grid-template-columns: 1fr;
    gap: 48px;
}
@media (min-width: 640px) {
    .footer-links { grid-template-columns: 1fr 1fr; }
}
@media (min-width: 1120px) {
    .footer-links { grid-template-columns: repeat(4, 1fr); }
}
.footer-links ul { list-style: none; }
.footer-links li + li { margin-top: 16px; }
```

#### 7. Kết luận tư duy

- **Layout:** `footer → top(1.35:1:1:1) + links(4) + copyright`
- **CSS:** `Grid tỷ lệ lẻ · contact Flex row · ul Flow`
- **HTML:** `footer · address · section>h3+ul · p`

**Bằng chứng:**

- ✓ Đã xác minh từ DOM: grid lg:grid-cols-[1.35fr_1fr_1fr_1fr]
- ✓ Đã xác minh từ DOM: contact flex items-start gap-2
- ✓ Đã xác minh từ DOM: footer-links grid sm:2 lg:4
- ✓ Đã xác minh từ DOM: section > h3 + ul space-y-4

---


---

## 03. Bản đồ Flexbox toàn trang (Bước 3)

```text
PAGE
├── HEADER                        [BLOCK + STICKY]
│   ├── header-inner              [FLEX row · justify-between]
│   │   ├── brand (a>img)         (flex-none)
│   │   └── header-actions        [FLEX row · gap 12]
│   └── nav-inner                 [FLEX row]
│       └── nav-list (ul)         [FLEX row · items-stretch]
│           └── nav-item (li)     [FLEX · flex-auto]  ← nested
│               └── nav-link (a)  [FLEX · center · gap 6] ← nested
├── mobile-drawer (aside)         [FLEX column · fixed]
│   ├── drawer-head               [FLEX row · justify-between]
│   └── drawer-nav                (flex-1 · overflow-y-auto)
├── MAIN
│   ├── hero .swiper-wrapper      [FLEX row · JS điều khiển translateX]
│   ├── news-headlines (ul)       [BLOCK — li xếp dọc tự nhiên]
│   ├── event-track               [FLEX row · slider]
│   │   └── event-date (time)     [FLEX column · absolute]
│   ├── event-dots                [FLEX row · justify-center]
│   ├── stat (div trong dl)       [FLEX column · items-center]
│   ├── footer-contact            [FLEX row · items-start · gap 8]
│   └── social-rail (nav)         [FLEX column · fixed · gap 8]
└── FOOTER
    ├── footer-top                [GRID 1.35fr 1fr 1fr 1fr]
    └── footer-links              [GRID 4 cột]
        └── ul > li > a           [BLOCK + space-y]
```

**Đọc bản đồ:**

- **Nested Flexbox:** `nav-list → nav-item → nav-link` là 3 tầng flex lồng nhau. Tầng ngoài chia đều mục menu (`flex-auto`), tầng trong căn chữ + icon mũi tên vào giữa. Mẫu "flex lồng flex" kinh điển của mọi thanh menu.
- **Flex ratio:** trang gần như không dùng `flex: 2 / flex: 1` — tỷ lệ 2:1 của khối tin làm bằng **Grid** (`grid-cols-[2fr_1fr]`, `col-span-2/3`). Bài học: tỷ lệ cột cố định theo hàng → Grid thường gọn hơn Flex.
- **flex-wrap:** không xuất hiện ở bố cục chính! Các hàng card không wrap mà chuyển thành **slider ngang** (Sự kiện, Đối tác) hoặc đổi số cột Grid ở breakpoint.
- **Flex column bị lãng quên:** drawer, stat, social-rail, badge ngày đều là flex **dọc**. Flexbox không chỉ để xếp ngang.
- **Nơi KHÔNG cần Flex:** danh sách headlines (ul), cột link footer, đoạn bản quyền, các section xếp dọc — tất cả chỉ là **Normal Flow**. Thêm flex vào đó là thừa.
- **Flex "đặc biệt":** `.swiper-wrapper` là flex row nhưng slide không co giãn (`flex-shrink: 0`) và bị đẩy bằng `transform` do JS điều khiển — flex chỉ giữ các slide trên một hàng.

---

## 04. Bản đồ Grid — Flex vs Grid (Bước 4)

| Vùng | grid-template-columns (trang thật) | Vì sao Grid, không phải Flex? |
|---|---|---|
| Khối Tin Tức | lg: 3 cột + `col-span-2` · trong: `2fr 1fr` | Cột trái chiếm đúng 2/3 hàng — Grid khai báo tỷ lệ bằng `fr` trực tiếp, không cần tính % hay flex-basis. |
| Con số ấn tượng | 2 cột → 5 cột (≥850px) | 5 ô đều + ô đầu span 2 trên mobile. Flex phải tự tính width và xử lý hàng thừa; Grid `col-span-2` một dòng là xong. |
| Tuyển sinh | md: 2 cột · `items-center` | Hai cột ảnh–chữ cân giữa theo chiều dọc: `align-items: center` của Grid áp cho cả hàng. |
| Đào tạo / SV / Cựu SV | md: `repeat(3, 1fr)` | 3 mạch đọc dọc song song — Grid dựng khung, trong mỗi cột lại là Normal Flow. |
| HTQT / Nghiên cứu / Cộng đồng | `repeat(3, minmax(0,1fr)) minmax(230px,1fr)` | 4 cột, cột cuối có sàn 230px — `minmax()` là đặc sản Grid, Flex không diễn đạt gọn được. |
| Khoảnh khắc TLU | 3 → 4 → 5 cột · `auto-rows` 120→160px | Mosaic ô span 3×2, đổi vị trí theo breakpoint (`col-start-2`). Lãnh địa tuyệt đối của Grid. |
| Footer | `1.35fr 1fr 1fr 1fr` · rồi 4 cột | Cột logo rộng hơn 35%: tỷ lệ lẻ chỉ cần một giá trị fr. |

**Quy tắc bỏ túi:**

- Một chiều, **nội dung quyết định kích thước** (menu, hàng icon, badge + chữ) → **Flexbox**.
- Hai chiều, **khung quyết định kích thước** (lưới card, mosaic, cột tỷ lệ) → **Grid**.
- Card gallery 3×N: Flex vẫn làm được (wrap + width 33%) nhưng phải trừ gap thủ công, hàng cuối dễ lệch. Grid `repeat(3, 1fr)` không bao giờ lệch.
- Trang thật chứng minh: **Grid dựng khung lớn, Flex căn chỉnh bên trong** — hai công cụ bổ trợ, không loại trừ nhau.

```css
/* Lưới card 3 + 1 của trang thật */
.card-grid {
    display: grid;
    grid-template-columns:
        repeat(3, minmax(0, 1fr))
        minmax(230px, 1fr);
    gap: 20px;
}

/* minmax(0, 1fr) khác 1fr ở chỗ nào?
   1fr = minmax(AUTO, 1fr): cột bị nội dung dài
   (ảnh, chữ không ngắt) đẩy phình ra.
   minmax(0, 1fr): cho phép cột co về 0,
   nội dung phải tự cắt (line-clamp). */
```

---

## 05. Responsive — hành vi từng layout (Bước 5)

Breakpoint thật của trang: `640 / 768 / 850 / 992 / 1025 / 1120px` (Tailwind sm, md, lg + mốc tùy chỉnh).

| Layout | Desktop | Tablet | Mobile | Cơ chế |
|---|---|---|---|---|
| Navigation | Thanh ngang 8 mục + dropdown | Ẩn (<1025px) → hamburger mở drawer trái | Như tablet | `hidden min-[1025px]:block` + aside translateX |
| Khối Tin Tức | A A \| B (2:1) | Xếp dọc | Xếp dọc | `grid lg:grid-cols-3` → 1 cột |
| Con số | 5 ô một hàng | 2 cột, ô đầu span 2 | 2 cột, ô đầu span 2 | `grid-cols-2 min-[850px]:grid-cols-5` |
| Tuyển sinh | Ảnh \| Chữ | Ảnh \| Chữ (≥768) | Ảnh trên chữ dưới | `grid md:grid-cols-2` |
| Card HTQT… | 4 cột (3 card + 1 CTA) | 4 cột (≥768) | Ẩn grid, hiện bản slider | `hidden md:grid` + `md:hidden` (2 bản DOM) |
| Mosaic ảnh | 5 cột · hàng 160px | 4 cột · 140px | 3 cột · 120px | `grid-cols-3/4/5` + `auto-rows` + span đổi vị trí |
| Footer | 1.35:1:1:1 rồi 4 cột link | 2 cột | 1 cột dọc | `grid sm:grid-cols-2 lg:grid-cols-4` |

**Hành vi minh họa:**

```text
Desktop:  A | B | C | D      Tablet:  A | B      Mobile:  A
                                    C | D               B
                                                        C
                                                        D
```

> **Chiến lược đáng chú ý:** với dải card ngang, trang KHÔNG dùng `flex-wrap: wrap` mà render **hai phiên bản DOM** — grid cho ≥768px và slider cho mobile — rồi bật/tắt bằng `hidden md:grid` / `md:hidden`. Đổi trải nghiệm (lưới → vuốt) chứ không chỉ đổi số cột.

---

## 06. Cây DOM giả lập toàn trang (Bước 6)

### 6.1. DOM chỉ dùng div (để học khoanh hộp)

```text
div.page
├── div.header                          ← sticky
│   ├── div.header-inner                ← container 1400 · flex
│   │   ├── div.brand > img
│   │   └── div.header-actions
│   └── div.nav > div.nav-inner
│       └── div.nav-list
│           └── div.nav-item ×8
│               ├── div.nav-link
│               └── div.dropdown > div.nav-sub-item
├── div.main
│   ├── div.hero > div.hero-track > div.hero-slide ×7
│   ├── div.news-section
│   │   └── div.container > div.news-grid
│   │       ├── div.news-main
│   │       │   ├── div.section-title
│   │       │   └── div.news-main-grid
│   │       │       ├── div.news-featured
│   │       │       └── div.news-side-slider
│   │       └── div.news-aside
│   │           ├── div.section-title
│   │           └── div.headline-list > div.headline-item
│   ├── div.event-section
│   │   └── div.container
│   │       ├── div.section-title
│   │       └── div.event-slider
│   │           ├── div.event-track
│   │           │   └── div.event-card
│   │           │       ├── div.event-thumb (img + div.event-date)
│   │           │       └── div.event-body > div.event-title
│   │           └── div.event-dots
│   ├── div.stats-section
│   │   └── div.container > div.stats-grid
│   │       └── div.stat (div.stat-label + div.stat-value) ×5
│   ├── div.admission-section
│   │   └── div.admission-grid
│   │       ├── div.admission-media > img
│   │       └── div.admission-body (title + text + links + cta)
│   ├── div.edu-section > div.edu-grid
│   │   └── div.edu-col ×3 (title + div.edu-card)
│   ├── div.units-section (list dọc + panel chi tiết)
│   ├── div.article-section ×3            ← HTQT / Nghiên cứu / Cộng đồng
│   │   └── div.container
│   │       ├── div.section-title
│   │       └── div.card-grid
│   │           ├── div.card ×3 (thumb + body: cat, title, date)
│   │           └── div.card-cta
│   ├── div.partner-section (title + text + logo-slider)
│   └── div.moments-section
│       └── div.container > div.mosaic
│           └── div.tile ×15 (1 tile lớn span 3×2)
├── div.social-rail                      ← fixed
└── div.footer
    └── div.container
        ├── div.footer-top (brand + contact ×3)
        ├── div.footer-links > div.footer-col ×4
        └── div.copyright
```

### 6.2. DOM semantic HTML (chuẩn)

```text
body
├── header                               ← sticky top-0
│   ├── div.header-inner
│   │   ├── a.brand > img[alt="Đại học Thủy lợi"]
│   │   └── div.header-actions
│   │       ├── button.lang-switch       ← đổi ngôn ngữ = hành vi
│   │       └── button.menu-toggle       ← mở drawer = hành vi
│   └── nav[aria-label="Điều hướng chính"]
│       └── ul.nav-list
│           └── li.nav-item ×8
│               ├── a > span + svg
│               └── div.dropdown > ul > li > a
├── aside#mobile-nav[role="dialog"]      ← drawer
│   ├── div (span + button[aria-label="Đóng menu"])
│   ├── form[role="search"] > input + button
│   └── nav > ul > li > a
├── main
│   ├── section[aria-roledescription="carousel"]   ← hero
│   │   └── div.swiper-wrapper
│   │       └── div.swiper-slide[role="group"] ×7
│   │           └── picture > img
│   ├── section                          ← Tin Tức
│   │   └── div.news-grid
│   │       ├── div.news-main
│   │       │   ├── h2 > a "Tin Tức"
│   │       │   ├── article.news-featured (img, h3>a, p, time)
│   │       │   └── div.news-side-slider > article
│   │       └── div.news-aside
│   │           ├── h2
│   │           └── ul > li > (a + time)
│   ├── section[aria-label="Sự Kiện"]
│   │   ├── h2 > a
│   │   └── div.event-slider
│   │       ├── article.event-card
│   │       │   ├── a.event-thumb > img + time.event-date
│   │       │   └── div > h3 > a
│   │       └── div > button[aria-label="Hiện trang sự kiện N"]
│   ├── section#stats
│   │   ├── h2
│   │   └── dl.stats-grid
│   │       └── div.stat > dt + dd          ← cặp nhãn–giá trị!
│   ├── section[aria-label="Tuyển Sinh"]
│   │   ├── div.admission-media > img
│   │   └── div (h2, p, ul>li>a, a.cta)
│   ├── section                          ← Đào tạo / SV / Cựu SV
│   │   └── div.edu-col ×3
│   │       ├── h2 > a
│   │       └── article (a>img, a.title, p)
│   ├── section[aria-label="Các đơn vị đào tạo, KHCN"]
│   │   ├── h2 + danh sách đơn vị (swiper dọc)
│   │   └── div.panel (p mô tả + a "Khám phá")
│   ├── section ×3                       ← HTQT / Nghiên cứu / Cộng đồng
│   │   ├── h2
│   │   └── div.card-grid
│   │       ├── article.card ×3
│   │       │   ├── a > img
│   │       │   └── div > p.cat + h3>a + time
│   │       └── div.card-cta
│   ├── section[aria-label="Mạng Lưới Đối Tác"]
│   │   ├── h2 + p
│   │   └── div.swiper (logo đối tác)
│   └── section[aria-label="Khoảnh khắc TLU"]
│       ├── h2
│       └── div.mosaic
│           └── button.tile[aria-label="…"] ×15   ← mở lightbox
├── nav[aria-label="Mạng xã hội"]        ← fixed rail
│   └── a ×N > svg
└── footer
    ├── div.footer-top
    │   ├── div > img (logo)
    │   └── div.footer-contact ×3 (svg + address)
    ├── div.footer-links
    │   └── section ×4 > h3 + ul > li > a
    └── p.copyright > a
```

> **So sánh then chốt:** `div.stat-label + div.stat-value` trở thành `dt + dd` trong `dl`; `div.headline-item` thành `li`; `div.tile` là `button` vì bấm mở lightbox (hành vi tại chỗ), còn `div.card` là `article` chứa link điều hướng. **Bố cục không đổi — chỉ danh phận thẻ thay đổi.**

---

## 07. Bảng tổng hợp Component × Layout × CSS × HTML (Bước 7)

| Component | Layout Box | Layout Technique | Flex Container? | Flex Items | Nội dung | HTML semantic |
|---|---|---|---|---|---|---|
| Header brand bar | `header-inner` | Flex + Sticky | Có | `brand, header-actions` | Logo, nút ngôn ngữ, nút menu | `header, a>img, button` |
| Navigation | `nav-list` | Flex + Position | Có (3 tầng) | `nav-item ×8, nav-link` | Menu 8 mục + dropdown | `nav, ul, li, a, svg` |
| Mobile drawer | `mobile-drawer` | Fixed + Flex col | Có | `head, search, nav` | Menu, tìm kiếm, nút đóng | `aside[dialog], form, nav, button` |
| Hero carousel | `hero-track` | Flex + Position | Có | `hero-slide ×7` | 7 banner, dots | `section[carousel], picture, img, button` |
| Khối Tin Tức | `news-grid` | Grid lồng Grid | Không | `—` | Tin nổi bật, headlines | `section, article, ul/li, a, time` |
| Event card | `event-card` | Flex + Position | Có (badge) | `date-day, date-month` | Ảnh, badge ngày, tiêu đề | `article, a>img, time, h3>a` |
| Stats | `stats-grid` | Grid + Flex col | Có (trong ô) | `dt, dd` | 5 cặp nhãn–giá trị | `dl, dt, dd` |
| Tuyển sinh | `admission-grid` | Grid 2 cột | Không | `—` | Ảnh 4:3, mô tả, links | `section, img, h2, p, ul/li/a` |
| Đào tạo 3 cột | `edu-grid` | Grid 3 cột | Không | `—` | 3 mạch tin dọc | `h2>a, article, a>img, p` |
| Các đơn vị | `units-layout` | Swiper dọc + Grid | Có (dọc) | `unit-item` | Danh sách + panel | `h2, p, a` |
| Article card 3+1 | `card-grid` | Grid minmax | Không | `—` | 3 card + cột CTA | `article, a>img, p, h3>a, time` |
| Đối tác | `partner-slider` | Flex (virtual) | Có | `logo-slide` | Tiêu đề, dẫn, logo | `h2, p, img` |
| Mosaic ảnh | `mosaic` | Grid span | Không | `—` | 15 ô ảnh, 1 ô lớn | `button[aria-label] > img` |
| Social rail | `social-rail` | Fixed + Flex col | Có | `social-link ×N` | Icon mạng xã hội | `nav, a, svg` |
| Footer | `footer-top` | Grid + Flex | Có (contact) | `icon, address` | Logo, liên hệ, sitemap | `footer, address, section, h3, ul, p` |

---

## 08. Cây quyết định dành cho sinh viên (Bước 8)

```text
Nhìn thấy một vùng giao diện
        ↓
Khoanh hộp — vẽ viền tưởng tượng quanh vùng
        ↓
Đặt tên theo VAI TRÒ (news-card, không phải box1)
        ↓
Xác định cha – con: hộp nào chứa hộp nào?
        ↓
Các con cần đứng NGANG trên một hàng?
        ├── Có → FLEXBOX
        │        (container: display:flex · items: các con;
        │         chọn direction / justify / align / gap)
        └── Không
             ↓
        Có cấu trúc HÀNG × CỘT hoặc tỷ lệ cột?
             ├── Có → GRID (template-columns + gap · span cho ô lớn)
             └── Không → NORMAL FLOW (div block xếp dọc, không cần flex/grid!)
        ↓
Có phần tử đè lên phần tử khác (badge, dropdown, rail)?
        ├── Có → POSITION (cha relative làm mốc · con absolute bám vào)
        └── Không → giữ nguyên flow
        ↓
Xác định kiểu nội dung: tiêu đề? văn bản? link? nút? ảnh? ngày? danh sách?
        ↓
Chọn HTML semantic:
h1–h6 · p · a · button · img · ul/li · nav · section · article
time · figure/figcaption · dl/dt/dd · address · form/input
```

> **Phân biệt link và button — câu hỏi viva kinh điển:** `a` đưa người dùng **đi nơi khác** (URL khác, trang tin, anchor). `button` thực hiện **hành vi tại chỗ** (mở drawer, đổi slide, mở lightbox, đổi ngôn ngữ). Trang TLU làm đúng: tile ảnh là `button`, dot slider là `button`, tiêu đề tin là `a`. Hỏi: *"bấm vào thì ĐI ĐÂU hay LÀM GÌ?"*

---

## 09. Nguyên tắc đặt tên class (Bước 9)

**✓ Nên — theo vai trò:** `header`, `nav-list`, `nav-item`, `hero`, `news-grid`, `news-featured`, `news-aside`, `event-card`, `event-date`, `stats-grid`, `stat`, `card-grid`, `card-thumb`, `card-body`, `mosaic`, `tile`, `footer-links`, `footer-contact`.

**✗ Tránh — theo hình dáng / vô nghĩa:** `box1`, `box2`, `blue-box`, `left-area`, `big-box`, `div-to`, `hang-ngang`, `cot-phai`. Vì sao tệ? Đổi màu là tên sai; responsive đảo cột là tên sai; người đọc không biết hộp đó **làm gì**.

**Hệ thống nhất quán đề xuất** (trang thật dùng Tailwind utility không có class ngữ nghĩa; khi viết lại để học, theo pattern `khối` → `khối-thành phần`):

```text
news-grid          ← khung lưới của khối tin
news-featured      ← bài đinh bên trái
news-aside         ← cột headlines bên phải
headline-item      ← một dòng tin trong aside

event-card         ← khối = tên card
event-thumb        ← thành phần: vùng ảnh
event-date         ← thành phần: badge ngày
event-title        ← thành phần: tiêu đề

card-grid / card / card-thumb / card-body / card-cat / card-title
→ một họ "card" dùng chung cho HTQT, Nghiên cứu, Cộng đồng, Đào tạo
```

> **Quan sát:** trang thật lặp lại đúng một cấu trúc card ở 4 section khác nhau (thumb 16:9 + category + title + time). Thấy pattern lặp → đặt **một họ tên chung**: đó chính là tư duy component.

---

## 10. Các lỗi tư duy phổ biến cần tránh (Bước 10)

1. **Div soup — mọi thứ đều là div.** Dùng `div` cho menu, tiêu đề, ngày tháng, nút → mất ngữ nghĩa. *Trang thật:* nav/ul/li/a cho menu, dl/dt/dd cho chỉ số, time cho ngày, button cho dot slider. Div chỉ còn là hộp bố cục vô danh.
2. **Flex hóa mọi thứ.** Thấy hộp là thêm `display:flex`, kể cả danh sách xếp dọc vốn đúng Normal Flow. *Trang thật:* ul headlines, cột link footer, đoạn copyright đều là block thường.
3. **Dùng absolute để dàn bố cục chính.** Đặt tọa độ px cho từng card → vỡ khi đổi màn hình/nội dung. *Trang thật:* absolute chỉ cho phần tử **đè** (badge ngày, dropdown, pagination, rail); khung chính 100% Flex/Grid/Flow.
4. **Quên min-w-0 / minmax(0,1fr).** Chữ dài/ảnh lớn đẩy cột phình, tràn ngang. *Trang thật:* mọi cột `min-w-0`, mọi track `minmax(0,1fr)`, tiêu đề `line-clamp-2/3` — bộ ba chống tràn phải đi cùng nhau.
5. **Nhầm link với button.** `a href="#"` cho nút mở menu, hoặc `button` cho tiêu đề tin cần điều hướng → sai ngữ nghĩa, keyboard, SEO. *Trang thật:* tile Khoảnh khắc là button (lightbox tại chỗ), hamburger là button, tiêu đề tin là a (đi trang khác).
6. **Chiều cao cố định cho hộp chữ.** `height: 200px` cho card tin → chữ tràn khi tiêu đề dài. *Trang thật:* chỉ khóa tỷ lệ ẢNH (`aspect-video`, `aspect-[4/3]`) và chiều cao hàng mosaic; hộp chữ cao tự do + line-clamp số dòng.
7. **Phân tích CSS trang trí thay vì bố cục.** Sa đà màu sắc, shadow, font mà không trả lời được hộp nào chứa hộp nào, flex container ở đâu. *Kỷ luật 3 lớp:* Layout Box → CSS Layout → Content + HTML trước, trang trí sau.
8. **Suy diễn quá mức từ ảnh chụp.** Khẳng định chắc nịch một vùng là slider/dropdown khi chỉ nhìn ảnh tĩnh. *Trong bài này:* hero, slider sự kiện, dropdown xác minh từ DOM (`swiper-initialized`, `group-hover`); chỗ chưa chắc đều gắn nhãn "? suy luận — cần kiểm tra interaction".

---

*Kết thúc bài phân tích · 15 component · 10 chương · đủ 15 đầu mục của master prompt 3 lớp.*