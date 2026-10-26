## 第六章  物件屬性延伸章節：屬性的特徵
### 6-1 屬性特徵是什麼？
JavaScript 物件的每個屬性，除了「值」以外，還帶有一組**特徵（Property Descriptor，屬性描述器）**，用來控制這個屬性「能不能被改、能不能被列舉、能不能被刪除」。平常用 `obj.a = 1` 建立的屬性，特徵全部預設為 `true`，所以我們感覺不到它們的存在。

#### 一、屬性的四個特徵

| 特徵 | 意義 | 用 `obj.a = 1` 建立時 | 用 `defineProperty` 建立時（未指定） |
| --- | --- | --- | --- |
| `value` | 屬性的值 | 指定的值 | `undefined` |
| `writable` | 值能不能被修改 | `true` | `false` |
| `enumerable` | 能不能被列舉（`for...in`、`Object.keys`） | `true` | `false` |
| `configurable` | 特徵能不能再被修改、屬性能不能被刪除 | `true` | `false` |

> 另外還有 `get` / `set`（存取器屬性），會在 6-4 介紹。

#### 二、查看屬性特徵：`Object.getOwnPropertyDescriptor`
```
const person = { name: "Kent" };

console.log(Object.getOwnPropertyDescriptor(person, "name"));
// { value: "Kent", writable: true, enumerable: true, configurable: true }
```

#### 三、`Object.defineProperty`：定義或修改「單一」屬性
語法：`Object.defineProperty(物件, "屬性名稱", 描述器)`
```
const person = {};

Object.defineProperty(person, "id", {
  value: 1001,
  writable: false,      // 不可修改
  enumerable: true,     // 可列舉
  configurable: false   // 不可刪除、不可再調整特徵
});

person.id = 9999;       // 失敗（嚴格模式 "use strict" 會丟出 TypeError）
console.log(person.id); // 1001
delete person.id;       // 失敗
console.log(person.id); // 1001
```
* **沒寫的特徵預設為 `false`**，這是最容易踩雷的地方。
* 若屬性已存在、且 `configurable` 為 `true`，`defineProperty` 只會修改你有寫的特徵，其他維持原樣。

#### 四、`Object.defineProperties`：一次定義「多個」屬性
```
const book = {};

Object.defineProperties(book, {
  title: { value: "JS 核心", writable: true, enumerable: true },
  isbn:  { value: "978-xxx", enumerable: false }   // 隱藏屬性
});

console.log(Object.keys(book));   // ["title"]（isbn 不可列舉，不會出現）
console.log(book.isbn);           // "978-xxx"（仍然讀得到）
```

#### 五、四個特徵實際效果對照
| 特徵設為 `false` 時 | 效果 |
| --- | --- |
| `writable` | 不能重新賦值（唯讀） |
| `enumerable` | `for...in`、`Object.keys`、`JSON.stringify` 看不到它 |
| `configurable` | 不能 `delete`、不能改特徵（唯一例外：`writable` 可從 `true` 改成 `false`） |

---

### 6-2 物件擴充的修改與調整
前面是控制「單一屬性」，這一節則是控制「整個物件」能不能新增、刪除、修改屬性。三種方法限制由鬆到嚴：

> 大綱中的 `Object.preventExtension` 正確名稱為 **`Object.preventExtensions`**（最後有 s）。

| 方法 | 新增屬性 | 刪除屬性 | 修改屬性值 | 檢查方法 |
| --- | --- | --- | --- | --- |
| `Object.preventExtensions(obj)` | ❌ | ✅ | ✅ | `Object.isExtensible(obj)` |
| `Object.seal(obj)` | ❌ | ❌ | ✅ | `Object.isSealed(obj)` |
| `Object.freeze(obj)` | ❌ | ❌ | ❌ | `Object.isFrozen(obj)` |

#### 一、`Object.preventExtensions`：禁止新增
```
const a = { x: 1 };
Object.preventExtensions(a);

a.y = 2;          // 失敗，不能新增
a.x = 10;         // 成功，可修改
delete a.x;       // 成功，可刪除
console.log(a);   // {}
```

