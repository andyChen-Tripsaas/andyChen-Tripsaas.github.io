---
title: TypeScript獨有型別
date: 2023-05-03 23:10:18
tags: 
  - TypeScript
---
## 元組(Tuple)
有固定個數，且固定型別順序的組合。

先來看元組的範例:

```typescript
const locations = [
	[116, '文山區'],
	[105, '松山區']
]
```

以上面的code來說，是一個二維陣列，如果透過型別推論的方法來推論，我們會發現推論出來的結果
```typescript
const locations: (string | number)[][]
```

由此可知，如果是要使用元組的狀況，我們需要主動進行註記。

元組註記方式與我們先前說過的array不同，元組是透過`中括弧`來放置元素型別。
```typescript
// array
// 這是指array裡面可以是number or string type
const arr: (number | string)[] = ['1', 2, '3']

// tuple
const tup: [number, string][] = [[116, '文山區'], [105, '松山區']]
```

元組一般來說使用上較少，比較常用到的地方可能會類似讀取csv or excel資料時可以用到，或是在座標的系統上可以用到。

不過一般來說我自己會建議使用物件的型別來去註記型別，畢竟在元組那，單看資料有時候是無法理解其順序的。

P.S. 座標系統
```typescript
type Coordinate = [number, number] // 緯度, 經度
```

## 列舉(enum)
列舉是將性質相同的元素，透過物件Key的方式來聚集。

而在宣告enum的同時，其實也就是在做型別化名(type x = xxx)
```typescript
// 不透過enum的方式
const Colors = {
	Blue: 'Blue',
	Red: 'Red',
	Green: 'Green'
}

// enum
enum Colors { Blue, Red, Green }
```

enum的註記如果是要用在變數上，一般來說我是不會註記，我們可以同下面的code來看。

```typescript
let color1 = Colors.Blue; // 推論出let color1 = Colors.Blue;
let color2: Colors = Colors.Red; // let color2: Colors

let color3: Colors.Green = Colors.Green; // let color3: Colors.Green (這不就是明文型別)
const color4 = Colors.Green;
```

基本上都可以直接推論出是哪個enum，除非你是要把這個enum用在某個func的參數，那就一定要註記了。

### 列舉的值
前面我們都是在說enum的key或是如何宣告，這邊說明enum的value，可以想像就是前面不透過enum object的key。

在TS內，enum的預設值是從0開始遞增的數字，也就是說如果我們直接console.log enum的值
```typescript
console.log(Colors.Blue) // 0
console.log(Colors.Red) // 1
console.log(Colors.Green) // 2
```

但當然enum的值是可以更改的

```typescript
enum Colors {
	Blue = 1, // 1
	Red = 2, // 2
	Green = 3 // 3
}

enum Colors {
	Blue = 1, // 1
	Red, // 2 (Blue的1 + 1)
	Green // 3 (Red的2 + 1)
}
```

假使我們今天希望跟前面講到的不透過enum的object，key是string，那就需要自行更改值
```typescript
enum Colors {
	Blue = 'Blue', 
	Red = 'Red', // 我們前面的code有提到是根據前面的元素在去做動作，前面是字串，後面的要自行手動註記
	Green = 'Green' // 同上
}

// 這樣是OK的
enum Colors {
	Blue, // 0
	Red, // 1
	Green = 'Green' // 我不透過上面的元素再動作
}
```

另外enum的key只能是`string`或是`number`，以下是錯的
```typescript
enum Colors {
	Blue = true, // Type 'boolean' is not assignable to type 'number' as required for computed enum member values.
	Red = undefined, // Type 'undefined' is not assignable to type 'number' as required for computed enum member values.
	Green = { abc: 123 } // Type '{ abc: number; }' is not assignable to type 'number' as required for computed enum member values
}
```

P.S. enum不會幫你檢查值有沒有重複 or 不合理的地方，所以在定義的時候不要value定義相同來搞自己
```typescript
enum Colors {
	Blue = 0,
	Red = 0,
	Green = 1,
}

console.log(Colors.Blue === Colors.Red) // true
```

P.S.2
1. 若列舉的值為預設的數字值，我們可以逆著推回來值的Key。這是因為TypeScript在編譯TS的code時，會創建所謂的反向映射。
```typescript
enum Colors {
    Blue,
    Red,
    Green,
}

console.log(Colors[0] === 'Blue') // true
```
編譯結果
```typescript
var Colors;
(function (Colors) {
    Colors["Blue"] = "blue";
    Colors[Colors["Red"] = 237] = "Red";
    Colors[Colors["Green"] = 239] = "Green";
})(Colors || (Colors = {}));
```
2. 我們一般在用enum的功能時，根本不需要反向映射的功能，這是我們可以把我們enum的code加個`const`
```typescript
const enum Colors {
    Blue,
    Red,
    Green,
}
```
這時TS就不會在把enum創建反向映射，能減少code的量。