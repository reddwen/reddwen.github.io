---
title: 常见算法（JS实现）整理
date: 2018-05-16 21:12:44
tags: javascript 
---
# 排序和搜索算法
## 排序算法

常用的排序算法有很多，如
* 冒泡排序 
* 选择排序
* 插入排序
* 归并排序
* 快速排序
* 堆排序

下面我们就针对这几个排序算法来看看，了解一下思想

### 冒泡排序
```javascript
function ArrayList(){
    let arr = [];

    // 将数据插入到数组中
    this.insert = function(item){
        arr.push(item);
    }

    //展示数组结构用
    this.show = function(){
        return arr.join(' < ')
    }

    // 冒泡排序
    this.bubbleSort = function(){
        let len = arr.length;
        for( let i = 0 ; i < len ; i++ ){
            // 这里之所以再－i，是因为外层循环已经跑完一轮
            // 内循环就没有必要再比较一回了
            for (let j = 0; j < len - 1 - i; j++) {  
                if (arr[j] > arr[j + 1]) {  
                    // 当前项和下一项做比较，如果大于的话就按照下面交换位置
                    // ES6利用解构赋值的方式轻松实现j和j+1值的交换
                    [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
                }
            }
        }
    }
}

//测试一下

let arr = [5, 4, 3, 2, 1];

let createList = function(arr) {
    let list = new ArrayList();
    for (let i = 0; i < arr.length; i++) {
        list.insert(arr[i]);
    }
    return list;
};

let item = createList(arr);

console.log(item.string()); // 排序前 5 < 4 < 3 < 2 < 1

item.bubbleSort();

console.log(item.string()); // 排序后 1 < 2 < 3 < 4 < 5
```
>从实现方式来看**冒泡排序**是最简单的一种，而且从运行时间的角度来看，冒泡排序是最差的一个，因为做了两层循环导致，复杂度为**O(n^2)**

### 选择排序

所谓选择排序算法，其实就是拿数组中的一个值做标杆，和其他的值做比较，如果比标杆还小的就替换标杆的值，以此类推就可以了。
```javascript
function ArrayList(){
    // 前面省略，同上文中代码块...

    //选择排序
    this.selectSort = function(){
        let len = arr.length,
            min = '';
        for( let i = 0 ; i < len - 1 ; i++ ){
            // 取第一个数作为标杆
            min = i ;

            // 内部循环从i开始到数组结束
            for (let j = i; j < len; j++) { 

                // 比标杆的值还小，就替换新值
                if (arr[min] > arr[j]) {    
                    min = j;
                }
            }

            // 上面经过一顿比较替换，如果标杆的值和之前取的第一个值不同了，就交换位置
            if (i !== min) {    
                [arr[i], arr[min]] = [arr[min], arr[i]];
            }
        }
    }
}
```

>**选择排序**我们看到其实也是嵌套了两层循环的操作，这样的话，其实复杂度和冒泡排序是一样的，为**O(n^2)**

### 插入排序

插入排序算法是假定数组的第一项已经是排好序的了，直接用它和第二项去比较，如果比第二项大就将索引和值都进行交换，以此来推来实现排序

```javascript
function ArrayList() {
    // 省略...
    // 插入排序
    this.insertSort = function(){
        let len = arr.length,
            index,temp;

        // 这里默认第一项已经排序了，直接从第二项开始
        for (let i = 1; i < len; i++) {
            // 用来记录一个索引
            index = i ;

            // 储存一个临时变量，方便之后插入位置
            temp = arr[i];

            // 索引必须是大于0，并且数组前一项的值如果大于临时变量的值
            // 就将前一项的值赋给当期项，并且index--
            while(index > 0 && arr[ index - 1] > temp){
                arr[index] = arr[index -1];
                index--;
            }

            // 最后在一顿替换后插入到了正确的位置上
            arr[index] = temp;
        }
    }

    // 测试用例写在冒泡排序那里
    let arr = [3, 5, 1, 4, 2];
    // 按照这个数组，先来分析前两项，剩下的大家自己来推到即可了
/*
    Tips: -> 符号为最后对应的值
    进入for循环i从1开始那此时
        index = i -> 1;  next -> 2
        tmp = arr[1] -> 5;  next -> arr[2] -> 1
        while循环条件   1 > 0 && (arr[1-1]-> arr[0] -> 2) > 5
                                    不成立继续循环
                                    next  2>0 && 5 > 1
                                    next  1>0 && 3 > 1
            arr[2] = arr[1] -> 5
            arr[1] = arr[0] -> 3
            index--;  index -> 1  index -> 0
        最后
        arr[index] = tmp -> arr[1] = 5;   next  arr[1] = 1  arr[0] = 1
        
        就实现了值的交换
        次时arr为[1, 3, 5, 4, 2]
        剩下的以此类推即可了
*/
```
插入排序显然已经比冒泡和选择排序算法在性能上好很多了，不过有一点小遗憾啊，就是排序的数组不要太大，太大了它就受不鸟啦！

### 归并排序

