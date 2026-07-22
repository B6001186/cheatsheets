# CSS Cheatsheet

## ไวยากรณ์พื้นฐาน (Syntax)

```css
/* เลือก element ทั้งหมด */
* { box-sizing: border-box; }

/* เลือก element ตาม tag */
p { color: #333; }

/* เลือกตาม class */
.highlight { background: yellow; }

/* เลือกตาม id */
#header { font-size: 24px; }

/* เลือกหลายตัวพร้อมกัน */
h1, h2, h3 { margin: 0; }

/* เลือกลูกภายใน (descendant) */
div p { color: blue; }

/* เลือกลูกโดยตรง (child) */
div > p { color: red; }

/* เลือกพี่น้องถัดไป (adjacent sibling) */
h2 + p { margin-top: 0; }

/* เลือกพี่น้องทั้งหมด (general sibling) */
h2 ~ p { color: gray; }

/* เลือก attribute */
a[target="_blank"] { color: red; }
input[type="text"] { border: 1px solid #ccc; }
```

## สิ่งที่เลือกได้ (Selectors)

```css
/* ID Selector */
#main { }

/* Class Selector */
.card { }

/* Element Selector */
button { }

/* Universal Selector */
* { }

/* Attribute Selectors */
[title] { }                    /* มี attribute title */
[href="https://example.com"] { } /* ตรงกับค่า */
[href^="https"] { }           /* ขึ้นต้นด้วย */
[href$=".pdf"] { }            /* ลงท้ายด้วย */
[href*="example"] { }         /* มีคำว่า example */
[href~="btn"] { }             /* มีคำว่า btn อยู่ใน list */
[href|="en"] { }              /* เริ่มด้วย en หรือ en- */
```

## ลำดับความสำคัญ (Specificity & Cascade)

```css
/* ลำดับจากน้อยไปมาก */
/* inline > id > class/attribute/pseudo-class > element/pseudo-element */

div { color: red; }                 /* specificity: 0-0-1 */
.box { color: blue; }               /* specificity: 0-1-0 */
#main { color: green; }             /* specificity: 1-0-0 */
div.box#main { color: purple; }     /* specificity: 1-1-1 */

/* !important บังคับให้ใช้ค่านี้ */
.urgent { color: red !important; }

/* :is() - ใช้ specificity ของตัวที่สูงสุด */
:is(h1, h2, h3) { margin: 0; }

/* :where() - ไม่เพิ่ม specificity */
:where(h1, h2, h3) { margin: 0; }

/* :not() - เลือกที่ไม่ตรงเงื่อนไข */
:not(.hidden) { display: block; }
```

## โมเดลบ็อก (Box Model)

```css
/* box-sizing */
* { box-sizing: border-box; }     /* padding/border รวมใน width */

/* ขนาดกล่อง */
.box {
  width: 200px;
  height: 100px;
  min-width: 100px;
  max-width: 500px;
  min-height: 50px;
  max-height: 300px;
}

/* margin */
.box { margin: 10px; }                  /* ทุกด้าน 10px */
.box { margin: 10px 20px; }             /* บนล่าง 10px ซ้ายขวา 20px */
.box { margin: 10px 20px 30px; }        /* บน 10px ซ้ายขวา 20px ล่าง 30px */
.box { margin: 10px 20px 30px 40px; }   /* บน ขวา ล่าง ซ้าย */
.box { margin-top: 10px; }

/* padding */
.box { padding: 10px; }
.box { padding: 10px 20px; }
.box { padding: 10px 20px 30px; }
.box { padding: 10px 20px 30px 40px; }
.box { padding-left: 15px; }

/* margin collapse - margin ของแม่กับลูกจะรวมกัน */
.parent { margin-bottom: 20px; }
.child { margin-top: 30px; }    /* ได้ 30px ไม่ใช่ 50px */

/* ป้องกัน margin collapse */
.parent { padding-top: 1px; }
/* หรือใช้ overflow: auto; */
```

