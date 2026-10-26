# JavaScript 核心篇 單選題測驗（共 50 題）

> 說明：
> - 每題只有一個正確答案。
> - 除非特別說明，程式碼皆假設在**瀏覽器、非嚴格模式的一般 `<script>`** 環境下執行（全域 `this` 為 `window`，全域 `var` 會成為 `window` 的屬性）。
> - 題目依筆記章節排列，括號內為對應章節。
> - 答案與詳解請見 [answer.md](answer.md)。

---

### 第 1 題（第 1 章）
關於「直譯」與「編譯」，下列敘述何者正確？

- A. JavaScript 屬於編譯式語言，執行前必須先將整份原始碼轉成執行檔
- B. 直譯式語言逐行讀取原始碼並即時翻譯執行，優點是開發效率與靈活性高，缺點是執行速度較慢
- C. 編譯式語言的優點是跨平台彈性高，且修改程式碼後不必重新編譯
- D. 直譯式語言的優點是在執行前就能一次發現所有錯誤

### 第 2 題（第 1 章）
下列哪一個情境屬於 **LHS**（賦值運算子左側）錯誤？

- A. `console.log(x);`（x 從未宣告）
- B. `const num = 42; num();`
- C. `'use strict'; function test(){ target = 100; } test();`（target 從未宣告）
- D. `const obj = null; console.log(obj.name);`

### 第 3 題（第 1 章）
下列程式碼的輸出結果為何？

```javascript
console.log(typeof x);

var x = 100;
function x() {}

console.log(x);
```

- A. `"function"`、`100`
- B. `"undefined"`、`100`
- C. `"number"`、`100`
- D. `"function"`、`ƒ x() {}`

### 第 4 題（第 1 章）
下列程式碼的輸出結果為何？

```javascript
let value = '全域變數';

function fn1() {
  console.log(value);
}

function fn2() {
  let value = '區域變數';
  fn1();
}

fn2();
```

- A. `區域變數`
- B. `undefined`
- C. `全域變數`
- D. `ReferenceError`

### 第 5 題（第 2 章）
下列兩個運算式的結果依序為何？

```javascript
console.log(1 < 2 < 3);
console.log(3 > 2 > 1);
```

- A. `true`、`true`
- B. `true`、`false`
- C. `false`、`true`
- D. `false`、`false`

### 第 6 題（第 2 章）
執行下列程式碼會發生什麼事？

```javascript
var a = 10
(a + 10).toString();
```

- A. 正常執行，沒有任何輸出
- B. 解析階段出現 `SyntaxError`
- C. 執行時出現 `TypeError: 10 is not a function`
- D. 執行時出現 `ReferenceError`

### 第 7 題（第 2 章）
下列哪一個值在布林情境下會被視為 **Falsy**？

- A. `'0'`
- B. `'false'`
- C. `[]`
- D. `NaN`

### 第 8 題（第 2 章）
下列程式碼的輸出結果為何？

```javascript
let score = 0;
console.log(score || 10, score ?? 10);
```

- A. `10 10`
- B. `10 0`
- C. `0 0`
- D. `0 10`

### 第 9 題（第 3 章）
下列程式碼執行後，`a.x` 與 `a.y.z` 的值依序為何？

```javascript
const a = { x: 1, y: { z: 2 } };
const b = { ...a };

b.x = 99;
b.y.z = 88;

console.log(a.x, a.y.z);
```

- A. `1`、`88`
- B. `99`、`88`
- C. `1`、`2`
- D. `99`、`2`

### 第 10 題（第 3 章）
下列程式碼的輸出結果為何？

```javascript
var a = { x: 1 };
var b = a;
a.x = { x: 2 };
a.y = a = { y: 1 };
console.log(a);
console.log(b);
```

