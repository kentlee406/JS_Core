## 第四章  函式以及 This 的運作
### 4-1 什麼是函式
函式（Function）是一段「可重複呼叫」的程式碼區塊。在 JavaScript 中，函式本身也是一種**物件**（可呼叫的物件，callable object），因此可以擁有屬性、被賦值給變數、當作參數傳遞，也可以當作回傳值（一級函式，First-class Function）。

#### 一、函式的基本架構
```
function 函式名稱(參數1, 參數2) {  // 1. function 關鍵字 2. 名稱 3. 參數
  // 4. 函式本體（程式碼區塊）
  return 回傳值;                    // 5. 回傳值（沒寫 return 時預設回傳 undefined）
}
函式名稱(引數1, 引數2);              // 6. 呼叫（呼叫時才會建立函式執行環境）
```

範例：請說明執行結構與原理
```
function a(x){
  let y=1;
  return [this, x, y];
}
let result=a(3);
console.log(result);
```

執行結果（瀏覽器、非嚴格模式）：`[Window, 3, 1]`

執行原理說明：
1. **全域執行環境 — 創造階段**
   * 函式陳述式 `a` 會被完整提升（函式本體一起存入記憶體）。
   * `result` 以 let 宣告，也會提升但處於暫時性死區（TDZ），尚未初始化。
2. **全域執行環境 — 執行階段**
   * 執行到 `a(3)` 時，建立**函式執行環境**並推入執行堆疊（Call Stack）。
3. **函式執行環境 — 創造階段**，會準備以下內容：
   * `arguments`：類陣列物件 `{0: 3, length: 1}`。
   * 參數 `x`：直接被賦值為引數 `3`。
   * 區域變數 `y`：let 宣告，處於 TDZ。
   * `this`：由「呼叫方式」決定，`a(3)` 屬於簡易呼叫，非嚴格模式下指向全域物件（瀏覽器為 `window`）。
   * 外部環境參考（範圍鏈）：a 定義在全域，所以外層為全域環境。
4. **函式執行環境 — 執行階段**
   * `y=1` 完成初始化，接著 `return [this, x, y]` 回傳陣列。
5. **結束**
   * 函式執行環境從執行堆疊中移除，回傳值賦予 `result`，最後印出 `[Window, 3, 1]`。
   * 若在嚴格模式（`'use strict'`）下執行，`this` 為 `undefined`，結果為 `[undefined, 3, 1]`。

補充：函式是物件，因此擁有屬性
```
function a(x, y){}
console.log(a.name);    // "a"：函式名稱
console.log(a.length);  // 2：定義的參數數量
a.note = "自訂屬性";     // 可以像物件一樣新增屬性
console.log(typeof a);  // "function"
```

#### 二、函式陳述式(具名函式)與函式表達式(匿名函式)的差異
```
// 函式陳述式（Function Declaration）
function fn1() { return 1; }

// 函式表達式（Function Expression）：將函式當作「值」賦予變數
var fn2 = function () { return 2; };
```
是否有兩者同時混用的情形？

  有，稱為**具名函式表達式（Named Function Expression, NFE）**：外觀像函式陳述式，但因為出現在賦值運算子右側，所以仍是「函式表達式」。
```
var fn = function inner(n) {
  console.log(typeof inner); // "function"：函式名稱只能在函式內部使用
  return n <= 1 ? 1 : n * inner(n - 1); // 常用於遞迴
};

console.log(fn(5));         // 120
console.log(typeof inner);  // "undefined"：外部無法存取 inner
inner(5);                   // ReferenceError: inner is not defined
```
* 函式名稱 `inner` 只存在於函式自己的作用域，不會污染外部，也不會被提升。
* 優點：遞迴時不依賴外部變數名稱（即使 `fn` 被重新賦值也不影響）、除錯時錯誤堆疊會顯示函式名稱，比匿名函式更好追蹤。

此外，匿名函式可以直接當作「引數」傳入另一個函式（回呼函式 Callback）
```
function c(fn){
  fn();
}

c(function(){console.log(1);})
```

  執行結果：`1`

  執行原理說明：
   1. 函式陳述式 `c` 在創造階段被完整提升，它接收一個參數 `fn`。
   2. 呼叫 `c(...)` 時，括號內的 `function(){console.log(1);}` 是一個**匿名函式表達式**：它不是陳述式，而是被當作「值」直接傳入，不需要先存到變數中。
   3. 進入 `c` 的函式執行環境後，參數 `fn` 被賦值為這個匿名函式（傳遞的是函式物件的參考）。
   4. 執行 `fn()` 時，才真正呼叫該匿名函式，建立它的執行環境並印出 `1`。
   * 這種「被當作引數傳入、由其他函式在適當時機呼叫」的函式稱為**回呼函式（Callback Function）**。
   * 之所以可行，是因為 JavaScript 的函式是**一級函式（First-class Function）**：函式是物件，可以像一般值一樣被賦值給變數、當作引數傳遞、或作為回傳值。
   * 常見應用：`setTimeout(function(){...}, 1000)`、`arr.forEach(function(item){...})`、`btn.addEventListener('click', function(){...})`。
   * 等同於以下寫法，只是省去了額外的變數名稱：
```
var fn2 = function(){ console.log(1); };
c(fn2); // 1
```

### 4-2 立即函式
立即函式（IIFE, Immediately Invoked Function Expression）：又稱為Self-Executing Anonymous Function，定義完成後**立刻執行**的函式表達式。

#### 一、基本語法
```
(function () {
  console.log("立即執行");
})();

// 另一種寫法，效果相同
(function () {
  console.log("立即執行");
}());

// 傳入參數與取得回傳值
var result = (function (a, b) {
  return a + b;
})(1, 2);
console.log(result); // 3
```
* 外層的 `()` 讓 JavaScript 將 function 視為「表達式」而非「陳述式」，才能在後面加上 `()` 立即呼叫。
* 直接寫 `function(){}()` 會出現 SyntaxError，因為以 function 開頭會被解析為函式陳述式。

#### 二、用途
1. **建立獨立作用域，避免污染全域**
```
(function () {
  var count = 0; // 只存在於 IIFE 內部
})();
console.log(typeof count); // "undefined"
```
2. **搭配閉包保存私有變數**（模組模式，詳見 4-5）
```
var counter = (function () {
  var count = 0;
  return function () {
    return ++count;
  };
})();
console.log(counter()); // 1
console.log(counter()); // 2
```
3. **將外部變數傳入，產生當下的副本**
```
var a = 1;
(function (a) {
  a = 2;          // 修改的是參數 a（區域變數）
  console.log(a); // 2
})(a);
console.log(a);   // 1
```
4. **傳入同一個物件，讓多個 IIFE 共同擴充（跨作用域共享資料）**
```
var a = {};
(function (b) { b.x = 1; })(a);
(function (c) { c.y = 2; })(a);
console.log(a); // { x: 1, y: 2 }
```
* 與第 3 點不同：傳入的是**物件**，參數 `b`、`c` 拿到的是同一個物件的參考（Call by Sharing，參考 3-7），因此在 IIFE 內新增屬性會反映到外部的 `a`。
* 兩個 IIFE 各自擁有獨立作用域，參數名稱（`b`、`c`）互不影響，卻能透過同一個物件交換、累加資料。
* 常見應用：將程式拆成多個檔案或區塊，各自用 IIFE 包起來，並對同一個命名空間物件掛上功能，例如 `(function (app) { app.util = ...; })(window.app = window.app || {});`，既不污染全域，又能組合出完整模組。
* 注意：若在 IIFE 內**重新賦值**參數（如 `b = { x: 1 }`），只會改變參數指向，外部的 `a` 不受影響。

