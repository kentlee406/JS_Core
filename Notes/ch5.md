## 第五章  繼承與原型鍊
### 5-1 原型鍊的基本概念
JavaScript 的繼承方式與 Java、C# 等「以類別（Class）為基礎」的語言不同，它是**以原型（Prototype）為基礎**的語言：物件可以直接「繼承」另一個物件，不需要先有類別。即使 ES6 之後有 `class` 語法，底層仍是原型繼承（只是語法糖）。

#### 一、Java 類別繼承 與 JavaScript 原型繼承 的不同

| 比較項目 | Java（類別繼承） | JavaScript（原型繼承） |
| --- | --- | --- |
| 基礎單位 | 類別（藍圖）→ 實例 | 物件 → 物件 |
| 繼承對象 | 子類別繼承父類別 | 物件繼承另一個物件（原型） |
| 結構是否固定 | 編譯時就決定，執行時不能新增成員 | 執行時可隨時新增、修改屬性與方法 |
| 成員存放位置 | 每個實例依類別定義複製一份結構 | 共用的方法放在原型上，實例透過「參考」取得 |

Java 物件導向範例：必須先定義類別，再用 `new` 產生實例
```
class Dog {
  private String skinColor;    // 屬性必須事先宣告型別
  private String weight;

  public Dog(String skinColor) {  // 建構子
    this.skinColor = skinColor;
  }
  public void bark() {         // 方法定義在類別中
    System.out.println("汪汪");
  }
}

class Corgi extends Dog {      // 子類別繼承父類別
  public Corgi(String skinColor) { super(skinColor); }
}

Dog bibi = new Dog("brown");   // 由類別（藍圖）產生實例
bibi.bark();
```

JavaScript 物件原型範例：不需要類別，物件直接繼承物件
```
const bibi = {
  skinColor: "brown",
  weight: "small",
  bark() {                     // 方法簡寫
    console.log(this.name + "：汪汪");
  }
};

// cici 以 bibi 為原型（cici 繼承 bibi）
const cici = Object.create(bibi);
cici.name = "cici";

cici.bark();                   // "cici：汪汪"（bark 是從原型 bibi 借來的）
console.log(cici.skinColor);   // "brown"（cici 本身沒有，向上從 bibi 找到）
console.log(Object.getPrototypeOf(cici) === bibi); // true
```
* `Object.create(bibi)` 會建立一個**空物件**，並把它的原型設定為 `bibi`。
* `cici` 本身只有 `name` 一個屬性，`skinColor`、`bark` 都是透過原型鍊「借用」的，並不是複製一份。

#### 二、原型的特性
1. **原型也是物件**：具有物件的特性，原型上的屬性與方法可被所有繼承它的物件**共用**（節省記憶體）。
2. **向上查找**：存取屬性時，若物件本身找不到，就沿著 `__proto__` 往上一層原型找，一直找到 `Object.prototype` 為止；再往上是 `null`，仍找不到則回傳 `undefined`。這條由 `__proto__` 串起來的路徑就稱為**原型鍊（Prototype Chain）**。

範例：陣列為什麼「天生」就有 `length`、`forEach`？
```
var a = [1, 2, 3];               // 陣列也是一種物件
console.log(typeof a);           // "object"
console.log(a[1], a.length);     // 2 3：屬性
a.forEach(item => console.log(item)); // 方法：a 本身沒有 forEach，是從 Array.prototype 找到的

console.log(a.__proto__ === Array.prototype);                 // true
console.log(a.__proto__.__proto__ === Object.prototype);      // true
console.log(a.__proto__.__proto__.__proto__);                 // null：原型鍊的終點
```

原型鍊示意：
```
a ──__proto__──> Array.prototype ──__proto__──> Object.prototype ──__proto__──> null
(自身: 0,1,2,length) (forEach, map, push...)     (toString, hasOwnProperty...)
```
* 呼叫 `a.toString()` 時：a 本身沒有 → `Array.prototype` 有（陣列自己的 toString）→ 找到即停止。
* 呼叫 `a.hasOwnProperty(0)` 時：a 沒有 → `Array.prototype` 沒有 → `Object.prototype` 找到。

