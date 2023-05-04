---
title: 物件型別
date: 2023-05-03 23:03:03
tags:
  - TypeScript
---
## JSON物件(就是物件)
直接看code
```typescript
const info = {
	name: "andy",
	isMale: true
}

// 上面的code
// 透過vscode可以幫我們推論出型別如下
// const info: {  
//	name: string;  
//	isMale: boolean;  
// }
```

從上面的code我們可以知道，就算是JSON物件，vscode等IDE還是可以幫我們直接推論出JSON的型別。

但一般來說遇到JSON狀況，會建議把JSON的型別去定義出來。

```typescript
const info: {
	name: string;
	isMale: boolean;
} = {
	name: "andy",
	isMale: true
}

// OR

type PersonInfo = {
	name: string;
	isMale: boolean;	
}

const info: PersonInfo = {
	name: "andy",
	isMale: true
} // info的型別此時會是PersonInfo
```

可以獨立把型別定義出來or做一次性的宣告。

P.S. 在TS定義這種物件的型別時，每個變數的結尾你要用`;`還是`,`都可以。但我自己是習慣`;`。

### JSON物件的新增修改刪除

#### 新增
```typescript
// 新增
let info = {
    name: 'andy',
    age: 18
}

info.luckyNumber = 20; // Property 'luckyNumber' does not exist on type '{ name: string; age: number; }'.(2339
```

直接新增一屬性，必然報錯。可透過optional property來解決此問題。

#### 刪除
在TS4.0以前，下面的code是不會報錯的，而且再重新對age屬性讀取，會發現還是number型別。
```typescript
let info = {
    name: 'andy',
    age: 18
}

delete info.age;

info.age; // (property) age: number
```

在TS4.0以後，會出現錯誤訊息
```typescript
let info = {
    name: 'andy',
    age: 18
}

delete info.age; // The operand of a 'delete' operator must be optional.(2790)
```

要解決上述問題，可先指定info的age為聯集型別 or 透過optional property來解決此問題。

#### 修改
直接上code
```typescript
let info = {
    name: 'andy',
    age: 18
}

info.age = 20;

info = {
    name: 'linus',
    age: 15
}
```

在修改上，基本上要複寫整個object或是object裡面的某個屬性都可以，唯一要求就是型別必須相同。唯複寫整個object要注意object的結構不可被改變。

### 選用屬性(optional property)
簡單來說就是告數TS，這個物件的某個屬性不是必要的。透過在`:`前加上`?`來註記。
```typescript
type PersonInfo = {
	name: string;
	age?: number;
}

let info1: PersonInfo = {
	name: 'andy',
	age: 20
}

let info2: PersonInfo = {
	name: 'linus',
}
```

P.S. 選用屬性與聯集型別有什麼差別
```typescript
type PersonInfo = {
	name: string;
	age: number | undefined
}

type PersonInfo2 = {
	name: string;
	age?: number;
}
```

上面兩種的寫法，最主要的差異在於object的`age`屬性是否一定要有。
如果將變數的型別設定為PersonInfo，那麼age屬性`一定`要給number或是undefined型別。無法像PersonInfo2型別，裡面的age屬性可以不給。

簡單來說`省略`欄位與`值為undefined`為兩件不同的事情。

P.S. 選用屬性推論出的型別
直接在透過上面PersonInfo的例子，PersonInfo的型別會被推論為以下的格式。

```typescript
type PersonInfo = {  
	name: string;  
	age?: number | undefined;  
}
```

### 唯讀屬性(Read-Only property)
簡單來說，就是要避免直接複寫屬性的值。我們繼續拿上面的PersonInfo當範例：

```typescript
type PersonInfo = {
    name: string;
    readonly age?: number;
}

let info: PersonInfo = {
    name: 'andy',
    age: 20
}

info.age = 30; // error: Cannot assign to 'age' because it is a read-only property.(2540)

// 當然delete也會錯
delete info.age;
```

但假設，我把整個info的reference都換掉，也是複寫值，但這時候就不會有錯了。
```typescript
info = {
    name: 'linus',
    age: 30
}
``` 

假使真的要避免可以整個被複寫，可以透過以下兩種寫法，不過建議使用第二種。
```typescript
// Method1
type PersonInfo = {
	readonly name: string;
	readonly age: number;
}

// Method2
type PersonInfo = {
	name: string;
	age: number;
}

let info: Readonly<PersonInfo> = {
	name: 'andy',
	age: 20
}
```

不使用第一種方法主要是
1. 很麻煩，你JSON物件越複雜，每個屬性都要標記readonly
2. 每個屬性都變成readonly後，會導致該型別綁很死，很難繼續使用。
3. 若該定義是從第三方套件來的，很難確保該套件是否有這麼做，況且如果強行修改該套件的型別，更容易導致該套件失靈。

