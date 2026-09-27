# JS核心篇個人筆記
## 第一章  執行環境、作用域
### 1-1 JavaScript 是如何運行的
#### 一、直譯與編譯的比較
##### (一)直譯
1. 定義：逐行讀取原始碼、即時翻譯並執行。
2. 優點：開發效率高、靈活性高。
3. 缺點：執行速度慢、錯誤晚期發現。
4. 程式語言：Python、JavaScript、PHP等。
##### (二)編譯
1. 定義：先透過編譯器將整份原始碼一次性轉換成執行檔。
2. 優點：執行速度快、原始碼不易外洩、早期偵測錯誤
3. 缺點：跨平台彈性較低、每次修改程式碼必須重新編譯。
4. 程式語言：C++、JAVA等。
##### (三)JS直譯器轉換過程 
包括以下步驟：
1. 語法基本單元化
2. 抽象結構樹
3. 代碼生成

[詳細說明](https://share.gemini.google/VMVl1OsSmMIc)

### 1-2 執行的錯誤情境 RHS, LHS
#### 一、LHS (Left-Hand Side)：賦值運算子的左側發生錯誤。如：
1.  賦值給非法的目標（語法錯誤）

```
// 錯誤：數字 2 是一個純值，不是容器，無法接受賦值
2 = x; // Uncaught SyntaxError/ReferenceError: Invalid left-hand side in assignment

// 錯誤：字串不能被賦值
"hello" = a;

```
2. 嚴格模式下賦值給未宣告的變數

```
'use strict';

function test() {
  target = 100; // ReferenceError: target is not defined
}
test();
```
#### 二、RHS (Right-Hand Side)：賦值運算子的右側發生錯誤。如：
1. 讀取從未宣告過的變數（ReferenceError）
```
console.log(x); 
// ReferenceError: x is not defined (RHS 失敗)
```
2. 對取得的值進行非法操作（TypeError）
```
const num = 42;
num(); 
// TypeError: num is not a function (RHS 成功拿到 42，但 42 不能被呼叫)

const obj = null;
console.log(obj.name); 
// TypeError: Cannot read properties of null (reading 'name')
```
[詳細說明](https://share.gemini.google/jtkjHZzpav6z)

### 1-3 語法作用域
#### 一、意義
JavaScript 的語法作用域（Lexical Scope，又稱詞法作用域或靜態作用域）是定義變數存取權限的一套規則。

核心觀念非常簡單：變數的作用域在「程式碼寫好的那一刻（編譯/解析階段）」就已經決定了，而不是在程式碼「執行」時決定。

#### 二、靜態作用域與動態作用域
1. 靜態作用域（Lexical Scope，JS採用的機制）： 函式在哪裡定義，決定了它能存取哪些變數。

2. 動態作用域（Dynamic Scope）： 函式在哪裡呼叫，決定了它能存取哪些變數（JS 不採用此機制）。

3. JS範例說明
當 fn1 印出 value 時，因為 fn1 是在全域環境中定義的，其外層作用域就是全域。即使它是在 fn2 內部被呼叫，它依然會存取定義時所在環境的 value（全域變數）。
```
let value = '全域變數';

function fn1() {
  console.log(value);
}

function fn2() {
  let value = '區域變數';
  fn1(); // 呼叫 fn1
}

fn2(); // 印出："全域變數"
```

#### 三、JavaScript 的三種作用域層級
1. 全域作用域（Global Scope）
在所有函式或程式塊（Block）之外宣告的變數，在程式碼的任何地方都可以存取。

2. 函式作用域（Function Scope）
在函式內部宣告的變數（使用 var、let、const），只能在該函式內部存取。

3. 區塊作用域（Block Scope, ES6+）
使用 {} 包裹的程式塊（如 if、for、while 語法塊）。
  * 使用 let 和 const 宣告的變數具有區塊作用域，離開 {} 後即無法存取。
  * 使用 var 宣告的變數不具備區塊作用域（只會受函式作用域限制）。

### 四、總結
1. 靜態性： 作用域取決於程式碼寫在哪裡，而非在哪裡執行。
2. 作用域鏈： 搜尋變數由內而外，找不到就往上一層找。
3. 區塊保護： ES6 推薦使用 let 與 const 建立明確的區塊作用域，避免變數污染全域或不小心被覆蓋。

### 1-4 執行環境與執行堆疊
#### 一、 全域執行環境（Global Execution Context）
1. 定義：全域執行環境是 JavaScript 程式碼開始執行時所建立的第一個、也是最底層的執行環境。
2. 建立時機：當 JavaScript 檔案被載入並開始執行時自動建立。
3. 數量：在整個應用程式（或單一網頁）生命週期中，只會存在一個。

#### 二、 函數執行環境（Function Execution Context）
1. 定義：每當一個函式被呼叫時，JavaScript 引擎就會為該次呼叫獨立建立一個全新的執行環境。
2. 建立時機：只有在函式被呼叫時才會建立（單純宣告函式是不會建立的）。
3. 數量：可以有無數個。每次呼叫同一個函式，都會產生一個全新的、互不干擾的執行環境。

#### 三、執行堆疊
JavaScript 透過執行堆疊來管理這些執行環境：
1. 程式一啟動，全域執行環境先被壓入（Push）堆疊底部。
2. 當呼叫 Function A 時，A 的執行環境被壓入堆疊頂部。
3. 若 Function A 內部又呼叫了 Function B，B 的執行環境會再被壓入最頂層。
4. Function B 執行結束後先被推出（Pop）銷毀，接著輪到 A，最後只留下全域執行環境。
* [程式範例](./Examples/ch01-02.html)
* [Google Chrome偵錯環境錄影](./Examples/Ch01-02.mp4)

### 1-5 範圍鏈
當在目前的作用域（Scope）找不到某個變數時，JavaScript 會順著外層作用域一層一層往上尋找，直到找到該變數或到達全域作用域（Global Scope）為止，這條尋找的路徑就是範圍鏈。若最後仍找不到，則會拋出 ReferenceError。例如：
```
const globalVar = 'A';

function outer() {
  const outerVar = 'B';

  function inner() {
    const innerVar = 'C';
    // 可以在 inner 存取 innerVar, outerVar, globalVar
    console.log(innerVar, outerVar, globalVar); // 印出：C B A
  }

  inner();
}

outer();
```

### 1-6 提升
提升（Hoisting）是 JavaScript 中一種獨特的語言機制。在 JavaScript 代碼執行之前，編譯器/解釋器會進行「預編譯」階段，在這個階段中，變數與函式的宣告會被提升到其所在作用域（Scope）的最頂端。

簡單來說：在代碼中，你可以「先使用，後宣告」某些變數或函式，而不會直接拋出錯誤。

#### 一、 提升的運作方式
需要特別注意的是：提升只會「提升宣告」，不會「提升賦值（初始化）」。

1. var 變數的提升
使用 var 宣告變數時，宣告部分會被提升，但賦值會留在原地。在賦值執行前存取該變數，會得到 undefined。

你寫的程式碼
```
console.log(a); // undefined（不會報錯）
var a = 10;
console.log(a); // 10
```
JavaScript 實際運作（理解上的提升效果）：
```
var a;          // 宣告被提升到最頂端，預設值為 undefined
console.log(a); // undefined
a = 10;         // 賦值留在原地
console.log(a); // 10
```

2. 函式宣告（Function Declaration）的提升
傳統的「函式宣告」會將整個函式內容（包含函數體）一起提升到作用域頂端，因此可以在宣告前直接呼叫它。
```
sayHello(); // 輸出："Hello!"

function sayHello() {
  console.log("Hello!");
}
```

* 注意： 如果使用「函式運算式（Function Expression）」，則會遵循變數提升的規則：

```
sayHello(); // TypeError: sayHello is not a function
var sayHello = function() {
  console.log("Hello!");
};
```

3. let 與 const 的提升（暫時性死區 TDZ）
ES6 引進的 let 與 const 其實也會被提升，但它們與 var 有一個關鍵差異：在提升後到變數被實際賦值/宣告的這段區域，稱為暫時性死區（Temporal Dead Zone, TDZ）。在 TDZ 期間存取該變數會直接拋出 ReferenceError。

範例：
```
console.log(b); // Uncaught ReferenceError: Cannot access 'b' before initialization
let b = 20;
```
#### 二、優先順序與常見面試題
當函式與變數同名時，函式宣告的提升優先權高於變數宣告。
```
console.log(typeof x); // "function"

var x = 100;
function x() {}

console.log(x); // 100 （因為執行到 var x = 100 時進行了覆蓋）
```

#### 三、開發建議
1. 為了避免因提升帶來的不可預期行為與程式碼可讀性問題，現代 JavaScript 開發強烈建議：
2. 盡量使用 let 與 const 代替 var。
3. 養成「先宣告，後使用」的好習慣。


#### 四、習題
請寫出提升程式碼之後的效果，並說明執行結果與其原因。
```
whosName()
function whosName() {
  if (myName) {
    myName = '杰倫';
  }
}
var myName = '小明';
console.log(myName);
```

1. 提升後的程式碼：
```
// (1) 函式宣告整體被提升到最頂端
function whosName() {
  if (myName) {
    myName = '杰倫';
  }
}

// (2) 變數宣告被提升，此時 myName 被賦予預設值 undefined
var myName;

// --- 以下為實際按順序執行的程式碼 ---

// (3) 呼叫 whosName()
whosName();

// (4) 為 myName 賦值
myName = '小明';

// (5) 印出 myName
console.log(myName);
```

2. 執行過程與原因詳細說明
* 提升階段（Creation Phase）：
  * 函式 whosName 的宣告與內部定義被完整提升到頂端。
  * 全域變數 myName 的宣告被提升，此時它的值為 undefined。
* 執行 whosName()：
  * 進入 whosName 函式執行環境。
  * 函式內部執行 if (myName) 判斷：
    * 函式內部沒有宣告 myName，因此向外層（全域作用域）尋找。
    * 此時全域的 myName 已經被提升，但尚未執行到 myName = '小明' 的賦值階段，所以其值為 undefined。
    * 在 JavaScript 中，undefined 轉為布林值為 falsy（偽值），因此 if 條件判斷不成立。
    * 內部的 myName = '杰倫' 完全沒有被執行。
* 執行 myName = '小明'：全域變數 myName 被正式賦值為 '小明'。
* 執行 console.log(myName)：印出當前全域變數 myName 的值，結果即為 '小明'。

### 1-7 Not Defined vs. Undefined

```
let a;
console.log(a);  // undefined
console.log(b);  // [Error] Uncaught ReferenceError: b is not defined

let c=null;  // 避免直接賦予undefined給變數
```
### 1-8 JavaScript 記憶體存放與釋放機制
JavaScript (JS) 的記憶體管理主要分為 **存放（Memory Allocation）** 與 **釋放（Garbage Collection, GC）** 兩個核心部分。雖然 JS 是一門自動管理記憶體的高階語言，但理解其運作機制能有效避免記憶體洩漏（Memory Leak）並提升程式效能。

---

#### 一、 記憶體的存放機制（Stack vs Heap）

JS 將記憶體劃分為兩個主要的區域：**棧記憶體（Stack）** 與 **堆記憶體（Heap）**。

| 比較項目 | Stack (棧記憶體) | Heap (堆記憶體) |
| :--- | :--- | :--- |
| **存放內容** | 原始型態資料、引用位址 | 物件、陣列、函式等複雜型態 |
| **結構特點** | 連續、大小固定、LIFO（後進先出） | 動態分配、不連續空間 |
| **存取速度** | 非常快速 | 較 Stack 慢 |
| **管理方式** | 由系統自動分配與釋放 | 由 JS 引擎的 GC 回收機制管理 |

---

##### 1. 棧記憶體（Stack Memory）

* **存放內容**：
  * **原始型態（Primitive Types）**：`number`, `string`, `boolean`, `null`, `undefined`, `symbol`, `bigint`。
  * **引用指標（References）**：指向 Heap 中物件的記憶體位址（Memory Address）。
* **運作方式**：
  * 資料大小固定且不可變（Immutable），直接按值（Value）存取。

##### 2. 堆記憶體（Heap Memory）

* **存放內容**：
  * **引用型態（Reference Types）**：`Object`, `Array`, `Function`, `Date`, `RegExp` 等。
* **運作方式**：
  * 用於存放大小不固定或會動態擴展的資料。
  * 變數本身在 Stack 中只會儲存一個**指向 Heap 的記憶體位址（Reference Pointer）**。

---

#### 二、 記憶體的釋放機制：垃圾回收（Garbage Collection）

JS 引擎（如 V8）配有**垃圾回收器（Garbage Collector, GC）**，會定期找出不再被使用的記憶體空間並進行釋放。

---

##### 核心演算法：可達性分析（Reachability）

現代 JS 引擎主要採用 **可達性（Reachability）** 的概念：

1. **定義 Roots（根）**：例如全域物件 (`window` / `globalThis`)、當前執行堆疊中的區域變數與參數等。
2. **追蹤引用**：從 Roots 出發，遍歷所有能被訪問到的變數與物件。
3. **判定與清理**：
   * **可達（Reachable）**：仍有引用的物件，保留於記憶體中。
   * **不可達（Unreachable）**：無法被追蹤到的物件，標記為廢棄資料並準備回收。

---



#### 三、 常見的記憶體洩漏（Memory Leak）情境

若程式碼寫法導致無用物件仍保持與 Roots 的引用關係，GC 將無法將其回收：

##### 1. 意外的全域變數
```javascript
function foo() {
  // 未宣告變數，會掛載到全域 window/global 物件上，無法被回收
  bar = "a global variable"; 
}
```

##### 2. 未清除的計時器（Timers）
```javascript
const bigData = loadData();

const timerId = setInterval(() => {
  // 只要計時器未執行 clearInterval，bigData 的引用就會一直存在
  console.log(bigData);
}, 1000);
```

##### 3. 閉包（Closure）的過度或不當使用
```javascript
function outer() {
  const largeArray = new Array(1000000);
  return function inner() {
    // 內部函式持有了 largeArray 的引用，使 outer 執行完後 largeArray 仍無法被釋放
    console.log(largeArray.length);
  };
}
```

##### 4. 被移除 DOM 節點的 JS 引用
```javascript
const button = document.getElementById("myButton");
document.body.removeChild(button); 
// 雖然 DOM 節點已從頁面移除，但 JS 變數 button 仍保留其位址引用，記憶體無法釋放
```

---

#### 四、 總結

1. **存放**：基本型態資料與引用指標存於 **Stack**；物件與陣列等複雜型態存於 **Heap**。
2. **釋放**：透過 **Garbage Collection** 判斷物件是否**可達**。
3. **優化機制**：V8 引擎利用**新生代（複製/交換）**與**老生代（標記-清除/整理）**劃分處理，提升整體執行效率。

點選[範例程式](./Examples/ch01-03.html)，並透過「Google Chrome→F12→記憶體→圓形錄製按鈕」查看記憶體的情形。

### 1-9 同步、非同步
1. 同步：必須等待前一任務完成才能繼續執行下一行。
2. 非同步：任務發出後，不等待，繼續執行下一行。

### 1-10 課後練習
#### 第1題
請問第一個a和第二個a答案是什麼?
```
console.log(a);  // 
var a='Hello';
console.log(a);
```

#### 第2題
請問以下程式碼會依序出現什麼錯誤訊息?
```
1=true;
console.log(a);
```

#### 第3題
請問這個時候的 console 會出現什麼?
```
function x(){
  var a="Mary";
  a="Tom";
}
var a="Kent";
sayHi();
console.log(a);
```

#### 第4題
請問當前 JavaScript 的執行順序狀況是如何以及執行堆疊結束時候順序又是如何?
```
function a(){
  function c(){
    function b(){
      ...
    }
    b();
  }
  c();
}
a();
```
#### 第5題
請問過3秒後會出現什麼訊息呢？
```
var x;
function a(x){
  console.log(x);
}
x = "Tomy";
setTimeout(function(){
  x = "Mary";
  a(x);
}, 3000)
x = "Kent";
```

#### 第6題
請問 console 會是什麼?
```
function fu(){
  if(a){
    console.log('x');
  }else{
    console.log('y');
  }
}
fu();
var a=true;
```

#### 第7題
請問實際程式運作的樣子是什麼(拆解)?
```
function fu(){
  console.log(a);
}
var a='Hello';
fu();
```

#### 第8題
請問 console 依序會是什麼?
```
function a(){
  console.log("a");
}
function b(){
  console.log("b");
}
function c(){
  setTimeout(()=>{ console.log("c");})
}
a();
c();
b();
```
#### 第9題
請問這樣出現什麼訊息呢?
```
function a(x){
  console.log(x);
}
a()="m";
```

#### 第10題
請問這樣出現什麼訊息呢?
```
function x(a){
  var a="b";
  function y(){
    var a="c";
  }
  y();
  a="d"; 
}
var a="a";
x(a);
console.log(a);
```



#### 解答
1. undefined, Hello
2. LHS, Not defined(RHS)
3. Kent
4. 執行順序：a()→c()→b()，執行堆疊結束順序：b()→c()→a()
5. Mary
6. y
7. 
```
function fu(){
  console.log(a);
}
var a;

a='Hello';
fu();
```
8. a→b→c
9. LHS
10. a

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

## 第三章  物件
### 3-1 物件結構
#### 一、定義物件的結構範例與new Object
```
var family={
  name: "曉華家",
  deposit: 1000,
  members: {
    mom: "老媽",
    hua: "曉華"
  },
  contact: function(){
    console.log("0987654321");
  }
}
console.log(family);

var newFamily = new Object(family);
console.log(newFamily);
```
#### 二、JavaScript 物件複製與賦值差異解析

假設給定以下初始物件：

```javascript
const a = {
  x: 1,
  y: { z: 2 }
};
```

以下針對 `b = a`、`b = {...a}` 與 `b = new Object(a)` 三種寫法的行為差異進行詳細說明。

---

##### 總覽比較表

| 賦值 / 複製方式 | 複製類型 | `b === a` | 修改 `b.x` 是否影響 `a.x`？ | 修改 `b.y.z` 是否影響 `a.y.z`？ |
| :--- | :--- | :---: | :---: | :---: |
| **`b = a`** | **引用指派 (Reference)** | `true` | **是** | **是** |
| **`b = {...a}`** | **淺層複製 (Shallow Copy)** | `false` | **否** | **是** |
| **`b = new Object(a)`** | **引用指派 (Reference)** | `true` | **是** | **是** |

---

##### 詳細說明

###### 1. `b = a`（引用指派 / Reference Assignment）

* **原理**：將 `a` 在記憶體中的位址（引用）指派給 `b`。`a` 與 `b` 指向同一區塊的記憶體空間，沒有建立新的物件。
* **特點**：
  * `b === a` 的結果為 `true`。
  * 對 `b` 的**任何修改**（無論是第一層的 `x` 或第二層的 `y.z`）都會直接反映在 `a` 上。

```javascript
const a = { x: 1, y: { z: 2 } };
const b = a;

b.x = 99;
b.y.z = 88;

console.log(a.x);   // 99 (被修改了)
console.log(a.y.z); // 88 (被修改了)
console.log(b === a); // true
```

---

###### 2. `b = {...a}`（淺複製 / Shallow Copy）

* **原理**：使用 ES6 擴展運算子（Spread Operator），會建立一個**全新的第一層物件**，並將 `a` 的所有屬性複製過去。
* **特點**：
  * `b === a` 的結果為 `false`（因為第一層為不同物件）。
  * **第一層（基本型態屬性 `x`）**：重新複製了一份值，因此修改 `b.x` **不會**影響 `a.x`。
  * **第二層（物件型態屬性 `y`）**：僅複製屬性 `y` 的記憶體引用位址（因為是淺複製），因此修改 `b.y.z` **仍然會**影響 `a.y.z`。

```javascript
const a = { x: 1, y: { z: 2 } };
const b = { ...a };

b.x = 99;
b.y.z = 88;

console.log(a.x);   // 1  (未受影響)
console.log(a.y.z); // 88 (被修改了！)
console.log(b === a); // false
```

---

###### 3. `b = new Object(a)`（物件包裹/引用指派）

* **原理**：`new Object(value)` 的建構函式行為如下：
  1. 如果傳入的參數是 null 或 undefined，會回傳一個新的空物件 `{}`。
  2. 如果傳入的是基本型態（數字、字串等），會轉為對應的包裝物件（Boxed Object）。
  3. **如果傳入的已經是一個物件（Object），則直接回傳該物件本身。**
* **特點**：
  * 因為 `a` 已經是一個物件，`new Object(a)` 行為等同於直接寫 `b = a`。
  * `b === a` 的結果為 `true`。
  * 修改 `b` 的任何屬性都會同步影響 `a`。

```javascript
const a = { x: 1, y: { z: 2 } };
const b = new Object(a);

b.x = 99;
b.y.z = 88;

console.log(a.x);   // 99 (被修改了)
console.log(a.y.z); // 88 (被修改了)
console.log(b === a); // true
```

---

##### 補充：如果需要「深層複製 (Deep Copy)」該怎麼做？

若希望修改 `b` 的任何層級屬性，**完全不影響** `a`，可採用以下方式：

###### 1. `structuredClone()`（現代瀏覽器與 Node.js 原生推薦）

```javascript
const b = structuredClone(a);

b.y.z = 88;
console.log(a.y.z); // 2 (完全受保護，未被影響)
```

###### 2. `JSON.parse(JSON.stringify(a))`（簡易傳統作法，有限制）

```javascript
const b = JSON.parse(JSON.stringify(a));

b.y.z = 88;
console.log(a.y.z); // 2 (未被影響)
```
*(注意：JSON 做法無法複製 Function、Undefined、Symbol 或處理循環引用問題)*

### 3-2 物件取值、新增、刪除
### 3-3 變數與物件屬性的差異
### 3-4 物件與純值
### 3-5 未定義的物件屬性預設值
### 3-6 物件的參考特性
### 3-7 物件參考概念與實際運作模式
### 3-8 Call by Reference vs. Call by Sharing
### 3-9 淺拷貝與深拷貝
### 3-10 陣列
### 3-11 JSON
### 3-12 課後練習

## 第四章  函式以及 This 的運作
## 第五章  繼承與原型鍊
## 第六章  物件屬性延伸章節：屬性的特徵
## 第七章  ES6 章節：Let 及 Const
## 第八章  ES6 章節：箭頭函式
## 第九章  ES6 章節：Template Literial
## 第十章  ES6 章節：Promise
## 第十一章  ES6 章節：Async/Await
## 第十二章  ES6 章節：Class

