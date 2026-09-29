## 第二章  運算子、型別與文法
### 2-1 陳述式與表達式
在 JavaScript 中，陳述式（Statements）與表達式（Expressions）最核心的差異在於：「是否會產生一個值（Value）」。

#### 一、表達式（Expressions）
1. 定義：任何一段會運算並傳回一個值的程式碼。
2. 特性：可以放在任何需要「值」的地方（例如賦值、函式參數、樣板字面值中）。
3. 常見範例：
  * 字面值：5, 'Hello'
  * 算術運算：2 + 3（傳回 5）
  * 變數存取：a
  * 邏輯與比較：a > 10
  * 三元運算子：isAdult ? 'Adult' : 'Child'
  * 函式呼叫：add(1, 2)

#### 二、陳述式（Statements）
1. 定義：執行某個動作或指令的程式碼結構，用來控制程式流程或宣告變數，不會直接傳回值。
2. 特性：不能當作函式參數或賦值給變數。
3. 常見範例：
  * 變數宣告：let x;, const y = 10;
  * 條件判斷：if (...) { ... } else { ... }, switch
  * 迴圈結構：for (...), while (...)
  * 流程控制：return, break, continue

#### 三、函數陳述式 vs. 函數表達式  vs. 物件實字
1. 函數陳述式(具名函式)，如：function x(){...}
2. 函數表達式(匿名函式)，如：const x=function(){...}
3. 物件實字，如：var x={a:1};

### 2-2 ASI (Automatic Semicolon Insertion)
當JavaScript語句沒有加上分號的時候，則會受到自動插入分號(ASI)規則影響。如：
```
function a(){
  return 1;
}
function b(){
  return 
  1;
}
console.log(a());  // 1
console.log(b());  // undefined
```
另外請留意以下判斷式
```
if(true) a=1
else a=2
console.log(a)  // 執行結果：1
```
```
if(true) a=1 else a=2
console.log(a)  // Unexpected token 'else' 
```
立即函式宣告兩次的時候要自己補分號
```
(function(){console.log(1);}());
(function(){console.log(2);}()); 
```

**結論：為了避免不可預期的ASI陷阱，在實際開發中，絕大多數團隊與規範仍強烈建議（甚至強制要求）明確加上分號。**

### 2-3 動態型別
#### 一、顯性轉換
開發者透過語法或函式明確指示型別轉換。又稱為強制轉型（Type Casting / Explicit Coercion），如：
```
let name;
console.log(typeof name); // undefined
name = "Kent";
console.log(typeof name); // string
name = 132;
console.log(typeof name); // number
```
#### 二、隱性轉換
語言執行環境在運算時自動觸發型別轉換。又稱為自動轉型（Implicit Coercion / Type Promotion），如：
```
let num = 3;
console.log(typeof num);  // number 
num += "3";
console.log(typeof num); // string
num *= "3";
console.log(typeof num); // number
```

### 2-4 原始型別與物件型別
| 特性 | 原始型別（Primitive Types） | 物件型別（Object Types） |
| :--- | :--- | :--- |
| **包含成員** | `String`、`Number`、`Boolean`、`Null`、`Undefined`、`Symbol`、`BigInt` | `Object`、`Array`、`Function`、`Date`、`RegExp` 等 |
| **可變性（Mutability）** | **不可變（Immutable）**，無法直接修改原始值 | **可變（Mutable）**，可以新增、修改或刪除屬性 |
| **記憶體儲存** | 儲存於 **Stack（棧記憶體）**，直接儲存「實際的值」 | 儲存於 **Heap（堆記憶體）**，儲存指向記憶體的「引用位址」 |
| **指派與傳遞** | **傳值（Pass by Value）**，複製獨立的完整數值 | **傳址（Pass by Reference）**，複製記憶體位址，指向同一資料 |
| **比較方式** | 比較**實際數值**是否相等 | 比較**記憶體位址**是否相同 |

