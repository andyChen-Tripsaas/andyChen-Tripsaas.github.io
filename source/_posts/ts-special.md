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

所以，如果用了unknown型別後要怎麼使用各型別的方法
1. 透過Type Guards限縮
    - 使用in(通常用於確認某屬性於object中，in的前面是`屬性名稱`後面是`變數`)
    ```typescript
    interface A {
      ID: string
    }
    interface B {
      name: string
    }
    interface C {
      age: number
    }
   
    let idCard: A | B | C = {
      name: 'andy'
    }
   
    const getIdentify = (input: A | B | C) => {
        if("id" in input) {
            console.log("interface a")
        }
        if("name" in input) {
            console.log("interface b")
        }
        if("age" in input) {
            console.log("interface c")
        }
        console.log("none")
    }
    getIdentify(idCard)
    ```
    - typeof or instances
    ```typescript
    function stringOrDate(input: unknown): void {
        if(typeof input === "string") {
            input.split(",");
        }
    
        if(input instanceof Date) {
            console.log(input.toISOString())
        }
    
        console.log("none");
    }
    ```
2. 型別斷言
    ```typescript
    const val: unknown = "my name, andy";
    const x: string = val as string;
    console.log(x.toUpperCase())
    ```

講了那麼多，所以我們什麼時候會用到unknown
1. 用於檢查使用者的輸入
```typescript
function splitString(input: unknown): string[] {
  if(typeof input === "string") {
    return input.split(",")
  }else{
    return []
  }
}

// 當然你要把input限定成input才行，並且先做檢查才使用string的method也可以
function splitString(input: string): string[] {
  if(typeof input === "string") {
    return input.split(",")
  }else{
    return []
  }
}
```
2. 確保變數的屬性or方法不會被輕易使用
假設你今天要寫一個可以兼容各種方法的
```typescript
function checkArrayPenultimatePlaceBeSame(a: unknown, b: unknown): boolean {
  if(typeof a === "string") {
    if(typeof b === "string") {
      return true
    }
    if(typeof b === "number") {
      console.log(b.toString());
      return false;
    }
  }
  // ...
}
```
講白了，範例2也是有變形的方法，就是透過複合型別，不過在一些最底層的code，可能會要處理各種型別的傳入，我們就可以透過`unknown`型別。

## Never
Never型別是TS 2.0所推出的型別，用於代表
1. 應該要有回傳卻沒有回傳值的函示（ex: 函示無限迴缺，不會停止）
2. 拋出error的函示

根據TS的文檔
1. 沒有任何型別是Never型別的子型別（簡單來說就是Never型別只有自己）
2. Never型別是所有型別的子型別（簡單來說就是所有型別都包含Never型別）

```typescript
function check(x: string | number): boolean | never {
  if (typeof x === 'string') {
    return true;
  } else if (typeof x === 'number') {
    return false;
  }
  throw new Error("error");
}

// 我們check的方法回傳寫 `boolean` or `boolean | never`都不會錯
// 在舊一點的TS版本甚至會看到check的回傳格式變成`boolean`沒有`never`型別
```

一般來說，除非你的function很明確就是回傳錯誤訊息（throw error)，不然我個人建議never型別可以讓他被推論就可以。

P.S. never vs void
void我會說成是我們預期是沒有回傳內容，但never是表示他是無法正常的返回回傳內容。