#### 三、在原型上新增方法
因為原型是共用的，所以在原型上新增方法，所有繼承它的物件都能立即使用：
```
var a = [1, 2, 3];
var b = [4, 5];

// 等同於 Array.prototype.getLast = ...
a.__proto__.getLast = function () {
  return this[this.length - 1];  // this 指向「呼叫它的陣列」
};

console.log(a.getLast());  // 3
console.log(b.getLast());  // 5：b 也能使用，因為 a、b 共用同一個 Array.prototype
```
注意：這裡**不能使用箭頭函式**
```
a.__proto__.getLast = () => this[this.length - 1];
a.getLast();  // 錯誤結果：箭頭函式沒有自己的 this，this 會指向外層（全域 window），而不是陣列 a
```
* 另外原寫法 `() => return ...` 是語法錯誤：箭頭函式省略大括號時會自動回傳，不能再寫 `return`。

物件實字的原型是 `Object.prototype`：
```
var family = { name: "Ming" };
console.log(family.__proto__ === Object.prototype); // true

family.__proto__.getName = function () {  // 等同於 Object.prototype.getName = ...
  return this.name;
};
console.log(family.getName());  // "Ming"

var arr = [1, 2];
console.log(arr.getName());     // undefined：連陣列都多了 getName！（arr → Array.prototype → Object.prototype）
```
* `Object.prototype` 位於幾乎所有物件原型鍊的最頂端，在它上面新增方法會**影響所有物件**，容易造成命名衝突、`for...in` 列舉出額外屬性等問題。
* 實務上**不建議修改內建物件的原型**（如 `Array.prototype`、`Object.prototype`），此處僅用於理解原型鍊的運作。

#### 四、`__proto__` 的補充
* `__proto__` 是早期瀏覽器提供的存取方式，現已列入規範但僅為了相容性保留，**實務上建議使用**：
  * `Object.getPrototypeOf(obj)`：取得原型
  * `Object.setPrototypeOf(obj, proto)`：設定原型（效能較差，盡量避免）
  * `Object.create(proto)`：建立物件時直接指定原型
* 判斷屬性是「自己的」還是「繼承來的」：
```
const cici = Object.create({ skinColor: "brown" });
cici.name = "cici";

console.log(cici.hasOwnProperty("name"));       // true：自身屬性
console.log(cici.hasOwnProperty("skinColor"));  // false：來自原型
console.log("skinColor" in cici);               // true：in 會沿著原型鍊查找
```

### 5-2 使用建構式自定義原型
5-1 是用 `Object.create` 讓「物件繼承物件」；實務上更常見的是透過**建構函式（Constructor Function）** 搭配 `new` 大量產生結構相同的物件，並把共用的方法放在建構函式的 `prototype` 上。

#### 一、建構函式與 new
```
function Dogs(name, color, size){  // 建構函式慣例以「大寫開頭」命名
  this.name = name;                // this 指向「new 出來的新物件」
  this.color = color;
  this.size = size;
}
var dog1 = new Dogs("bibi", "brown", "small");
var dog2 = new Dogs("pupu", "white", "big");

console.log(dog1);  // Dogs {name: "bibi", color: "brown", size: "small"}
```
`new Dogs(...)` 時，JavaScript 會做以下四件事：
1. 建立一個新的空物件 `{}`。
2. 將新物件的 `__proto__` 指向 `Dogs.prototype`（建立原型鍊）。
3. 以新物件作為 `this` 執行 `Dogs` 函式本體（賦予 name、color、size）。
4. 若函式沒有回傳其他物件，就自動回傳這個新物件。

> 若忘記寫 `new`，`Dogs("bibi", ...)` 會變成簡易呼叫，`this` 指向全域 `window`，屬性會被寫到全域上，且回傳 `undefined`。

#### 二、在 prototype 上新增方法
```
// 新增方法（注意拼字：prototype，而非 propotype）
Dogs.prototype.bark = function(){
  console.log(this.name + " barked.");  // this 指向「呼叫它的實例」
};

dog1.bark();  // "bibi barked."
dog2.bark();  // "pupu barked."
console.log(dog1.bark === dog2.bark);  // true：兩隻狗共用同一個函式
console.log(dog1.hasOwnProperty("bark"));  // false：bark 不在實例本身，而在原型上
```
* 即使 `dog1`、`dog2` 先建立、方法後新增，仍然可以使用，因為實例是透過**參考**找到原型，而非複製。
* 為什麼不把方法寫在建構函式裡（`this.bark = function(){...}`）？那樣每 new 一次就會建立一個新的函式，100 隻狗就有 100 份 bark，浪費記憶體；放在 prototype 上則只有一份。

