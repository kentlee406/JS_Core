# JavaScript 核心篇 單選題 答案與詳解

> 題目請見 [questions.md](questions.md)。除非特別說明，皆假設在瀏覽器、非嚴格模式的一般 `<script>` 環境執行。

## 一、答案總覽

| 題號 | 答案 | 題號 | 答案 | 題號 | 答案 | 題號 | 答案 | 題號 | 答案 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 1 | B | 11 | D | 21 | D | 31 | B | 41 | A |
| 2 | C | 12 | C | 22 | C | 32 | C | 42 | B |
| 3 | A | 13 | D | 23 | D | 33 | A | 43 | C |
| 4 | C | 14 | B | 24 | A | 34 | D | 44 | A |
| 5 | B | 15 | C | 25 | D | 35 | B | 45 | C |
| 6 | C | 16 | A | 26 | A | 36 | D | 46 | B |
| 7 | D | 17 | C | 27 | D | 37 | A | 47 | D |
| 8 | B | 18 | D | 28 | A | 38 | B | 48 | A |
| 9 | A | 19 | B | 29 | B | 39 | C | 49 | B |
| 10 | B | 20 | A | 30 | C | 40 | D | 50 | C |

---

## 二、逐題詳解

### 第 1 題　答案：B（第 1 章）
- 直譯：逐行讀取、即時翻譯執行，優點是開發效率與靈活性高，缺點是執行速度慢、錯誤較晚發現。JavaScript 屬於直譯。
- A 錯：JavaScript 是直譯式，不是先編譯成執行檔。
- C 錯：編譯式語言的缺點才是跨平台彈性較低、每次修改都必須重新編譯。
- D 錯：「早期偵測錯誤」是編譯式語言的優點。

### 第 2 題　答案：C（第 1 章）
- LHS 是「賦值目標」找不到或不合法。嚴格模式下對未宣告的變數賦值（`target = 100`）會拋出 `ReferenceError`，屬於 LHS。
- A：讀取從未宣告的變數，是 RHS 的 `ReferenceError`。
- B：RHS 成功取得 `42`，但 `42` 不能被呼叫，是 RHS 的 `TypeError`。
- D：對 `null` 讀取屬性，是 RHS 的 `TypeError`。

### 第 3 題　答案：A（第 1 章）
- 函式與變數同名時，函式宣告的提升優先權較高，所以第一行 `typeof x` 為 `"function"`。
- 執行到 `var x = 100` 時 `x` 被重新賦值為 `100`，所以最後印出 `100`。

### 第 4 題　答案：C（第 1 章）
- JavaScript 採用靜態（語法）作用域：變數的作用域在程式碼寫好時就決定，而不是呼叫時決定。
- `fn1` 定義在全域，其外層作用域是全域，即使在 `fn2` 內被呼叫，仍然取得全域的 `value`，所以印出「全域變數」。

### 第 5 題　答案：B（第 2 章）
- `1 < 2 < 3`：先算 `1 < 2` 得 `true`，再算 `true < 3`，`true` 轉成 `1`，`1 < 3` 為 `true`。
- `3 > 2 > 1`：先算 `3 > 2` 得 `true`，再算 `true > 1`，即 `1 > 1`，為 `false`。
- 要表達數學的連續比較，應寫成 `3 > 2 && 2 > 1`。

### 第 6 題　答案：C（第 2 章）
- 第一行結尾沒有分號，而下一行以 `(` 開頭，ASI 不會介入（`10 (a+10)` 在語法上合法），所以被解析成 `var a = 10(a+10).toString();`。
- 這是把數字 `10` 當成函式呼叫，執行時拋出 `TypeError: 10 is not a function`。
- 修正方式：第一行補上分號。

### 第 7 題　答案：D（第 2 章）
- Falsy 值有 `false`、`0`（含 `-0`、`0n`）、`""`、`null`、`undefined`、`NaN`。
- `'0'`、`'false'` 是非空字串，`[]` 是物件，全都是 Truthy。

### 第 8 題　答案：B（第 2 章）
- `score || 10`：`0` 是 Falsy，所以回傳右側的 `10`。
- `score ?? 10`：只有 `null` 或 `undefined` 才取右側，`0` 不是，所以保留 `0`。