5. **傳入全域物件，有意識地對外公開變數**
```
(function (global) { global.x = 1; })(window);
console.log(window.x); // 1
console.log(x);        // 1（瀏覽器中，window 的屬性即全域變數）
```
* 執行過程：
   1. 外層 `()` 將 function 轉為函式表達式，後方 `(window)` 立即呼叫它，並把 `window` 當作引數傳入。
   2. 進入函式執行環境後，參數 `global` 拿到 `window` 物件的參考（Call by Sharing，同第 4 點）。
   3. `global.x = 1` 等同於 `window.x = 1`，因此在全域新增了屬性 `x`。
* 為什麼不直接寫 `window.x = 1`？
   1. **明確標示對外出口**：IIFE 內部的變數預設都是私有的，只有透過 `global.xxx` 掛上去的才會公開，一眼就能看出模組「輸出了什麼」，避免不小心漏寫 `var` 造成意外的全域變數（參考 1-8）。
   2. **不綁定特定執行環境**：函式內部只認得參數 `global`，不認得 `window`。瀏覽器傳 `window`、Node.js 傳 `global`、Web Worker 傳 `self`（現代可統一用 `globalThis`），同一段程式碼就能在不同環境運作。
   3. **查找較快、利於壓縮**：`global` 是區域變數，在範圍鏈（參考 1-5）第一層就能找到；壓縮工具也能把參數名稱縮短成 `e` 之類的單一字母，但無法改名 `window`。
* 與第 4 點的關係：兩者原理相同（都是傳入物件並擴充屬性），差別只在傳入的是自己建立的命名空間物件，還是全域物件本身。

**實際案例：Vue 的打包檔**

Vue 透過 `<script>` 標籤直接引入時所使用的打包檔，正是用 IIFE 包住整個框架，只對外公開一個 `Vue` 變數。

Vue 2（`vue.js`，UMD 格式，已調整排版並加上註解）：
```
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined'
    ? module.exports = factory()                 // CommonJS（Node.js / webpack）
    : typeof define === 'function' && define.amd
      ? define(factory)                          // AMD（RequireJS）
      : (global = global || self, global.Vue = factory()); // 瀏覽器：掛到全域
}(this, function () {
  'use strict';
  // ……整個 Vue 框架的原始碼（數千行，全部是私有變數）……
  return Vue;
}));
```
* 傳入兩個引數：`this`（在瀏覽器全域中即 `window`）作為 `global`，以及一個會回傳 `Vue` 的工廠函式 `factory`。
* IIFE 內先判斷目前的模組環境，決定要用哪種方式輸出；在一般瀏覽器中走到最後一行 `global.Vue = factory()`，原理就和 `global.x = 1` 完全一樣。
* `global || self` 是短路評估（參考 2-9），若 `this` 取不到值（如嚴格模式或 Worker 環境）就改用 `self`。
* 結果：框架內部的上千個函式、變數都被封裝在 `factory` 的作用域中，全域只多了一個 `window.Vue`。

Vue 3（`vue.global.js`）：
```
var Vue = (function (exports) {
  'use strict';
  // ……框架原始碼……
  exports.createApp = createApp;
  exports.ref = ref;
  // ……
  return exports;
})({});
```
* 傳入一個空物件 `{}` 作為 `exports`，在 IIFE 內逐一掛上要公開的 API，最後回傳並指派給全域變數 `Vue`，屬於第 2 點（回傳值）與第 4 點（擴充傳入物件）的組合。
* 使用時即可 `const { createApp, ref } = Vue;`。
* 補充：現代專案多改用 ES Module（`import { createApp } from 'vue'`），模組本身就有獨立作用域，不再需要 IIFE；但為了支援直接用 `<script>` 引入，函式庫仍會提供 IIFE / UMD 版本的打包檔（jQuery、Lodash 等也是同樣手法）。

#### 三、注意事項
上一行沒有分號時，IIFE 開頭的 `(` 會被當成「呼叫」上一行的結果（參考 2-2 ASI），因此常見在 IIFE 前面加上分號：
```
var a = 1
;(function () { console.log(a); })()
```

還有，IIFE無法在函式外執行：
```
(function IIFE(){console.log(1);}());
IIFE();  // Uncaught ReferenceError: IIFE is not defined
```


### 4-3 參數
#### 一、參數（Parameter）與引數（Argument）
* 參數：定義函式時括號內的變數名稱，屬於函式的區域變數。
* 引數：呼叫函式時實際傳入的值。
```
function add(a, b) {  // a, b 為參數
  return a + b;
}
add(1, 2);            // 1, 2 為引數
```

參數在函式執行環境的**創造階段**就會被賦值，並與函式內的提升互相影響：
```
function c(a){
  console.log(a);

  function a(){}

  var a;
  console.log(a);

  a=2;
  console.log(a);
}
c(1)
```

  執行結果：
```
ƒ a(){}
ƒ a(){}
2
```

  執行原理說明：
   1. **創造階段**依序處理：
      * 參數 `a` 先被賦值為引數 `1`。
      * 函式陳述式 `function a(){}` 被完整提升，覆蓋同名的參數，`a` 變成函式。
      * `var a` 發現 `a` 已存在，宣告直接被忽略，**不會**把 `a` 重設為 `undefined`（參考 1-6 提升的優先順序）。
   2. **執行階段**：
      * 第一個 `console.log(a)` 印出函式 `ƒ a(){}`。
      * `function a(){}` 與 `var a;` 已在創造階段處理完畢，執行到這兩行時不會有任何作用，第二個 `console.log(a)` 仍是函式。
      * `a=2` 才真正重新賦值，第三個 `console.log(a)` 印出 `2`。
   * 結論：同名時的優先順序為「函式陳述式 > 參數 > var 宣告（無賦值）」；而執行階段的賦值會再覆蓋掉所有結果。

#### 二、引數數量不一致
JavaScript 不會檢查引數數量：
```
function fn(a, b) {
  console.log(a, b);
}
fn(1);        // 1 undefined：未傳入的參數為 undefined
fn(1, 2, 3);  // 1 2：多傳入的引數會被忽略（但可透過 arguments 取得）
```

引數是依照**位置順序**對應到參數，與外部變數的名稱無關：
```
function d(d, c, b, a){
  console.log(d, c, b, a);
}
let a=1, b=2, c=3;
d(a, b, c);
```

  執行結果：`1 2 3 undefined`

  執行原理說明：
   1. 呼叫 `d(a, b, c)` 時，先在全域取出 `a`、`b`、`c` 的值，實際傳入的是 `1, 2, 3`。
   2. 引數依位置賦值給參數：第 1 個參數 `d` = 1、第 2 個參數 `c` = 2、第 3 個參數 `b` = 3；第 4 個參數 `a` 沒有對應的引數，因此為 `undefined`。
   * 參數是函式的**區域變數**，即使名稱和外部的 `a`、`b`、`c` 相同，也是各自獨立的變數，並會遮蔽（shadowing）外部同名變數。
   * 參數 `d` 與函式名稱 `d` 同名，在函式內部 `d` 指的是參數（值為 1），而非函式本身。
   * 實務上應避免這種容易混淆的命名方式。

#### 三、arguments 物件
* 每個一般函式執行時都會自動建立 `arguments`，內含所有傳入的引數。
* 它是**類陣列（Array-like）**：有索引與 length，但沒有陣列方法（如 map、forEach）。
* 箭頭函式沒有自己的 arguments。
```
function sum() {
  console.log(arguments);        // [Arguments] { '0': 1, '1': 2, '2': 3 }
  console.log(arguments.length); // 3
  return Array.from(arguments).reduce((total, n) => total + n, 0); // 轉為陣列才能使用陣列方法
}
console.log(sum(1, 2, 3)); // 6
```

