---
title: 一道有意思的promise题
date: 2018-05-14 09:04:08
tags: promise promise面试题 funny
---

# 题目
不多说！来了解一下。
```
const first = () => (new Promise((resovle,reject)=>{
    console.log(3);
    let p = new Promise((resovle, reject)=>{
         console.log(7);
        setTimeout(()=>{
           console.log(5);
           resovle(6); 
        },0)
        resovle(1);
    }); 
    resovle(2);
    p.then((arg)=>{
        console.log(arg);
    });

}));

first().then((arg)=>{
    console.log(arg);
});
console.log(4);
```
# 第一轮事件循环
先执行宏任务，主script ，new Promise立即执行，输出【3】，执行p这个new Promise 操作，输出【7】，发现setTimeout，将回调放入下一轮任务队列（Event Queue），p的then，姑且叫做then1，放入微任务队列，发现first的then，叫then2，放入微任务队列。执行console.log(4)，输出【4】,宏任务执行结束。  

再执行微任务，执行then1，输出【1】，执行then2，输出【2】。到此为止，第一轮事件循环结束。开始执行第二轮。

# 第二轮事件循环
先执行宏任务里面的，也就是setTimeout的回调，输出【5】。resovle不会生效，因为p这个Promise的状态一旦改变就不会在改变了。 

所以最终的输出顺序是3、7、4、1、2、5。

# 总结
对JavaScript执行机制有了解，并且知道Promise构造函数是立即执行的

# 参考资料
[这一次，彻底弄懂 JavaScript 执行机制 - 掘金](https://juejin.im/post/59e85eebf265da430d571f89)