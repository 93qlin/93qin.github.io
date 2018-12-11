---
title: 算法
categories: "算法"
tags:
  - JavaScript
  - 算法
---
#### JS实现的数组全排列输出算法
> 从n个不同元素中任取m（m≤n）个元素，按照一定的顺序排列起来，叫做从n个不同元素中取出m个元素的一个排列。当m=n时所有的排列情况叫全排列。
```
function permute(input) {
  var permArr = [],
  usedChars = [];
  function main(input){
    var i, ch;
    for (i = 0; i < input.length; i++) {
      ch = input.splice(i, 1)[0];
      usedChars.push(ch);
      if (input.length == 0) {
        permArr.push(usedChars.slice());
      }
      main(input);
      input.splice(i, 0, ch);
      usedChars.pop();
    }
    return permArr
  }
  return main(input);
};
console.log(permute([5, 3, 7, 1]));
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
