## 第八章  ES6 章節：箭頭函式
### 8-1 傳統函式轉箭頭函式的規則
箭頭函式（Arrow Function）是 ES6 新增的函式寫法，語法更精簡，且 `this` 的行為與傳統函式不同。

#### 一、轉換步驟（以函式運算式為例）
```
// 傳統函式
const add = function (a, b) {
  return a + b;
};

// 步驟 1：拿掉 function 關鍵字，在 ( ) 與 { } 之間加上 =>
const add = (a, b) => {
  return a + b;
};

// 步驟 2：函式本體只有一行 return，可省略 { } 與 return（隱含回傳）
const add = (a, b) => a + b;
```

#### 二、各種簡寫規則
| 情況 | 寫法 | 說明 |
| --- | --- | --- |
| 沒有參數 | `() => 1` | `()` 不可省略 |
| 只有一個參數 | `n => n * 2` 或 `(n) => n * 2` | 單一參數可省略 `()` |
| 兩個以上參數 | `(a, b) => a + b` | `()` 不可省略 |
| 預設值、解構、其餘參數 | `(a = 1) => a`、`({x}) => x`、`(...args) => args` | 使用這些語法時 `()` 不可省略 |
| 函式本體只有一個**運算式** | `n => n * 2` | 省略 `{ }` 與 `return`，運算結果自動回傳 |
| 函式本體有多行敘述 | `n => { const m = n * 2; return m; }` | 使用 `{ }` 就必須自己寫 `return`，否則回傳 `undefined` |
| 回傳物件字面值 | `() => ({ a: 1 })` | 物件要用 `( )` 包起來（見 8-3 第 4 點） |

#### 三、注意事項
* `=>` 前面**不可以換行**。
* 箭頭函式一律是「匿名函式」，要重複使用就指派給變數（`const fn = () => {}`）。
* 函式**宣告**（`function foo(){}`）不能直接改成箭頭函式，只能改寫成「函式運算式」的形式。
* 要立即執行（IIFE）時，整個函式要用 `( )` 包起來：`(() => { ... })();`
* 一般函式的方法簡寫 `foo() { }` 與箭頭函式 `foo: () => { }` 是不同東西，語法不可混用（`foo() => {}` 是語法錯誤）。

---

### 8-2 傳統函式與箭頭函式的差異

| 比較項目 | 傳統函式 `function` | 箭頭函式 `=>` |
| --- | --- | --- |
| 語法 | 較冗長 | 簡潔，可省略 `return`、`{ }` |
| `this` | **呼叫時**才決定（看誰呼叫它） | **定義時**決定，沿用外層作用域的 `this`（Lexical this） |
| `call` / `apply` / `bind` 改變 `this` | 可以 | **無效**（參數可傳入，但 `this` 不會變） |
| `arguments` | 有 | **沒有**（使用外層函式的 `arguments`，或改用 `...args`） |
| 當作建構函式（`new`） | 可以 | **不行**（TypeError: xxx is not a constructor） |
| `prototype` 屬性 | 有 | **沒有**（`undefined`） |
| `new.target`、`super` | 有 | 沒有（同樣沿用外層） |
| 作為 generator（`function*`） | 可以 | 不行 |
| 函式提升（Hoisting） | 函式宣告會整個提升 | 只是變數賦值，不會提升（`const` 有暫時性死區） |
| 作為物件方法 | 適合（`this` 指向該物件） | **不適合**（`this` 不是該物件） |
| 作為事件處理函式、需要 `this` 的 callback | 適合（如 DOM 事件中 `this` 為元素） | 不適合 |
| 作為 `setTimeout`、陣列方法的 callback | 可，但 `this` 要另外處理 | 適合（不用再 `var self = this`） |

#### 重點整理
1. **箭頭函式本身沒有 `this`**，在函式內使用 `this` 時，會像一般變數一樣沿著作用域鏈往外找，找到「定義它的那個外層」的 `this`。
2. 因為 `this` 在定義時就固定了，所以不管是「物件.方法()」、`call`、`apply`、`bind` 都改變不了它。
3. 需要動態 `this`（物件方法、建構函式、DOM 事件處理）→ 用傳統函式；需要沿用外層 `this`（callback、計時器）→ 用箭頭函式。

```
const obj = {
  n: 1,
  normal: function () { return this.n; },   // this 是 obj
  arrow: () => this.n                        // this 是外層（全域 / 模組）的 this，不是 obj
};
console.log(obj.normal()); // 1
console.log(obj.arrow());  // undefined（瀏覽器中是 window.n）
```

---