#### 二、`Object.seal`：密封（不增、不刪，可改值）
等同於「禁止擴充 ＋ 把所有屬性的 `configurable` 設為 `false`」。
```
const b = { x: 1 };
Object.seal(b);

b.x = 10;         // 成功
b.y = 2;          // 失敗
delete b.x;       // 失敗
console.log(b);   // { x: 10 }
```

#### 三、`Object.freeze`：凍結（完全唯讀）
等同於「密封 ＋ 把所有屬性的 `writable` 設為 `false`」。
```
const c = { x: 1 };
Object.freeze(c);

c.x = 10;         // 失敗
c.y = 2;          // 失敗
delete c.x;       // 失敗
console.log(c);   // { x: 1 }
```

#### 四、注意：`freeze` 是「淺層」凍結
```
const d = { info: { age: 20 } };
Object.freeze(d);

d.info.age = 30;          // 成功！內層物件沒被凍結
console.log(d.info.age);  // 30
```
要完全凍結，必須**遞迴**對內層物件也呼叫 `Object.freeze`。
```
function deepFreeze(obj) {
  Object.values(obj).forEach(value => {
    if (typeof value === "object" && value !== null) {
      deepFreeze(value);   // 先遞迴凍結內層物件
    }
  });
  return Object.freeze(obj); // 再凍結自己
}

const e = { info: { age: 20 } };
deepFreeze(e);

e.info.age = 30;          // 失敗：內層也被凍結了
console.log(e.info.age);  // 20
console.log(Object.isFrozen(e.info)); // true
```

---

### 6-3 屬性列舉與原型列舉的關係
「列舉」就是把物件的屬性一個一個走訪出來。能不能被走訪，由 `enumerable` 決定，而且**不同方法會不會包含原型上的屬性也不一樣**。

#### 一、常見列舉方式比較

| 方法 | 只含自己的屬性？ | 包含原型鍊上的屬性？ | 包含不可列舉屬性？ |
| --- | --- | --- | --- |
| `for...in` | ❌ | ✅（可列舉者） | ❌ |
| `Object.keys(obj)` | ✅ | ❌ | ❌ |
| `Object.getOwnPropertyNames(obj)` | ✅ | ❌ | ✅ |
| `JSON.stringify(obj)` | ✅ | ❌ | ❌ |

#### 二、範例
```
const parent = { family: "Lee" };
const child = Object.create(parent);
child.name = "Kent";

Object.defineProperty(child, "secret", { value: "abc", enumerable: false });

for (const key in child) {
  console.log(key);   // "name"、"family"（secret 不可列舉；family 來自原型）
}

console.log(Object.keys(child));                 // ["name"]
console.log(Object.getOwnPropertyNames(child));  // ["name", "secret"]
```
* **為什麼 `getOwnPropertyNames` 會出現 `secret`？**
  * `secret` 是用 `Object.defineProperty(child, "secret", ...)` 直接定義在 `child` 身上，所以它是 `child` 的「**自己的屬性**」。
  * 它雖然設了 `enumerable: false`（不可列舉），但 `getOwnPropertyNames` 的規則是「回傳自己所有的屬性名稱，**不管可不可列舉**」，所以 `secret` 會被列出來。
  * 相對地，`Object.keys` 只收「自己的 ＋ 可列舉」的屬性，所以只剩 `name`；而 `family` 在原型 `parent` 上，不是自己的屬性，兩個方法都不會收。

#### 範例對照表格（逐一驗證）
```
const parent = { family: "Lee" };
const child = Object.create(parent);
child.name = "Kent";
Object.defineProperty(child, "secret", { value: "abc", enumerable: false });

// 1. for...in：自己 ＋ 原型鍊上「可列舉」的屬性，不含不可列舉
const forInKeys = [];
for (const key in child) forInKeys.push(key);
console.log(forInKeys);                          // ["name", "family"]
//   → 含原型的 family（✅）；不含不可列舉的 secret（❌）

// 2. Object.keys：只含自己的、可列舉的屬性
console.log(Object.keys(child));                 // ["name"]
//   → 沒有 family（原型）、沒有 secret（不可列舉）

// 3. Object.getOwnPropertyNames：只含自己的屬性，但包含不可列舉
console.log(Object.getOwnPropertyNames(child));  // ["name", "secret"]
//   → 有 secret（✅ 含不可列舉）、沒有 family（原型）

// 4. JSON.stringify：只序列化自己的、可列舉的屬性
console.log(JSON.stringify(child));              // {"name":"Kent"}
//   → 與 Object.keys 結果一致，secret 與 family 都不會出現
```
* `for...in` 會一路往上走訪原型鍊，所以看得到 `family`。
* 想只處理自己的屬性，可搭配 `hasOwnProperty` 過濾：
```
for (const key in child) {
  if (child.hasOwnProperty(key)) {
    console.log(key);   // 只剩 "name"
  }
}
```
* 為什麼內建的 `toString`、`hasOwnProperty` 不會出現在 `for...in`？因為 `Object.prototype` 上這些方法的 `enumerable` 都是 `false`。