範例：函式內可同時取得區域變數、全域變數、參數、arguments 與 this
```
var G=1;
var obj={
 a: function(b){
   var L=2;
   console.log(L, G, b, arguments, this);
 }
}
obj.a(9,8,7);

// arguments是類陣列，不能直接使用陣列方法，如forEach()
```

  執行結果：`2 1 9 Arguments(3) [9, 8, 7] {a: ƒ}`

  執行原理說明：
   1. `L`：函式內的區域變數，值為 `2`。
   2. `G`：函式內找不到，沿著範圍鏈（參考 1-5）往外到全域找到 `G = 1`。
   3. `b`：參數只有一個，因此只接收第一個引數 `9`，多傳入的 `8`、`7` 不會對應到任何參數。
   4. `arguments`：包含**所有**傳入的引數 `[9, 8, 7]`，即使沒有對應的參數也能取得（`arguments[1]` 為 8、`arguments[2]` 為 7）。
   5. `this`：以 `obj.a()` 的方式呼叫（物件方法調用，參考 4-6），`this` 指向 `obj`。
   * `arguments` 是類陣列，直接呼叫 `arguments.forEach()` 會出現 TypeError；需先用 `Array.from(arguments)` 或 `[...arguments]` 轉為陣列。

#### 四、ES6 預設參數與其餘參數
```
// 預設參數：未傳入或傳入 undefined 時才會使用預設值
function greet(name = "訪客") {
  return "Hello " + name;
}
console.log(greet());          // Hello 訪客
console.log(greet(undefined)); // Hello 訪客
console.log(greet(null));      // Hello null：null 不會觸發預設值

// 其餘參數（Rest Parameters）：將剩餘引數收集成「真正的陣列」，必須放在最後一個
function sum(first, ...others) {
  console.log(first, others); // 1 [2, 3, 4]
  return others.reduce((total, n) => total + n, first);
}
console.log(sum(1, 2, 3, 4)); // 10
```

#### 五、傳值與傳參考
參數的行為與變數賦值相同（參考第三章）：
* 原始型別：傳入值的**副本**，函式內修改不影響外部。
* 物件型別：傳入**參考**，修改物件屬性會影響外部；但將參數重新賦值為新物件則不影響外部。
```
function change(num, obj1, obj2) {
  num = 100;             // 修改副本
  obj1.value = 100;      // 透過參考修改原物件
  obj2 = { value: 100 }; // 參數指向新物件，與外部斷開
}
var n = 1, o1 = { value: 1 }, o2 = { value: 1 };
change(n, o1, o2);
console.log(n, o1.value, o2.value); // 1 100 1
```

範例：
```
function e(obj){obj.x=2;}
var obj={x:1}
e(obj);
console.log(obj);
```

  執行結果：`{x: 2}`

  執行原理說明：
   1. 呼叫 `e(obj)` 時，將全域 `obj` 存放的物件參考**複製一份**給參數 `obj`（Call by Sharing，參考 3-7）。
   2. 參數 `obj` 雖然與全域 `obj` 同名，但它是 `e` 的區域變數；兩者指向**同一個物件**。
   3. `obj.x=2` 修改的是該物件的屬性，因此外部的 `obj` 也看得到變化。
   * 若改寫為 `function e(obj){ obj = {x:2}; }`，只是讓參數指向新物件，外部 `obj` 仍為 `{x: 1}`。

#### 六、函式作為參數（回呼函式 Callback）
函式是一級物件，因此可以作為參數傳入另一個函式，稍後再被呼叫：
```
function calculate(a, b, operation) {
  return operation(a, b);
}
console.log(calculate(2, 3, function (x, y) { return x * y; })); // 6
console.log(calculate(2, 3, (x, y) => x + y));                    // 5
```

範例：傳入匿名函式與傳入具名函式
```
function CS(x){console.log(x);}
function functionB(fn){fn(1);}
functionB(function(a){console.log(a);})
functionB(CS);
```

  執行結果：
```
1
1
```

  執行原理說明：
   1. `functionB` 接收參數 `fn`，並在內部以 `fn(1)` 呼叫它，把 `1` 當作引數傳給回呼函式。
   2. 第一次呼叫：傳入**匿名函式表達式**，`fn` 指向該匿名函式，執行 `fn(1)` 時參數 `a` = 1，印出 `1`。
   3. 第二次呼叫：傳入**具名函式** `CS`，注意寫的是 `CS` 而不是 `CS()`：
      * `CS`：傳入函式本身（函式物件的參考），由 `functionB` 決定何時呼叫。
      * `CS()`：會先立即執行 `CS`，再把它的回傳值 `undefined` 傳入，導致 `fn(1)` 出現 TypeError: fn is not a function。
   4. `fn` 指向 `CS`，執行 `fn(1)` 等同於 `CS(1)`，參數 `x` = 1，印出 `1`。
   * 回呼函式的參數值由「呼叫它的函式」決定（這裡是 `functionB` 傳入的 `1`），而不是由定義回呼函式的地方決定。

### 4-4 閉包
#### 一、定義
閉包（Closure）：**函式與其定義時所在的語法環境（Lexical Environment）的組合**。
當內部函式被回傳或傳到外部使用時，即使外部函式已經執行完畢，內部函式仍然可以存取外部函式的變數。

形成閉包的條件：
1. 函式內部有另一個函式。
2. 內部函式使用了外部函式的變數。
3. 內部函式在外部函式之外被使用（例如被 return 出去）。

#### 二、範例
```
function storeMoney(){
  var money = 1000;
  return function(price){
    money+=price
    return money;
  }
}
console.log(storeMoney()(100));

var MingMoney=storeMoney();
console.log(MingMoney(100));
console.log(MingMoney(100));
console.log(MingMoney(100));


var JayMoney=storeMoney();
console.log(JayMoney(150));
console.log(JayMoney(150));
console.log(JayMoney(150));
```

  執行結果：
```
1100
1100
1200
1300
1150
1300
1450
```

  執行原理說明：
   1. `storeMoney` 內宣告區域變數 `money = 1000`，並 **return 一個匿名函式**；該匿名函式使用了外部的 `money`，符合形成閉包的三個條件。
   2. `storeMoney()(100)`：
      * `storeMoney()` 先執行，回傳內部匿名函式；緊接著的 `(100)` 立即呼叫它，`price` = 100。
      * `money` 由 1000 變成 1100，印出 `1100`。
      * 回傳的函式沒有被任何變數保存，執行完後無法再被存取，這個 `money` 會被垃圾回收機制釋放（參考 1-8）。
   3. `var MingMoney=storeMoney()`：
      * 再次呼叫 `storeMoney`，建立一個**全新的**執行環境，其中的 `money` 重新從 1000 開始（與步驟 2 的 `money` 無關）。
      * `storeMoney` 執行完畢，執行環境從堆疊移除；但 `MingMoney` 保存了內部函式，而內部函式依照語法作用域，範圍鏈指向 `storeMoney` 的變數環境，垃圾回收機制判定其仍「可達」，所以 `money` 不會被釋放。
   4. 連續呼叫 `MingMoney(100)` 三次，每次都修改**同一個**被保存的 `money`：1000 → `1100` → `1200` → `1300`，數值會累加。
   5. `var JayMoney=storeMoney()`：又建立一個新的閉包環境，擁有**自己的** `money`（從 1000 開始），連續呼叫 `JayMoney(150)`：1000 → `1150` → `1300` → `1450`。
   * 重點：每呼叫一次 `storeMoney()` 就產生一份獨立的 `money`，`MingMoney` 與 `JayMoney` 各自保存自己的狀態、彼此互不影響，像是兩個人各自的錢包。
   * `money` 只能透過回傳的函式修改，外部無法直接讀寫（例如 `MingMoney.money` 為 `undefined`），達到保護變數的效果（私有變數的應用參考 4-5）。