#### 三、屬性遮蔽（Shadowing）
實例本身若有同名屬性／方法，會**優先使用自己的**，不會再往原型找：
```
dog1.bark = function(){ console.log("我是 bibi 自己的 bark"); };
dog1.bark();  // "我是 bibi 自己的 bark"
dog2.bark();  // "pupu barked."（不受影響）
delete dog1.bark;
dog1.bark();  // "bibi barked."（刪除自身屬性後，又找回原型上的）
```

#### 四、`__proto__` vs. `prototype`
這兩個名稱很像，但意義不同：

| | `prototype` | `__proto__` |
| --- | --- | --- |
| 誰擁有 | **函式**才有（建構函式） | **所有物件**都有（包含函式） |
| 意義 | 「將來用 new 產生的實例」要使用的原型 | 「自己」的上一層原型（原型鍊的連結） |
| 用途 | 定義共用方法 | 屬性查找時往上找的路徑 |

```
console.log(dog1.__proto__ === Dogs.prototype);    // true：實例的 __proto__ 就是建構函式的 prototype
console.log(Dogs.prototype.constructor === Dogs);  // true：prototype 預設有 constructor 指回建構函式
console.log(dog1.constructor === Dogs);            // true：dog1 本身沒有，是從原型上找到的
console.log(dog1 instanceof Dogs);                 // true：Dogs.prototype 在 dog1 的原型鍊上
console.log(Dogs.__proto__ === Function.prototype);// true：Dogs 本身是函式，它的原型是 Function.prototype
```
口訣：**實例.__proto__ === 建構函式.prototype**

### 5-3 原始型別的包裹物件與原型的關聯
原始型別（string、number、boolean…）本身**不是物件**，理論上不能有屬性與方法，但我們卻可以寫 `'abc'.length`、`(5).toFixed(2)`。這是因為 JavaScript 在存取時會**自動將原始型別包裹成對應的物件**（String、Number、Boolean），用完即丟棄，稱為**包裹物件（Wrapper Object）**。

#### 一、原始型別 vs. 包裹物件
```
var a = 'a';                    // 原始型別
var b = new String('bcde');     // 用建構函式建立的「字串物件」
console.log(a);                 // "a"
console.log(b);                 // String {"bcde"}：類陣列的物件 {0:"b", 1:"c", 2:"d", 3:"e", length:4}
console.log(typeof a, typeof b);// "string" "object"

console.log(a.toUpperCase());   // "A"：a 被暫時包裹成 new String('a') 才能呼叫方法
console.log(a.__proto__ === String.prototype); // true
```
* 不建議用 `new String()`、`new Number()` 建立值：型別變成 object，比較時容易出錯（`new String('a') === 'a'` 為 false）。

#### 二、擴充內建建構函式的原型
因為原始型別最終都會連到對應的 prototype，在上面新增方法，所有該型別的值都能使用：
```
String.prototype.lastText = function(){
  return this[this.length - 1];  // this 為包裹後的字串物件
};
console.log(b.lastText());    // "e"（注意要加 () 才是呼叫，b.lastText 只會取得函式本身）
console.log('hello'.lastText()); // "o"：原始字串也能使用

Number.prototype.secondPower = function(){
  return this * this;          // this 是 Number 物件，做運算時會自動轉回原始數值
};
var num = 5;
console.log(num.secondPower());  // 25
// console.log(5.secondPower()); // SyntaxError：小數點會被當成數字的一部分
console.log((5).secondPower());  // 25：數字字面值需加括號

var date = new Date();
console.log(date);  // 現在的時間
Date.prototype.getROCYear = function(){
  return this.getFullYear() - 1911;  // 西元年轉民國年
};
console.log(date.getROCYear());  // 例如 2026 年 → 115
```
* `null`、`undefined` 沒有包裹物件，所以 `null.toString()` 會出現 TypeError。
* 與 5-1 相同：修改內建原型僅作學習用途，實務上應避免（可能與未來新增的標準方法衝突）。

