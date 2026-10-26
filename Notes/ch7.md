## 第七章  ES6 章節：Let 及 Const
### 7-1 let, const, var 的差異與常見問題
ES6（ES2015）新增了 `let` 與 `const` 兩種宣告方式，用來解決 `var` 長期以來作用域不明確、容易造成變數汙染的問題。**現代 JavaScript 的建議：預設使用 `const`，需要重新指派時才用 `let`，不再使用 `var`。**

#### 一、三者差異一覽表

| 比較項目 | var | let | const |
| --- | --- | --- | --- |
| 作用域 | **函式作用域**（Function Scope） | **區塊作用域**（Block Scope） | **區塊作用域**（Block Scope） |
| 重複宣告（同作用域） | 可以 | 不可（SyntaxError） | 不可（SyntaxError） |
| 重新指派值 | 可以 | 可以 | **不可**（TypeError） |
| 宣告時必須初始化 | 否 | 否 | **是**（`const a;` 會 SyntaxError） |
| Hoisting（提升） | 有，且自動初始化為 `undefined` | 有，但**不初始化**（暫時性死區） | 有，但**不初始化**（暫時性死區） |
| 在全域宣告時是否成為 `window` 屬性 | 是（`window.a`） | 否 | 否 |
| 宣告前使用 | 得到 `undefined` | ReferenceError | ReferenceError |

#### 1. var 變數汙染問題說明
`var` 只認「函式」的邊界，不認 `{ }` 區塊（`if`、`for`、`while`...），所以在區塊內宣告的變數會「漏」到外面；全域的 `var` 還會變成 `window` 的屬性，也容易被重複宣告而覆蓋。

```
// 問題一：區塊內的 var 會漏到區塊外
if (true) {
  var x = 10;
}
console.log(x);  // 10：if 結束了，x 還在

// 問題二：重複宣告不會報錯，容易不小心覆蓋
var name = "Ming";
var name = "Lee";      // 不報錯，直接覆蓋
console.log(name);     // "Lee"

// 問題三：全域 var 會汙染 window
var city = "Taipei";
console.log(window.city);  // "Taipei"
var alert = 1;             // 甚至可能蓋掉 window 內建的東西

// 問題四：Hoisting 讓人誤判
console.log(a);  // undefined（不是報錯）
var a = 5;
```
* 在多人協作或引入多個 script 時，全域 `var` 同名變數會互相覆蓋，造成難以追查的 bug，這就是「變數汙染」。
* `let`、`const` 不會有以上問題：離開區塊就消失、不能重複宣告、不會掛到 `window`。

#### 2. let 所宣告的變數可更改值，但 const 不行
```
let a = 1;
a = 2;               // OK
console.log(a);      // 2

const b = 1;
b = 2;               // TypeError: Assignment to constant variable.

const c;             // SyntaxError: Missing initializer in const declaration
```
* `const` 代表「**變數與值之間的綁定（參考）不可改變**」，不是「值本身不可變」，下方第 4 點會詳細說明。
* 適合用 `const` 的情況：常數（如 `const PI = 3.14`）、函式、物件、陣列、DOM 元素等不會被重新指派的東西。

#### 3. let, const 的作用域
`let`、`const` 的有效範圍是**最近的一組 `{ }`**（區塊），包含 `if`、`for`、`while`、`switch`、`try...catch` 以及單純的 `{ }`。

```
{
  let a = 1;
  const b = 2;
  var c = 3;
}
console.log(c);  // 3
console.log(a);  // ReferenceError: a is not defined
console.log(b);  // ReferenceError: b is not defined
```

```
let x = "外層";
{
  let x = "內層";      // 與外層的 x 是不同變數（遮蔽 shadowing）
  console.log(x);      // "內層"
}
console.log(x);        // "外層"
```