#### 三、經典問題：迴圈與 setTimeout
```
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  }, 1000);
}
// 結果：3 3 3
```
* 原因：var 沒有區塊作用域，三個回呼函式共用同一個全域的 `i`；等到 setTimeout 執行時（非同步），迴圈早已結束，`i` 為 3。

解決方式：
```
// 方法 1：使用 IIFE 建立閉包，保存每一次的 i
for (var i = 0; i < 3; i++) {
  (function (j) {
    setTimeout(function () {
      console.log(j);
    }, 1000);
  })(i);
}
// 結果：0 1 2

// 方法 2：使用 let，每次迴圈都會產生新的區塊作用域
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  }, 1000);
}
// 結果：0 1 2
```

延伸：不只 setTimeout，**把函式存起來、之後才執行**都會遇到相同問題
```
function a(){
  var arr=[];
  for(var i=0;i<3;i++){ arr.push(()=>console.log(i)); }
  return arr;
}
var fns = a();
fns[0](); // 3
fns[1](); // 3
fns[2](); // 3
```
* 原因：
  1. `var i` 是**函式作用域**，整個 `a` 的執行環境中只有**一個** `i`。
  2. 迴圈只是把三個箭頭函式放進陣列，**並沒有執行** `console.log(i)`；三個函式記住的是「同一個變數 `i`」，而不是當下的值。
  3. 迴圈結束時 `i` 已經變成 3（`i<3` 不成立才跳出），`a` 回傳後，三個函式透過閉包仍能存取 `a` 的變數環境，呼叫時才去查 `i`，所以都印出 `3`。

解決方式：
```
// 方法 1（最推薦）：var 改為 let
// let 具有區塊作用域，for 迴圈每一輪都會建立新的 i，每個函式各自記住自己那一輪的 i
function a(){
  var arr=[];
  for(let i=0;i<3;i++){ arr.push(()=>console.log(i)); }
  return arr;
}

// 方法 2：IIFE 建立新的函式作用域，把當下的 i 以參數 j 傳入保存
function a(){
  var arr=[];
  for(var i=0;i<3;i++){
    (function(j){ arr.push(()=>console.log(j)); })(i);
  }
  return arr;
}

// 方法 3：函式工廠，每次呼叫 makeLog 都產生新的執行環境保存 n
function a(){
  var arr=[];
  function makeLog(n){ return ()=>console.log(n); }
  for(var i=0;i<3;i++){ arr.push(makeLog(i)); }
  return arr;
}

// 方法 4：bind 預先綁定引數（參考 4-6），把當下的值「複製」進新函式
function a(){
  var arr=[];
  for(var i=0;i<3;i++){ arr.push(console.log.bind(console, i)); }
  return arr;
}

a().forEach(fn => fn()); // 以上皆為：0 1 2
```
* 共通觀念：要讓每個函式記住**不同的值**，就必須讓每一輪都有**各自獨立的變數環境**（let 的區塊作用域、或每次呼叫函式產生新的執行環境），或直接把值複製進去（bind）。

#### 四、注意事項
閉包會讓變數持續存在記憶體中，若大量或不當使用（例如保存大型資料、DOM 節點）可能造成記憶體洩漏；不再需要時可將參考設為 `null`，讓垃圾回收機制釋放。

### 4-5 閉包進階：工廠模式與私有方法
#### 一、工廠模式（Factory Pattern）
利用函式「量產」物件，每次呼叫都回傳一個新的物件；搭配閉包，每個物件都擁有自己獨立的狀態。

範例：從 4-4 的 `storeMoney` 改寫，說明函式工廠與私有變數
```
function storeMoney(initValue){
  var money = initValue || 1000;
  return function (price){money+=price; return money}
}
var MingMoney=storeMoney(100);
console.log(MingMoney(500)); // 600
```
* **函式工廠**：
  * `storeMoney` 像一座工廠，本身不做存錢的動作，而是**生產**「存錢函式」並回傳。
  * 透過參數 `initValue` 可以客製化產品：`storeMoney(100)` 產生初始金額 100 的錢包；不傳引數時 `initValue` 為 `undefined`，`||` 會取預設值 1000。
  * 每呼叫一次工廠就建立一個新的執行環境，產出的函式各自擁有獨立的 `money`：
    ```
    var JayMoney = storeMoney();   // 從 1000 開始
    console.log(JayMoney(500));    // 1500
    console.log(MingMoney(500));   // 1100（與 JayMoney 互不影響）
    ```
* **私有變數**：
  * `money` 宣告在 `storeMoney` 內，外部無法直接讀取或修改（`MingMoney.money` 為 `undefined`，直接寫 `money` 則是 ReferenceError）。
  * 唯一能操作 `money` 的管道就是回傳的函式，這個能存取私有變數、並對外公開的函式稱為**特權方法**（Privileged Method）。
* **私有方法**：此範例只有私有「變數」；若在工廠內再宣告一個**不回傳**的函式，就成為私有方法，只能由內部呼叫：
  ```
  function storeMoney(initValue){
    var money = initValue ?? 1000;       // 改用 ??：只有 null/undefined 才取預設值
    function check(price){               // 私有方法：外部無法呼叫
      return typeof price === "number" && money + price >= 0;
    }
    return function (price){             // 特權方法：對外公開的唯一介面
      if (!check(price)) return "金額錯誤";
      money += price;
      return money;
    }
  }
  var MingMoney = storeMoney(100);
  console.log(MingMoney(500));    // 600
  console.log(MingMoney(-1000));  // 金額錯誤（餘額不可為負）
  ```
* 注意：`initValue || 1000` 在傳入 `0` 時，因為 0 是 falsy，會被當成 1000；若允許初始金額為 0，應改用 `??`（空值合併運算子）或 ES6 預設參數 `function storeMoney(initValue = 1000)`（參考 4-3）。

當工廠需要回傳**多個**方法時，就改為回傳物件：
```
function createCounter(initValue) {
  var count = initValue; // 每次呼叫都會建立新的 count
  return {
    increase: function () { return ++count; },
    decrease: function () { return --count; },
    getValue: function () { return count; },
  };
}

var counter1 = createCounter(0);
var counter2 = createCounter(100);
counter1.increase();
counter1.increase();
counter2.decrease();
console.log(counter1.getValue()); // 2
console.log(counter2.getValue()); // 99
```