### 第 9 題　答案：A（第 3 章）
- `{...a}` 是淺拷貝，會建立新的第一層物件。
- 第一層的 `x` 是原始值，`b.x = 99` 不影響 `a.x`，仍為 `1`。
- 第二層的 `y` 只複製了參考，`b.y.z = 88` 會同步改到 `a.y.z`，所以是 `88`。

### 第 10 題　答案：B（第 3 章）
- `var b = a` 後，a、b 指向同一物件（0x01）。
- `a.x = { x: 2 }` 修改的是 0x01 的屬性，b 也看得到。
- `a.y = a = { y: 1 }`：先解析左側 `a.y` 的目標物件是 0x01，再由右往左賦值，`a` 改指向新物件 `{ y: 1 }`，並把它設為 0x01 的 `y`。
- 結果：`a` 為 `{ y: 1 }`；`b` 仍指向 0x01，為 `{ x: { x: 2 }, y: { y: 1 } }`。

### 第 11 題　答案：D（第 3 章）
- JavaScript 是 Call by Sharing：傳入的是參考位址的副本。
- `obj.x = 100` 透過參考修改原物件，外部看得到。
- `obj = { y: 200 }` 只是讓參數指向新物件，與外部 `myObj` 斷開，不影響外部。
- 所以 `myObj` 為 `{ x: 100 }`。

### 第 12 題　答案：C（第 3 章）
- `JSON.parse(JSON.stringify(obj))` 的限制：無法複製 Function、`undefined`、Symbol，也無法處理循環引用（會拋出錯誤）。
- A 錯：Function 會被忽略。B 錯：值為 `undefined` 的屬性會被丟掉。D 錯：Symbol 屬性不會被複製。

### 第 13 題　答案：D（第 4 章）
- 創造階段的順序：參數 `a` 先被賦值為 `1`，函式宣告 `function a(){}` 被完整提升並覆蓋參數，`var a;`（沒有賦值）發現 `a` 已存在，直接被忽略，不會重設為 `undefined`。
- 所以前兩次 `console.log(a)` 都是函式 `ƒ a(){}`；執行 `a = 2` 後，第三次印出 `2`。

### 第 14 題　答案：B（第 4 章）
- 每呼叫一次 `storeMoney()` 都會產生一份獨立的 `money`（閉包）。
- `A` 的 `money` 從 1000 開始：1000 → 1100 → 1200。
- `B` 擁有自己的 `money`，從 1000 開始：1000 + 150 = 1150，與 `A` 互不影響。

### 第 15 題　答案：C（第 4 章）
- `var` 沒有區塊作用域，三個回呼共用同一個 `i`。
- `setTimeout` 是非同步，等回呼執行時迴圈早已結束，`i` 為 `3`（`i < 3` 不成立才跳出）。
- 解法：改用 `let`，或用 IIFE / 函式工廠保存每一輪的值。

### 第 16 題　答案：A（第 4 章）
- `fn = obj.getName` 只是取出函式，`fn()` 是簡易呼叫，`this` 為 `window`，得到全域的「全域」。
- `obj.getName()` 是物件方法調用，`this` 為 `obj`，得到「物件」。
- `(false || obj.getName)()`：`||` 回傳的是單純的函式值，已失去 `obj` 的關聯，呼叫時屬於簡易呼叫，`this` 為 `window`，得到「全域」。

### 第 17 題　答案：C（第 4 章）
- `bind(person, 18)` 回傳一個 `this` 永久綁定為 `person` 的新函式，並預先傳入第一個參數 `age = 18`。
- 之後再用 `call` 指定其他 `this` 是無效的，`this` 仍為 `person`；`"台中"` 傳給 `city`。
- 輸出：`小明，18 歲，住在台中`。

### 第 18 題　答案：D（第 4 章）
- `map` 的回呼會收到 `(元素, 索引, 原陣列)`，所以實際執行的是 `parseInt('1', 0)`、`parseInt('2', 1)`、`parseInt('3', 2)`。
- `radix` 為 0 視為 10 進位 → `1`；`radix` 為 1 不在 2～36 範圍 → `NaN`；2 進位無法解析 `'3'` → `NaN`。
- 結果為 `[1, NaN, NaN]`。

### 第 19 題　答案：B（第 4 章）
- ES6 預設參數只有在引數為 `undefined`（未傳入或明確傳入 `undefined`）時才會觸發。
- `null` 不會觸發預設值，所以第三個是 `Hello null`。

