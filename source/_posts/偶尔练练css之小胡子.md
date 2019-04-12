---
title: 偶尔练练css之小胡子
categories: "css"
tags:
  - css
---
# css实现之小胡子
> 知识要点
1.hsl(h,s,l):CSS3 颜色值HSL表示方式&简单实例
HSL色彩模式：就是色调(Hue)、饱和度(Saturation)、亮度(Lightness)三个颜色通道的改变以及它们相互之间的叠加来获得各种颜色，色调(Hue)色调最大值360，饱和度和亮度有百分比表示0-100%之间。
2.js操作css变量（变量要用--开头）：

设置：
```
root.style.setProperty("--mouse-x", x);
```
使用：
```
var(--mouse-y,0.5) //0.5表示默认值
```

#### 直接上代码（喜欢干货，不喜欢啰嗦，我也喜欢看别人的代码直接，然后看不懂的就去查资料，因为别人啰嗦一堆，很快失去兴趣很难集中精力）
> html
```
<div class="mustache"> </div>
```
> css
```
.mustache{
  width: 180px;
  height: 180px;
  left: 100px;
  border-radius: 50%;
  position: absolute;
  color: hsl(calc(360*var(--mouse-x)),100%, 75%);
  /* background-color: currentColor; */
  box-shadow: 150px 240px 0 0 currentColor, 300px 240px 0 0 currentColor
}
.mustache::before{
  width: 210px;
  height: 120px;
  content: '';
  position: absolute;
  left: 30px; /* 150+90-210 */
  top: 120px;
  border-bottom: solid 180px currentColor;
  /* background-color: red; */
  border-radius: 0 0 0 100%;
  /* transform: rotate(-40deg); */
  transform: rotate(calc(-20deg - var(--mouse-y,0.5)*40deg));
  transform-origin: right 210px;  /* 300-90 */
}
.mustache::after{
  width: 210px;
  height: 120px;
  content: '';
  position: absolute;
  left: 390px;
  top: 120px;
  border-bottom: solid 180px currentColor;
  /* background-color: red; */
  border-radius: 0 0 100% 0;
  /* transform: rotate(40deg); */
  transform: rotate(calc(20deg + var(--mouse-y,0.5)*40deg));
  transform-origin: left 210px  /* 300-90 */
}
/* .mustache:before{
  animation: shakeLeft 1s ease-in-out infinite;
}
.mustache:after{
  animation: shakeRight 1s ease-in-out infinite;
}
@keyframes shakeLeft{
  0%{
    transform: rotate(-50deg);
  }
  50%{
    transform: rotate(-30deg);
  }
  100%{
    transform: rotate(-50deg);
  }

}
@keyframes shakeRight{
  0%{
    transform: rotate(50deg);
  }
  50%{
    transform: rotate(30deg);
  }
  100%{
    transform: rotate(50deg);
  }

} */
```
> js
```
var root = document.getElementsByTagName('body')[0]
document.addEventListener('mousemove', evt => {
  let x= evt.clientX / innerWidth;
  let y = evt.clientY / innerHeight;
  root.style.setProperty("--mouse-x", x);
  root.style.setProperty("--mouse-y", y);
})
```

### 最终实现效果（鼠标上下移动胡须上下摆动，鼠标左右移动，颜色随之改变）
图略