- A. `a` 為 `{ x: { x: 2 }, y: { y: 1 } }`，`b` 為 `{ y: 1 }`
- B. `a` 為 `{ y: 1 }`，`b` 為 `{ x: { x: 2 }, y: { y: 1 } }`
- C. `a` 與 `b` 皆為 `{ y: 1 }`
- D. `a` 為 `{ y: 1 }`，`b` 為 `{ x: 1 }`

### 第 11 題（第 3 章）
下列程式碼的輸出結果為何？

```javascript
function update(obj) {
  obj.x = 100;
  obj = { y: 200 };
}

let myObj = { x: 1 };
update(myObj);
console.log(myObj);
```

- A. `{ x: 1 }`
- B. `{ y: 200 }`
- C. `{ x: 100, y: 200 }`
- D. `{ x: 100 }`

### 第 12 題（第 3 章）
關於使用 `JSON.parse(JSON.stringify(obj))` 進行深拷貝，下列敘述何者正確？

- A. 可以完整複製物件中的 Function 屬性
- B. 值為 `undefined` 的屬性會被保留
- C. 遇到循環引用（circular reference）時無法處理
- D. 會把 Symbol 屬性原封不動複製過去

### 第 13 題（第 4 章）
下列程式碼依序會印出什麼？

```javascript
function c(a) {
  console.log(a);

  function a() {}

  var a;
  console.log(a);

  a = 2;
  console.log(a);
}
c(1);
```

- A. `1`、`undefined`、`2`
- B. `1`、`ƒ a(){}`、`2`
- C. `ƒ a(){}`、`undefined`、`2`
- D. `ƒ a(){}`、`ƒ a(){}`、`2`

### 第 14 題（第 4 章）
下列程式碼依序會印出什麼？

```javascript
function storeMoney() {
  var money = 1000;
  return function (price) {
    money += price;
    return money;
  };
}

var A = storeMoney();
var B = storeMoney();
console.log(A(100));
console.log(A(100));
console.log(B(150));
```

- A. `1100`、`1200`、`1250`
- B. `1100`、`1200`、`1150`
- C. `1100`、`1100`、`1150`
- D. `1100`、`1200`、`1300`

### 第 15 題（第 4 章）
下列程式碼在 1 秒後會印出什麼？

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  }, 1000);
}
```

- A. `0 1 2`
- B. `2 2 2`
- C. `3 3 3`
- D. `undefined undefined undefined`

### 第 16 題（第 4 章）
下列程式碼依序會印出什麼？

```javascript
var name = '全域';
var obj = {
  name: '物件',
  getName: function () {
    return this.name;
  }
};

var fn = obj.getName;
console.log(fn(), obj.getName(), (false || obj.getName)());
```

- A. `全域`、`物件`、`全域`
- B. `物件`、`物件`、`物件`
- C. `全域`、`物件`、`物件`
- D. `物件`、`全域`、`全域`

### 第 17 題（第 4 章）
下列程式碼會印出什麼？

```javascript
function intro(age, city) {
  console.log(this.name + "，" + age + " 歲，住在" + city);
}

var person = { name: "小明" };
var bound = intro.bind(person, 18);
bound.call({ name: "小華" }, "台中");
```

- A. `小華，18 歲，住在台中`
- B. `小明，18 歲，住在undefined`
- C. `小明，18 歲，住在台中`
- D. `TypeError`

### 第 18 題（第 4 章）
下列程式碼的執行結果為何？

```javascript
var arr = ['1', '2', '3'].map(parseInt);
console.log(arr);
```

- A. `[1, 2, 3]`
- B. `[1, 2, NaN]`
- C. `[NaN, NaN, NaN]`
- D. `[1, NaN, NaN]`

### 第 19 題（第 4 章）
下列程式碼依序會印出什麼？

```javascript
function greet(name = "訪客") {
  return "Hello " + name;
}