### 第 20 題　答案：A（第 5 章）
- 口訣：**實例.__proto__ === 建構函式.prototype**，所以 A 為 `true`。
- B：`prototype` 是函式才有的屬性，實例 `dog1.prototype` 為 `undefined`。
- C：`Dogs.__proto__` 是 `Function.prototype`，不是 `Dogs.prototype`。
- D：`Dogs.prototype.__proto__` 是 `Object.prototype`，不是 `Function.prototype`。

### 第 21 題　答案：D（第 5 章）
- 忘記寫 `new`，`Dogs("bibi")` 變成簡易呼叫：非嚴格模式下 `this` 指向 `window`，屬性被寫到全域物件上。
- 函式沒有 `return`，所以 `d` 為 `undefined`。
- 一般函式本來就可以被當成建構函式，不會出現 `is not a constructor`（箭頭函式才會）。

### 第 22 題　答案：C（第 5 章）
- `Object.create(Animals.prototype)` 建立一個以 `Animals.prototype` 為原型的新物件，當作 `Dogs.prototype`，同時串接原型鍊；因為整個 prototype 被換掉，要補回 `constructor`。
- A 錯：兩者變成同一個物件，在 Dogs 新增方法會影響 Animals 的實例。
- B 錯：把建構函式本身當成原型，原型鍊不正確。
- D 錯：`Animals.call(Dogs)` 只是以 `Dogs` 函式當 `this` 執行 Animals，與原型繼承無關。

### 第 23 題　答案：D（第 5 章）
- `Object.create(person)` 建立一個空物件並把原型設為 `person`，所以印出 `family` 只顯示自身屬性，為 `{}`。
- `family.name` 自身找不到，沿原型鍊在 `person` 找到 `"father"`。
- `name` 不是自身屬性，`hasOwnProperty("name")` 為 `false`。

### 第 24 題　答案：A（第 6 章）
- `defineProperty` 沒有指定的特徵預設都是 `false`：`writable`、`enumerable`、`configurable` 皆為 `false`。
- `o.id = 2` 在非嚴格模式下靜默失敗（嚴格模式會 TypeError），仍為 `1`。
- `delete o.id` 因 `configurable: false` 失敗，屬性仍在。
- `enumerable: false`，所以 `Object.keys(o)` 為 `[]`。

### 第 25 題　答案：D（第 6 章）
- `Object.seal` 等於「禁止擴充 + 所有屬性 `configurable` 設為 `false`」，可以修改既有屬性的值，但不能新增、刪除，也不能再調整特徵。
- 若要連值都不能改，需使用 `Object.freeze`。

### 第 26 題　答案：A（第 6 章）
- `for...in`：自己與原型鍊上「可列舉」的屬性，所以是 name、family（secret 不可列舉）。
- `Object.keys`：只含自己、可列舉的屬性，所以只有 name。
- `Object.getOwnPropertyNames`：自己所有的屬性（不管可不可列舉），所以是 name、secret。

### 第 27 題　答案：D（第 6 章）
- Vue 2 用 `Object.defineProperty` 改寫既有屬性為 getter/setter，只能針對已存在的屬性，因此新增、刪除屬性、以索引改陣列都偵測不到，需改用 `Vue.set` / `Vue.delete`。
- Vue 3 改用 `Proxy` 代理整個物件，可以攔截新增、刪除等操作。

### 第 28 題　答案：A（第 7 章）
- `let` 也有提升，但提升後不初始化，從區塊開頭到宣告之間是暫時性死區（TDZ）。
- 區塊內已有自己的 `x`，所以不會去讀外層的 `'外層'`，在 TDZ 存取會拋出 `ReferenceError: Cannot access 'x' before initialization`。

### 第 29 題　答案：B（第 7 章）
- 第一輪印出 `0`，接著執行 `i++`，等於重新指派 `const`，拋出 `TypeError: Assignment to constant variable.`，迴圈中斷。
- 這是執行階段錯誤，不是 SyntaxError（`const` 有初始化，語法合法）。

### 第 30 題　答案：C（第 7 章）
- `const` 鎖住的是變數與參考之間的綁定，不是物件內容，所以 `obj.b = 2` 可以成功，並印出 `{ a: 1, b: 2 }`。
- `obj = {...}` 是重新指派，拋出 `TypeError: Assignment to constant variable.`（執行階段錯誤，所以前面的 `console.log` 已經執行）。