## ตำแหน่ง (Position)

```css
/* position: static (ค่าเริ่มต้น) */
.static { position: static; }

/* position: relative - เลื่อนจากตำแหน่งเดิม */
.relative {
  position: relative;
  top: 10px;       /* เลื่อนลง 10px */
  left: 20px;      /* เลื่อนขวา 20px */
}

/* position: absolute - ออกจาก document flow */
.absolute {
  position: absolute;
  top: 0;
  right: 0;
  z-index: 100;
}

/* position: fixed - ตรึงอยู่กับ viewport */
.fixed {
  position: fixed;
  bottom: 20px;
  right: 20px;
}

/* position: sticky - relative จนถึงจุดนึง แล้วเป็น fixed */
.sticky {
  position: sticky;
  top: 0;
}

/* z-index (ใช้กับ position ที่ไม่ใช่ static) */
.layer { z-index: 10; }
```

## Flexbox

```css
/* Container */
.container {
  display: flex;
  flex-direction: row;            /* row | row-reverse | column | column-reverse */
  flex-wrap: wrap;                /* nowrap | wrap | wrap-reverse */
  justify-content: center;        /* flex-start | flex-end | center | space-between | space-around | space-evenly */
  align-items: center;            /* flex-start | flex-end | center | stretch | baseline */
  align-content: center;          /* จัด alignment ของหลายแถว */
  gap: 10px;                      /* ระยะห่างระหว่าง item */
  row-gap: 10px;
  column-gap: 20px;
}

/* Item */
.item {
  flex-grow: 1;                   /* สัดส่วนเติมพื้นที่ */
  flex-shrink: 0;                 /* สัดส่วนหดตัว */
  flex-basis: 200px;              /* ขนาดเริ่มต้น */
  flex: 1;                        /* shorthand: grow shrink basis */
  flex: 0 0 200px;                /* ไม่ grow ไม่ shrink basis=200px */

  align-self: flex-start;         /* จัด alignment ตัวเอง */
  order: -1;                      /* ลำดับการแสดงผล */

  margin: 5px;                    /* margin auto ใช้จัดตำแหน่งได้ */
}

/* Flex Shorthand */
.item { flex: 1; }                /* grow:1 shrink:1 basis:0 */
.item { flex: 1 1 auto; }
.item { flex: 0 0 250px; }
.item { flex: none; }             /* ไม่ grow ไม่ shrink */

/* Centering */
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

## CSS Grid

```css
/* Container */
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;           /* 3 คอลัมน์เท่ากัน */
  grid-template-columns: 200px 1fr 1fr;          /* คอลัมน์แรก 200px */
  grid-template-columns: repeat(3, 1fr);          /* 3 คอลัมน์เท่ากัน */
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); /* responsive */
  grid-template-rows: 100px auto 100px;
  gap: 10px;                                      /* ระยะห่าง */
  row-gap: 10px;
  column-gap: 20px;
}

/* Item */
.item {
  grid-column: 1 / 3;               /* ข้าม 2 คอลัมน์ */
  grid-column: span 2;              /* ข้าม 2 คอลัมน์ */
  grid-row: 1 / 3;                  /* ข้าม 2 แถว */
  grid-area: 1 / 1 / 3 / 3;        /* row-start / col-start / row-end / col-end */

  /* Grid Area แบบตั้งชื่อ */
  /* grid-area: header; */
  justify-self: center;             /* จัดแนวนอน */
  align-self: center;               /* จัดแนวตั้ง */
  place-self: center;               /* shorthand */
}

/* Named Grid Areas */
.page {
  display: grid;
  grid-template-areas:
    "header header header"
    "sidebar main main"
    "footer footer footer";
  grid-template-columns: 200px 1fr 1fr;
  grid-template-rows: 60px 1fr 40px;
}
.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
.footer { grid-area: footer; }
```

## Display

```css
/* Block - ขึ้นบรรทัดใหม่ กว้างเต็มพื้นที่ */
div { display: block; }