#### 二、私有變數與私有方法
JavaScript（ES2022 之前）沒有 private 語法，可利用閉包模擬：
* **私有**：只宣告在外部函式內、不放入回傳物件的變數或函式，外部無法直接存取。
* **公開**：放在回傳物件中的方法，可存取私有成員，作為與外部溝通的介面。
```
function createBankAccount(owner) {
  // 私有變數
  var balance = 0;
  var records = [];

  // 私有方法
  function log(type, amount) {
    records.push(type + " " + amount + " 元，餘額 " + balance + " 元");
  }

  // 公開方法
  return {
    deposit: function (amount) {
      if (amount <= 0) return;
      balance += amount;
      log("存入", amount);
    },
    withdraw: function (amount) {
      if (amount > balance) {
        console.log("餘額不足");
        return;
      }
      balance -= amount;
      log("提出", amount);
    },
    getBalance: function () {
      return owner + " 的餘額：" + balance;
    },
    getRecords: function () {
      return records.slice(); // 回傳副本，避免外部修改私有陣列
    },
  };
}

var account = createBankAccount("小明");
account.deposit(1000);
account.withdraw(300);
account.withdraw(5000);             // 餘額不足
console.log(account.getBalance());  // 小明 的餘額：700
console.log(account.getRecords());  // ['存入 1000 元，餘額 1000 元', '提出 300 元，餘額 700 元']

console.log(account.balance);       // undefined：無法直接存取私有變數
account.log("存入", 99999);         // TypeError: account.log is not a function
```

#### 三、模組模式（Module Pattern）
工廠模式搭配 IIFE，只建立「單一」實例，常用於封裝模組：
```
var cart = (function () {
  var items = []; // 私有

  return {
    add: function (item) { items.push(item); },
    count: function () { return items.length; },
  };
})();

cart.add("紅茶");
cart.add("奶茶");
console.log(cart.count()); // 2
console.log(cart.items);   // undefined
```

#### 四、優點
1. 資料封裝：避免外部任意修改內部狀態，只能透過指定的方法操作。
2. 避免全域污染：變數不會暴露在全域。
3. 狀態獨立：每個實例都有自己的閉包環境。

### 4-6 this
`this` 是函式執行時自動產生的關鍵字，**與函式如何定義、在哪裡定義無關，只與「如何被呼叫」有關**（箭頭函式例外，它沒有自己的 this，會沿用外層的 this）。

#### 一、物件方法調用
以 `物件.方法()` 的形式呼叫時，`this` 指向「呼叫它的物件」（也就是 `.` 前面的物件）。
```
var name = "全域";
var person = {
  name: "小明",
  sayHi: function () {
    console.log(this.name);
  },
  child: {
    name: "小華",
    sayHi: function () {
      console.log(this.name);
    },
  },
};

person.sayHi();        // 小明：this 為 person
person.child.sayHi();  // 小華：this 為最接近的 person.child
```

同一個函式，呼叫方式不同，this 就不同：
```
function sayHi() {
  console.log(this.name);
}
var a = { name: "A", sayHi: sayHi };
var b = { name: "B", sayHi: sayHi };
a.sayHi(); // A
b.sayHi(); // B
```

常見陷阱：將方法賦值給變數後再呼叫，會失去原本的物件（變成簡易呼叫）
```
var fn = person.sayHi;
fn(); // 全域：this 變成全域物件（非嚴格模式）
```

#### 二、簡易呼叫
直接以 `函式()` 呼叫（Simple Call），前面沒有任何物件：
* 非嚴格模式：this 指向**全域物件**（瀏覽器為 `window`）。
* 嚴格模式：this 為 `undefined`。
* 建議：**簡易呼叫時不要使用 this**。
```
var name = "全域";
function fn() {
  console.log(this.name);
}
fn(); // 全域
```

常見情況：物件方法內的「內部函式」與「回呼函式」也是簡易呼叫
```
var name = "全域";
var obj = {
  name: "物件",
  outer: function () {
    console.log(this.name);   // 物件

    function inner() {
      console.log(this.name); // 全域：inner() 是簡易呼叫
    }
    inner();

    setTimeout(function () {
      console.log(this.name); // 全域：回呼函式由 setTimeout 以簡易呼叫方式執行
    }, 0);
  },
};
obj.outer();
```

解決方式：
```
var obj = {
  name: "物件",
  outer: function () {
    // 方法 1：先將 this 存到變數（常見命名 self、vm、that）
    var self = this;
    function inner() {
      console.log(self.name); // 物件
    }
    inner();

    // 方法 2：箭頭函式沒有自己的 this，會沿用外層 outer 的 this
    setTimeout(() => {
      console.log(this.name); // 物件
    }, 0);
  },
};
obj.outer();
```

#### 三、call, apply, bind與嚴謹模式
這三個方法都可以**明確指定**函式執行時的 this。

| 方法 | 是否立即執行 | 傳入引數的方式 | 回傳值 |
| --- | --- | --- | --- |
| `fn.call(thisArg, a, b)` | 是 | 逐一傳入 | 函式執行結果 |
| `fn.apply(thisArg, [a, b])` | 是 | 以陣列傳入 | 函式執行結果 |
| `fn.bind(thisArg, a, b)` | 否 | 逐一傳入（可預先綁定部分引數） | 綁定好 this 的新函式 |

```
function intro(age, city) {
  console.log(this.name + "，" + age + " 歲，住在" + city);
}
var person = { name: "小明" };

intro.call(person, 18, "台北");       // 小明，18 歲，住在台北
intro.apply(person, [18, "台北"]);    // 小明，18 歲，住在台北

var bound = intro.bind(person, 18);   // 綁定 this 與第一個參數，不會立即執行
bound("高雄");                         // 小明，18 歲，住在高雄
bound.call({ name: "小華" }, "台中");  // 小明，18 歲，住在台中：bind 後的 this 無法再被 call 改變
```

apply 常見用法：將陣列展開為引數
```
var nums = [3, 8, 1];
console.log(Math.max.apply(null, nums)); // 8
console.log(Math.max(...nums));          // 8（ES6 展開運算子寫法）
```

**嚴謹模式（Strict Mode）對 this 的影響**

在檔案或函式開頭加上 `'use strict'` 即可啟用嚴格模式。

| 情況 | 非嚴格模式 | 嚴格模式 |
| --- | --- | --- |
| 簡易呼叫 `fn()` | 全域物件（window） | `undefined` |
| `fn.call(null)` / `fn.call(undefined)` | 全域物件（window） | `null` / `undefined` |
| `fn.call(1)`（傳入原始型別） | 包裹成物件 `Number {1}` | 維持原始值 `1` |

```
function normal() {
  return this;
}
function strict() {
  'use strict';
  return this;
}

console.log(normal());              // Window
console.log(strict());              // undefined
console.log(normal.call(null));     // Window
console.log(strict.call(null));     // null
console.log(typeof normal.call(1)); // "object"
console.log(typeof strict.call(1)); // "number"
```
* 嚴格模式避免 this 意外指向全域物件，防止不小心透過 this 修改到全域變數。

#### 四、DOM
使用 `addEventListener` 綁定事件時，事件處理函式中的 `this` 指向**綁定事件的 DOM 元素**（等同 `event.currentTarget`）。
```
<button id="btn">按鈕</button>
<script>
  var btn = document.querySelector("#btn");

  // 一般函式：this 為綁定事件的元素
  btn.addEventListener("click", function (e) {
    console.log(this);                     // <button id="btn">按鈕</button>
    console.log(this === e.currentTarget); // true
    this.textContent = "已點擊";
  });

  // 箭頭函式：沒有自己的 this，沿用外層（此處為全域 window）
  btn.addEventListener("click", (e) => {
    console.log(this);            // Window
    console.log(e.currentTarget); // 箭頭函式中改用 e.currentTarget 取得元素
  });
</script>
```

行內事件（HTML 屬性）中的 this：
```
<!-- 屬性值中的 this 為該元素 -->
<button onclick="console.log(this)">按鈕</button>

<!-- 呼叫函式時，函式內部屬於簡易呼叫，this 為 window，需將 this 當作引數傳入 -->
<button onclick="handle(this)">按鈕</button>
<script>
  function handle(el) {
    console.log(this); // Window
    console.log(el);   // <button>
  }
</script>
```