### 8-3 箭頭函式常見問題
#### 1. 箭頭函式沒有 arguments 參數？使用 (...args) 即可嗎？
* 箭頭函式**沒有自己的 `arguments`**。在箭頭函式裡寫 `arguments`，取得的是「外層傳統函式」的 `arguments`；若外層沒有函式，在瀏覽器會出現 `ReferenceError: arguments is not defined`。
* 使用**其餘參數（rest parameter）`(...args)`** 是官方建議的替代方式，大多數情況下可以取代。

```
function outer() {
  const inner = () => console.log(arguments); // 取得的是 outer 的 arguments
  inner(9, 9);
}
outer(1, 2);  // [1, 2]（不是 [9, 9]）

const fn = (...args) => console.log(args);
fn(1, 2, 3);  // [1, 2, 3]
```
* `...args` 與 `arguments` 的差異：
  * `args` 是**真正的陣列**，可直接使用 `map`、`filter`、`reduce`；`arguments` 是類陣列物件，需先 `Array.from(arguments)`。
  * `...args` 必須寫在參數列的**最後一個**，且只收集「剩下」的參數；`arguments` 包含所有傳入的參數。
  * `arguments` 還有 `arguments.callee`、`arguments.length` 等舊式特性，rest 沒有。

#### 2. this 綁定方式與傳統函式不同？
* 傳統函式：`this` 由**呼叫方式**決定。
  * 一般呼叫 `f()` → 全域物件 `window`（嚴格模式為 `undefined`）。
  * 方法呼叫 `p.f()` → `p`。
  * `new F()` → 新建的物件。
  * `call / apply / bind` → 指定的物件。
* 箭頭函式：**沒有自己的 `this`**，沿用定義位置外層的 `this`；因此 `call`、`apply`、`bind` 無法重新給予 `this`（第一個參數會被忽略，其餘參數仍會傳入）。

```
const arrow = () => this;
console.log(arrow.call({ a: 1 }) === this);  // true，call 沒有改變 this
```

##### 程式碼解釋一：setTimeout
```
var n=1;
var f=function(){
  console.log(this, this.n);
  setTimeout(()=>{console.log(this, this.n);}, 0)
}
var p={n:2, f:f}
```
此段程式只有定義，沒有呼叫，所以不會輸出任何東西。分兩種呼叫方式看：

| 呼叫方式 | 外層 `f` 的 `this` | 輸出一 | `setTimeout` 內箭頭函式的 `this` | 輸出二 |
| --- | --- | --- | --- | --- |
| `f()` | `window`（全域 `var n` 會成為 `window.n`） | `window 1` | 沿用外層，仍是 `window` | `window 1` |
| `p.f()` | `p` | `{n:2, f:ƒ} 2` | 沿用外層，仍是 `p` | `{n:2, f:ƒ} 2` |

* 重點：箭頭函式的 `this` 是 `f` **被呼叫當下**的 `this`（因為箭頭函式是在 `f` 執行時才建立的），所以 `p.f()` 時，延遲執行的箭頭函式裡 `this` 依然是 `p`。
* 若 `setTimeout` 裡改用傳統函式，`this` 會變成 `window`（`setTimeout` 呼叫 callback 時是一般呼叫），輸出 `window 1`，這就是以前要寫 `var self = this` 的原因。

##### 程式碼解釋二：事件處理
```
const p =document.querySelector("p"); // 假設有一個p標籤
p.addEventListener("click", function(){ console.log(this); })
p.addEventListener("click", ()=>{ console.log(this); })  // 改成箭頭函式
```
* 傳統函式：`this` 為**觸發事件的元素**（等同於 `event.currentTarget`），印出 `<p>…</p>`。
* 箭頭函式：`this` 為定義位置外層的 `this`，在全域撰寫時即 `window`（ES Module 中為 `undefined`），**不是**那個 `<p>`。
* 若使用箭頭函式又要拿到元素，改用 `event.currentTarget`（或 `e.target`）。

#### 3. 傳統函式和箭頭函式的 prototype、建構函式的使用有何不同？
```
function Person(name) { this.name = name; }
Person.prototype.hi = function () { return "hi " + this.name; };
const a = new Person("Ming");
console.log(a.hi());                 // "hi Ming"
console.log(typeof Person.prototype); // "object"

const Per = (name) => { this.name = name; };
console.log(Per.prototype);          // undefined
new Per("Ming");                     // TypeError: Per is not a constructor
Per.prototype.hi = () => {};         // TypeError: Cannot set properties of undefined
```
* 傳統函式宣告時，引擎會自動建立 `prototype` 物件（內含 `constructor`），並具有內部方法 `[[Construct]]`，因此能用 `new`。
* 箭頭函式沒有 `prototype`，也沒有 `[[Construct]]`，不能用 `new`，也不能用來擴充原型方法。
* 建構函式與原型方法一律使用傳統函式（或 ES6 `class`）。

