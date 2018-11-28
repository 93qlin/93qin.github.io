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