物件方法作為事件處理函式時，this 會變成 DOM 元素，需使用 bind 綁定：
```
var app = {
  count: 0,
  add: function () {
    this.count++;
    console.log(this.count);
  },
};

btn.addEventListener("click", app.add);           // NaN：this 為 btn，btn.count 為 undefined
btn.addEventListener("click", app.add.bind(app)); // 1, 2, 3...：this 為 app
```
* 注意：`bind` 會產生新函式，若之後需要 `removeEventListener`，必須先將綁定後的函式存到變數中，才能移除同一個函式。

#### 五、this 判斷總結
| 呼叫方式 | this 指向 |
| --- | --- |
| 物件方法調用 `obj.fn()` | obj |
| 簡易呼叫 `fn()` | 非嚴格模式：全域物件；嚴格模式：undefined |
| `call` / `apply` / `bind` | 指定的物件 |
| DOM 事件 `addEventListener` | 綁定事件的元素 |
| 箭頭函式 | 沒有自己的 this，沿用定義時外層的 this |


#### 六、this課後練習
請寫出以下各題的執行結果並說明原因
##### 第1題
```
function callName(){
  console.log(this.name);
}
var a = {
  name: 'b',
  callName: callName,
  c: {
    name: 'd',
    callName: callName
  }
}
console.log(a.callName(), a.c.callName());
```
##### 第2題
```
function callName(){
  console.log(this.name);
}
var a = {
  name: 'b',
  callName: callName,
  c: {
    name: 'd',
    callName: callName
  }
}
var w=a;
var x=a.c;
var y=w.callName();
var z=x.callName;
console.log(y, z);
```
##### 第3題
```
var name="Ming";
function namefu(){
  console.log(this.name);
}
var a={ name: "Wang", myname: namefu };
namefu.name= "Mei"
a.myname();
```

##### 第4題
```
var name='Ming';
var obj={
  x:()=>{
    name: 'Wang';
    console.log(this.name);
  },
  y:'Mei',
}
obj.x();
```
##### 第5題
```
var name='Ming';
var obj={
  x:()=>{
    name='Wang';
    console.log(this.name);
  },
  y:'Mei',
}
obj.x();
```

##### 第6題
```
var name='Ming';
var obj={
  x: {
    name: 'Wang',
    myName: function(){
      console.log(this.name);
      setTimeout(()=>{console.log(this.name);}, 500)
    }
  },
  y:'Mei',
  name: 'Hu',
}
obj.x.myName();
```

##### 第7題
```
function callName(name){
  console.log(this.name, name);
}
var name="Wang";
var x={name: "Mei"};
callName(undefined, "Ming");
callName.call(x, "Ming");
```
##### 第8題
```
var name="Wang";
var x={
  name: "Mei",
  callName: function(){
    console.log(this.name);
  }
};
(()=>{
  var a = x.callName;
  a();
})();
```
##### 第9題
```
var name="Wang";
function callName(name){
  console.log(this.name);
}
var obj={
  name: "Mei",
  family: {name: "Wang"}
}

callName.name="Power";
var a=callName.bind(obj, "Chen");
a();
```


##### 答案
> 前提：以下皆假設在瀏覽器、非嚴格模式的全域環境下執行（全域 `this` 為 `window`，全域 `var` 會成為 `window` 的屬性）。
1. 輸出：`b` → `d` → `undefined undefined`
   * `console.log` 會先計算參數：`a.callName()` 以物件方法調用，`this` 為 `a`，印出 `b`；`a.c.callName()` 的 `this` 為 `a.c`，印出 `d`。
   * 兩個函式都沒有 `return`，回傳值皆為 `undefined`，所以最外層印出 `undefined undefined`。

2. 輸出：`b` → `undefined ƒ callName(){ console.log(this.name); }`
   * `w` 與 `a` 指向同一個物件，`w.callName()` 的 `this` 為 `a`，印出 `b`，回傳 `undefined`，所以 `y` 為 `undefined`。
   * `z = x.callName` 只是取得函式本身，並沒有呼叫，所以 `z` 是函式；印出 `undefined` 與函式內容。

3. 輸出：`Wang`
   * `a.myname()` 以物件方法調用，`this` 為 `a`，`this.name` 為 `Wang`。
   * 全域的 `name="Ming"` 只有在簡易呼叫 `namefu()` 時才會印出。
   * `namefu.name = "Mei"` 不會生效：函式的 `name` 屬性是唯讀的（非嚴格模式下靜默失敗），且它與 `this.name` 無關。

4. 輸出：`Ming`
   * 箭頭函式內的 `name: 'Wang';` 不是賦值，而是「標籤（label）＋字串運算式」，不會改變任何變數。
   * 箭頭函式沒有自己的 `this`，沿用定義時外層（全域）的 `this`，即 `window`，`window.name` 為 `Ming`。

5. 輸出：`Wang`
   * 箭頭函式的 `this` 為 `window`；函式內 `name='Wang'` 沒有宣告，會修改全域變數 `name`（即 `window.name`），所以印出 `Wang`。

6. 輸出：`Wang` →（500ms 後）`Wang`
   * `obj.x.myName()` 以物件方法調用，`this` 為「點前面的物件」`obj.x`（不是 `obj`），所以印出 `Wang` 而非 `Hu`。
   * `setTimeout` 中的箭頭函式沒有自己的 `this`，沿用外層 `myName` 的 `this`（`obj.x`），500ms 後再印出 `Wang`。
   * 若改成一般函式 `setTimeout(function(){...})`，回呼函式為簡易呼叫，`this` 為 `window`，會印出 `Ming`。

7. 輸出：`Wang undefined` → `Mei Ming`
   * `callName(undefined, "Ming")` 為簡易呼叫，`this` 為 `window`，`this.name` 為 `Wang`；第一個參數 `undefined` 傳給 `name`，`"Ming"` 沒有對應的參數被忽略。
   * `callName.call(x, "Ming")`：`call` 的第一個參數指定 `this` 為 `x`，`this.name` 為 `Mei`；`"Ming"` 傳給參數 `name`。

8. 輸出：`Wang`
   * `var a = x.callName` 只是把函式取出指定給變數，與 `x` 失去關聯。
   * `a()` 是簡易呼叫，`this` 為 `window`，印出全域的 `Wang`。外層包著的箭頭函式 IIFE 不影響 `a()` 的呼叫方式。

9. 輸出：`Mei`
   * `bind(obj, "Chen")` 回傳一個 `this` 永久綁定為 `obj` 的新函式，`"Chen"` 預先傳入參數 `name`。
   * `a()` 執行時 `this` 為 `obj`，`this.name` 為 `Mei`。
   * `callName.name = "Power"` 同第3題，函式 `name` 屬性唯讀，不會生效也與 `this` 無關；`family` 屬性在此題沒有作用。