```
function test() {
  var v = 1;
  let l = 2;
}
test();
console.log(v);  // ReferenceError：var 在函式內時，同樣離開函式就消失
console.log(l);  // ReferenceError
```
* `var` 的作用域是「函式」，`let`/`const` 的作用域是「區塊」，區塊比函式更小，所以變數的生命週期更容易掌控。

#### 4. const 與陣列、物件
```
let arr = [1];
arr.push(2);
console.log(arr);   // [1, 2]
```
* 執行成功。`push` 是修改陣列「內容」，並沒有重新指派 `arr`。

```
const arr = [1];
arr.push(2);
console.log(arr);   // [1, 2]：一樣成功！
arr = [1, 2];       // TypeError: Assignment to constant variable.
```
* 改成 `const` 後 `push` **依然成功**。`const` 鎖住的是 `arr` 這個變數所存的「記憶體位址（參考）」，陣列本身的內容仍可修改；只有**把 `arr` 指向另一個陣列**（重新指派）才會報錯。

> 講義中的 `let obj=[a: 1]` 為筆誤，`[ ]` 是陣列，不能寫 `a: 1`（SyntaxError），物件應寫成 `{ a: 1 }`，以下以 `{ a: 1 }` 說明。

```
let obj = { a: 1 };
obj.b = 2;
console.log(obj);   // { a: 1, b: 2 }：新增屬性成功
```

```
const obj = { a: 1 };
obj.b = 2;
console.log(obj);   // { a: 1, b: 2 }：依然成功
obj = { a: 1, b: 2 };  // TypeError: Assignment to constant variable.
```
* 與陣列相同，`obj.b = 2` 是修改物件內容，而不是改變 `obj` 的指向，所以 `let`、`const` 都可以。

```
// 若真的要讓物件「內容」也不能改，可使用 Object.freeze
const frozen = Object.freeze({ a: 1 });
frozen.b = 2;        // 一般模式下被忽略；strict mode 會 TypeError
console.log(frozen); // { a: 1 }
```
* `Object.freeze` 只凍結第一層（淺層凍結），巢狀物件需要遞迴凍結。

**是否安裝 ESLint 會對執行結果有差異嗎？**
* **執行結果沒有差異。** ESLint 是「靜態程式碼檢查工具」，只在撰寫（編輯器標紅線）或執行 `eslint` 指令時檢查，**不會改變 JavaScript 引擎實際執行的結果**。以上程式碼不論有沒有 ESLint，結果都相同。
* 差別在於「**提示**」：
  * `let arr = [1]` 若之後從未被重新指派，ESLint 的 `prefer-const` 規則會提示「請改用 const」。
  * `const x = 1; x = 2;` 會被 `no-const-assign` 規則在**執行前**就標示錯誤（但若無視警告硬執行，仍然會在執行時拋出 TypeError）。
  * `var` 會被 `no-var` 規則提示改用 `let`/`const`。

#### 5. 比較三種 for 迴圈

**(1) var**
```
for (var i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i);
```
* 輸出 `0` ~ `9`，最後一行輸出 **`10`**。
* 原因：`var i` 屬於外層（全域或函式）作用域，迴圈結束後 `i` 仍存在，且最後一次 `i++` 後值為 10 才讓條件不成立。

**(2) let**
```
for (let i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i);
```
* 輸出 `0` ~ `9`，最後一行拋出 **`ReferenceError: i is not defined`**。
* 原因：`let i` 只存在於迴圈的區塊內，迴圈結束後就不存在了。

**(3) const**
```
for (const i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i);
```
* 只輸出 `0`，接著在第一輪結束執行 `i++` 時拋出 **`TypeError: Assignment to constant variable.`**，程式中斷，最後一行 `console.log(i)` 根本不會執行（就算執行也一樣是 ReferenceError）。
* 原因：`i++` 等於重新指派，`const` 不允許。
* 補充：`for...of`、`for...in` 每一輪都是建立一個新的變數，所以可以用 `const`：
```
for (const item of [1, 2, 3]) {
  console.log(item);   // 1 2 3，沒問題
}
```

