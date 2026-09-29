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
1. 可使用點記法或括弧記法取值與新增屬性
2. 可使用delete刪除屬性
3. [程式範例](../Examples/ch03-01.html)

### 3-3 變數與物件屬性的差異
1. 檢查變數是否成為全域物件的屬性
2. 嘗試刪除變數與物件屬性
3. [程式範例](../Examples/ch03-02.html)

### 3-4 物件與純值
在 JavaScript 中，所有的資料型態主要可以分為兩大類：**原始型態（Primitive Types，即純值）** 與 **物件型態（Object Types，包括物件、陣列、函式等）**。

兩者最核心的差異在於**傳值（Pass by Value）與傳參考（Pass by Reference）**的記憶體運作機制，以及**是否具備可變性（Mutability）**。

---

#### 一、核心差異比較表

| 特性 | 純值（Primitive / Primitives） | 物件（Objects） |
| :--- | :--- | :--- |
| **包含種類** | `number`, `string`, `boolean`, `null`, `undefined`, `symbol`, `bigint` | `Object`, `Array`, `Function`, `Date`, `RegExp` 等 |
| **記憶體儲存方式** | 變數直接儲存**實際的值**（直接存放在 Stack 中） | 變數儲存的是**記憶體位址（參考/指標）**（實際內容放在 Heap 中） |
| **可變性 (Mutability)** | **不可變（Immutable）** | **可變（Mutable）** |
| **比較方式 (`==` / `===`)** | 比較**實際的值**是否相等 | 比較**記憶體位址**是否相同 |
| **賦值與複製動作** | **傳值 (Pass by Value)**：複製一份獨立的新值 | **傳參考 (Pass by Reference)**：複製位址，指向同一塊資料 |

---

#### 二、詳細觀念與程式碼範例

##### 差異一：不可變性 (Immutability) vs 可變性 (Mutability)

* **純值不可變**：無法修改一個純值本身。對字串或數字進行運算時，只會「產生一個新的純值」，而不是修改原本的值。
  ```javascript
  let str = "hello";
  str.toUpperCase(); // 回傳 "HELLO"，但不會改變原本的 str
  console.log(str); // 輸出: "hello"

  str = "world"; // 這是把變數重新指向新的純值 "world"，而不是修改 "hello" 本身
  ```

* **物件可變**：可以隨時新增、修改或刪除物件內部的屬性，而無需重新賦值給變數。
  ```javascript
  const person = { name: "Alice" };
  person.name = "Bob"; // 直接修改物件內部的屬性
  console.log(person.name); // 輸出: "Bob"
  ```

---

##### 差異二：傳值 (Pass by Value) vs 傳參考 (Pass by Reference)

* **純值（傳值）**：複製變數時，會建立一個完全獨立的新值，兩者互不影響。
  ```javascript
  let a = 10;
  let b = a; // 複製一份 10 給 b
  b = 20;    // 修改 b

  console.log(a); // 10（a 完全不受影響）
  console.log(b); // 20
  ```

* **物件（傳參考）**：複製物件變數時，複製的是「記憶體位址」。因此兩個變數會指向同一個物件，修改其中一個，另一個也會跟著改變。
  ```javascript
  let obj1 = { score: 100 };
  let obj2 = obj1; // 複製的是記憶體位址
  obj2.score = 50;  // 修改 obj2 屬性的同時，也等於修改了該位址的資料

  console.log(obj1.score); // 50（obj1 也跟著變了！）
  console.log(obj2.score); // 50
  ```

---

##### 差異三：相等性比較 (Equality Comparison)

* **純值**：只看「值」是否相同。
  ```javascript
  console.log("apple" === "apple"); // true
  console.log(100 === 100);         // true
  ```

* **物件**：比較的是「記憶體位址」，即使內容一模一樣，只要不是同一個實體，就不相等。
  ```javascript
  let a = { id: 1 };
  let b = { id: 1 };

  console.log(a === b); // false！（因為它們存在於不同的記憶體位址）

  let c = a;
  console.log(a === c); // true （因為指向同一個位址）
  ```

---

#### 三、常見例外與觀念補充：包裹物件 (Primitive Wrappers)