### 5-4 使用 Object.create 建立多層繼承
內建物件本身就是多層繼承，例如陣列：
```
var a = [];  // a（實例）→ Array.prototype → Object.prototype → null
```
我們也可以自己建立「Animals → Dogs → 實例」的多層結構。

#### 一、物件直接繼承物件
```
function Dogs(name, color, size){
  this.name = name;
  this.color = color;
  this.size = size;
}
var dog1 = new Dogs("Bibi", "Brown", "Small");
var dog2 = Object.create(dog1);  // dog2 的原型是 dog1
dog2.name = "Pupu";              // 只覆蓋 name，其餘從 dog1 繼承

console.log(dog2.name, dog2.color);  // "Pupu" "Brown"
// 原型鍊：dog2 → dog1 → Dogs.prototype → Object.prototype → null
```

#### 二、建構函式的多層繼承（完整寫法）
目標結構：`Object.prototype → Animals.prototype → Dogs.prototype → dog1`
```
// 1. 父層建構函式
function Animals(family){
  this.kingdom = "Animals";
  this.family = family || "people";  // 沒有傳值時給預設值
}
Animals.prototype.move = function(){
  console.log(this.name + " is moving.");
};

// 2. 子層建構函式
function Dogs(name, color, size){
  Animals.call(this, "dogs");  // 借用父層建構函式，讓實例擁有 kingdom、family 屬性
  this.name = name;
  this.color = color;
  this.size = size || "small";
}

// 3. 串接原型鍊：讓 Dogs.prototype 的 __proto__ 指向 Animals.prototype
Dogs.prototype = Object.create(Animals.prototype);
// 4. 補回 constructor（上一行把 prototype 整個換掉，原本的 constructor 也不見了）
Dogs.prototype.constructor = Dogs;

// 5. 子層自己的方法（必須在步驟 3 之後才新增，否則會被覆蓋掉）
Dogs.prototype.bark = function(){
  console.log(this.name + " barked.");
};

// 6. 建立實例（必須在步驟 3 之後，否則實例會連到舊的 prototype）
var dog1 = new Dogs("Bibi", "Brown", "Small");
console.log(dog1);
// Dogs {kingdom: "Animals", family: "dogs", name: "Bibi", color: "Brown", size: "Small"}
dog1.bark();  // "Bibi barked."    ← Dogs.prototype
dog1.move();  // "Bibi is moving." ← Animals.prototype
```
重點說明：
* **`Animals.call(this, "dogs")`**：繼承「屬性」。以目前的實例為 this 執行 Animals，等同於把父層的屬性設定步驟搬過來。
* **`Object.create(Animals.prototype)`**：繼承「方法」。建立一個以 `Animals.prototype` 為原型的新物件當作 `Dogs.prototype`。
* 為什麼不寫 `Dogs.prototype = Animals.prototype`？這樣兩者會是**同一個物件**，在 Dogs 上新增 bark，Animals 的實例也會有 bark。
* 為什麼不寫 `Dogs.prototype = new Animals()`？會多執行一次 Animals，並把 kingdom、family 等屬性放到原型上，不夠乾淨。
* ES6 的 `class Dogs extends Animals { constructor(){ super("dogs"); } }` 底層做的就是上述步驟。

### 5-5 原型鏈、建構函式整體結構概念
將 5-2 ~ 5-4 整合後，完整結構如下（加入同層的 Cats 對照）：
```
                         null
                          ↑ __proto__
                   Object.prototype   (toString, hasOwnProperty...)
                          ↑ __proto__
                   Animals.prototype  (move)        ←prototype── Animals
                   ↑               ↑
          __proto__│               │__proto__
       Dogs.prototype         Cats.prototype        ←prototype── Dogs / Cats
          (bark)                 (meow)
          ↑     ↑                ↑     ↑
       dog1   dog2            cat1   cat2           ← new Dogs() / new Cats()
```
* 實線往上的 `__proto__` 就是**原型鍊**，屬性查找沿這條路往上找。
* 每個 `prototype` 物件都有 `constructor` 指回自己的建構函式。
* 建構函式本身也是物件：`Dogs.__proto__ === Function.prototype`，`Function.prototype.__proto__ === Object.prototype`。所以 Object 位於整個結構的頂端，**萬物皆繼承自 Object.prototype**（除了 `Object.create(null)` 建立的物件）。