| 寫法 | 迴圈內輸出 | 迴圈外 `console.log(i)` |
| --- | --- | --- |
| `var` | 0 ~ 9 | `10` |
| `let` | 0 ~ 9 | ReferenceError |
| `const` | `0` 後即 TypeError | 不會執行到 |

#### 6. 迴圈內改成 setTimeout(() => console.log(i), 0)

**(1) var**
```
for (var i = 0; i < 10; i++) {
  setTimeout(() => console.log(i), 0);
}
console.log(i);
```
* 先輸出 `10`（同步的最後一行），之後輸出**十個 `10`**。
* 原因：
  1. `setTimeout` 的回呼是「非同步」，要等同步程式碼（整個迴圈）執行完才會執行，即使延遲設為 0 也一樣（事件迴圈的特性）。
  2. `var i` 只有**一個**共用變數，十個回呼參考的都是同一個 `i`，等它們執行時 `i` 早已變成 10。

**(2) let**
```
for (let i = 0; i < 10; i++) {
  setTimeout(() => console.log(i), 0);
}
console.log(i);   // ReferenceError，中斷同步程式碼，但已排入的 setTimeout 仍會執行
```
* 先出現 ReferenceError，之後依序輸出 `0` ~ `9`。
* 原因：`for (let ...)` 在**每一輪迴圈都會建立一份新的 `i`**（每一輪各自有獨立的區塊作用域，並把上一輪的值複製過來），每個回呼（閉包）記住的是**自己那一輪的 `i`**。
* 這也是 `let` 解決「閉包 + 迴圈」經典問題的方式，以前使用 `var` 時必須靠 IIFE 來解決：
```
for (var i = 0; i < 10; i++) {
  (function (j) {
    setTimeout(() => console.log(j), 0);
  })(i);
}
```

**(3) const**
```
for (const i = 0; i < 10; i++) {
  setTimeout(() => console.log(i), 0);
}
```
* 第一輪排入一個 `setTimeout` 後，執行 `i++` 就拋出 `TypeError: Assignment to constant variable.`，迴圈中斷；但**已排入的那一個回呼仍會執行**，所以之後會輸出一個 `0`。

| 寫法 | 結果 |
| --- | --- |
| `var` | 十個 `10` |
| `let` | `0` ~ `9`（每輪獨立的 `i`） |
| `const` | TypeError，之後只輸出一個 `0` |

#### 7. let 和 const 有沒有 Hoisting？暫時性死區（TDZ）
**有 Hoisting。** JavaScript 引擎在執行前，會先掃描該作用域內的所有宣告，`let`、`const` 也不例外。差別在於：
* `var`：提升後**立即初始化為 `undefined`**，所以宣告前可以使用（值為 `undefined`）。
* `let` / `const`：提升後**不初始化**，從「區塊開頭」到「宣告那一行」之間，變數處於**暫時性死區（Temporal Dead Zone，TDZ）**，此時存取會拋出 `ReferenceError`。

```
console.log(a);   // undefined（var 提升並初始化）
var a = 1;

console.log(b);   // ReferenceError: Cannot access 'b' before initialization
let b = 2;

console.log(c);   // ReferenceError: Cannot access 'c' before initialization
const c = 3;
```

證明 `let` 真的有被提升（否則會讀到外層的值）：
```
let x = "外層";
{
  console.log(x);  // ReferenceError，而不是 "外層"
  let x = "內層";
}
```
* 若 `let x` 沒有被提升，第 3 行應該會讀到外層的 `"外層"`；但實際報錯，代表內層的 `x` 已經存在於這個區塊，只是還不能使用。

