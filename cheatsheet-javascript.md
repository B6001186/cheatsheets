# JavaScript Cheatsheet

## พื้นฐาน (Basics)

```javascript
// การประกาศตัวแปร
var name = 'John';           // function scope
let age = 25;                // block scope
const PI = 3.14;             // block scope, เปลี่ยนค่าไม่ได้

// ชนิดข้อมูล (Data Types)
typeof 'hello';              // "string"
typeof 42;                   // "number"
typeof true;                 // "boolean"
typeof undefined;            // "undefined"
typeof null;                 // "object"
typeof {};                   // "object"
typeof [];                   // "object"

// การแปลงชนิด (Type Conversion)
Number('123');               // 123
String(123);                 // "123"
Boolean(1);                  // true
parseInt('10.5');            // 10
parseFloat('10.5');          // 10.5

// Template Literals
const greeting = `Hello ${name}, age ${age}`;

// Ternary Operator
const status = age >= 18 ? 'Adult' : 'Minor';
```

## ฟังก์ชัน (Functions)

```javascript
// Function Declaration
function add(a, b) { return a + b; }

// Function Expression
const multiply = function(a, b) { return a * b; };

// Arrow Function
const subtract = (a, b) => a - b;
const square = x => x * x;
const noParam = () => console.log('Hi');

// Default Parameters
function greet(name = 'Guest') { return `Hi ${name}`; }

// Rest Parameters
function sum(...numbers) { return numbers.reduce((a, b) => a + b); }

// Spread Operator
const arr = [1, 2, 3];
const copy = [...arr];
const merged = [...arr, 4, 5];
const obj = { a: 1, b: 2 };
const objCopy = { ...obj, c: 3 };

// Destructuring
const [first, second] = [10, 20];      // Array destructuring
const { name: userName, age: userAge } = { name: 'John', age: 25 }; // Object
const { x, y, ...rest } = { x: 1, y: 2, z: 3, w: 4 };
```

## สตริง (Strings)

```javascript
'hello'.length;                      // 5
'hello'.toUpperCase();               // "HELLO"
'HELLO'.toLowerCase();               // "hello"
'hello world'.includes('world');     // true
'hello'.startsWith('he');            // true
'hello'.endsWith('lo');              // true
'hello'.indexOf('l');                // 2
'hello'.slice(1, 3);                 // "el"
'hello'.substring(1, 3);             // "el"
'a,b,c'.split(',');                  // ["a","b","c"]
'hello'.repeat(3);                   // "hellohellohello"
'  hello  '.trim();                  // "hello"
'hello world'.replace('world', 'JS');// "hello JS"
```

## อาร์เรย์ (Arrays)

```javascript
const arr = [1, 2, 3, 4, 5];

arr.length;                          // 5
arr.push(6);                         // เพิ่มท้าย [1,2,3,4,5,6]
arr.pop();                           // ลบท้าย [1,2,3,4,5]
arr.unshift(0);                      // เพิ่มหน้า [0,1,2,3,4,5]
arr.shift();                         // ลบหน้า [1,2,3,4,5]
arr.indexOf(3);                      // 2
arr.includes(3);                     // true
arr.slice(1, 3);                     // [2,3]
arr.splice(2, 1);                    // ลบ index 2 จำนวน 1 ตัว
arr.splice(2, 0, 99);                // แทรก 99 ที่ index 2
arr.concat([6, 7]);                  // รวมอาร์เรย์
arr.join('-');                       // "1-2-3-4-5"
arr.reverse();                       // [5,4,3,2,1]
arr.sort();                          // เรียงตาม string
arr.sort((a, b) => a - b);           // เรียงตัวเลขน้อยไปมาก
```

### Array Methods (สำคัญ)