### 第 31 題　答案：B（第 7 章）
- 全域的 `var` 會成為 `window` 的屬性，所以 `window.a` 為 `1`。
- 全域的 `let`、`const` 不會掛到 `window`，所以 `window.b` 為 `undefined`。

### 第 32 題　答案：C（第 8 章）
- 箭頭函式沒有 `prototype`，也沒有 `[[Construct]]`，使用 `new` 會拋出 `TypeError: xxx is not a constructor`。
- A 錯：箭頭函式沒有自己的 `arguments`，會取用外層的。
- B 錯：`this` 在定義時就固定，`call`、`apply`、`bind` 無法改變它。
- D 錯：箭頭函式的 `this` 不是該物件，不適合當物件方法。

### 第 33 題　答案：A（第 8 章）
- 箭頭函式後的 `{ }` 會被當成函式本體，`name: "Ming"` 是「標籤 + 字串運算式」，函式沒有 `return`，所以 `a()` 為 `undefined`。
- 要回傳物件字面值需用 `( )` 包起來，`b()` 才會回傳 `{ name: "Ming" }`。

### 第 34 題　答案：D（第 8 章）
- `f1` 內的箭頭函式沒有自己的 `this`，沿用 `f1` 被呼叫當下的 `this`，即 `p`，所以印出 `p.n = 2`。
- `f2` 內的傳統函式作為 `setTimeout` 的回呼，是簡易呼叫，`this` 為 `window`，`window.n` 為 `1`。
- 兩個計時器延遲相同，依登記順序執行，所以是 `2`、`1`。

### 第 35 題　答案：B（第 8 章）
- 箭頭函式的優先順序比 `||` 低，不能直接當作 `||` 右邊的運算元，`n || ()=>1` 會被解析成「`(n || ())` => 1」，造成 `SyntaxError: Malformed arrow function parameter list`。
- 正確寫法：`const b = n || (() => 1);`（結果為 `10`）。

### 第 36 題　答案：D（第 9 章）
- `map` 回傳的是陣列，放進樣板字面值時會呼叫 `toString()`，以逗號連接各元素，所以兩個 `<li>` 之間會多出逗號。
- 解法：`.map(...).join("")`。

### 第 37 題　答案：A（第 9 章）
- 標籤樣板的第一個參數是被 `${}` 切開的字串片段陣列，其餘參數依序為每個 `${}` 的運算結果。
- `${}` 在最前或最後時，對應位置為空字串，所以 `strings` 為 `["", "+", "+", ""]`，`arg` 為 `[1, 2, 3]`。
- 規則：`strings.length` 永遠等於 `arg.length + 1`。

### 第 38 題　答案：B（第 10 章）
- 先執行同步程式：`1`、Promise 的 executor 立即執行印出 `4`、最後 `6`。
- 同步結束後，先清空微任務（Promise 的 `.then`），依登記順序印出 `3`、`5`。
- 最後才是巨集任務 `setTimeout`，印出 `2`。
- 優先順序：同步程式 > 微任務 > 巨集任務。

### 第 39 題　答案：C（第 10 章）
1. `Promise.resolve(1)` → `x + 1` 得到 `2`。
2. 下一個 `.then` 丟出 `Error("oops 2")`，Promise 變成 rejected。
3. `.then(x => console.log("A", x))` 被跳過，錯誤繼續往下傳。
4. `.catch` 印出 `B oops 2` 並回傳 `10`，視為錯誤已處理，後面恢復為 fulfilled。
5. 印出 `C 10`，此回呼沒有 return，往後傳 `undefined`。
6. `.finally` 印出 `D`，回傳值被忽略，`undefined` 繼續往下傳。
7. 最後印出 `E undefined`。

### 第 40 題　答案：D（第 10 章）
- 第一個 `.then` 的回呼使用了大括號卻沒有 `return`，所以回傳 `undefined`，下一個 `.then` 不會等待 `delay(500, "B")`。
- 因此在 `delay(500, "A")` 完成的約 0.5 秒後，立刻印出 `undefined`。
- 加上 `return`（或使用箭頭函式簡寫）才會等待約 1 秒後印出 `"B"`。

