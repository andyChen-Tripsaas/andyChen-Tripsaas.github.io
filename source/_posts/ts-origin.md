---
title: 原始型別
date: 2023-05-03 23:01:57
tags: 
  - TypeScript
---
在TS裡的原始型別基本上和寫JS是一樣的東西。
```typescript
const num = 123;
const name = "andy";
const truthy = true;
const noAnnounce = undefined;
const nothing = null;
```

基本的宣告基本上與平常寫JS沒什麼差別，若需要註記也只需要注意變數後面實際的值。

在原始型別部分我們需要了解的會是以下幾點:
1. TS的型別是可以傳遞的
```typescript
// 型別傳遞
const num = 123; // number
const age = num; // age 也會被推論為number型別

// 推論出來的型別可能會有複數個
const twoType = (Math.ramdon() > 0.2) ? 123 : "123"; // 會被推論出 string | number

// 被宣告型別或推論出型別後，型別會固定起來，後續重新賦值之行為須遵照型別
let num2 = 123;
num2 = 345; // OK
num2 = "345"; // Type <Type> is not assignable to type <Other Type>
```

2. 所以到底什麼時候需要註記
   我自己是認為若這個變數是你在程式裡面初始化的(也就是說你知道他一開始的值)，這種都不需用註記型別，可以透過命名的方式了解型別。

但假設你是接收API來的資料、自訂interface...這種狀況一定要把值的型別給定義出來。

3. 延遲性指派
   所謂的延遲性指派如下面的code
```typescript
let num; // num會被推論為any
num; // num會是undefined
num = 123; // 這邊重新指派num型別，這邊看到還會是any
num; // num被重新指派為number型別
```

我們在程式中多少會遇到這種狀況，雖然能少出現就少出現。

通常遇到需要有變數延遲指派型別的狀況，會建議在該變數就先註記型別。但若是會有在對還未賦值得變數做操作，會出現錯誤訊息，遇到這種狀況可以透過聯集型別解決。
```typescript
let num: number | undefined;
num;
num = 123
```