驗證結構是否正確：
```
function Cats(name){ Animals.call(this, "cats"); this.name = name; }
Cats.prototype = Object.create(Animals.prototype);
Cats.prototype.constructor = Cats;
Cats.prototype.meow = function(){ console.log(this.name + " meowed."); };
var cat1 = new Cats("Mimi");

console.log(Object.getPrototypeOf(dog1) === Dogs.prototype);              // true
console.log(Object.getPrototypeOf(Dogs.prototype) === Animals.prototype); // true
console.log(Object.getPrototypeOf(Animals.prototype) === Object.prototype);// true
console.log(dog1 instanceof Dogs, dog1 instanceof Animals, dog1 instanceof Object); // true true true
console.log(dog1 instanceof Cats);       // false：Dogs 與 Cats 是兄弟關係
console.log(Animals.prototype.isPrototypeOf(cat1)); // true
console.log(dog1.constructor === Dogs);  // true：確認 constructor 有補回
console.log(typeof cat1.bark);           // "undefined"：貓沒有狗的方法
```

### 5-6 課後練習
#### 第1題
本章節提到原型鍊的使用：Object->Animals->Dogs(dog1, dog2), Cats(cat1, cat2)
請自己思考一個多層級的主題撰寫，必須符合上一小節的驗證概念
條件：
* 必須使用 Object.create
* 結構必須正確
* 每個原型需有獨立的方法

#### 第2題
請寫出此題的執行結果並說明原因
```
function s(n){
  var n=1;
  this.name=name;
  return this.name;
}
s.__proto__.h=function(n){
  return this(n);
}
console.log(s.h(n));
var n=2;
```
#### 第3題
請寫出此題的執行結果並說明原因
```
var a=new String("1");
var b=new Number("2");
Number.prototype.callFu=function(){return "3";}
console.log(b.callFu());
```

#### 第4題
請寫出此題的執行結果並說明原因
```
var person={name: "father", age: 28}
var family=Object.create(person);
console.log(family, family.name);
```

#### 第5題
請寫出此題的執行結果並說明原因
```
function sayHi(){
  var name="C";
  return name;
}
sayHi.name="M";
sayHi.__proto__.hello=function(){
  return this.name;
}
sayHi.hello();
```

#### 答案與解析

**第1題（參考答案）**

主題：`Object → Vehicles（交通工具）→ Cars（汽車）/ Motorcycles（機車）→ 實例`
```
// 第一層：交通工具
function Vehicles(wheels){
  this.wheels = wheels;
  this.speed = 0;
}
Vehicles.prototype.accelerate = function(n){
  this.speed += n;
  console.log(this.brand + " 加速到 " + this.speed + " km/h");
};

// 第二層：汽車
function Cars(brand, seats){
  Vehicles.call(this, 4);          // 繼承屬性：汽車 4 輪
  this.brand = brand;
  this.seats = seats;
}
Cars.prototype = Object.create(Vehicles.prototype);  // 繼承方法
Cars.prototype.constructor = Cars;
Cars.prototype.openTrunk = function(){
  console.log(this.brand + " 打開後車廂");
};

// 第二層：機車
function Motorcycles(brand, cc){
  Vehicles.call(this, 2);          // 機車 2 輪
  this.brand = brand;
  this.cc = cc;
}
Motorcycles.prototype = Object.create(Vehicles.prototype);
Motorcycles.prototype.constructor = Motorcycles;
Motorcycles.prototype.wheelie = function(){
  console.log(this.brand + " 拉孤輪！");
};

// 實例
var car1 = new Cars("Toyota", 5);
var car2 = new Cars("Honda", 7);
var moto1 = new Motorcycles("Gogoro", 125);
var moto2 = new Motorcycles("Kymco", 150);

car1.openTrunk();     // "Toyota 打開後車廂"   ← Cars.prototype
car1.accelerate(60);  // "Toyota 加速到 60 km/h" ← Vehicles.prototype
moto1.wheelie();      // "Gogoro 拉孤輪！"     ← Motorcycles.prototype

// 驗證
console.log(Object.getPrototypeOf(car1) === Cars.prototype);                 // true
console.log(Object.getPrototypeOf(Cars.prototype) === Vehicles.prototype);   // true
console.log(Object.getPrototypeOf(Vehicles.prototype) === Object.prototype); // true
console.log(moto2 instanceof Motorcycles, moto2 instanceof Vehicles);        // true true
console.log(car2 instanceof Motorcycles);   // false
console.log(car1.constructor === Cars);     // true
console.log(typeof moto1.openTrunk);        // "undefined"：各原型的方法互相獨立
```
檢查重點：每層都用 `Object.create` 串接 prototype、有補回 `constructor`、子層用 `.call(this)` 繼承屬性、每個 prototype 都有自己獨立的方法。