### 4-7 函式陷阱題與課後練習
```
console.clear();
// 更多題目：https://www.facebook.com/levelhunt/?locale=zh_TW

var myName="Global";

// Question 1
var person={
  myName: "Ming",
  getName: function(){
    return this.myName;
  }
}
var getName = person.getName;
console.log(getName());

// Question 2
var obj={
  myName: "Ming",
  fn: function(a, b, c){
    return [this.myName, a, b, c];
  }
}
var fnA=obj.fn;
var fnB=fnA.bind(null, 0);
console.log(fnB(1,2));  

// 若要將輸出改成 [null, 0, 1, 2]的方式，將函式改為 'use strict'，並將this.myName改成this

// Question 3
var foo={
  myName: "Ming",
  bar: function(){
    return this.myName;
  }
}
console.log(foo.bar()); 
console.log(foo.bar); 
console.log((foo.bar=foo.bar)()); 
console.log((false||foo.bar)());  

var b={};
Object.defineProperty(b, 'myName', {value: "Mary", writable: false});
b.myName="John";
console.log(b.myName); 

// Question 4
var arr=['1','2','3'].map(parseInt);
console.log(arr); // [1,null,null]

// Question 5
// function a(fu){
//   fu();
// }
// a(myName); 

// Question 6
// a, b, c, d分別是表達式還是陳述式
function a(){
  console.log(myName);
}
function b(){
  return myName;
}
var c=function (){
  console.log(myName);
}
var d;


// Question 7
(function(){
  console.log(myName);
}());

// Question 8
function myMoney(storage){
  var money=storage||10000;
  return function (price){
    return{
      fx: function(){ return console.log(money); },
      fy: function(price){
        if(money<price) return console.log("A");
        if(!money<0) return money=money-price;
        return console.log("B");
      }
    }
  }
}

var John = myMoney(9000);
var Mary = myMoney(10000);
var Tony = myMoney(12000);
for(let i=1;i<=2;i++){
  John().fy(8000);
  John().fx();
  Mary().fy(8000);
  Mary().fx();
  Tony().fy(8000);
  Tony().fx();
}


// Question 9
var a=1;
var obj={x: function(){a=2; console.log(this.name)}, y:2, a:3}
obj.x();  

// Question 10
var name="Kent";
function sayHi(){
  var name="Argus";
  console.log(this.name);
}
var objx={name: "Peter", callName: sayHi};
objx.callName(); 

// Question 11
function g(g){ g(); }
function h(h){ h(); }
function i(i){ console.log("i"); }
g(h(i));


```

#### 答案
1. Global
2. ["Global",0,1,2]
3. Ming, function(){ return this.myName; }, Global, Global, Mary
4. [1,null,null]
5. Message: fu is not a funciton
6. a-陳述式、b-陳述式、c-表達式、d-陳述式
7. Global
8. 
"B"
9000
"B"
10000
"B"
12000
"B"
9000
"B"
10000
"B"
12000
9. undefined
10. Peter
11. "i" g is not a function

#### 解析
> 前提：同 4-6，以下皆假設在瀏覽器、非嚴格模式的全域環境下執行（全域 `this` 為 `window`，全域 `var` 會成為 `window` 的屬性），因此 `this.myName` 在全域下等同 `window.myName`，值為 `"Global"`。

##### Question 1：方法被取出後失去 this
```
var getName = person.getName;
console.log(getName());   // Global
```
* `person.getName` 沒有加上 `()`，只是把函式「取出來」指定給變數 `getName`，此時函式與 `person` 已經沒有關聯。
* `this` 不是由函式定義的位置決定，而是由「呼叫方式」決定。`getName()` 前面沒有物件，屬於簡易呼叫，`this` 指向 `window`。
* `window.myName` 為全域的 `"Global"`，所以印出 `Global`；若寫成 `person.getName()` 才會印出 `Ming`。

##### Question 2：bind 傳入 null 與預先傳入參數
```
var fnA = obj.fn;
var fnB = fnA.bind(null, 0);
console.log(fnB(1,2));    // ["Global", 0, 1, 2]
```
* `fnA = obj.fn` 同 Question 1，函式被取出，與 `obj` 失去關聯。
* `bind(null, 0)` 做了兩件事：
  1. 綁定 `this` 為 `null`：非嚴格模式下，`this` 若被指定為 `null` 或 `undefined`，會自動被替換成全域物件 `window`，所以 `this.myName` 為 `"Global"`。
  2. 預先傳入第一個參數：`a` 被固定為 `0`（又稱部分套用 Partial Application）。
* 呼叫 `fnB(1, 2)` 時，新傳入的引數會接在預先傳入的引數後面，所以 `b = 1`、`c = 2`，結果為 `["Global", 0, 1, 2]`。
* 若在函式內加上 `'use strict'`，`this` 不會被替換成 `window`，會維持 `null`；此時再存取 `this.myName` 會因為 `null.myName` 拋出 TypeError，所以題目中提到要將 `this.myName` 改成 `this`，輸出才會是 `[null, 0, 1, 2]`。
```
var obj={
  myName: "Ming",
  fn: function(a, b, c){
    'use strict';
    return [this, a, b, c];
  }
}
console.log(obj.fn.bind(null, 0)(1, 2)); // [null, 0, 1, 2]
```

##### Question 3：運算式回傳的函式會失去 this、唯讀屬性
```
console.log(foo.bar());                  // Ming
console.log(foo.bar);                    // ƒ (){ return this.myName; }
console.log((foo.bar=foo.bar)());        // Global
console.log((false||foo.bar)());         // Global
```
* `foo.bar()`：物件方法調用，`this` 為 `foo`，印出 `Ming`。
* `foo.bar`：沒有呼叫，印出函式本身。
* `(foo.bar=foo.bar)()`：
  * 賦值運算式 `=` 本身也會回傳一個值，也就是「右邊的函式」。
  * 括號內運算完的結果只是一個單純的函式值，已經不是 `foo.bar` 這種「物件.屬性」的參考，所以接著 `()` 呼叫時屬於簡易呼叫，`this` 為 `window`，印出 `Global`。
* `(false||foo.bar)()`：
  * `||` 會回傳第一個轉型為 true 的值，`false` 為假，所以回傳 `foo.bar` 這個函式。
  * 同上，回傳的是單純的函式值，呼叫時 `this` 為 `window`，印出 `Global`。
* 判斷技巧：只看呼叫的那一刻，`()` 的正前方是不是「物件.方法」的形式；只要經過賦值、`||`、`&&`、逗號 `,` 等運算，就會失去 `this`。
  * 注意：單純加上括號 `(foo.bar)()` 不算運算，`this` 仍然是 `foo`，會印出 `Ming`。

```
var b={};
Object.defineProperty(b, 'myName', {value: "Mary", writable: false});
b.myName="John";
console.log(b.myName);    // Mary
```
* `Object.defineProperty` 可以定義屬性的特性，`writable: false` 表示此屬性唯讀、不能被修改。
* `b.myName = "John"` 在非嚴格模式下會「靜默失敗」（不報錯但也不會生效），所以仍然印出 `Mary`。
* 若在嚴格模式下，則會拋出 `TypeError: Cannot assign to read only property 'myName' of object`。
* 這也是 4-6 第3題 `namefu.name = "Mei"` 不生效的原因：函式的 `name` 屬性預設就是 `writable: false`。

##### Question 4：map 搭配 parseInt
```
var arr=['1','2','3'].map(parseInt);
console.log(arr);         // [1, NaN, NaN]
```
* `map` 的回呼函式會收到三個引數：`(元素, 索引, 原陣列)`。
* `parseInt(string, radix)` 的第二個參數是「進位制」(radix)，可接受 2~36，或 0（代表自動判斷，一般視為 10 進位）。
* 因此實際執行的是：

| 呼叫 | 說明 | 結果 |
| --- | --- | --- |
| `parseInt('1', 0)` | radix 為 0，視為 10 進位 | `1` |
| `parseInt('2', 1)` | radix 為 1，不在 2~36 範圍內 | `NaN` |
| `parseInt('3', 2)` | 2 進位只有 0 和 1，`'3'` 無法解析 | `NaN` |

