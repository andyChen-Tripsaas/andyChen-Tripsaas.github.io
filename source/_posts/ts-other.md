---
title: TypeScript 其他型別
date: 2023-05-14 15:53:47
tags: 
  - TypeScript
---

## 索引型別
基本上這個只有在object上面會看到，直接來看最基本的code
```typescript
type stringDict = {[key: string]: string}
const obj: stringDict = {
  name: "andy",
  age: "18"
}

obj.abc = 123; // Type 'number' is not assignable to type 'string'.(2322)
```
上面的code就是最基本的用法

而像前面有介紹過的readonly都可以用上去，這邊就不多做介紹。

所以簡單來說，這是一個可以控制物件key-value型別的型別。

但還是要注意，key的部分型別只能是`string` or `number`，使用`string`時就是模擬`map`（在其他語言裡面就是`Dictionary`的概念）。而使用`number`時就是模擬`array`(我自己是比較少這樣做）。

## 複合型別
### 聯集型別
通常用在變數具有多種型別的狀態。
```typescript
type a = number | string | boolean;

interface bb {
  type: string;
  name: string;
}

interface bbb {
  type: string;
  age: number
}

type user = bb | bbb;
```

同常用上聯集型別後，我們就必須了解什麼是Type Guard。
Type Guard主要是用來檢查我們的變數是什麼型別，這樣我們可以針對不同型別進行不同的處理。

以下說明各種型別我自己比較常用的方式
1. 如果是原始型別，除了`null`和`undefined`，其他的都可以直接用`typeof`來判斷
2. 如果是物件型別，可以用`instanceof`來判斷(有時候會用到`in` or `hasOwnProperty`)
3. 如果是`null`和`undefined`，直接用`===`來判斷

### 交集型別
簡單來說和JS的`&&`是類似的東西。主要是用於表達變數具有多個型別的狀態。
通常我們會用到這個型別的地方是object。因為假如拿多個原始型別交集會變成`never`型別。


```typescript
type Personal = {
  name: string;
  age: number;
}

type Company = {
  name: string;
  build: string;
}

type aaa = Personal & Company;

const obj: aaa =  {
  name: "234",
  age: 23,
  build: "234"
}
```

P.S. 假如你交集的物件型別裡面比此有的屬性名稱相同但型別不同，會得到`never`型別。