---

### 6-4 Getter 與 Setter
Getter / Setter 是一種「**看起來像屬性、實際上是函式**」的存取器屬性：讀取時自動執行 `get`，賦值時自動執行 `set`。適合用來**計算值**、**驗證資料**、**攔截讀寫**。

#### 一、物件字面值寫法
```
const user = {
  firstName: "Kent",
  lastName: "Lee",

  get fullName() {                 // 讀取時觸發
    return this.firstName + " " + this.lastName;
  },
  set fullName(value) {            // 賦值時觸發
    [this.firstName, this.lastName] = value.split(" ");
  }
};

console.log(user.fullName);   // "Kent Lee"（不用加括號）
user.fullName = "Amy Chen";
console.log(user.firstName);  // "Amy"
```
> **能不能把 `set fullName(value)` 改成 `set fullname(firstname, lastname)`？**
> 不行，有兩個原因：
> 1. **setter 只能有「剛好一個」參數**，寫兩個參數會直接語法錯誤（`Setter must have exactly one formal parameter`）。因為 `user.fullName = x` 賦值時，右邊只能傳一個值進來。
> 2. **大小寫要一致**：JavaScript 區分大小寫，`fullname` 與 `fullName` 是兩個不同的屬性，`get fullName` 和 `set fullname` 不會配成一對。
>
> 如果想一次傳入姓和名，可以改成傳「一個陣列／物件」，或乾脆改用一般方法：
> ```
> const user2 = {
>   firstName: "Kent",
>   lastName: "Lee",
>   get fullName() {
>     return this.firstName + " " + this.lastName;
>   },
>   set fullName([first, last]) {        // 解構：仍然只有一個參數（陣列）
>     this.firstName = first;
>     this.lastName = last;
>   },
>   setName(first, last) {               // 需要多個參數時，用一般方法
>     this.firstName = first;
>     this.lastName = last;
>   }
> };
>
> user2.fullName = ["Amy", "Chen"];      // 傳陣列
> user2.setName("Bob", "Wang");          // 或呼叫方法
> console.log(user2.fullName);           // "Bob Wang"
> ```

#### 二、`defineProperty` 寫法
```
const product = { _price: 100 };

Object.defineProperty(product, "price", {
  get() {
    return this._price;
  },
  set(value) {
    if (value < 0) {
      console.log("價格不能是負數");
      return;
    }
    this._price = value;
  },
  enumerable: true,
  configurable: true
});

product.price = -5;           // "價格不能是負數"
product.price = 200;
console.log(product.price);   // 200
```

#### 三、重點整理
* **存取器屬性**（`get` / `set`）與**資料屬性**（`value` / `writable`）**不能同時存在**於同一個描述器。
* 只有 `get`、沒有 `set` → 唯讀屬性（賦值會被忽略，嚴格模式會報錯）。
* 習慣上，真正存資料的屬性會加底線（如 `_price`）表示「內部使用，請勿直接動它」。
* 賦值時如果在 `set` 裡又寫 `this.price = value`，會造成**無限遞迴**，要寫到另一個屬性（如 `_price`）。

---

### 6-5 屬性特徵與主流框架的關係
主流前端框架的「資料變了，畫面自動更新」，核心就是靠 **Getter / Setter（或其後繼技術）攔截屬性的讀寫**。