```javascript
// forEach - วนลูป
[1, 2, 3].forEach(x => console.log(x));

// map - สร้างอาร์เรย์ใหม่
[1, 2, 3].map(x => x * 2);          // [2,4,6]

// filter - กรองค่า
[1, 2, 3, 4].filter(x => x > 2);    // [3,4]

// reduce - รวมค่า
[1, 2, 3, 4].reduce((acc, cur) => acc + cur, 0); // 10

// find - หาค่าแรกที่ตรงเงื่อนไข
[1, 2, 3].find(x => x > 1);         // 2

// findIndex
[1, 2, 3].findIndex(x => x > 1);    // 1

// some - มีตัวใดตัวหนึ่งตรงเงื่อนไข?
[1, 2, 3].some(x => x > 2);         // true

// every - ทุกตัวตรงเงื่อนไข?
[1, 2, 3].every(x => x > 0);        // true

// flat - ทำให้อาร์เรย์เรียบ
[[1, 2], [3, 4]].flat();            // [1,2,3,4]

// flatMap
['hi', 'bye'].flatMap(x => x.split('')); // ['h','i','b','y','e']
```

## ออบเจ็กต์ (Objects)

```javascript
const user = {
  name: 'John',
  age: 25,
  greet() { return `Hi, I'm ${this.name}`; }
};

Object.keys(user);                   // ['name', 'age', 'greet']
Object.values(user);                 // ['John', 25, ƒ]
Object.entries(user);                // [['name','John'], ['age',25]]
Object.assign({}, user, { age: 30 });
const { name, age } = user;         // Destructuring
delete user.age;                     // ลบ property
'name' in user;                      // true
user?.address?.city;                 // Optional chaining
```

## DOM Manipulation

```javascript
// การเลือก Element
document.getElementById('id');
document.querySelector('.class');
document.querySelectorAll('div');
document.getElementsByClassName('class');
document.getElementsByTagName('div');

// การสร้างและจัดการ Element
const div = document.createElement('div');
div.textContent = 'Hello';
div.innerHTML = '<b>Hello</b>';
div.setAttribute('id', 'myId');
div.classList.add('active');
div.classList.remove('active');
div.classList.toggle('active');
div.style.color = 'red';
div.style.backgroundColor = 'black';

// การเพิ่ม/ลบ Element
parent.appendChild(div);
parent.insertBefore(div, reference);
parent.removeChild(div);
div.remove();
parent.replaceChild(newChild, oldChild);
parent.prepend(div);    // ลูกคนแรก
parent.append(div);     // ลูกคนสุดท้าย

// Event Listeners
element.addEventListener('click', (e) => {
  e.preventDefault();                  // ป้องกันพฤติกรรม default
  e.stopPropagation();                 // หยุดการกระจาย event
  console.log(e.target);               // element ที่ถูกคลิก
  console.log(e.currentTarget);        // element ที่ผูก event
});

element.removeEventListener('click', handler);

// Event Delegation
parent.addEventListener('click', (e) => {
  if (e.target.matches('.child')) { /* ... */ }
});
```

## Events ที่พบบ่อย

```javascript
click, dblclick, mouseover, mouseout, mousedown, mouseup
keydown, keyup, keypress
submit, change, input, focus, blur
load, DOMContentLoaded, resize, scroll
touchstart, touchmove, touchend
```

## Forms

```javascript
document.querySelector('form').addEventListener('submit', (e) => {
  e.preventDefault();
  const form = e.target;
  const data = new FormData(form);
  const values = Object.fromEntries(data);
  // หรือ
  const input = document.querySelector('input[name="email"]').value;
});
```

## JSON

```javascript
// แปลง Object -> JSON string
JSON.stringify({ name: 'John', age: 25 });

// แปลง JSON string -> Object
JSON.parse('{"name": "John", "age": 25}');
```

## Fetch API / AJAX

```javascript
// GET
fetch('https://api.example.com/data')
  .then(res => {
    if (!res.ok) throw new Error(res.status);
    return res.json();
  })
  .then(data => console.log(data))
  .catch(err => console.error(err));

// POST
fetch('https://api.example.com/data', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'John' })
}).then(res => res.json());

// Async/Await
async function fetchData() {
  try {
    const res = await fetch('https://api.example.com/data');
    if (!res.ok) throw new Error(res.status);
    const data = await res.json();
    return data;
  } catch (err) {
    console.error(err);
  }
}
```

## Promises & Async/Await

```javascript
// สร้าง Promise
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('Done'), 1000);
  // reject(new Error('Failed'));
});

promise.then(data => console.log(data)).catch(err => console.error(err)).finally(() => {});

// Promise.all - รอทุกตัวสำเร็จ
const [a, b] = await Promise.all([p1, p2]);