这一个算法有点儿难度，我在写这篇文章时，发现我读过的一个作者写的是错的，我模拟了步骤，怎么也进行不下去，所以仔细查了资料，找到了下面这种算法实现，大家不需要死记，理解原理是最重要的。

```javascript
function ArrayList(){
    //省略

    function mergeSort(arr){
        // 设置终止的条件，
        if (arr.length < 2) {
            return arr;
        }

        //设立中间值
        var middle = parseInt(arr.length / 2);

        //第1个和middle个之间为左子列
        var left = arr.slice(0, middle);

        //第middle+1到最后为右子列
        var right = arr.slice(middle);

        if(left=="undefined"&&right=="undefined"){
            return false;
        }
        return merge(mergeSort(left), mergeSort(right));
    }

    function merge(left, right){
        var result = [];

        while (left.length && right.length) {
            if(left[0] <= right[0]){
                //把left的左子树推出一个，然后push进result数组里
                result.push(left.shift());
            }else{
                //把right的右子树推出一个，然后push进result数组里
                result.push(right.shift());
            }
        }
        //经过上面一次循环，只能左子列或右子列一个不为空，或者都为空
        while (left.length){
            result.push(left.shift());
        } 
        while (right.length){
            result.push(right.shift());
        }
        return result;
    }
}    

```
归并排序采用的是一种分治算法，就是将一个大问题拆成小问题来解决，等每个小问题都解决了，大问题也就搞定了。

实现思想：将原数组切分成小数组，直到每个数组只有一项，然后再将小数组合并成大数组，最后得到一个排好序的大数组。

### 快速排序

我们要实现一个标准快排，首先要规避上面提到的问题：

取中间值要用下角标，切勿用splice。
要用原地数组交换，切勿用临时数组。

我们期望实现的函数是这样的：
```javascript
sort(arr);
```
那么，首先我们先实现一个交换函数，用于数组成员之间的交换
```javascript
    // 原地交换函数，而非用临时数组
    function swap(array, a, b) {
        [array[a], array[b]] = [array[b], array[a]];
    } 
```
快速排序是分治策略的经典实现，分治的策略如下：
* 分解(Divide)步骤：将问题划分未一些子问题，子问题的形式与原问题一样，只是规模更小
* 解决(Conquer)步骤：递归地求解出子问题。如果子问题的规模足够小，则停止递归，直接求解
* 合并(Combine)步骤：将子问题的解组合成原问题的解

快速排序函数,我们需要将排序问题划分为一些子问题进行排序，然后通过递归求解，我们的终止条件就是,当 `array.length > 1` 不再生效时返回数组。

```javascript
function quick(array, left, right) {
    let index;
    if (array.length > 1) {
      index = partition(array, left, right);
      if (left < index - 1) {
        quick(array, left, index - 1);
      }
      if (index < right) {
        quick(array, index, right);
      }
    }
    return array;
  }
function quickSort(array) {
    return quick(array, 0, array.length - 1);
  };
```
接下来实现划分操作
```javascript
// 划分操作函数
  function partition(array, left, right) {
    // 用index取中间值而非splice
    const pivot = array[Math.floor((right + left) / 2)];
    let i = left;
    let j = right;

    while (i <= j) {
      while (compare(array[i], pivot) === -1) {
        i++;
      }
      while (compare(array[j], pivot) === 1) {
        j--;
      }
      if (i <= j) {
        swap(array, i, j);
        i++;
        j--;
      }
    }
    return i;
  }
  
  // 比较函数
  function compare(a, b) {
    if (a === b) {
      return 0;
    }
    return a < b ? -1 : 1;
  }
```

### 二分法查找算法（查找位置）

这个算法是查找算法，不是排序了。
二分查找，又叫做折半查找

* 首先，从有序数组的中间的元素开始搜索，如果该元素正好是目标元素（即要查找的元素），则搜索过程结束，否则进行下一步。
* 如果目标元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半区域查找，然后重复第一步的操作。
* 如果某一步数组为空，则表示找不到目标元素。

```javascript
function binSearch (arr, data) {
	var low = 0;
	var high = arr.length - 1
	while (low <= high) {
		var middle = Math.floor((low + high) / 2)
		if (arr[middle] < data) {
			low = middle + 1
		} else if (arr[middle] > data) {
			high = middle - 1
		} else {
			return middle
		}
	}
	return -1
}
var arr=[3,44,38,5,47,15,36,26,27,2,46,4,19,50,48];
console.log(binSearch(arr, 15))
```

# 梳理一下

## 排序算法

冒泡排序 -> 嵌套两层循环，和下面的选择排序一样性能不好
选择排序 -> 楼上的没脑子
插入排序 -> 相比楼上两位，它的性能会好很多，不过只适用于数据量少的数组
归并排序 -> 很有来头，被认可的排序算法
快速排序 -> 很常用的排序算法，性能也很好

## 搜索算法

二分查找法 -> 猜猜猜，押大押小，小了就在小的一侧找，大了就去大的一侧找

## 总结 

算法很有用，提高工作效率，重构的时候也很有效果，算法也很费脑，很难吃下去，所以需要坚持，我也会继续学习，继续补全这篇文章。