#### 4. 箭頭函式不用 return 如何回傳物件？
`{ }` 在箭頭函式後會被當成**函式本體**，而不是物件，所以要把物件用 `( )` 包起來：
```
const a = () => { name: "Ming" };    // 函式本體，name: 是標籤（label），回傳 undefined
const b = () => ({ name: "Ming" });  // 正確，回傳 { name: "Ming" }
const c = () => { return { name: "Ming" }; };  // 也可以，明確寫 return
console.log(a(), b(), c());
```

#### 5. 請問以下程式說明了什麼？
```
let n=10;
const a= n || function(){return 1; }
const b= n || ()=>1
```
* `a` 可以執行：`function(){}` 是函式運算式，可以直接放在 `||` 右邊。結果 `a` 為 `10`（`n` 為真值，短路運算後不會取右邊）。
* `b` 這行會出現**語法錯誤**（`SyntaxError: Malformed arrow function parameter list`）：箭頭函式的優先順序比 `||` 低，不能直接當作 `||` 右邊的運算元。`n || ()=>1` 會被解析成「`(n || ())` => 1」，因此錯誤。
* 這說明：**箭頭函式在運算式中需要用 `( )` 包起來**，正確寫法：
```
const b = n || (() => 1);   // b 為 10
```
* 三元運算子 `cond ? () => 1 : () => 2` 則可以直接寫，因為箭頭函式在該位置是合法的。

#### 6. 搭配 Vue 來使用時，以下程式碼會有什麼問題
```
const app=new Vue({
  data: { num: 1 },
  created: ()=>{console.log(this.num);}
})
```
* Vue 在呼叫 `created` 等生命週期函式與 `methods` 時，會把 `this` 綁定為 **Vue 實例**，因此 `this.num` 才能取得 `data` 的值。
* 箭頭函式不能被重新綁定 `this`，它的 `this` 是外層（全域）的 `this`：瀏覽器中是 `window`，所以 `this.num` 為 `undefined`（模組中 `this` 為 `undefined`，會直接 `TypeError`）。結果印出 `undefined` 而非 `1`。
* 解法：生命週期、`methods`、`computed`、`watch` 都使用傳統函式或方法簡寫：
```
created() { console.log(this.num); }              // 方法簡寫（建議）
created: function () { console.log(this.num); }   // 傳統函式
```
* 但方法內部的 callback 則適合用箭頭函式，這樣 `this` 才會繼續指向 Vue 實例：
```
created() {
  setTimeout(() => console.log(this.num), 100);   // 1
}
```

---

### 8-4 箭頭函式的實戰運用
通常搭配陣列方法使用，例如 `map()`、`filter()`、`reduce()`、`forEach()`、`sort()`、`find()` 等，傳入簡短的 callback，讓程式碼更簡潔。

```
const nums = [1, 2, 3, 4, 5];

// map：每個元素轉換後回傳新陣列
const double = nums.map(n => n * 2);               // [2, 4, 6, 8, 10]

// filter：篩選符合條件的元素
const even = nums.filter(n => n % 2 === 0);         // [2, 4]

// reduce：累加成單一值
const sum = nums.reduce((acc, n) => acc + n, 0);    // 15

// find / some / every
const firstBig = nums.find(n => n > 3);             // 4
const hasZero = nums.some(n => n === 0);            // false

// sort：由大到小
const desc = [...nums].sort((a, b) => b - a);       // [5, 4, 3, 2, 1]

// 組合運用：陣列內物件
const users = [{ name: "A", age: 20 }, { name: "B", age: 17 }];
const adultNames = users.filter(u => u.age >= 18).map(u => u.name);  // ["A"]

// 回傳物件記得加 ( )
const objs = nums.map(n => ({ id: n }));            // [{id:1}, {id:2}, ...]
```

**其他常見運用**
* **計時器 / 非同步 callback 需保留外層 `this`**：
```
const timer = {
  seconds: 0,
  start() {
    setInterval(() => { this.seconds++; }, 1000);   // this 仍是 timer
  }
};
```
* **Promise 鏈**：`fetch(url).then(res => res.json()).then(data => console.log(data));`
* **柯里化（Currying）/ 高階函式**：`const add = a => b => a + b; add(1)(2); // 3`

**不建議使用箭頭函式的場合**
* 物件的方法（需要 `this` 指向該物件）。
* 建構函式與原型方法。
* DOM 事件處理函式中需要用 `this` 取得元素時。
* 需要使用 `arguments` 時。
* Vue 的 `data`、`methods`、生命週期函式等需要 `this` 為實例的地方。