#### 一、Vue 2：`Object.defineProperty`
Vue 2 初始化時，會走訪 `data` 內每個屬性，用 `defineProperty` 改寫成 getter / setter：
* **getter**：被讀取時，記錄「誰用了這個資料」（依賴收集）。
* **setter**：被修改時，通知所有用到它的地方重新渲染（派發更新）。

簡化概念：
```
function defineReactive(obj, key, val) {
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get() {
      console.log("讀取 " + key);
      return val;
    },
    set(newVal) {
      console.log(key + " 更新為 " + newVal + "，通知畫面重繪");
      val = newVal;
    }
  });
}

const data = {};
defineReactive(data, "count", 0);
data.count++;   // 先 get 再 set
```
**限制**：`defineProperty` 只能針對「已存在的屬性」，所以**新增屬性、刪除屬性、直接用索引改陣列**，Vue 2 都偵測不到，需改用 `Vue.set` / `Vue.delete`。

#### 二、Vue 3：`Proxy`
Vue 3 改用 `Proxy` 代理整個物件，可攔截新增、刪除、陣列索引等操作，解決了 Vue 2 的限制。
```
const state = new Proxy({ count: 0 }, {
  get(target, key) {
    console.log("讀取 " + key);
    return target[key];
  },
  set(target, key, value) {
    console.log(key + " 更新為 " + value);
    target[key] = value;
    return true;
  }
});

state.count = 1;     // 觸發 set
state.newProp = 2;   // 新增屬性也能攔截
```

#### 三、React 與其他
* **React** 不攔截屬性，而是靠 `setState` / `useState` 明確通知更新，並依賴「不可變資料」（不直接改原物件，而是產生新物件）；`Object.freeze` 常用來在開發時防止誤改 state。
* **MobX** 等狀態管理工具，也是以 getter / setter 或 Proxy 實現自動追蹤。

#### 四、總結對照

| 框架 / 工具 | 偵測資料變化的方式 | 能否偵測新增屬性 |
| --- | --- | --- |
| Vue 2 | `Object.defineProperty`（getter / setter） | ❌ |
| Vue 3 | `Proxy` | ✅ |
| React | 手動呼叫 `setState` / `useState` | 不適用（靠不可變資料比對） |

---

### 本章重點速記
1. 屬性有四個特徵：`value`、`writable`、`enumerable`、`configurable`；`defineProperty` 沒寫的特徵預設為 `false`。
2. `preventExtensions`（不能增）→ `seal`（不能增刪）→ `freeze`（不能增刪改），`freeze` 只有淺層效果。
3. `enumerable: false` 的屬性不會出現在 `for...in`、`Object.keys`、`JSON.stringify`；`for...in` 還會走訪原型鍊。
4. Getter / Setter 讓屬性在讀寫時執行函式，可用於計算、驗證與攔截。
5. Vue 2 靠 `defineProperty` 的 getter / setter 做響應式，Vue 3 改用 `Proxy`。

### 6-5 本章習題
#### 第一題
請問 console.log() 會出現什麼訊息並是屬於哪一種錯誤?
```
var person={};
Object.preventExtensions(person);
person.a="a";
console.log(person.a);
```
#### 第二題
請問 console.log() 會出現什麼什麼?
```
function fu(){var a = 'a';}
fu.a='b';
Object.preventExtensions(fu);
fu.a='c'
console.log(fu.a);
```

#### 第三題
請問 console.log() 會出現什麼什麼?
```
'use strict';
function fu(){var a = 'a';}
Object.preventExtensions(fu);
fu.a='b';
fu.a='c'
console.log(fu.a);
```

#### 第四題
請問 console.log() 會出現什麼什麼? 如果在第一行加入'use strict'後，console.log() 會出現什麼什麼? 如果再移除 Object.freeze(w); 後，console.log() 又會出現什麼什麼？此時 w.t 的值是多少？
```
var w={t: 100, set s(p){this.t+=p/2}, get s(){return this.p/2}}
Object.freeze(w);
w.s=10
console.log(w.s);
```

#### 答案與解析
**第1題**

答案：`undefined`，屬於「靜默錯誤」（silent fail，不會丟出例外）。