console.log(greet());
console.log(greet(undefined));
console.log(greet(null));
```

- A. `Hello 訪客`、`Hello 訪客`、`Hello 訪客`
- B. `Hello 訪客`、`Hello 訪客`、`Hello null`
- C. `Hello 訪客`、`Hello undefined`、`Hello null`
- D. `Hello undefined`、`Hello undefined`、`Hello null`

### 第 20 題（第 5 章）
已知 `function Dogs(){}` 與 `var dog1 = new Dogs();`，下列哪一個運算結果為 `true`？

- A. `dog1.__proto__ === Dogs.prototype`
- B. `dog1.prototype === Dogs.prototype`
- C. `Dogs.__proto__ === Dogs.prototype`
- D. `Dogs.prototype.__proto__ === Function.prototype`

### 第 21 題（第 5 章）
下列程式碼忘記寫 `new`，執行後的結果為何？

```javascript
function Dogs(name) {
  this.name = name;
}
var d = Dogs("bibi");
```

- A. `d` 是 `Dogs` 的實例，其 `name` 為 `"bibi"`
- B. 拋出 `TypeError: Dogs is not a constructor`
- C. `d` 為 `{}`，`name` 屬性被掛在 `Dogs.prototype` 上
- D. `d` 為 `undefined`，`this` 指向 `window`，`name` 被寫到全域物件上

### 第 22 題（第 5 章）
要讓 `Dogs` 的實例能使用 `Animals.prototype` 上的方法，且之後在 `Dogs.prototype` 新增方法時**不會影響** `Animals` 的實例，下列哪一種寫法正確？

- A. `Dogs.prototype = Animals.prototype;`
- B. `Dogs.prototype = Animals;`
- C. `Dogs.prototype = Object.create(Animals.prototype); Dogs.prototype.constructor = Dogs;`
- D. `Animals.call(Dogs);`

### 第 23 題（第 5 章）
下列程式碼的輸出結果為何？

```javascript
var person = { name: "father", age: 28 };
var family = Object.create(person);
console.log(family, family.name, family.hasOwnProperty("name"));
```

- A. `{ name: "father", age: 28 }`、`"father"`、`true`
- B. `{}`、`undefined`、`false`
- C. `{}`、`"father"`、`true`
- D. `{}`、`"father"`、`false`

### 第 24 題（第 6 章）
在非嚴格模式下，執行下列程式碼的輸出結果為何？

```javascript
var o = {};
Object.defineProperty(o, "id", { value: 1 });
o.id = 2;
delete o.id;
console.log(o.id, Object.keys(o));
```

- A. `1 []`
- B. `2 []`
- C. `undefined []`
- D. `1 ["id"]`

### 第 25 題（第 6 章）
對物件執行 `Object.seal(obj)` 後，在非嚴格模式下，下列哪一項操作會**成功**？

- A. 新增屬性
- B. 刪除既有屬性
- C. 把屬性的 `configurable` 改成 `true`
- D. 修改既有屬性的值

### 第 26 題（第 6 章）
已知下列物件，哪一組列舉結果正確？

```javascript
const parent = { family: "Lee" };
const child = Object.create(parent);
child.name = "Kent";
Object.defineProperty(child, "secret", { value: "abc", enumerable: false });
```

- A. `for...in`：name、family；`Object.keys`：name；`Object.getOwnPropertyNames`：name、secret
- B. `for...in`：name、family、secret；`Object.keys`：name；`Object.getOwnPropertyNames`：name、secret
- C. `for...in`：name；`Object.keys`：name、family；`Object.getOwnPropertyNames`：name、secret
- D. `for...in`：name、family；`Object.keys`：name、secret；`Object.getOwnPropertyNames`：name、secret

### 第 27 題（第 6 章）
關於 Vue 2 與 Vue 3 的響應式（資料偵測）原理，下列敘述何者正確？

- A. Vue 2 使用 `Proxy`，因此可以偵測到新增的屬性
- B. Vue 3 使用 `Object.defineProperty`，因此無法偵測新增的屬性
- C. Vue 2 因為使用 `Proxy`，所以不需要 `Vue.set`
- D. Vue 2 使用 `Object.defineProperty` 的 getter/setter，無法偵測新增屬性而需用 `Vue.set`；Vue 3 改用 `Proxy`，可攔截新增屬性

### 第 28 題（第 7 章）
下列程式碼的執行結果為何？

```javascript
let x = '外層';
{
  console.log(x);
  let x = '內層';
}
```

- A. `ReferenceError`
- B. `外層`
- C. `內層`
- D. `undefined`

### 第 29 題（第 7 章）
下列程式碼的執行結果為何？

```javascript
for (const i = 0; i < 3; i++) {
  console.log(i);
}
```

- A. 依序印出 `0 1 2`
- B. 先印出 `0`，接著拋出 `TypeError: Assignment to constant variable.`
- C. 只印出 `0`，之後正常結束，不報錯
- D. 解析階段出現 `SyntaxError`，什麼都不會印出

### 第 30 題（第 7 章）
執行下列程式碼，結果為何？

```javascript
const obj = { a: 1 };
obj.b = 2;
console.log(obj);
obj = { a: 1, b: 2 };
```

- A. 印出 `{ a: 1, b: 2 }`，且沒有任何錯誤
- B. 執行 `obj.b = 2` 時就拋出 `TypeError`
- C. 印出 `{ a: 1, b: 2 }`，接著在重新指派時拋出 `TypeError`
- D. 解析階段出現 `SyntaxError`，什麼都不會印出

### 第 31 題（第 7 章）
在瀏覽器的一般 `<script>` 中執行下列程式碼，輸出結果為何？

```javascript
var a = 1;
let b = 2;
console.log(window.a, window.b);
```

- A. `1 2`
- B. `1 undefined`
- C. `undefined undefined`
- D. `undefined 2`

### 第 32 題（第 8 章）
關於箭頭函式（Arrow Function），下列敘述何者正確？

- A. 擁有自己的 `arguments` 物件
- B. 可以透過 `call`、`apply`、`bind` 改變 `this`
- C. 沒有 `prototype` 屬性，也不能使用 `new` 當作建構函式
- D. 最適合用來定義物件的方法（需要 `this` 指向該物件時）

### 第 33 題（第 8 章）
下列程式碼的輸出結果為何？

```javascript
const a = () => { name: "Ming" };
const b = () => ({ name: "Ming" });
console.log(a(), b());
```

- A. `undefined { name: "Ming" }`
- B. `{ name: "Ming" } { name: "Ming" }`
- C. `undefined undefined`
- D. `SyntaxError`

### 第 34 題（第 8 章）
下列程式碼依序會印出什麼？

```javascript
var n = 1;
var p = {
  n: 2,
  f1: function () {
    setTimeout(() => console.log(this.n), 0);
  },
  f2: function () {
    setTimeout(function () { console.log(this.n); }, 0);
  }
};
p.f1();
p.f2();
```

- A. `1`、`1`
- B. `2`、`2`
- C. `1`、`2`
- D. `2`、`1`

### 第 35 題（第 8 章）
執行下列程式碼會發生什麼事？

```javascript
let n = 10;
const b = n || () => 1;
```

- A. `b` 的值為 `10`
- B. 出現 `SyntaxError`，箭頭函式在運算式中需用 `( )` 包起來
- C. `b` 的值為函式 `() => 1`
- D. 出現 `TypeError`

### 第 36 題（第 9 章）
下列程式碼的輸出結果有什麼問題？

```javascript
const hobbies = ['閱讀', '游泳'];
console.log(`<ul>${hobbies.map(h => `<li>${h}</li>`)}</ul>`);
```

- A. 會出現 `SyntaxError`，因為樣板字面值不能巢狀
- B. `<li>` 的位置會顯示 `undefined`
- C. `<li>` 標籤會被跳脫成文字
- D. 兩個 `<li>` 之間會多出一個逗號，需加上 `.join("")`

### 第 37 題（第 9 章）
下列標籤樣板（Tagged Template）的輸出結果為何？

```javascript
function x(strings, ...arg) {
  console.log(strings, arg);
}
x`${1}+${2}+${3}`
```

- A. `["", "+", "+", ""]` 與 `[1, 2, 3]`
- B. `["+", "+"]` 與 `[1, 2, 3]`
- C. `[1, 2, 3]` 與 `["", "+", "+", ""]`
- D. `"1+2+3"` 與 `[]`

### 第 38 題（第 10 章）
下列程式碼的輸出順序為何？

```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
new Promise(resolve => {
  console.log("4");
  resolve();
}).then(() => console.log("5"));
console.log("6");
```

- A. `1 → 4 → 6 → 2 → 3 → 5`
- B. `1 → 4 → 6 → 3 → 5 → 2`
- C. `1 → 6 → 3 → 5 → 4 → 2`
- D. `1 → 4 → 3 → 5 → 6 → 2`

### 第 39 題（第 10 章）
下列程式碼的輸出為何？

```javascript
Promise.resolve(1)
  .then(x => x + 1)
  .then(x => { throw new Error("oops " + x); })
  .then(x => console.log("A", x))
  .catch(e => { console.log("B", e.message); return 10; })
  .then(x => console.log("C", x))
  .finally(() => console.log("D"))
  .then(x => console.log("E", x));