P.S. 可以把屬性設為選用屬性又是唯讀屬性嗎?
答案是可以，但受readonly的影響會較大，無法做像延遲型指派的方法。
```typescript
type PersonInfo = {
    name: string;
    readonly age?: number;
}

let info: PersonInfo = {
    name: 'andy',
}
info.age = 30; // Cannot assign to 'age' because it is a read-only property.(2540)
delete info.age; // The operand of a 'delete' operator cannot be a read-only property.(2704)

info = {
    name: 'linus',
    age: 30
}

let info2: PersonInfo = {
    name: 'andy',
    age: 20
}

info2.age = 30; // The operand of a 'delete' operator cannot be a read-only property.(2704)
```

## Function
函式的型別我會分兩邊來看

### 輸出(output)
基本上就是根據是否有沒有return與return的值來決定。
```typescript
const myName = () => {
    return 'andy'
} // TS推論為 const myName: () => string

// 遇到程式回傳會有分岔點的，結果可能會是聯集
const getNumOrString = () => {
    const a = Math.random();
    if(a > 0.6) return a;
    else return a.toString();
} // const getNumOrString: () => string | number
```

比較需要注意的是若回傳的內容是固定的，也就是說是明文，TS推論回傳會是明文型別
。
```typescript
// 注意，若回傳的值是固定的，TS會直接推論回傳明文型別
const getNumOrString = () => {
	const a = Math.random();
	if(a > 0.6) return 1;
	else return "1";
} // const getNumOrString: () => 1 | "1"
```

若函式沒有任何`return`的字詞 or return後面`為空`，TS都會推論函式的回傳型別是void;
```typescript
const test1 = () => {
	console.log("a")
} // const test1: () => void

const test2 = () => {
	return;
} // const test2: () => void
```

### 輸入(input)
輸入基本上都是無法被推論的，會被TS認定為是any型別。所以在輸入的參數基本上都需要註記型別。
```typescript
const add1: (a: number, b: number) => number = function(a, b) => {
	return a + b;
}

const add2 = function(a: number, b: number): number {
	return a + b;
}

function add3(a: number, b: number): number {
	return a + b;
}

const add4 = (a: number, b: number): number => {
	return a + b;
}

const add5 = function(a, b) {
	return a + b;
} as (a: number, b: number) => number;
```

上面的code是function的各種寫法，基本上我自己是習慣2和3和4的寫法，至於第五種則很少用。

所以function的註記基本上我會這樣說:
1. 輸入的參數一定要註記值
2. function的output在開發中我會註記型別，方便在開發中確保output與自己所想的一樣。但在該function開發完後，會盡量透過function命名的方式來知道function的output型別，並去除output的型別。

### 選用參數
有時候我們的function的輸入參數並不會每一個都需要帶，這時候我們可以一樣透過`?`來標註該參數。
```typescript
const add = (a: number, b?: number) => {
	return a + (b ? b : 0)
}
```

基本上使用選用參數時要注意若有多個參數是選用，不是必需的。須把這寫參數統一排在函式的最後面引入，且中間不可穿插必須的參數。(就跟你平常寫function一樣XDD)

### 預設參數
直接上code
```typescript
const add = (a: number, b: number = 1) => {
	return a + b;
}
```

這也不是TS才有的東西，有在寫JS的應該都有寫過。與上面的選用參數比較不一樣的地方是
- 選用參數的b會被推論為 number | undefined
- 預設參數的b會被推論為 number

再來同樣建議若有預設參數，一樣建議把它排在最後面，以防若第一個參數就是預設參數需要使用時，須帶入undefined。

P.S. 在function的輸入參數，選用參數與預設參數是`不可以`同時使用的。(Parameter cannot have question mark and initializer.(1015))。詳細可以從上述講的選用參數與預設參數，參數被推論出來的型別去做思考。

## Array
在Array裡，我覺得最重要的就是了解如何註記型別。且JS/TS的Array不像有些程式語言會限定為單一型別，所以基本上就是了解Array如何做型別註記。
```typescript
const feb: number[] = [1,1,2,3,5]

const arr: (number | string)[] = [1,2,'3','4']

const emptyArr: number[] = []

type User = {
    name: string;
    age?: number;
}

type Maker = {
    makerName: string;
}
const arr2: (User | Maker)[] = [{name: 'andy'}, {makerName: 'andy'}]
```

空陣列若沒有主動註記型別，會被推論為any型別，因此建議一定要註記型別。

## 明文型別
明文型別簡單來說就是每個值都是一個型別。

比較需要注意的地方我覺得是兩個:
1. 需要注意的是使用`const`和`let`的差異。
```typescript
const a = 123; // const a: 123
let b = 123; // let b: number
```

2. object出來的一定是結構，不會是明文