* 在瀏覽器 console 會顯示 `[1, NaN, NaN]`；答案中的 `[1, null, null]` 是經過 `JSON.stringify` 處理後的結果（JSON 不支援 `NaN`，會轉成 `null`），部分線上編輯器的 console 也會以這種方式顯示。
* 正確寫法：明確指定進位制，或改用 `Number`。
```
['1','2','3'].map((item) => parseInt(item, 10)); // [1, 2, 3]
['1','2','3'].map(Number);                       // [1, 2, 3]
```

##### Question 5：把非函式當成函式呼叫
```
function a(fu){
  fu();
}
a(myName);                // TypeError: fu is not a function
```
* `myName` 的值是字串 `"Global"`，傳入後參數 `fu = "Global"`。
* `fu()` 試圖呼叫一個字串，所以拋出 `TypeError: fu is not a function`。
* 若要正確執行，應傳入函式本身，例如 `a(function(){ console.log(myName); })`。
* 補充：若直接把本題的註解拿掉、和其他題目放在同一個檔案執行，結果會不同。因為函式陳述式會被提升，Question 6 也宣告了 `function a()`，後宣告的會覆蓋先宣告的，所以 `a(myName)` 實際呼叫的是 Question 6 的 `a`，會印出 `Global` 而不會報錯。這也說明了在同一個作用域中重複使用變數名稱的風險。

##### Question 6：函式陳述式與函式表達式
* 判斷方式：以 `function` 關鍵字「開頭」的那一行是函式陳述式；`function` 出現在 `=` 右邊等「需要一個值」的位置，則是函式表達式。
* `a`：函式陳述式（具名函式），會整個被提升，可在宣告前呼叫。
* `b`：函式陳述式，函式內有沒有 `return` 不影響它是陳述式。
* `c`：`var c = ...` 這一整行是變數宣告的陳述式，但等號右邊的 `function (){...}` 是函式表達式（匿名函式）。只有變數 `c` 會被提升（值為 `undefined`），在賦值前呼叫 `c()` 會出現 `TypeError: c is not a function`。
* `d`：`var d;` 是變數宣告陳述式，跟函式無關，值為 `undefined`。
* 補充：陳述式不會回傳值，表達式（運算式）會產生一個值，這也是 Question 3 `(foo.bar=foo.bar)` 會回傳函式的原因。

##### Question 7：立即函式
```
(function(){
  console.log(myName);
}());                     // Global
```
* 用括號包住函式，讓 `function` 不在開頭，就會變成函式表達式，後面再加上 `()` 即可立即執行（見 4-2）。
* `(function(){}())` 與 `(function(){})()` 兩種寫法效果相同。
* 函式內沒有宣告 `myName`，依照範圍鏈往外層（全域）尋找，找到 `"Global"`。

##### Question 8：閉包與運算子優先順序
```
function myMoney(storage){
  var money=storage||10000;
  return function (price){
    return{
      fx: function(){ return console.log(money); },
      fy: function(price){
        if(money<price) return console.log("A");
        if(!money<0) return money=money-price;
        return console.log("B");
      }
    }
  }
}
```
* 結構分析：
  1. `myMoney(9000)` 執行後，`money = 9000`，回傳一個內層函式指定給 `John`，此時形成閉包，`money` 被保存在 `John` 自己的環境中。
  2. `John`、`Mary`、`Tony` 各自呼叫一次 `myMoney`，所以擁有三個互相獨立的 `money`（9000、10000、12000）。
  3. 每次執行 `John()` 都會回傳一個「新的」物件，但物件中的 `fx`、`fy` 參考的都是同一個 `money`，所以 `fy` 修改金額後，下一次 `John().fx()` 讀到的是修改後的值。
  4. 外層 `function (price)` 的參數 `price` 沒有被使用，且被 `fy` 自己的參數 `price` 遮蔽。
* 以 `John().fy(8000)` 逐行判斷：
  1. `money < price` → `9000 < 8000` 為 `false`，不執行。
  2. `!money < 0`：`!` 的優先順序比 `<` 高，所以實際上是 `(!money) < 0`：
     * `!9000` → `false`
     * `false < 0` → 比較時 `false` 轉型為 `0`，`0 < 0` 為 `false`，不執行扣款。
     * 只要 `money` 不為 0，`!money` 永遠是 `false`；就算是 0，`!0` 為 `true`，`1 < 0` 仍為 `false`。所以這行條件「永遠不成立」，金額永遠不會被扣除。
  3. 執行 `return console.log("B")`，印出 `B`。
* `John().fx()` 印出 9000，Mary、Tony 同理；迴圈執行兩次，金額都沒有改變，所以輸出兩輪相同的 `B 9000 B 10000 B 12000`。
* 若要做到預期的扣款效果，條件應改為 `if(!(money<price))` 或 `if(money>=price)`，此時輸出會變成：

| 輪次 | John | Mary | Tony |
| --- | --- | --- | --- |
| 第1輪 | 扣款後 money 為 1000，印出 `1000` | `2000` | `4000` |
| 第2輪 | `1000 < 8000`，印出 `A`、`1000` | `A`、`2000` | `A`、`4000` |

##### Question 9：變數與物件屬性的差別
```
var a=1;
var obj={x: function(){a=2; console.log(this.name)}, y:2, a:3}
obj.x();                  // undefined
```
* `obj.x()` 為物件方法調用，`this` 為 `obj`。
* `obj` 只有 `x`、`y`、`a` 三個屬性，沒有 `name`，存取不存在的屬性會得到 `undefined`。
* `a=2` 修改的是「變數」：函式內沒有宣告 `a`，沿著範圍鏈找到全域變數 `a`，所以全域 `a` 從 1 變成 2。
* 物件的屬性 `a: 3` 不在範圍鏈上，必須透過 `this.a` 或 `obj.a` 才能存取，所以 `obj.a` 仍然是 3。

##### Question 10：this 與範圍鏈
```
var name="Kent";
function sayHi(){
  var name="Argus";
  console.log(this.name);
}
var objx={name: "Peter", callName: sayHi};
objx.callName();          // Peter
```
* `objx.callName()` 為物件方法調用，`this` 為 `objx`，`this.name` 為 `Peter`。
* 函式內的區域變數 `name = "Argus"` 只有在直接寫 `name`（透過範圍鏈查找變數）時才會用到；`this.name` 是「查找物件的屬性」，兩者是不同的查找機制。
* 全域的 `"Kent"` 只有在簡易呼叫 `sayHi()` 時才會印出。

| 寫法 | 查找方式 | 結果 |
| --- | --- | --- |
| `this.name` | 物件屬性，由呼叫方式決定 | `Peter` |
| `name` | 範圍鏈，由定義位置決定 | `Argus` |

##### Question 11：參數名稱遮蔽函式名稱
```
function g(g){ g(); }
function h(h){ h(); }
function i(i){ console.log("i"); }
g(h(i));                  // "i" → TypeError: g is not a function
```
* 呼叫函式前，會先計算引數的值，所以先執行內層的 `h(i)`：
  1. 參數 `h` 接收了函式 `i`，在函式 `h` 內部，參數 `h` 會遮蔽外層的函式名稱 `h`。
  2. `h()` 實際上呼叫的是函式 `i`，印出 `i`。
  3. 函式 `h` 沒有 `return`，回傳 `undefined`。
* 接著執行 `g(undefined)`：
  1. 參數 `g` 為 `undefined`，同樣遮蔽了外層的函式名稱 `g`。
  2. `g()` 等於呼叫 `undefined()`，拋出 `TypeError: g is not a function`。
* 重點：函式內的參數就是區域變數，名稱與外層相同時會優先使用參數（遮蔽 Shadowing），實務上應避免參數與函式同名。

