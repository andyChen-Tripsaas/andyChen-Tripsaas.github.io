---
title: TypeScript型別
date: 2023-05-03 23:00:04
tags: 
  - TypeScript
---

## 原始型別 {% post_link ts-origin '連結' %}
- number
- string
- boolean
- undefined:  未定義的空值
- null: 代表空值
- void: 通常用在函式型別沒有回傳的狀態
- symbol: 想成是一個獨一無二的東西
```javascript
Symbol("Andy") === Symbol("Andy") // false
```

上述的七個原始型別<b>各自獨立</b>，也就是<b>誰也不屬於誰</b>。

P.S: [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

## 物件型別 {% post_link ts-object '連結' %}
- Function
- Array
- Instance (由 Class New出來的)

## 明文型別 {% post_link ts-object '連結' %}

## TypeScript獨有型別 {% post_link ts-only '連結' %}
- Enum
- Tuple

## 特殊型別 {% post_link ts-special '連結' %}
- any
- never
- unknown

## Other {% post_link ts-other '連結' %}
- 索引型別
- 複合型別

## Interface vs Type {% post_link ts-interface '連結' %}

## Class用法

## 泛型


