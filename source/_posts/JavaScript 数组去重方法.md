---
title: JavaScript 数组去重方法
date: 2018-05-28 11:47:08
tags: javascript 数组 排重
---
# 前提

一个简单的生成数组的方法，用于测试
```javascript
const arr = [];
// 生成[0, 100000]之间的随机数
for (let i = 0; i < 100000; i++) {
  arr.push(0 + Math.floor((100000 - 0 + 1) * Math.random()))
}

// ...实现算法
console.time('test');
arr.unique();
console.timeEnd('test');

```

# 方法一 双重循环  
这是一种最简单的去重的方式，但是性能是最差的。  
```javascript
Array.prototype.unique = () =>{
  const newArray = [];
  for (let i = 0; i < this.length; i++) {
    for (let j = i + 1; j < this.length; j++) {
      if (this[i] === this[j]) {
        j = ++i;
      }
    }
    newArray.push(this[i]);
  }
  return newArray;
}
```
# 方法二 对象键值对
基本思路：利用了对象的key不可以重复的特性来进行去重。
但需要注意：

* 无法区分隐式类型转换成字符串后一样的值，比如 1 和 '1'
* 无法处理复杂数据类型，比如对象（因为对象作为 key 会变成 [object Object]）
* 特殊数据，比如 'proto'，因为对象的 proto 属性无法被重写  

```javascript
Array.prototype.unique = () => {
  const newArray = [];
  const tmp = {};

  for(let i = 0; i < this.length; i++){
    if( !tmp[typeof this[i] + JSON.stringify(this[i])) {
      // 将对象进行序列化后 当做key 来使用
      tmp[(typeof this[i]) + (JSON.stringify(this[i]))] = 1;
      newArray.push(this[i]);
    }
  }

  return  newArray;
}
```

# 方法三 使用Map数据结构

```javascript
Array.prototype.unique = function () {
  const tmp = new Map();
  return this.filter(item => {
    return !tmp.has(item) && tmp.set(item, 1);
  })
}

```

# 方法四 使用set数据结构
```javascript
Array.prototype.unique = function () {
  const set = new Set(this);
  return Array.from(set);
}
//或者
Array.prototype.unique = function () {
  return [...new Set(this)];
}
```