// Promise.allSettled - รอทุกตัวแม้บางตัว fail
Promise.allSettled([p1, p2]).then(results =>
  results.forEach(r => console.log(r.status, r.value))
);

// Promise.race - ตัวที่เสร็จก่อน
Promise.race([p1, p2]).then(val => console.log(val));

// Promise.any - ตัวที่ resolve ตัวแรก
Promise.any([p1, p2]).then(val => console.log(val));
```

## Timing Functions

```javascript
setTimeout(() => console.log('3 วิ'), 3000);
clearTimeout(timeoutId);

setInterval(() => console.log('ทุก 2 วิ'), 2000);
clearInterval(intervalId);

requestAnimationFrame(() => {});  // สำหรับ animation
```

## LocalStorage / SessionStorage

```javascript
localStorage.setItem('key', 'value');
localStorage.getItem('key');        // "value"
localStorage.removeItem('key');
localStorage.clear();

sessionStorage.setItem('key', 'value'); // ลบเมื่อปิด tab

// เก็บ object ต้องแปลงเป็น string
localStorage.setItem('user', JSON.stringify({ name: 'John' }));
JSON.parse(localStorage.getItem('user'));
```

## Error Handling

```javascript
try {
  // code ที่อาจ error
  throw new Error('Something went wrong');
} catch (error) {
  console.error(error.message);
  console.error(error.stack);
} finally {
  // ทำงานเสมอ
}

// Custom Error
class ValidationError extends Error {
  constructor(message) { super(message); this.name = 'ValidationError'; }
}
```

## Classes (OOP)

```javascript
class Animal {
  constructor(name) { this.name = name; }
  speak() { return `${this.name} makes a sound`; }
  static describe() { return 'Animals are living beings'; }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  speak() { return `${this.name} barks`; }
}

// Private fields (ES2022)
class Person {
  #ssn;  // private field
  constructor(name, ssn) { this.name = name; this.#ssn = ssn; }
  get #privateMethod() { return this.#ssn; }
}
```

## Modules (ES Modules)

```javascript
// ----- utils.js -----
export const add = (a, b) => a + b;
export default function greet(name) { return `Hi ${name}`; }
export const PI = 3.14;

// ----- main.js -----
import greet, { add, PI as piValue } from './utils.js';
import * as utils from './utils.js';
```

## สูตรที่ใช้บ่อย

```javascript
// Unique array
[...new Set([1,2,2,3,1])];               // [1,2,3]

// Shuffle array
arr.sort(() => Math.random() - 0.5);

// Debounce
function debounce(fn, delay) {
  let timer;
  return (...args) => { clearTimeout(timer); timer = setTimeout(() => fn(...args), delay); };
}

// Throttle
function throttle(fn, limit) {
  let inThrottle;
  return (...args) => { if (!inThrottle) { fn(...args); inThrottle = setTimeout(() => inThrottle = false, limit); } };
}

// Copy to clipboard
navigator.clipboard.writeText('text');

// Detect mobile
const isMobile = /Android|iPhone|iPad|iPod/i.test(navigator.userAgent);

// Random number ระหว่าง min-max
Math.floor(Math.random() * (max - min + 1)) + min;

// Group by
const groupBy = (arr, key) => arr.reduce((acc, item) => {
  (acc[item[key]] = acc[item[key]] || []).push(item); return acc;
}, {});
```

## Shortcuts & Tips

```javascript
// ตรวจสอบ null/undefined แล้วกำหนดค่า default
const val = obj ?? 'default';            // Nullish coalescing
const val = obj || 'default';            // Logical OR

// Optional chaining
user?.profile?.address?.city;

// Logical assignment
x ||= y;    // x = x || y
x &&= y;    // x = x && y
x ??= y;    // x = x ?? y

// Swap variables
[a, b] = [b, a];

// Convert string to number
+'42';      // 42
~~3.14;     // 3

// Flatten array 1 level
[].concat(...arr);
arr.flat();
```

## Console

```javascript
console.log('text');
console.error('error');
console.warn('warning');
console.table([{ name: 'A' }, { name: 'B' }]);
console.time('label'); /* code */ console.timeEnd('label');
console.group('Group'); /* logs */ console.groupEnd();
console.trace();  // แสดง call stack
```