純值雖然不是物件，但像 `'hello'.length` 或 `(123.456).toFixed(2)` 這類語法可以運作，是因為 JavaScript 在執行時會自動將純值轉為對應的**包裹物件（Wrapper Object）**（例如 `String`、`Number`、`Boolean`），執行完方法後再立即釋放掉。這個過程稱為 **Auto-boxing（自動裝箱）**。

### 3-5 未定義的物件屬性預設值
[程式範例](../Examples/ch03-03.html)

### 3-6 物件參考概念與實際運作模式
請依據執行的步驟拆分頁面
說明以下程式碼的執行結果
```
var a = { x: 1 };
var b = a;
a.x = { x: 2};
a.y = a = { y: 1};
console.log(a); // 結果？
console.log(b); // 結果？
```
答案：
| 步驟 | 最終答案 | 變數 a 參考 | 變數 b 參考 | 記憶體 0x01 | 記憶體 0x02 | 記憶體 0x03 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `var a = { x: 1 };` | | `0x01` | | `{ x: 1 }` | | |
| `var b = a;` | | `0x01` | `0x01` | `{ x: 1 }` | | |
| `a.x = { x: 2 };` | | `0x01` | `0x01` | `{ x: 0x02 }` | `{ x: 2 }` | |
| `a.y = a = { y: 1 };` | | `0x03` | `0x01` | `{ x: 0x02, y: 0x03 }` | `{ x: 2 }` | `{ y: 1 }` |
| `console.log(a);` | **`{ y: 1 }`** | | | | | |
| `console.log(b);` | **`{ x: { x: 2 }, y: { y: 1 } }`** | | | | | |


### 3-7 Call by Reference vs. Call by Sharing
在 JavaScript 中，關於參數與變數傳遞機制，常見的說法是「基本型別是 Call by Value，物件是 Call by Reference」。然而，更嚴謹且精確的定義為：**JavaScript 永遠是 Call by Value，而傳遞物件時傳的是「參考的位址值 (Reference Value)」，這種行為稱為 Call by Sharing (按共享傳遞)**。

---

#### 什麼是 Call by Sharing？

* **修改屬性 (Mutate Property)**：可以透過傳入的參考修改原物件內部的屬性，**外部會跟著改變**。
* **重新賦值 (Reassignment)**：若對變數直接賦予新物件，只會改變該變數本身的指向，**不會影響**外部原有的變數。

---

#### 範例解析：從記憶體追蹤看 Call by Sharing

以之前的程式碼為例：

```javascript
var a = { x: 1 };
var b = a;

// 1. 修改屬性
a.x = { x: 2 };

// 2. 連續賦值（包含重新賦值 a = ...）
a.y = a = { y: 1 };
```

#### 記憶體狀態變化

| 步驟 | 變數 a 指向 | 變數 b 指向 | 記憶體 0x01 (原物件) | 說明 |
| :--- | :--- | :--- | :--- | :--- |
| `var a = { x: 1 };` | `0x01` | - | `{ x: 1 }` | 建立物件 `0x01` |
| `var b = a;` | `0x01` | `0x01` | `{ x: 1 }` | 共享相同位址 `0x01` |
| `a.x = { x: 2 };` | `0x01` | `0x01` | `{ x: { x: 2 } }` | **修改屬性**：透過 `a` 修改 `0x01`，`b` 也能看到變化 |
| `a.y = a = { y: 1 };` | **`0x03`** | `0x01` | `{ x: { x: 2 }, y: { y: 1 } }` | **重新賦值**：`a` 改指向新位址 `0x03`，但 `b` 依然保持指向 `0x01` |

#### 為什麼這證明了不是純粹的 Call by Reference？

若 JavaScript 是純粹的 **Call by Reference**：
* 當 `a` 被重新賦值指向 `{ y: 1 }`（`0x03`）時，與它綁定的變數 `b` 也應該同步被改指向 `0x03`。

然而實際結果是：
* `console.log(a);` $\rightarrow$ `{ y: 1 }`（`0x03`）
* `console.log(b);` $\rightarrow$ `{ x: { x: 2 }, y: { y: 1 } }`（`0x01`）

這說明了 `b = a` 傳遞的是「位址值的副本」，而不是變數本身的別名綁定。

---

#### 函式參數中的 Call by Sharing

這在函式傳參時表現得更為明顯：

```javascript
function updateObject(obj) {
  // 1. 修改屬性：影響外部
  obj.x = 100; 

  // 2. 重新賦值：只切斷內部 obj 的指向，不影響外部 myObj
  obj = { y: 200 }; 
}

let myObj = { x: 1 };
updateObject(myObj);

console.log(myObj); // 結果：{ x: 100 }
```

