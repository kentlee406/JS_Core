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
* [程式範例](../Examples/ch01-02.html)
* [Google Chrome偵錯環境錄影](../Examples/Ch01-02.mp4)

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

點選[範例程式](../Examples/ch01-03.html)，並透過「Google Chrome→F12→記憶體→圓形錄製按鈕」查看記憶體的情形。

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