### 2-5 運算子
運算子（Operator）是程式語言中用來對一個或多個數值（稱為「運算元」，Operand）執行特定計算、比較或邏輯操作的符號或關鍵字。常見運算子種類如下：
1. 算術運算子（Arithmetic Operators）：執行基本數學運算，如：`+`、`-`、`++`、`--`、`*`、`/`、`%`、`**`等。
2. 賦值/指定運算子（Assignment Operators）：用於將值賦予變數，並可結合算術運算進行簡寫。如：`=`、`+=`、`-=`、`*=`、`/=`、`%=`等。
3. 比較/關係運算子（Comparison / Relational Operators）：用於比較兩個運算元的值，結果會傳回布林值（true 或 false）。如：`==`、`===`、`!=`、`!==`、`>`、`<`、`>=`、`<=`等。
4. 邏輯運算子（Logical Operators）：用於結合或反轉布林條件判斷。如：`&&`、`||`、`!`等。
5. 位元運算子（Bitwise Operators）：直接對數字的二進位位元（0 與 1）進行操作，常用於低階系統開發或高效能演算法。如：`&`、`|`、`^`、`~`、`<<`、`>>`等。
6. 型別與特殊運算子（Type & Special Operators）：用於檢查、轉換資料型別或進行特定語法操作（以 JavaScript 為例）：
  * typeof：回傳變數的資料型別。
  * instanceof：檢查物件是否為特定建構函式的實例。
  * ?.（可選鏈運算子）：安全地讀取深層物件屬性，避免 null/undefined 錯誤。
  * ??（空值合併運算子）：當左側為 null 或 undefined 時，回傳右側的值。

除了功能分類，運算子也可以依據處理的運算元數量來區分：
1. 一元運算子（Unary）：只需要一個運算元，如 !flag、++count、typeof x。
2. 二元運算子（Binary）：需要兩個運算元，如 a + b、x > y。
3. 三元運算子（Ternary）：需要三個運算元，如 (a > b) ? x : y。

### 2-6 優先性與相依性
#### 一、核心概念
1. 優先性（Precedence）：當運算式中有多個不同的運算子時，決定哪個運算子先被執行（類似數學中的「先乘除後加減」）。運算子優先順序可參閱此[連結](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/Operator_precedence)。
2. 相依性（Associativity）：當運算式中有多個優先性相同的運算子時，決定運算的執行方向。其方向可分為由左至右(如算術運算子、比較運算子等)及由右至左(如賦值運算子、!、typeof等)

#### 二、開發注意事項
1. 括號擁有最高優先性：如果不確定執行順序，或想提高程式碼可讀性，請直接使用括號包覆。
2. 比較運算子的連續使用陷阱：JavaScript 不支援數學上的連續比較（例如 1 < 2 < 3），會因型態轉換產生非預期結果。
3. 鏈結賦值（Chained Assignment）運算順序：賦值運算是「由右至左」，但屬性存取運算子（. 或 []）優先性高於賦值，計算時需特別留意引用位址。

#### 三、JavaScript 優先性與相依性範例詳細解析

##### 範例 1：`1 + 2 * 3 / 4`

* **解析**：
  1. `*` 與 `/` 的優先性（Precedence）高於 `+`。
  2. `*` 與 `/` 的優先性相同，且相依性（Associativity）為**由左至右**。
  3. 先計算 `2 * 3 = 6`。
  4. 再計算 `6 / 4 = 1.5`。
  5. 最後計算 `1 + 1.5 = 2.5`。
* **結果**：`2.5`

---

##### 範例 2：`1 < 2 < 3`

* **解析**：
  1. `<` 的相依性為**由左至右**。
  2. 先計算 `1 < 2`，結果為 Boolean 值 `true`。
  3. 接著計算 `true < 3`。進行比較時，`true` 會被隱式型態轉換為數字 `1`。
  4. 計算 `1 < 3`，結果為 `true`。
* **結果**：`true`

---

##### 範例 3：`3 > 2 > 1`

* **解析**：
  1. `>` 的相依性為**由左至右**。
  2. 先計算 `3 > 2`，結果為 Boolean 值 `true`。
  3. 接著計算 `true > 1`。`true` 被轉換為數字 `1`。
  4. 計算 `1 > 1`，結果為 `false`。
* **結果**：`false`
* **提醒**：若要表達數學上的連續比較，應使用邏輯運算子組合：`3 > 2 && 2 > 1`。

---

##### 範例 4：賦值運算子與變數宣告

```javascript
let a = 1, b = 2;
a = b = 3;
console.log(a, b);
```

* **解析**：
  1. `=` 賦值運算子的相依性為**由右至左**。
  2. `a = b = 3` 等同於 `a = (b = 3)`。
  3. 先執行 `b = 3`，此表達式回傳數值 `3`，同時 `b` 被賦值為 `3`。
  4. 再執行 `a = 3`，`a` 被賦值為 `3`。
* **輸出結果**：`3 3`

---

##### 範例 5：物件屬性存取與賦值