#### 運作過程分析
1. 呼叫 `updateObject(myObj)` 時，將 `myObj` 存放的位址（例如 `0x01`）**複製一份**傳給參數 `obj`。
2. `obj.x = 100` 透過位址 `0x01` 修改內部屬性 $\rightarrow$ 外部 `myObj` 受到影響。
3. `obj = { y: 200 }` 讓 `obj` 改指向新位址 `0x02` $\rightarrow$ 複製出來的位址被覆蓋掉，但不影響外部 `myObj` 依然指向 `0x01`。

---

#### 三種傳遞機制比較

| 機制 | 概念 | 能否修改物件內部？ | 重新賦值是否影響外部？ | 代表語言 / 情況 |
| :--- | :--- | :---: | :---: | :--- |
| **Call by Value** | 複製一份值傳入 | 否 | 否 | C、JS Primitive 型別 |
| **Call by Reference** | 傳入變數本身的別名 | 是 | **是** | C++ (& reference) |
| **Call by Sharing** | 複製一份「記憶體位址」傳入 | **是** | **否** | JavaScript 物件、Python、Ruby |

### 3-8 淺拷貝與深拷貝
* 淺拷貝：僅複製物件的第一層屬性。
* 深拷貝：遞迴複製物件的所有層級。

範例一：淺拷貝
```
const arr1 = ["mike", "andy"];
const arr2 = arr1.slice(0); // 從第0個元素開始複製整個陣列
const arr3 = [...arr1]; // 使用展開運算子複製整個陣列
arr2[1] = "jacky";
arr3[1] = "xuan";
console.log(arr1);  // ["mike", "andy"]
console.log(arr2);  // ["mike", "jacky"]
console.log(arr3);  // ["mike", "xuan"]

const obj1 = {
  name: "mike",
  age: 12,
};
const obj2 = Object.assign({}, obj1);  // 使用 Object.assign 複製整個物件，第一個參數為目標物件，後續參數為來源物件
const obj3 = { ...obj1 };  // 使用展開運算子複製整個物件
obj2.name = "andy";
obj2.age = 100;
obj3.name = "xuan";
obj3.age = 55;
console.log(obj1);  // { name: "mike", age: 12 }
console.log(obj2);  // { name: "andy", age: 100 }
console.log(obj3);  // { name: "xuan", age: 55 }
```

範例二：使用 lodash 的 cloneDeep 方法進行深拷貝
```
const obj1 = {
   name: "kent",
   info: {
     age: 32,
     interest: {
       code: "javascript",
     },
   },
 };
 const obj2 = _.cloneDeep(obj1);
 obj2.name = "jacky";
 obj2.info.age = 100;
 console.log(obj1); // 結果：{ name: "kent", info: { age: 32, interest: { code: "javascript" } } }
 console.log(obj2); // 結果：{ name: "jacky", info: { age: 100, interest: { code: "javascript"}}}
```