### 第 41 題　答案：A（第 10 章）
- `race`：看「最先定案」的，p2 在 100ms 最先定案（失敗），所以整體失敗，原因 `"B"`。
- `any`：跳過失敗，等「第一個成功」的，p3 在 200ms 先成功，所以成功，值為 `"C"`。

### 第 42 題　答案：B（第 10 章）
- `fetch` 只有在網路層級錯誤（斷網、DNS 失敗、被 CORS 擋下）才會 reject。
- 伺服器回應 404、500 仍視為請求成功，Promise 為 fulfilled，需自行檢查 `response.ok`，不是 `true` 就手動 `throw`，才能進入 `.catch`。
- `axios` 則會在 404、500 時自動 reject。`throwOnError` 不是 `fetch` 的選項。

### 第 43 題　答案：C（第 11 章）
- `async` 函式在遇到第一個 `await` 之前，行為與一般函式相同，所以先印 `3`，呼叫 `a()` 同步印出 `1`。
- 遇到 `await null` 暫停 `a`，控制權交還外面，接著印出 `4`。
- 同步結束後處理微任務，`await` 之後的程式碼印出 `2`。

### 第 44 題　答案：A（第 11 章）
- `f1` 連續 `await`，第二個 `delay(2000)` 要等第一個完成後才會建立並開始計時，所以時間相加，約 3 秒。
- `f2` 的兩個 `delay` 在放入陣列時就同時啟動，`Promise.all` 等最慢者，約 2 秒。
- 判斷原則：B 需要 A 的結果 → 依序；彼此獨立 → `Promise.all`。

### 第 45 題　答案：C（第 11 章）
- `forEach` 不會等待 callback 回傳的 Promise。三個 async callback 各自在 `await delay(100)` 暫停，`forEach` 立刻結束，所以 `結束` 先印出。
- 約 100ms 後，三個計時器依序完成，印出 `1`、`2`、`3`。
- 修正：改用 `for...of`（依序）或 `await Promise.all(arr.map(async ...))`（同時）。

### 第 46 題　答案：B（第 11 章）
- t1：沒有 `await`，`fail()` 回傳 rejected 的 Promise，`try` 正常結束，沒人處理，變成 Unhandled Rejection。
- t2：`await` 讓 rejected 的 Promise 在該行轉為例外，被 catch 抓到。
- t3：`return fail()` 直接回傳 rejected 的 Promise，函式已離開 `try`，catch 抓不到，由呼叫端處理。
- t4：`return await` 讓錯誤在 `try` 內就被轉成例外，catch 抓得到。

### 第 47 題　答案：D（第 12 章）
- `class` 本質上是「建構函式 + 原型」的語法糖（`typeof` 為 `"function"`），內部自動為嚴格模式。
- A 錯：不加 `new` 會拋出 `TypeError: Class constructor ... cannot be invoked without 'new'`。
- B 錯：`class` 與 `let` 相同，有暫時性死區，宣告前使用會 `ReferenceError`。
- C 錯：`class` 內的方法不可列舉。

### 第 48 題　答案：A（第 12 章）
- `const fn = p.sayHi` 只取出函式，`fn()` 是簡易呼叫。
- `class` 內部是嚴格模式，簡易呼叫的 `this` 為 `undefined`，所以讀取 `.name` 時拋出 `TypeError: Cannot read properties of undefined (reading 'name')`。
- 解法：在 constructor 中 `bind`、使用箭頭函式包一層，或把方法寫成箭頭函式類別欄位。

### 第 49 題　答案：B（第 12 章）
- 子類別的 `constructor` 中，使用 `this` 之前必須先呼叫 `super()`，否則拋出 `ReferenceError: Must call super constructor in derived class before accessing 'this' ...`。
- 這是執行階段錯誤，不是 SyntaxError。

### 第 50 題　答案：C（第 12 章）
- 靜態方法的 `this` 是「呼叫它的 class」。`Base.who()` 的 `this` 為 `Base`；`Derived.who()` 的 `this` 為 `Derived`（靜態成員會被繼承）。
- 實例方法中的 `this.constructor` 指向實例的建構子，`new Derived()` 的 `constructor` 是 `Derived`。
- `extends` 同時建立兩條原型鏈：`Derived.__proto__ === Base`（靜態）與 `Derived.prototype.__proto__ === Base.prototype`（實例）。