```

- A. `B oops 2`、`C 10`、`D`、`E 10`
- B. `A undefined`、`B oops 2`、`C 10`、`D`
- C. `B oops 2`、`C 10`、`D`、`E undefined`
- D. `B oops 2`、`C undefined`、`D`、`E undefined`

### 第 40 題（第 10 章）
下列程式碼的執行結果為何？

```javascript
const delay = (ms, v) => new Promise(r => setTimeout(() => r(v), ms));

delay(500, "A")
  .then(() => { delay(500, "B"); })
  .then(v => console.log(v));
```

- A. 約 1 秒後印出 `"B"`
- B. 約 0.5 秒後印出 `"B"`
- C. 約 1 秒後印出 `undefined`
- D. 約 0.5 秒後印出 `undefined`

### 第 41 題（第 10 章）
已知下列 Promise，`Promise.race` 與 `Promise.any` 的結果依序為何？

```javascript
const ok   = (ms, v) => new Promise(resolve => setTimeout(() => resolve(v), ms));
const fail = (ms, v) => new Promise((_, reject) => setTimeout(() => reject(v), ms));

const p1 = ok(300, "A");
const p2 = fail(100, "B");
const p3 = ok(200, "C");

Promise.race([p1, p2, p3]);
Promise.any([p1, p2, p3]);
```

- A. `race` 失敗，原因 `"B"`；`any` 成功，值為 `"C"`
- B. `race` 成功，值為 `"C"`；`any` 失敗，原因 `"B"`
- C. 兩者皆失敗，原因 `"B"`
- D. 兩者皆成功，值為 `"C"`

### 第 42 題（第 10 章）
當 `fetch` 請求的網址伺服器回應 **404** 時，下列敘述何者正確？

- A. `fetch` 回傳的 Promise 會 reject，直接進入 `.catch`
- B. `fetch` 回傳的 Promise 仍為 fulfilled，需自行檢查 `response.ok` 並手動 `throw` 才能進入 `.catch`
- C. 只有 404 不會 reject，500 以上的錯誤才會 reject
- D. 在 `fetch` 的設定中加上 `{ throwOnError: true }` 即可讓 404 進入 `.catch`

### 第 43 題（第 11 章）
下列程式碼的輸出順序為何？

```javascript
async function a() {
  console.log("1");
  await null;
  console.log("2");
}
console.log("3");
a();
console.log("4");
```

- A. `3 → 1 → 2 → 4`
- B. `1 → 3 → 4 → 2`
- C. `3 → 1 → 4 → 2`
- D. `3 → 4 → 1 → 2`

### 第 44 題（第 11 章）
下列兩個函式，各自約需要多久才能完成？

```javascript
const delay = (ms, v) => new Promise(r => setTimeout(() => r(v), ms));