```
{
  // ---- 此處是 tmp 的 TDZ 開始 ----
  console.log(typeof tmp);  // ReferenceError：連 typeof 也會報錯
  let tmp = 1;              // ---- TDZ 結束，可以使用 ----
}
console.log(typeof notDeclared);  // "undefined"：沒宣告的變數 typeof 才是安全的
```
* TDZ 的好處：強迫開發者「**先宣告再使用**」，把原本 `var` 悄悄回傳 `undefined` 的 bug，變成明確的錯誤。
* 函式宣告 `function foo(){}` 的提升則是整個函式一起提升，所以宣告前可以呼叫；而 `let foo = function(){}` 與 `var foo = function(){}` 則不行。

---

### 7-2 ESLint
#### 一、何謂 ESLint？
ESLint 是 JavaScript 最主流的**靜態程式碼分析工具（Linter）**，會在不執行程式的情況下，依據規則檢查程式碼中的**語法錯誤、潛在 bug 與風格不一致**，部分問題還可以自動修正（`--fix`）。

#### 二、為什麼要使用？
* **提早發現錯誤**：例如使用未宣告的變數、重新指派 `const`、寫了永遠不會執行的程式碼，在撰寫階段就能發現，而不是等到執行才爆炸。
* **統一團隊風格**：縮排、引號、分號、命名等規則一致，Code Review 時不必再爭論風格。
* **避免不良寫法**：例如禁止使用 `var`、禁止使用 `==`、要求能用 `const` 就用 `const`。
* **可自動修正**：`eslint --fix` 一次修好大量格式問題。
* **可客製化**：規則可開關、可設為警告（warn）或錯誤（error），也有許多社群規則集（如 Airbnb、Standard）。

#### 三、使用前後差異
```
var name = "Ming";
var name = "Lee";
let count = 0;
function unused() {}
console.log(nme);
```

| 項目 | 未使用 ESLint | 使用 ESLint（recommended 規則） |
| --- | --- | --- |
| 重複宣告 `name` | 不會有任何提示 | 標示 `no-redeclare` |
| 沒用到的 `unused` | 不會有任何提示 | 標示 `no-unused-vars` |
| 打錯字的 `nme` | 執行時才報 ReferenceError | 編輯時就標示 `no-undef` |
| 使用 `var` | 沒有提示 | 開啟 `no-var` 時會提示改用 let/const |
| 沒重新指派的 `let` | 沒有提示 | 開啟 `prefer-const` 時會提示改用 const |

* **程式的執行結果不會因為有無 ESLint 而改變**，ESLint 只負責「提醒」，是否修正由開發者決定。