---

### 8-5 課後習題
#### 第一題
以下程式碼會出現什麼錯誤訊息？
```
s()=>{ console.log("s"); }
s();
```
如果改成IIFE的寫法會正常執行嗎？
```
(()=>{ console.log("s"); })();
s();
```
**答：**
1. 第一段：出現 `SyntaxError: Malformed arrow function parameter list`。
   * 箭頭函式沒有「名稱」的寫法，`s()` 會被當成一般的函式呼叫，後面接 `=>` 就不合文法。
   * 這是**語法錯誤**，在解析階段就失敗，整支程式都不會執行（連第二行 `s()` 也不會跑到）。
   * 正確寫法：`const s = () => { console.log("s"); }; s();`
2. 第二段：**不會完全正常**。
   * 第一行的 IIFE 是合法的，會先印出 `s`。
   * 但 IIFE 是匿名函式，並沒有建立變數 `s`，所以第二行 `s();` 會出現 `ReferenceError: s is not defined`。
   * 結果：先輸出 `s`，再出現 ReferenceError。

#### 第二題
以下程式碼會出現什麼訊息？請說明原因。
```
const n='a';
const o={n:'b', f:()=>{const n=c; console.log(n, this.n);}}
```
**答：什麼訊息都不會出現（沒有輸出，也沒有錯誤）。**
* 程式只是宣告了 `n` 與物件 `o`，函式 `f` 只有被「定義」，並沒有被呼叫（沒有 `o.f()`），函式本體不會執行。
* 函式內的 `const n=c` 要到執行時才會去找變數 `c`，所以定義階段不會報錯。
* 延伸：若呼叫 `o.f()`，會出現 `ReferenceError: c is not defined`（`c` 沒有宣告；若原意是字串 `'c'` 則要加引號）。
* 再延伸：假設改成 `const n='c'` 後呼叫 `o.f()`，輸出 `c undefined`。
  * 內部 `n` 是函式內自己的區域變數，為 `'c'`。
  * `this.n` 的 `this` 是外層（全域）的 `this`，不是 `o`（箭頭函式沒有自己的 `this`，`{ }` 物件也不會形成作用域）；而且 `const n='a'` 不會成為 `window.n`，因此是 `undefined`（若是 `var n='a'` 則為 `'a'`）。

#### 第三題
以下程式碼會出現什麼訊息？請說明原因。
```
const n='a';
s()=>{
  const n="b";
  const o = { n="c", h:()=>{console.log(this.n)} }
  o.h();
}
s();
```
**答：出現 `SyntaxError`，整支程式不會執行。**
* 原因一：`s()=>{ ... }` 箭頭函式寫法錯誤，會出現 `Malformed arrow function parameter list`（同第一題）。
* 原因二：物件字面值中應使用 `n: "c"`，寫成 `n="c"` 會出現 `SyntaxError: Invalid shorthand property initializer`（`=` 只能用在解構賦值的預設值）。
* 修正後的程式：
```
const n = 'a';
const s = () => {
  const n = "b";
  const o = { n: "c", h: () => { console.log(this.n); } };
  o.h();
};
s();
```
  * 輸出 **`undefined`**（Node 模組中也是 `undefined`）。
  * 原因：`h` 是箭頭函式，`this` 不是 `o`，而是 `s` 的 `this`，`s` 又是箭頭函式，所以再往外找到全域的 `this`（瀏覽器為 `window`）。
  * `const n='a'` 是 `const` 宣告，不會成為 `window` 的屬性，所以 `window.n` 為 `undefined`。
  * 若把 `console.log(this.n)` 改為 `console.log(n)`，則依作用域鏈輸出 `"b"`（`this.n` 與變數 `n` 是不同的東西）。

#### 第四題
以下程式碼會出現什麼訊息？
如果 `const sum=b+c;` 改成 `const this.sum=this.b+this.c;` 會出現什麼訊息？
請說明原因。
```
let a=(b, c)=>{
  this.b=b;
  this.c=c;
}
a.prototype.calcA()=>(
  const sum=b+c;
  return sum;
)
new a(2,3).calcA();
```
**答：**
1. 原程式出現 `SyntaxError`，整支程式不會執行。
   * `a.prototype.calcA()=>( ... )` 寫法錯誤：方法不能這樣寫，應為 `a.prototype.calcA = () => { ... }`；而且箭頭函式本體要用 `{ }`，用 `( )` 包起來時只能放「運算式」，不能放 `const`、`return` 敘述。