範例三：使用 JSON 方法進行深拷貝
```
const obj1 = {
  name: "kent",
  info: {
    age: 32,
    interest: {
code: "javascript",
    },
  },
};
function deepCopy(obj) {
  return JSON.parse(JSON.stringify(obj));
}

const obj2 = deepCopy(obj1); // 使用自訂的 deepCopy 函式進行深拷貝
obj2.name = "jacky";
obj2.info.age = 100;
console.log(obj1); // 結果：{ name: "kent", info: { age: 32, interest: { code: "javascript" } } }
console.log(obj2); // 結果：{ name: "jacky", info: { age: 100, interest: { code: "javascript" }}}
```
[傳位址與淺拷貝的差異](https://share.gemini.google/DVC1lFZOUQSh)

### 3-9 課後練習
#### 第1題
* 請確認物件設置是否有誤。
* 請使用陣列 const functions=['fn1', 'fn2']呼叫物件內的函數。
```
let obj = {
  str: "0",
  num: 1,
  boo: true,
  arr: [2, 3],
  obj: { x: 4, y: 5 },
  fn1: function () {
    return 6;
  },
  und: undefined,
  nul: null,
  "a-b": 7,
  8: 8,
  "a b": 9,
  _ab: 10,
  $ab: 11,
  fn2: function(){
    return 12;
  }
};
```
#### 第2題
請從以下陣列物件中取得2
```
var a=[{b:{c:1}},{b:{c:2}}]
```

#### 第3題
* 拆解每一步驟變數與記憶體參考位址
* 寫出x, y, z的值
```
var x={};
var y=x;
var z=y={w:1};
z.w=3;
console.log(x,y,z);
```

#### 第4題
請問以下JSON格式是否正確？如果不正確，請改正。
```
{
  "a":1,
  "b":2,
}
```

#### 第5題
請問以下程式碼的執行結果為何？請說明原因。
```
function g(){
  a=1;
}
g.a=2
console.log(g.a);
```

#### 第6題
請問以下程式碼屬於淺拷貝還是深拷貝？執行結果為何？請說明原因。
```
var x=[{a:1, b:{c: 2}}];
var y=[];
x.forEach(item=>array.push(item));
x[0].b.c=3;
console.log(x[0].b.c, y[0].b.c);
```

#### 第7題
請問以下程式碼執行結果為何？請說明原因。
```
var x=[1,2,3,4,5];
var y=x;
y[4]=6;
console.log(x, y);
```

#### 第8題
請問以下程式碼執行結果為何？另外如何取得"z"？請說明原因。
```
var a=function() { return "z"; }
a.c="x";
var b=a;
b.c="y";
console.log(a.c, b.c);
```

#### 第9題
請問以下程式碼執行結果為何？請說明原因。
```
var a={x:1};
var b=a={y:2};
console.log(a.x, a.y, b.x, b.y);
b.y=3;
console.log(a.x, a.y, b.x, b.y);
```

#### 第10題
某間冷飲店想開發單頁式商品訂購系統，但卻為了定義JavaScript物件結構而煩惱，而該冷飲店提出的需求如下：
1. 冷飲共分為三類，產品與價格列表如下：
  * 茶飲類包括：紅茶、綠茶、烏龍茶，全部30元
  * 奶茶類包括：奶茶、珍珠奶茶、波霸奶茶，全部40元
  * 果汁：檸檬汁、柚子茶、檸檬多多，全部45元
2. 訂購冷飲時，提供以下選項供顧客選擇
  * 甜度：0%~100% (以25%為單位)
  * 冰塊：0%~90% (以30%為單位)
  * 加料：波霸 (+15元)、珍珠(+10元)、燕麥(+15元)、椰果(+15元)
  * 尺寸：中杯 (+0元)、大杯(+10元)
3. 系統操作流程
  * 訂購系統先選擇一項飲料→再讓使用者決定甜度、冰塊、加料、尺寸→最後加入至購物車。
  * 接著使用者繼續選擇其他飲料完成上述動作，直到完成購買動作按下列印訂單。



#### 答案
1. 正確
```
functions.forEach(fnName => {
  // 先檢查物件內是否存在該函數，避免名稱錯誤導致程式崩潰
  if (typeof myObject[fnName] === 'function') {
    myObject[fnName]();
  }
});
```

2. a[1].b.c

3. x 為 `{}`，y 與 z 皆為 `{ w: 3 }`
```
var x={};        // 建立物件 A（位址 0x01），x → 0x01
var y=x;         // y 複製 x 的參考，y → 0x01（與 x 指向同一物件 A）
var z=y={w:1};   // 賦值由右往左：先建立物件 B（位址 0x02）{w:1}，y → 0x02，再將 y 的值給 z，z → 0x02
z.w=3;           // 透過 z 修改物件 B 的屬性，B 變成 {w:3}（y 也指向 B）
console.log(x,y,z); // 結果：{} { w: 3 } { w: 3 }
```
   * 重點：`y={w:1}` 是「重新賦值」，讓 y 指向新物件，而不是修改原本的物件 A，所以 x 不受影響。

4. 不正確。JSON 不允許最後一個屬性後面有多餘的逗號（trailing comma）。
```
{
  "a":1,
  "b":2
}
```

5. 結果：`2`
   * 函式在 JavaScript 中也是物件，因此可以直接新增屬性，`g.a=2` 是在函式物件 g 上新增屬性 a。
   * 函式內的 `a=1` 只有在呼叫 `g()` 時才會執行，且它是對「全域變數 a」賦值（未宣告變數），並不是設定 `g.a`，所以不影響結果。

6. 屬於**淺拷貝**。
   * 原程式碼有誤：箭頭函式的簡寫本體內不可加分號，且 `array` 未定義，應改為 `y.push(item)`：
```
var x=[{a:1, b:{c: 2}}];
var y=[];
x.forEach(item=>y.push(item));
x[0].b.c=3;
console.log(x[0].b.c, y[0].b.c); // 結果：3 3
```
   * 原因：y 雖然是一個新陣列，但 push 進去的 item 是物件的「參考」，x[0] 與 y[0] 指向同一個物件，因此修改 `x[0].b.c` 時 `y[0].b.c` 也會改變。

7. 結果：`[1, 2, 3, 4, 6] [1, 2, 3, 4, 6]`
   * 陣列也是物件，`y=x` 是複製參考（傳參考），x 與 y 指向同一個陣列，所以透過 y 修改元素，x 也會跟著改變。

8. 
```
var a=function () { return "z"; }
a.c="x";
var b=a;
b.c="y";
console.log(a.c, b.c); // 結果：y y
console.log(a(), b()); // 取得 "z"：呼叫函式 → z z
```
   * 原因：函式是物件，`b=a` 讓 a、b 指向同一個函式物件，所以 `b.c="y"` 會覆蓋 `a.c`。
   * 要取得 "z" 必須「呼叫」函式，也就是 `a()` 或 `b()`。

9. 結果：
```
var a={x:1};
var b=a={y:2};  // 賦值由右往左：a 指向新物件 {y:2}，b 再取得 a 的參考，a、b 指向同一物件
console.log(a.x, a.y, b.x, b.y); // 結果：undefined 2 undefined 2
b.y=3;
console.log(a.x, a.y, b.x, b.y); // 結果：undefined 3 undefined 3
```
   * 原因：原本的 `{x:1}` 已經沒有任何變數參考它，a 與 b 都指向 `{y:2}`，所以 x 為 undefined；透過 b 修改 y，a 也看得到變化。

10. 物件結構設計如下：
```
// 1. 商品資料：以分類為單位，同類商品共用價格
const menu = {
  tea: { name: "茶飲類", price: 30, items: ["紅茶", "綠茶", "烏龍茶"] },
  milkTea: { name: "奶茶類", price: 40, items: ["奶茶", "珍珠奶茶", "波霸奶茶"] },
  juice: { name: "果汁類", price: 45, items: ["檸檬汁", "柚子茶", "檸檬多多"] },
};

// 2. 客製化選項
const options = {
  sugar: [0, 25, 50, 75, 100],   // 甜度（%）
  ice: [0, 30, 60, 90],          // 冰塊（%）
  toppings: {                    // 加料（可複選）
    boba: { name: "波霸", price: 15 },
    pearl: { name: "珍珠", price: 10 },
    oat: { name: "燕麥", price: 15 },
    coconut: { name: "椰果", price: 15 },
  },
  size: {
    medium: { name: "中杯", price: 0 },
    large: { name: "大杯", price: 10 },
  },
};

// 3. 購物車與操作流程
const cart = [];

function addToCart(category, item, sugar, ice, toppings, size) {
  const toppingPrice = toppings.reduce((sum, t) => sum + options.toppings[t].price, 0);
  const order = {
    item: item,
    sugar: sugar,
    ice: ice,
    toppings: toppings.map(t => options.toppings[t].name),
    size: options.size[size].name,
    price: menu[category].price + toppingPrice + options.size[size].price,
  };
  cart.push(order);
}

function printOrder() {
  let total = 0;
  cart.forEach((order, i) => {
    total += order.price;
    console.log(`${i + 1}. ${order.item} / 甜度${order.sugar}% / 冰塊${order.ice}% / 加料：${order.toppings.join("、") || "無"} / ${order.size} / ${order.price}元`);
  });
  console.log(`總計：${total}元`);
}

// 使用範例
addToCart("milkTea", "奶茶", 50, 30, ["pearl"], "large");  // 40 + 10 + 10 = 60
addToCart("tea", "綠茶", 0, 0, [], "medium");               // 30
printOrder();
// 1. 奶茶 / 甜度50% / 冰塊30% / 加料：珍珠 / 大杯 / 60元
// 2. 綠茶 / 甜度0% / 冰塊0% / 加料：無 / 中杯 / 30元
// 總計：90元
```
