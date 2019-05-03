---
title: 算法
categories: "算法"
tags:
  - JavaScript
  - 算法
---
#### JS实现的数组全排列输出算法
> 从n个不同元素中任取m（m≤n）个元素，按照一定的顺序排列起来，叫做从n个不同元素中取出m个元素的一个排列。当m=n时所有的排列情况叫全排列。
全排列（递归交换）算法  
1、将第一个位置分别放置各个不同的元素；  
2、对剩余的位置进行全排列（递归）；
```
function permutation(arr){
  if (arr.length == 1)
    return arr;
  else if (arr.length == 2)
    return [[arr[0],arr[1]],[arr[1],arr[0]]];
  else {
    var temp = [];
    for (var i = 0; i < arr.length; i++) {
      var save = arr[i];
      arr.splice(i, 1);//取出arr[i]
      var res = permutation(arr);//递归排列arr[0],arr[1],...,arr[i-1],arr[i+1],...,arr[n]
      arr.splice(i, 0, save);//将arr[j]放入数组，保持原来的位置
      for (var j = 0; j < res.length; j++) {
        res[j].unshift(arr[i]);
        temp.push(res[j]);//将arr[j]组合起来
      }
    }
    return temp;
  }
}
```

#### 判断一个单词是否是回文？
回文是指把相同的词汇或句子，在下文中调换位置或颠倒过来，产生首尾回环的情趣，叫做回文，也叫回环。比如 mamam redivider .
```
function checkPalindrom(str) {  
    return str == str.split('').reverse().join('');
}
```
#### 去掉一组整型数组重复的值
比如 输入: [1,13,24,11,11,14,1,2]，  输出: [1,13,24,11,14,2] ，需要去掉重复的11 和 1 这两个元素。
```
function(arr) {  
  let hashTable = {};
  let data = [];
  for(let i=0,l=arr.length;i<l;i++) {
    if(!hashTable[arr[i]]) {
      hashTable[arr[i]] = true;
      data.push(arr[i]);
    }
  }
  return data

}
```
#### 随机生成指定长度的字符串
实现一个算法，随机生成指制定长度的字符窜。
```
function randomString(n) {  
  let str = 'abcdefghijklmnopqrstuvwxyz9876543210';
  let tmp = '',
      i = 0,
      l = str.length;
  for (i = 0; i < n; i++) {
    tmp += str.charAt(Math.floor(Math.random() * l));
  }
  return tmp;
}
```