2. 修正語法後（`a.prototype.calcA = () => { const sum=b+c; return sum; }`）仍然會有執行錯誤，依序是：
   * `a.prototype` 為 `undefined`（箭頭函式沒有 `prototype`），所以設定 `calcA` 時出現 `TypeError: Cannot set properties of undefined (setting 'calcA')`。
   * 就算上面通過，`new a(2,3)` 也會出現 `TypeError: a is not a constructor`（箭頭函式不能當建構函式）。
   * 再就算通過，`calcA` 裡的 `b + c` 是找外層變數，而 `b`、`c` 並沒有宣告為變數（是 `this.b`、`this.c` 的屬性），會出現 `ReferenceError: b is not defined`。
3. 把 `const sum=b+c;` 改成 `const this.sum=this.b+this.c;`：出現 `SyntaxError: Unexpected token '.'`。
   * `const` 後面只能接**識別字（變數名稱）**，`this.sum` 是物件屬性，不能用 `const` 宣告；要給屬性賦值，直接寫 `this.sum = this.b + this.c;`，不加 `const`。
4. 正確寫法（使用傳統函式）：
```
function A(b, c) {
  this.b = b;
  this.c = c;
}
A.prototype.calcA = function () {
  const sum = this.b + this.c;
  return sum;
};
console.log(new A(2, 3).calcA());  // 5
```

#### 第五題
請寫出以下程式的執行結果：
```
const s={
  n: 10, 
  f1(){return this.number*10}, 
  f2()=>this.number*10
}
console.log(s.f1(), s.f2());
```
**答：出現 `SyntaxError`（`Unexpected token '=>'`），沒有任何輸出。**
* `f2()=>this.number*10` 寫法錯誤：物件中的方法簡寫是 `f2(){ ... }`，箭頭函式要寫成屬性 `f2: () => ...`，兩者不能混用。語法錯誤在解析階段就發生，`console.log` 不會執行。
* 修正成 `f2: () => this.number*10` 之後：輸出 **`NaN NaN`**。
  * `s.f1()`：`this` 是 `s`，但 `s` 沒有 `number` 屬性（屬性名稱是 `n`），`undefined * 10` 為 `NaN`。
  * `s.f2()`：箭頭函式的 `this` 是外層（全域），也沒有 `number`，`undefined * 10` 為 `NaN`。
* 補充：若屬性名稱用對（`this.n`），`f1()` 會是 `100`，`f2()` 仍是 `NaN`（全域沒有 `n`；若是 `var n` 才會取到全域的值），可看出一般方法與箭頭函式的 `this` 差異。

#### 第六題
請寫出以下程式的執行結果：
```
var n=1;
var o={
  n=2, 
  f(){ 
    console.log(this.name);
    setTimeout(()=>{console.log(this.name)}, 0);
  }
}
```
**答：出現 `SyntaxError: Invalid shorthand property initializer`，沒有任何輸出。**
* 物件字面值內 `n=2` 應寫成 `n: 2`。
* 且此程式即使語法正確，也**沒有呼叫 `o.f()`**，不會有輸出。
* 修正（`n: 2`）並呼叫 `o.f()` 後：依序輸出 **`undefined`**、**`undefined`**。
  * `o.f()` 是方法呼叫，`this` 是 `o`；`o` 只有 `n` 與 `f`，**沒有 `name` 屬性**，所以第一個輸出 `undefined`。
  * `setTimeout` 裡的箭頭函式沿用 `f` 的 `this`（即 `o`），所以第二個也是 `undefined`（約在 0 毫秒後的下一輪事件才輸出，晚於第一個）。
* 若原意是 `this.n`：兩次都輸出 `2`（箭頭函式沿用 `o`，不是 `window.n` 的 `1`）。若是 `f` 用 `setTimeout(function(){...})` 傳統函式，`this` 會變成 `window`，`this.n` 為 `1`，`this.name` 在瀏覽器為 `""`（`window.name` 預設是空字串）。

### 答案與詳解
本章習題答案已整理於各題「答」的段落。
**重點回顧**
1. 箭頭函式沒有自己的 `this`、`arguments`、`prototype`，不能 `new`。
2. 物件方法、建構函式、DOM 事件、Vue 生命週期 → 用傳統函式 / 方法簡寫。
3. callback、計時器、陣列方法 → 用箭頭函式。
4. 回傳物件要加 `( )`；箭頭函式放在運算式中（如 `||`）也要加 `( )`。
5. 語法錯誤（SyntaxError）發生在解析階段，整支程式都不會執行；執行錯誤（ReferenceError、TypeError）則是執行到該行才發生。