```javascript
let c = { d: 3 };
let e = c.d = 4;
console.log(c, d, e);
```

* **解析**：
  1. **優先性**：點運算子 `.`（優先性高）優於賦值運算子 `=`（優先性低）。
  2. 執行 `let e = c.d = 4` 時：
     * 先解析物件屬性引用 `c.d`。
     * `=` 相依性為由右至左：先將 `4` 賦值給 `c.d`，此時 `c` 物件內容變更為 `{ d: 4 }`。
     * 賦值表達式 `c.d = 4` 的回傳值為 `4`。
     * 最後將回傳值 `4` 賦值給變數 `e`。
  3. **變數 `d` 的作用域**：程式中僅宣告了物件 `c` 的屬性 `d`，並未宣告全域或區域變數 `d`。因此直接印出 `d` 會拋出錯誤。
* **輸出結果**：
  * **狀態**：`c` 為 `{ d: 4 }`，`e` 為 `4`。
  * **執行錯誤**：`ReferenceError: d is not defined`
### 2-7 寬鬆相等、嚴格相等與隱含轉型

在 JavaScript 中，比較兩個值是否相等有兩種主要方式：**寬鬆相等（`==`）** 與 **嚴格相等（`===`）**。兩者的核心差異在於「是否會在比較前進行型別轉換」。

#### 1. 嚴格相等（`===` 與 `!==`）
* **規則**：比較時**不進行型別轉換**。只有當兩個值的「型別」與「數值」皆相同時，才會回傳 `true`。
* **推薦**：這是開發中最推薦且安全的比較方式。

```javascript
console.log(5 === 5);            // true
console.log(5 === '5');          // false (數字 vs 字串)
console.log(true === 1);         // false (布林 vs 數字)
console.log(null === undefined); // false
```

#### 2. 寬鬆相等（`==` 與 `!=`）與隱含轉型
* **規則**：若兩邊的型別不同，JavaScript 會**自動嘗試將值轉換為相同型別**（即「隱含轉型」，Implicit Coercion），然後再進行比較。
* **隱含轉型常見規則**：
  * **字串與數字比較**：字串會被轉為數字（例如 `'5' == 5` $\rightarrow$ `5 == 5`）。
  * **布林值與其他型別比較**：布林值會先被轉為數字（`true` 轉為 `1`，`false` 轉為 `0`）。
  * **`null` 與 `undefined`**：兩者在寬鬆相等下相等（`null == undefined` 為 `true`），且不等於其他任何值。

```javascript
console.log(5 == '5');           // true (字串 '5' 自動轉為數字 5)
console.log(true == 1);          // true (true 自動轉為 1)
console.log(false == 0);         // true (false 自動轉為 0)
console.log(null == undefined);  // true

// 特殊陷阱
console.log('' == 0);            // true (空字串轉為數字 0)
console.log([] == 0);            // true (空陣列轉為數字 0)
```

> **最佳實踐**：為了避免隱含轉型帶來的不可預期錯誤，一律優先使用 `===` 與 `!==`。

---

### 2-8 Truthy and Falsy

JavaScript 中的每個值，在需要布林值（例如 `if` 條件式）的環境下，都會被評估為 **Truthy（真值）** 或 **Falsy（假值）**。

#### 1. Falsy 值（假值）
在 JavaScript 中，**只有以下 8 個值**在布林情境下會被視為 `false`：

| Falsy 值 | 說明 |
| :--- | :--- |
| `false` | 布林值的假 |
| `0` (含 `-0`, `0n`) | 數字零與 BigInt 零 |
| `""` (`''`, `` ``) | 空字串 |
| `null` | 無 / 空值 |
| `undefined` | 未定義 |
| `NaN` | 不是一個數字 (Not a Number) |

#### 2. Truthy 值（真值）
除了上述 8 個 Falsy 值以外，JavaScript 中的**其他所有值都是 Truthy**。

包含以下容易混淆的例子：
* **`'0'`**（非空字串） $\rightarrow$ `true`
* **`'false'`**（非空字串） $\rightarrow$ `true`
* **`[]`**（空陣列） $\rightarrow$ `true`
* **`{}`**（空物件） $\rightarrow$ `true`
* **`function(){}`**（函式） $\rightarrow$ `true`

```javascript
// 範例：利用 Truthy / Falsy 進行條件判斷
let username = "Alice";

if (username) {
  console.log("使用者已登入"); // 會執行，因為非空字串是 Truthy
}

let cartCount = 0;
if (cartCount) {
  console.log("購物車有商品"); // 不會執行，因為 0 是 Falsy
}
```

