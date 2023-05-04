---
title: TypeScript型別指定
date: 2023-05-02 20:30:26
tags: 
  - TypeScript
---
## 型別推論 - Type Inference

沒有明確指定型別時，編譯器會依據型別推斷來推測型別。

PS: 大部分時間來說我們可以使用型別推斷，除以下狀況建議使用註記 or 斷言。
- 先宣告參數沒賦予值（延遲指派）
- function的參數 & function的回傳值

## 型別註記 - Type Annotation

手動指定型別。告訴編譯器哪個變數就是哪個型別。

1. 冒號（：）註記：
```typescript
const fruit: string = "鳳梨"
// Equal
const fruit = "鳳梨"
```
2. 箭頭（ => ）註記：
```typescript
// function Declaration
function add(a: number, b: number): number {
	return a + b
}

// function Expression
let add = function(a: number, b: number): number {
	return a + b
}		
// Equal
let add = (a: number, b: number): number => {
	return a + b
}
// Equal
let add: (a: number, b: number) => number = (a, b) => {
	return a + b
}
```

## 型別斷言 - Type Assertions

覆蓋推斷的型別，並且只能用在`表達式`上。

```typescript
let obj = {} // Typescript推斷為{}
obj.age = 28; // error
obj.name = 'andy' // error

// fix
interface People {
	age: number
	name: string
}
const obj = {} as People
obj.age = 28
obj.name = 'andy'

// Equal
const obj = <People> {
	age: 28,
	name: 'andy'
}

// But
// 不另外針對obj裡面的屬性賦值也不會錯
const obj = {} as People;

// 推薦做法
const obj2: obj = {
	// ...
}

// 另外一個使用情境
function getLength(param: string | number): number {
	if((<string>param).length) {
		return (<string>param).length;
	}else{
		return param.toString().length;
	}
}
```


## 型別註記 vs 型別斷言
- 註記通常用在宣告上面，規範內容
- 斷言只能用在表達式上面
- 斷言大多用在第三方套件的方法or物件
- 斷言會用在JSON.parse回來的資料