async function f1() {
  const a = await delay(1000, "A");
  const b = await delay(2000, "B");
  return [a, b];
}

async function f2() {
  const [a, b] = await Promise.all([delay(1000, "A"), delay(2000, "B")]);
  return [a, b];
}
```

- A. `f1` 約 3 秒、`f2` 約 2 秒
- B. 兩者皆約 3 秒
- C. `f1` 約 2 秒、`f2` 約 3 秒
- D. 兩者皆約 2 秒

### 第 45 題（第 11 章）
下列程式碼的輸出順序為何？

```javascript
const delay = (ms, v) => new Promise(r => setTimeout(() => r(v), ms));

async function main() {
  [1, 2, 3].forEach(async n => {
    await delay(100);
    console.log(n);
  });
  console.log("結束");
}
main();
```

- A. `1`、`2`、`3`、`結束`
- B. `結束`、`3`、`2`、`1`
- C. `結束`、`1`、`2`、`3`
- D. 只印出 `結束`，其餘不會執行

### 第 46 題（第 11 章）
下列四個函式，哪些能讓 `catch` 抓到 `fail()` 丟出的錯誤？

```javascript
async function fail() { throw new Error("X"); }

async function t1() { try { fail();              } catch (e) { console.log("t1 抓到"); } }
async function t2() { try { await fail();        } catch (e) { console.log("t2 抓到"); } }
async function t3() { try { return fail();       } catch (e) { console.log("t3 抓到"); } }
async function t4() { try { return await fail(); } catch (e) { console.log("t4 抓到"); } }
```

- A. 四個都抓得到
- B. 只有 `t2` 與 `t4`
- C. 只有 `t2`
- D. 只有 `t2`、`t3`、`t4`

### 第 47 題（第 12 章）
關於 ES6 `class` 與 ES5 建構函式的比較，下列敘述何者正確？

- A. `class` 可以不加 `new` 直接呼叫
- B. `class` 宣告會像函式宣告一樣整個提升，宣告前即可使用
- C. `class` 內定義的方法可以被 `for...in` 列舉出來
- D. `class` 本質上仍是建構函式與原型的語法糖，且內部自動為嚴格模式

### 第 48 題（第 12 章）
下列程式碼的執行結果為何？

```javascript
class Person {
  constructor(name) { this.name = name; }
  sayHi() { console.log("Hi, " + this.name); }
}