/* Inline - อยู่ในบรรทัดเดียวกัน ขนาดตามเนื้อหา */
span { display: inline; }

/* Inline-block - inline แต่ตั้ง width/height ได้ */
.badge {
  display: inline-block;
  width: 100px;
  height: 30px;
}

/* None - ซ่อนและลบออกจาก document flow */
.hidden { display: none; }

/* Grid */
.grid-item { display: grid; }

/* Flexbox */
.flex-item { display: flex; }
```

## Visibility vs Display

```css
/* display:none - ซ่อนและลบพื้นที่ */
.hidden { display: none; }

/* visibility:hidden - ซ่อนแต่ยังกินพื้นที่ */
.invisible { visibility: hidden; }

/* visibility:collapse - สำหรับ table row/column */
.collapsed { visibility: collapse; }
```

## สี (Colors)

```css
/* ชื่อสี */
p { color: red; }

/* HEX */
p { color: #ff0000; }
p { color: #f00; }              /* shorthand */

/* RGB */
p { color: rgb(255, 0, 0); }
p { color: rgb(255 0 0); }      /* modern syntax */

/* RGBA (มี opacity) */
p { color: rgba(255, 0, 0, 0.5); }
p { color: rgb(255 0 0 / 50%); }

/* HSL */
p { color: hsl(0, 100%, 50%); }
p { color: hsl(0 100% 50%); }

/* HSLA */
p { color: hsla(0, 100%, 50%, 0.5); }

/* Modern CSS Color Functions */
p { color: oklch(0.7 0.15 30); }
p { color: color(display-p3 1 0 0); }
```

## ตัวอักษรและข้อความ (Typography)

```css
body {
  font-family: 'Arial', sans-serif;
  font-size: 16px;
  font-weight: bold;              /* 100-900 | normal | bold */
  font-style: italic;
  line-height: 1.5;              /* ระยะห่างระหว่างบรรทัด */
  letter-spacing: 1px;           /* ระยะห่างระหว่างตัวอักษร */
  word-spacing: 2px;
  text-align: center;            /* left | right | center | justify */
  text-decoration: underline;
  text-decoration-color: red;
  text-decoration-style: dashed;
  text-transform: uppercase;     /* capitalize | uppercase | lowercase */
  text-indent: 20px;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
  white-space: nowrap;           /* pre | pre-wrap | pre-line | normal */
  word-break: break-all;         /* break-word | keep-all */
  overflow-wrap: break-word;
}

/* Font Sizes */
h1 { font-size: 2.5rem; }       /* -relative to root font size */
p  { font-size: 1rem; }
small { font-size: 0.875em; }   /* relative to parent */

/* Font Weight */
.light { font-weight: 300; }
.normal { font-weight: 400; }
.medium { font-weight: 500; }
.bold { font-weight: 700; }

/* Google Fonts */
/* @import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap'); */
```

## พื้นหลัง (Background)

```css
body {
  background-color: #f0f0f0;
  background-image: url('image.jpg');
  background-size: cover;              /* contain | cover | auto | width height */
  background-position: center;         /* top | bottom | left | right | center */
  background-repeat: no-repeat;        /* repeat | repeat-x | repeat-y | no-repeat */
  background-attachment: fixed;         /* scroll | fixed | local */
  background-origin: padding-box;       /* padding-box | border-box | content-box */
  background-clip: border-box;          /* padding-box | border-box | content-box */

  /* Gradient */
  background: linear-gradient(to right, #ff0000, #0000ff);
  background: linear-gradient(45deg, red, blue);
  background: radial-gradient(circle, red, blue);
  background: conic-gradient(red, orange, yellow, green, blue);
}

/* Multiple backgrounds */
.bg {
  background:
    url('overlay.png') center no-repeat,
    linear-gradient(to bottom, #fff, #000);
}
```

## เส้นขอบ (Border)

```css
.box {
  border: 1px solid #333;                  /* width style color */
  border-width: 1px;
  border-style: solid;                     /* solid | dashed | dotted | double | groove | ridge | inset | outset */
  border-color: #333;

  /* ด้านเดียว */
  border-top: 2px solid red;
  border-bottom: 1px dashed blue;

  /* ลดขนาดมุม */
  border-radius: 10px;                     /* ทุกด้าน */
  border-radius: 10px 20px;                /* ซ้ายบนขวาล่าง / ขวาบนซ้ายล่าง */
  border-radius: 10px 20px 30px;           /* ซ้ายบน / ขวาบน+ซ้ายล่าง / ขวาล่าง */
  border-radius: 10px 20px 30px 40px;      /* บนซ้าย บนขวา ล่างขวา ล่างซ้าย */
  border-radius: 50%;                      /* วงกลม */
}

/* Outline (ไม่กินพื้นที่ เหมาะกับ focus) */
button:focus {
  outline: 2px solid blue;
  outline-offset: 2px;
}
button:focus-visible { outline: 2px solid blue; }
```

## เงา (Shadow)

```css
/* Box Shadow */
.box {
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
  box-shadow: 2px 2px 5px rgba(0,0,0,0.3), -2px -2px 5px rgba(0,0,0,0.1);
  box-shadow: inset 2px 2px 5px rgba(0,0,0,0.3);   /* เงาด้านใน */
}

/* Text Shadow */
h1 {
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
}

/* Filter Shadow */
.shadow {
  filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.3));
}
```

## ขนาดและหน่วย (Units)

```css
/* Absolute Units */
.px { width: 100px; }
.pt { font-size: 12pt; }

/* Relative Units - ขึ้นอยู่กับ viewport */
.vw { width: 50vw; }        /* 50% ของ viewport width */
.vh { height: 100vh; }      /* 100% ของ viewport height */
.vmin { width: 50vmin; }    /* min(vw, vh) */
.vmax { height: 50vmax; }   /* max(vw, vh) */

/* Relative Units - ขึ้นอยู่กับ parent (font) */
.em { width: 2em; }         /* 2 เท่าของ font-size ตัวเอง */
.rem { font-size: 1.5rem; } /* 1.5 เท่าของ font-size root */

/* Relative Units - ขึ้นอยู่กับ container */
.percentage { width: 50%; }  /* 50% ของ parent width */

/* แนะนำ */
/* font-size: ใช้ rem */
/* width: ใช้ %, vw, หรือ max-width */
/* margin/padding: ใช้ em หรือ rem */
```

## Overflow

```css
.box {
  overflow: visible;           /* default - แสดง overflow */
  overflow: hidden;            /* ซ่อน overflow */
  overflow: scroll;            /* scrollbar เสมอ */
  overflow: auto;              /* scrollbar เมื่อ overflow */

  overflow-x: auto;            /* overflow แนวนอน */
  overflow-y: scroll;          /* overflow แนวตั้ง */

  text-overflow: ellipsis;     /* ตัดข้อความด้วย ... (ต้องใช้กับ overflow:hidden) */
  white-space: nowrap;
}
```

## Display Types

```css
/* Block */
div { display: block; }

/* Inline */
span { display: inline; }

/* Inline-block */
.badge { display: inline-block; width: 50px; height: 20px; }

/* Grid */
.grid { display: grid; }

/* Flex */
.flex { display: flex; }

/* ซ่อน */
.none { display: none; }
```

## Cursor

```css
.pointer { cursor: pointer; }
.default { cursor: default; }
.move { cursor: move; }
.text { cursor: text; }
.wait { cursor: wait; }
.not-allowed { cursor: not-allowed; }
.grab { cursor: grab; }
.zoom-in { cursor: zoom-in; }
.custom { cursor: url('custom.cur'), auto; }
```

## Transition และ Animation

```css
/* Transition */
.button {
  transition: all 0.3s ease;
  transition-property: background-color;
  transition-duration: 0.3s;
  transition-timing-function: ease;     /* ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier */
  transition-delay: 0.1s;
}

/* Animation */
@keyframes slideIn {
  from { transform: translateX(-100%); opacity: 0; }
  to { transform: translateX(0); opacity: 1; }
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}

.animated {
  animation-name: slideIn;
  animation-duration: 0.5s;
  animation-timing-function: ease-in-out;
  animation-delay: 0.2s;
  animation-iteration-count: infinite;  /* จำนวนครั้ง | infinite */
  animation-direction: alternate;        /* normal | reverse | alternate | alternate-reverse */
  animation-fill-mode: forwards;         /* none | forwards | backwards | both */
  animation-play-state: running;         /* running | paused */

  /* shorthand */
  animation: slideIn 0.5s ease-in-out 0.2s infinite alternate forwards;
}
```

## Transform

```css
.box {
  /* หมุน */
  transform: rotate(45deg);
  transform: rotateX(45deg);
  transform: rotateY(45deg);

  /* ขยาย/ลดขนาด */
  transform: scale(1.5);
  transform: scaleX(2);
  transform: scaleY(0.5);

  /* เลื่อนตำแหน่ง */
  transform: translate(100px, 50px);
  transform: translateX(100px);
  transform: translateY(50px);

  /* เอียง */
  transform: skew(10deg, 5deg);
  transform: skewX(10deg);

  /* หลาย transform */
  transform: rotate(45deg) scale(1.5) translateX(100px);

  /* จุดศูนย์กลาง */
  transform-origin: center;
  transform-origin: top left;
  transform-origin: 50% 50%;

  /* 3D */
  transform: perspective(500px) rotateY(45deg);
}
```

## Pseudo-Classes

```css
/* Link States */
a:link { color: blue; }           /* ยังไม่ได้คลิก */
a:visited { color: purple; }      /* คลิกแล้ว */
a:hover { color: red; }           /* เมาส์อยู่บน */
a:active { color: orange; }       /* กำลังคลิก */

/* Form States */
input:focus { border-color: blue; }
input:disabled { opacity: 0.5; }
input:enabled { }
input:checked { }
input:required { }
input:optional { }
input:valid { border-color: green; }
input:invalid { border-color: red; }
input:read-only { }
input:read-write { }
input:placeholder-shown { color: gray; }

/* Structure */
p:first-child { font-weight: bold; }
p:last-child { margin-bottom: 0; }
p:nth-child(2) { color: red; }        /* ลูกคนที่ 2 */
p:nth-child(odd) { background: #f5f5f5; }
p:nth-child(even) { background: #fff; }
p:nth-child(3n) { }                    /* ทุกๆ 3 ตัว */
p:not(.special) { }

/* Other */
li:first-of-type { }
li:last-of-type { }
li:nth-of-type(2) { }
:empty { display: none; }
:target { background: yellow; }       /* anchor ที่ตรงกับ hash */
:is(.a, .b, .c) { color: red; }
:where(.x, .y, .z) { margin: 0; }
:has(> img) { padding: 10px; }
:has(.highlight) { border: 1px solid blue; }

/* Logical Pseudo-classes */
:dir(rtl) { text-align: right; }
```

## Pseudo-Elements

```css
/* ::before และ ::after */
.quote::before {
  content: '\201C';                /* " */
  font-size: 2em;
  color: gray;
}
.quote::after {
  content: '\201D';                /* " */
}

/* เพิ่ม icon */
.icon::before {
  content: '\1F4E6';              /* emoji/package */
  margin-right: 5px;
}

/* ใส่ข้อความ */
.empty::after {
  content: '(ไม่มีข้อมูล)';
  color: gray;
  font-style: italic;
}

/* ตัวอักษรตัวแรก */
p::first-letter {
  font-size: 3em;
  float: left;
  line-height: 1;
}

/* เนื้อหาที่เลือก */
::selection {
  background: yellow;
  color: black;
}

/* Placeholder */
::placeholder {
  color: #999;
  font-style: italic;
}

/* บรรทัดแรก */
p::first-line {
  font-weight: bold;
}

/* Marker (List) */
li::marker {
  content: '-> ';
  color: red;
}

/* Highlight */
::highlight(search) {
  background: yellow;
}
```

## Responsive Design

```css
/* Media Queries */
/* mobile first */
@media (min-width: 768px) {
  .container { max-width: 720px; }
}
@media (min-width: 1024px) {
  .container { max-width: 960px; }
}
@media (min-width: 1200px) {
  .container { max-width: 1140px; }
}

/* Desktop first */
@media (max-width: 768px) {
  .sidebar { display: none; }
}

/* ขนาดหน้าจอทั่วไป */
/* mobile: < 768px */
/* tablet: 768px - 1023px */
/* desktop: 1024px - 1199px */
/* large desktop: >= 1200px */

/* Container Queries */
@container (min-width: 400px) {
  .card { display: flex; }
}

/* Container */
.card-container {
  container-type: inline-size;
  container-name: card;
}
```

## CSS Variables (Custom Properties)

```css
/* ประกาศ Variables */
:root {
  --primary: #3498db;
  --secondary: #2ecc71;
  --text-color: #333;
  --spacing: 16px;
  --border-radius: 8px;
  --font-main: 'Arial', sans-serif;
  --shadow: 0 2px 4px rgba(0,0,0,0.1);
  --transition: all 0.3s ease;
}

/* ใช้ Variables */
.button {
  background: var(--primary);
  color: white;
  padding: var(--spacing);
  border-radius: var(--border-radius);
  font-family: var(--font-main);
  box-shadow: var(--shadow);
  transition: var(--transition);
}

/* กำหนดค่าเริ่มต้น */
.element {
  color: var(--text-color, #333);
  font-size: var(--font-size, 16px);
}

/* ครอบคลุม (Scoped) */
.card {
  --card-bg: white;
  --card-padding: 20px;
  background: var(--card-bg);
  padding: var(--card-padding);
}

/* เปลี่ยนค่าใน theme ต่างๆ */
[data-theme="dark"] {
  --primary: #9b59b6;
  --text-color: #ecf0f1;
  --bg-color: #2c3e50;
}
```

## Filter

```css
.image {
  filter: blur(5px);                  /* เบลอ */
  filter: brightness(1.5);            /* ความสว่าง */
  filter: contrast(200%);             /* ความต่างของสี */
  filter: grayscale(100%);            /* ขาวดำ */
  filter: sepia(100%);                /* น้ำตาล */
  filter: saturate(200%);             /* ความอิ่มตัวของสี */
  filter: hue-rotate(90deg);          /* หมุนวงล้อสี */
  filter: invert(100%);               /* กลับสี */
  filter: opacity(50%);               /* ความโปร่งแสง */
  filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.3));

  /* หลาย filter */
  filter: brightness(1.2) contrast(1.1) saturate(1.3);
}

/* Backdrop Filter */
.glass {
  background: rgba(255,255,255,0.2);
  backdrop-filter: blur(10px);
}
```

## Clip-path

```css
.circle {
  clip-path: circle(50%);
}

.triangle {
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}

.star {
  clip-path: polygon(
    50% 0%,
    61% 35%,
    98% 35%,
    68% 57%,
    79% 91%,
    50% 70%,
    21% 91%,
    32% 57%,
    2% 35%,
    39% 35%
  );
}

.hexagon {
  clip-path: polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%);
}
```

## Aspect Ratio

```css
.video {
  aspect-ratio: 16 / 9;
}
.square {
  aspect-ratio: 1;
}
.portrait {
  aspect-ratio: 9 / 16;
}
```

## Scroll Behavior

```css
html {
  scroll-behavior: smooth;
}

/* Scroll Snap */
.container {
  scroll-snap-type: x mandatory;      /* x | y | both | none | mandatory | proximity */
}

.item {
  scroll-snap-align: start;           /* start | center | end */
}

/* Scroll Padding */
section {
  scroll-padding-top: 80px;           /* หัว固定 header */
}

/* Scroll Margin */
section {
  scroll-margin-top: 80px;
}
```

## Object Fit

```css
img {
  object-fit: cover;                  /* fill | contain | cover | none | scale-down */
  object-position: center;
}

video {
  object-fit: contain;
  width: 100%;
  height: 300px;
}
```

## ตาราง (Table)

```css
table {
  border-collapse: collapse;          /* collapse | separate */
  border-spacing: 10px;               /* ใช้กับ border-collapse: separate */
  width: 100%;
  table-layout: fixed;                /* fixed | auto */
}

th, td {
  border: 1px solid #ddd;
  padding: 8px 12px;
  text-align: left;
  vertical-align: middle;             /* top | middle | bottom */
}

tr:nth-child(even) { background-color: #f5f5f5; }
tr:hover { background-color: #e0e0e0; }

th { background-color: #4CAF50; color: white; }

/* Striped */
.table-striped tbody tr:nth-child(odd) {
  background-color: rgba(0,0,0,0.05);
}
```

## List Style

```css
ul { list-style: none; padding: 0; }

ul {
  list-style-type: disc;              /* disc | circle | square | none */
  list-style-position: inside;        /* inside | outside */
}

ol {
  list-style-type: decimal;           /* decimal | lower-alpha | upper-alpha | lower-roman | upper-roman */
}

/* Custom Marker */
li::marker {
  content: '•';
  color: red;
  font-size: 1.5em;
}
```

## Print Styles

```css
@media print {
  body { font-size: 12pt; }
  .no-print { display: none; }
  a { color: black; text-decoration: none; }
  a[href]::after { content: " (" attr(href) ")"; }
  @page { margin: 2cm; }
  @page :first { margin-top: 0; }
  @page :last { margin-bottom: 0; }
  .page-break { page-break-after: always; }
}
```

## ไอคอน (Icons)

```css
/* Font Awesome */
.icon {
  font-family: 'Font Awesome 6 Free';
  font-weight: 900;
  content: '\f007';           /* user icon */
}

/* Emoji */
.emoji::after {
  content: '🚀';
  font-size: 1.5em;
}

/* SVG Background */
.icon {
  width: 24px;
  height: 24px;
  background: url('icon.svg') no-repeat center;
  background-size: contain;
}
```

## Common Patterns

### Centering
```css
/* Flexbox Centering */
.center-flex {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Grid Centering */
.center-grid {
  display: grid;
  place-items: center;
}

/* Absolute Centering */
.center-absolute {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* Margin Auto Centering */
.center-margin {
  width: fit-content;
  margin: 0 auto;
}
```

### Card Layout
```css
.card {
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  overflow: hidden;
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
}

.card-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.card-body {
  padding: 16px;
}
```

### Sticky Footer
```css
/* Using Flexbox */
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
main { flex: 1; }

/* Using Grid */
body {
  display: grid;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}
```

### Sticky Header
```css
.header {
  position: sticky;
  top: 0;
  z-index: 100;
  background: white;
}
```

### Responsive Image
```css
img {
  max-width: 100%;
  height: auto;
}
```

### Truncate Text
```css
.truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  width: 200px;
}

.truncate-2-lines {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

### Smooth Transition
```css
* {
  transition: all 0.3s ease;
}
```

### Glass Morphism
```css
.glass {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.18);
  border-radius: 12px;
}
```

### Neumorphism
```css
.neumorphism {
  background: #e0e0e0;
  border-radius: 12px;
  box-shadow:
    8px 8px 16px #bebebe,
    -8px -8px 16px #ffffff;
}
```

### Gradient Text
```css
.gradient-text {
  background: linear-gradient(45deg, #ff0000, #0000ff);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

### Skeleton Loading
```css
.skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}

@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
```

## Useful Utilities

```css
/* Hide element */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}

/* Truncate */
.truncate { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }

/* Clearfix */
.clearfix::after {
  content: '';
  display: table;
  clear: both;
}

/* Disable Selection */
.no-select { user-select: none; }

/* Disable Pointer Events */
.no-events { pointer-events: none; }

/* Text Ellipsis */
.text-ellipsis {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

## หน่วยอ้างอิง (Reference Units)

| Unit | คำอธิบาย | ใช้กับ |
|------|---------|--------|
| `px` | พิกเซล (ตายตัว) | ทุกอย่าง |
| `em` | เท่าของ font-size ตัวเอง | font, width, margin |
| `rem` | เท่าของ font-size root | font, width, margin |
| `%` | เปอร์เซ็นต์ของ parent | width, height, margin |
| `vw` | viewport width | width |
| `vh` | viewport height | height |
| `vmin` | min(vw, vh) | width, height |
| `vmax` | max(vw, vh) | width, height |
| `ch` | ความกว้างตัวอักษร "0" | width |
| `ex` | ความสูงตัวอักษร "x" | height |

## Tips

```css
/* 1. ใช้ box-sizing: border-box เสมอ */
* { box-sizing: border-box; }

/* 2. ใช้ rem สำหรับ font-size */
html { font-size: 16px; }
h1 { font-size: 2rem; }

/* 3. ใช้ CSS Variables สำหรับ consistent design */
:root { --spacing-sm: 8px; --spacing-md: 16px; }

/* 4. Mobile-first approach */
.container { padding: 16px; }
@media (min-width: 768px) { .container { max-width: 720px; margin: auto; } }

/* 5. ใช้ gap แทน margin hack */
.flex { display: flex; gap: 16px; }
.grid { display: grid; gap: 16px; }

/* 6. ใช้ aspect-ratio แทน padding-bottom hack */
.video { aspect-ratio: 16/9; width: 100%; }

/* 7. ใช้ scroll-snap */
.gallery { scroll-snap-type: x mandatory; display: flex; overflow-x: auto; }
.gallery > * { scroll-snap-align: center; }

/* 8. ใช้ clamp() สำหรับ responsive font */
h1 { font-size: clamp(1.5rem, 4vw, 3rem); }

/* 9. ใช้ min() และ max() */
.container { width: min(100% - 32px, 1200px); margin: auto; }

/* 10. Modern Reset */
body { margin: 0; -webkit-font-smoothing: antialiased; }
img, video { max-width: 100%; display: block; }
```

## CSS Functions

```css
/* calc() - คำนวณค่า */
.width { width: calc(100% - 200px); }
.spacing { margin: calc(1rem + 10px); }

/* min(), max() */
.container { width: min(100% - 32px, 1200px); }
.font { font-size: max(16px, 1vw + 10px); }

/* clamp() - ค่าระหว่าง min, preferred, max */
.font { font-size: clamp(1rem, 2.5vw, 2rem); }
.padding { padding: clamp(1rem, 3vw, 3rem); }

/* var() - ใช้ CSS Variables */
.color { color: var(--primary); }

/* url() - เรียกใช้ไฟล์ภายนอก */
.bg { background: url('image.jpg'); }

/* attr() - ใช้ค่าจาก attribute */
.tooltip::after { content: attr(data-tooltip); }

/* counter() - นับลำดับ */
ol { counter-reset: my-counter; }
ol li { counter-increment: my-counter; }
ol li::before { content: counter(my-counter) ". "; }

/* linear-gradient(), radial-gradient() */
.gradient { background: linear-gradient(to right, red, blue); }

/* min-content, max-content, fit-content */
.container { width: fit-content; }
```

## ค้นหาเพิ่มเติม (More Resources)

```css
/* MDN Web Docs: https://developer.mozilla.org/en-US/docs/Web/CSS */
/* CSS-Tricks: https://css-tricks.com */
/* Can I Use: https://caniuse.com */
/* CSS Spec: https://www.w3.org/Style/CSS/ */
```