---

### 2-9 邏輯運算子與函數預設值

邏輯運算子在 JavaScript 中不僅用於條件判斷，也經常用於短路邏輯與設定預設值。

#### 1. 短路評估（Short-Circuit Evaluation）
邏輯運算子不一定會回傳布林值，而是回傳**第一個決定結果的運算子數值**。

* **邏輯與 `&&`（AND）**：
  * 若第一個值是 **Falsy**，直接回傳第一個值；否則回傳第二個值。
  ```javascript
  console.log(false && "hello");    // false
  console.log("Apple" && "Banana"); // "Banana" (第一個是 Truthy，回傳第二個)
  ```
* **邏輯或 `||`（OR）**：
  * 若第一個值是 **Truthy**，直接回傳第一個值；否則回傳第二個值。
  ```javascript
  console.log("Default" || "Fallback"); // "Default"
  console.log("" || "Fallback");        // "Fallback" (空字串是 Falsy，回傳第二個)
  ```

#### 2. 使用 `||` 設定函數預設值（ES5 舊做法）
在 ES6 之前，常用 `||` 來替未傳入參數的函數提供預設值：

```javascript
function greet(name) {
  // 若未傳入 name (undefined)，會使用預設值 "Guest"
  name = name || "Guest";
  console.log("Hello, " + name);
}

greet();        // "Hello, Guest"
greet("Alice"); // "Hello, Alice"

// ⚠️ 潛在問題：當傳入的合法值是 Falsy 時（例如 0 或空字串）
function setScore(score) {
  score = score || 10; // 若傳入 0，0 是 Falsy，會被改為 10！
  console.log("Score:", score);
}

setScore(0); // 輸出 "Score: 10"（不符預期）
```

#### 3. ES6 參數預設值（Default Parameters）
ES6 引進了原生預設值語法，**只有當傳入的引數為 `undefined` 時**才會觸發預設值，完美解決了 `||` 的缺陷：

```javascript
function setScore(score = 10) {
  console.log("Score:", score);
}

setScore();  // Score: 10 (觸發預設值)
setScore(0); // Score: 0  (正確保留 0)
```

#### 4. 空值合併運算子 `??`（Nullish Coalescing Operator, ES2020）
若只想在值為 `null` 或 `undefined` 時才使用預設值（保留其他 Falsy 值如 `0` 或 `""`），可以使用 `??`：

```javascript
let inputScore = 0;

let finalScore1 = inputScore || 10; // 10 (因為 0 是 Falsy)
let finalScore2 = inputScore ?? 10; // 0  (因為 0 不是 null 或 undefined)

console.log(finalScore1); // 10
console.log(finalScore2); // 0
```

### 2-10 課後練習
#### 第1題
請寫出以下程式的執行結果
```
let a=true, b="undefined", c=1, d=null, e=NaN;
console.log(typeof a, typeof b, typeof c, typeof d, typeof e);
```

#### 第2題
請寫出以下程式的執行結果
```
let a=new Number('1','2','3');
console.log(typeof a);
```

#### 第3題
請寫出以下程式的執行結果
```
var a="10";
console.log(10==this.a, 10===this.a);
```
#### 第4題
請寫出以下程式的執行結果
```
let a="10";
console.log(10==this.a, 10===this.a);
```

#### 第5題
請寫出以下程式的執行結果
```
var a = new Object();
var b = a;
console.log(a===b);

const c = new Object();
const d = c;
console.log(c===d);
```

#### 第6題
請寫出以下程式的執行結果
```
var a = new Object('1234');
var b = BigInt(1234);
console.log(a==b);
```

#### 第7題
請寫出以下程式的執行結果
```
var a = new Object();
if (a){
  console.log("X");
} else {
  console.log("Y");
}
```

#### 第8題
請寫出以下程式的執行結果
```
console.log(10+10-10*2);
```

#### 第9題
請寫出以下程式的執行結果
```
var a=10;
console.log(++a*a);
a=10;
console.log(--a*a);
```

#### 第10題
以下程式碼是否會出現錯誤
```
var a=10
(a+10).toString();
```

#### 第11題
以下程式碼是否會出現錯誤
```
var a=1
(function(){console.log(a);})()
```
#### 答案
1. boolean string number object number
2. object
3. true false
4. false false
5. true true
6. true
7. X
8. 0
9. 121 81
10. 出現錯誤
11. 出現錯誤