const p = new Person("Ming");
const fn = p.sayHi;
fn();
```

- A. `TypeError: Cannot read properties of undefined (reading 'name')`
- B. `Hi, Ming`
- C. `Hi, undefined`
- D. `ReferenceError: name is not defined`

### 第 49 題（第 12 章）
執行下列程式碼會發生什麼事？

```javascript
class Animal {
  constructor(name) { this.name = name; }
}
class Dog extends Animal {
  constructor(name) {
    this.breed = "柴犬";
    super(name);
  }
}
new Dog("小黑");
```

- A. 正常建立，`breed` 為 `"柴犬"`
- B. `ReferenceError`：在衍生類別中，存取 `this` 之前必須先呼叫 `super()`
- C. `TypeError: Dog is not a constructor`
- D. 解析階段出現 `SyntaxError`

### 第 50 題（第 12 章）
下列程式碼的輸出結果為何？

```javascript
class Base {
  static who() { return this.name; }
  hello() { return "Hello from " + this.constructor.name; }
}
class Derived extends Base {}

console.log(Base.who(), Derived.who(), new Derived().hello());
```

- A. `Base`、`Base`、`Hello from Base`
- B. `Base`、`Derived`、`Hello from Base`
- C. `Base`、`Derived`、`Hello from Derived`
- D. `undefined`、`undefined`、`Hello from Derived`