---

**第2題**

執行結果（瀏覽器、非嚴格模式）：印出 `""`（空字串，console 看起來是一行空白）

解析：
1. **創造階段**：`var n` 被提升，值為 `undefined`；函式 `s` 完整提升。所以執行到 `console.log(s.h(n))` 時，`n` 是 `undefined`（`var n=2` 還沒執行）。
2. **`s.__proto__.h`**：`s` 是函式，`s.__proto__` 就是 `Function.prototype`，等於在**所有函式**上都新增了 `h` 方法。
3. **`s.h(undefined)`**：以 `s.` 呼叫，所以 `h` 裡的 `this` 是 `s`；`this(n)` 等於 `s(undefined)`。
4. **`s(undefined)`**：這是**簡易呼叫**，`this` 指向全域 `window`。
   * `var n=1`：參數 n 已存在，重複宣告無效果，只是重新賦值為 1（與結果無關）。
   * `this.name=name`：右邊的 `name` 在 s 內找不到，沿範圍鏈找到全域，也就是 `window.name`。`window.name` 是瀏覽器內建屬性，預設為空字串 `""`。
   * 所以等於 `window.name = window.name`，回傳 `""`。
5. 補充：若在 Node.js 執行，全域沒有 `name`，會拋出 `ReferenceError: name is not defined`；若是嚴格模式，`this` 為 `undefined`，`this.name` 會拋出 TypeError。

---

**第3題**

執行結果：`"3"`

解析：
* `b = new Number("2")` 是一個 Number 物件，其 `__proto__` 指向 `Number.prototype`。
* 在 `Number.prototype` 新增 `callFu` 後，`b` 本身沒有 `callFu`，沿原型鍊在 `Number.prototype` 上找到並執行，回傳 `"3"`。
* `a` 是 String 物件，原型鍊為 `a → String.prototype → Object.prototype`，和 `Number.prototype` 無關，因此 `a.callFu()` 會是 TypeError（本題沒有呼叫，屬於干擾項）。

---

**第4題**

執行結果：`{} "father"`（瀏覽器中展開 `{}` 可看到 `[[Prototype]]: {name: "father", age: 28}`）

解析：
* `Object.create(person)` 建立一個**空物件**，並將其原型設為 `person`。
* 印出 `family` 時，只會顯示它**自身**的屬性，所以是 `{}`。
* `family.name`：自身找不到，沿原型鍊往上在 `person` 找到 `"father"`。
* 驗證：`family.hasOwnProperty("name")` 為 false、`Object.getPrototypeOf(family) === person` 為 true。

---

**第5題**

執行結果：`"sayHi"`（在 console 直接執行會顯示回傳值；程式中沒有 console.log 所以不會印出東西）

解析：
* **`sayHi.name="M"` 沒有效果**：函式的 `name` 屬性是內建的**唯讀屬性**（`writable: false`），非嚴格模式下賦值會靜默失敗，嚴格模式下會拋出 TypeError。可用 `Object.getOwnPropertyDescriptor(sayHi, "name")` 驗證。
* `sayHi.__proto__` 即 `Function.prototype`，在上面新增 `hello`，所有函式都能使用。
* `sayHi.hello()`：以 `sayHi.` 呼叫，`this` 指向 `sayHi`，`this.name` 取得函式名稱 `"sayHi"`。
* 函式內的 `var name="C"` 是區域變數，只有執行 `sayHi()` 時才存在，與函式物件的 `name` 屬性無關，也是干擾項。