解析：
* `Object.preventExtensions(person)` 之後，`person` 不能再新增屬性。
* 非嚴格模式下，`person.a = "a"` 新增失敗但**不報錯**，因此 `person.a` 讀不到值，得到 `undefined`。
* 若在嚴格模式（`'use strict'`）下，同一行會丟出 `TypeError: Cannot add property a, object is not extensible`。

**第2題**

答案：`c`

解析：
* 函式也是物件，`fu.a = 'b'` 在凍結擴充前就已經新增，屬性 `a` 已存在。
* `preventExtensions` 只禁止「新增」屬性，**不影響既有屬性的修改與刪除**（`writable` 仍為 `true`）。
* 所以 `fu.a = 'c'` 修改成功，輸出 `c`。
* 注意：`fu` 內的 `var a` 是函式內部的區域變數，與 `fu.a` 屬性無關。

**第3題**

答案：不會輸出任何值，`fu.a = 'b'` 時丟出 `TypeError: Cannot add property a, object is not extensible`。

解析：
* 第一行 `'use strict'` 啟用嚴格模式，原本會「靜默失敗」的操作會改成丟出錯誤。
* `Object.preventExtensions(fu)` 後才第一次新增 `fu.a`，新增失敗，在嚴格模式下直接丟出 `TypeError`，程式中止，後面的 `fu.a = 'c'` 與 `console.log` 都不會執行。
* 與第2題的差別：第2題的屬性在禁止擴充**之前**就已存在，所以還能修改；本題是**之後**才新增，所以失敗。
* 若移除 `'use strict'`（非嚴格模式），則兩行賦值都靜默失敗，`console.log(fu.a)` 輸出 `undefined`。

**第4題**

答案：
* 原題（沒有嚴格模式）：`NaN`
* 第一行加入 `'use strict'` 後：`w.s = 10` 時丟出 `TypeError: Cannot assign to read only property 't' of object`，`console.log` 不會執行。
* 再移除 `Object.freeze(w);` 後（不論有無 `'use strict'`）：不會報錯，`console.log(w.s)` 輸出 `NaN`，而 `w.t` 變成 `105`。

解析：
* `Object.freeze(w)` 會把資料屬性 `t` 設為不可寫，但 `s` 是**存取器屬性**（getter / setter），freeze 不會阻止 setter 被呼叫。
* `w.s = 10` 會執行 setter：`this.t += 10 / 2`，但 `t` 已凍結不可寫，所以 `t` 維持 `100`。非嚴格模式靜默失敗；嚴格模式則丟出 `TypeError`。
* `console.log(w.s)` 執行 getter：`return this.p / 2`，但物件上沒有 `p` 屬性（應該是 `this.t` 才對），`undefined / 2` 得到 `NaN`。
* 重點：`freeze` 凍結的是屬性的「值與特徵」，不會讓 setter 失效；setter 內部若要修改被凍結的屬性才會失敗。
* 移除 `Object.freeze(w)` 後，`t` 恢復可寫：`w.s = 10` 執行 setter，`this.t += 10 / 2`，`t` 由 `100` 變成 `105`，嚴格模式下也不會丟出錯誤。
* 但 `console.log(w.s)` 的 getter 仍然 `return this.p / 2`，`this.p` 依舊是 `undefined`，所以輸出仍是 `NaN`。這是 getter 本身的寫法錯誤（應為 `this.t / 2`，那樣才會輸出 `52.5`），與 `freeze` 無關。
* 對照：有 freeze → `t` 維持 `100`（嚴格模式報錯）；無 freeze → `t` 變 `105`；兩者 `w.s` 都是 `NaN`。

**重點整理**

| 題號 | 輸出 | 關鍵觀念 |
| --- | --- | --- |
| 1 | `undefined` | `preventExtensions` 禁止新增，非嚴格模式靜默失敗 |
| 2 | `c` | 既有屬性仍可修改 |
| 3 | `TypeError`（無輸出） | 嚴格模式下，禁止擴充後才新增的屬性會丟出錯誤 |
| 4 | `NaN` | `freeze` 不阻止 setter 執行，但 setter 內改 `t` 失敗；getter 讀 `this.p` 為 `undefined`；移除 `freeze` 後 `t` 變 `105`，`w.s` 仍為 `NaN` |