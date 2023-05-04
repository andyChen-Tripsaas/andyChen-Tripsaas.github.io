---
title: TypeScript特殊型別
date: 2023-05-04 20:39:07
tags:
  - TypeScript
---

## Any
Any型別解單來說就是TypeScript所有型別聯集。簡單來說你任何型別與Any型別連結就是Any型別。

基本上來說，專案上建議不要有any型別的出現，能夠避免就避免。

## Unknown
Unknown型別是TypeScript3.0推出的型別，作用是創立比Any還安全的型別(幹話)
。

主要的差異是在會把`允許屬性的操作`變成`禁止操作`還有`指派Any型別的值給其他型別`。

我們先來看Any型別的部分：
```typescript
let x: any;
x = 123;
x = true;
x = '123';
x = undefined;
x = null;
x = new Date();
x = Symbol('andy');
x = [2, 3, 4, 5, 6];

let y: any = x; // OK
let y1: unknown = x; // OK
let y2: string = x; // OK

x.length;
x.push('123')
```

簡單來說你要怎麼蝦雞掰亂寫都可以，那跟寫JS有什麼不同。

再來看看Unknown的部分
```javascript
let x: unknown;
x = 123;
x = true;
x = '123';
x = undefined;
x = null;
x = new Date();
x = Symbol('andy');
x = [2, 3, 4, 5, 6];

let y: any = x; // OK
let y1: unknown = x; // OK
let y2: string = x; // Type 'unknown' is not assignable to type 'string'.

x.length; // 'x' is of type 'unknown'.(18046)
x.push('123') // 'x' is of type 'unknown'.(18046)
```

很明顯在賦值上一樣沒問題，但差在
1. `unknown`型別給其他型別時會出現error(給Any沒問題是因為如同前面說的，Any型別是所有型別的聯集)
2. 沒版法自由的對各個型別的方法做操作。