#### 四、如何下載與安裝
前置需求：已安裝 [Node.js](https://nodejs.org/)（內含 npm）。

**方式一：使用官方精靈快速建立（建議）**
```
# 在專案資料夾中先初始化 package.json（若已有可略過）
npm init -y

# 啟動 ESLint 設定精靈，依提示選擇（檢查語法與找出問題、ES Modules、瀏覽器環境等）
npm init @eslint/config@latest
```

**方式二：手動安裝**
```
npm install --save-dev eslint @eslint/js globals
```

建立設定檔 `eslint.config.js`（ESLint v9 之後預設的 flat config 格式）：
```
import js from "@eslint/js";
import globals from "globals";

export default [
  js.configs.recommended,           // 官方建議規則
  {
    languageOptions: {
      globals: globals.browser      // 允許使用 window、document 等瀏覽器全域變數
    },
    rules: {
      "no-var": "error",            // 禁止使用 var
      "prefer-const": "warn",       // 沒重新指派的變數建議用 const
      "no-unused-vars": "warn"
    }
  }
];
```
> 若專案不是 ES Modules（package.json 沒有 `"type": "module"`），設定檔需改用 `module.exports = [...]` 與 `require` 寫法。

**執行檢查**
```
npx eslint .            # 檢查整個專案
npx eslint app.js       # 檢查單一檔案
npx eslint . --fix      # 自動修正可修正的問題
```

**整合 VS Code（編輯時即時顯示紅色／黃色波浪線）**
1. 開啟擴充功能（`Ctrl + Shift + X`）。
2. 搜尋並安裝 **ESLint**（發行者：Microsoft）。
3. 專案根目錄有 `eslint.config.js` 時，開啟 `.js` 檔就會即時顯示檢查結果；將滑鼠移到波浪線上可看到規則名稱與修正建議。

> 若只是在瀏覽器的 `<script>` 中練習、沒有 Node.js 專案，ESLint 不會起作用；需要先建立 npm 專案並安裝 ESLint 才能使用。

### 7-3 課後習題
#### 第一題
請寫出執行結果
```
a();
let a=()=>console.log('a');
```

#### 第二題
請寫出執行結果
```
let a;
const b;
console.log(a);
console.log(b);
```

#### 第三題
請寫出拿掉 `let a=20;` 之前與之後的程式執行結果
```
let a=10;
function fu(){
  console.log(a);
  let a=20;
}
```
#### 答案與解析
**第一題**
```
a();
let a=()=>console.log('a');
```
* 結果：`ReferenceError: Cannot access 'a' before initialization`
* 解析：`let` 有 Hoisting，但提升後**不會初始化**，從區塊開頭到 `let a` 那一行之間是**暫時性死區（TDZ）**，此時呼叫 `a()` 等於存取未初始化的變數，所以報錯。
* 對照：若改成 `function a(){ console.log('a'); }`（函式宣告），整個函式會一起提升，`a()` 在宣告前呼叫可正常輸出 `a`；若改成 `var a = () => ...`，則是 `TypeError: a is not a function`（`a` 此時為 `undefined`）。

**第二題**
```
let a;
const b;
console.log(a);
console.log(b);
```
* 結果：`SyntaxError: Missing initializer in const declaration`，**什麼都不會輸出**（連 `console.log(a)` 也不會執行）。
* 解析：
  * `const` 宣告時**必須同時賦值**，`const b;` 違反語法規則。
  * `SyntaxError` 發生在**解析階段**（程式尚未開始執行），整份程式碼都不會跑，所以第 3 行的 `console.log(a)` 不會輸出 `undefined`。
  * 若只有 `let a;` 則合法，`a` 的值為 `undefined`。
* 修正：`const b = 1;`。

**第三題**

**(1) 拿掉 `let a=20;` 之前（保留）**
```
let a=10;
function fu(){
  console.log(a);
  let a=20;
}
fu();   // 題目未呼叫 fu()，需呼叫才會看到結果
```
* 結果：`ReferenceError: Cannot access 'a' before initialization`
* 解析：函式內有 `let a`，所以 `a` 在**整個函式區塊**內都屬於區域變數（宣告被提升到函式開頭）。`console.log(a)` 位於 `let a=20` 之前，處於 TDZ，**不會**去讀外層的 `a=10`，因此報錯。
* 注意：若只貼題目的程式碼而**沒有呼叫 `fu()`**，函式內容不會執行，什麼都不會輸出、也不會報錯。

**(2) 拿掉 `let a=20;` 之後**
```
let a=10;
function fu(){
  console.log(a);
}
fu();
```
* 結果：輸出 `10`
* 解析：函式內已沒有自己的 `a`，依**作用域鏈**往外層找，找到全域的 `let a=10`。

| 情況 | 結果 | 原因 |
| --- | --- | --- |
| 有 `let a=20;` | ReferenceError | 內層 `a` 遮蔽（shadow）外層，且位於 TDZ |
| 拿掉 `let a=20;` | `10` | 沿作用域鏈取得外層的 `a` |

**重點整理**
* `let`／`const` 有 Hoisting，但在宣告前處於 TDZ，存取會拋出 `ReferenceError`。
* `const` 必須在宣告時初始化，否則是 `SyntaxError`（解析階段就失敗）。
* 內層同名變數會遮蔽外層變數，即使宣告在後面，TDZ 也會讓前面的存取失敗。
