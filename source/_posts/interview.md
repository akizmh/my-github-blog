---
title: 题集(1)
date: 2017-03-06 09:40:21
tags: 
categories: 技术
---

## js中浮点数计算产生的问题如何产生，如何解决?
浮点数计算出现的误差问题，主要是计算机进行的是二进制计算，然后转成十进制。
因此0.1+0.2这种数学当中理所当然等于0.3的计算在计算机运算中，比如javascript中会变成0.30000000000000004这样奇特的结果。

那么如何做0.1+0.2==0.3这种判断呢？
一种方法采用一个精确范围来比较是否相等
，比如定义一个比较小的浮点数a，当判断的两个值相减的值小于a时，就认为两值相等。
```js
x = 0.2;
y = 0.3;
equal = (Math.abs(x - y) < 0.000001)
```
ES6里引入了这个极小的浮点数常量Number.EPSILON，其值为2.220446049250313e-16。
```js
function withinErrorMargin (left, right) {
  return Math.abs(left - right) < Number.EPSILON;
}
withinErrorMargin(0.1 + 0.2, 0.3)
// true
```
<!-- more -->
另一种方法是使用JavaScript内置的函数toPrecision或toFixed来保留一定的精度。
```js
(0.1 + 0.2).toPrecision(10) == 0.3
// true

(0.1 + 0.2).toFixed(10) == 0.3
// true
```
还有一种方法是通过某种手段绕过这种浮点数运算。这里以加法为例, 我们可以把0.1和0.2先转换成1+2=3，然后再除以10，这样得到0.3。这中间的过程就避免了浮点运算带来的问题。
```js
function accAdd(arg1, arg2) {
     var r1, r2, m, c;
     try {
         r1 = arg1.toString().split(".")[1].length;
     }
     catch (e) {
         r1 = 0;
     }
     try {
         r2 = arg2.toString().split(".")[1].length;
     }
     catch (e) {
         r2 = 0;
     }
     c = Math.abs(r1 - r2);
     m = Math.pow(10, Math.max(r1, r2));
     if (c > 0) {
         var cm = Math.pow(10, c);
         if (r1 > r2) {
             arg1 = Number(arg1.toString().replace(".", ""));
             arg2 = Number(arg2.toString().replace(".", "")) * cm;
         } else {
             arg1 = Number(arg1.toString().replace(".", "")) * cm;
             arg2 = Number(arg2.toString().replace(".", ""));
         }
     } else {
         arg1 = Number(arg1.toString().replace(".", ""));
         arg2 = Number(arg2.toString().replace(".", ""));
     }
     return (arg1 + arg2) / m;
 }
```
其他减法、乘法和除法参考这里: https://gist.github.com/binjoo/3926896

## 对象的遍历方法
1. for...in
for...in循环遍历对象自身的和继承的可枚举属性（不含Symbol属性）。

2. Object.keys(obj)
Object.keys返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含Symbol属性）。

3. Object.getOwnPropertyNames(obj)
Object.getOwnPropertyNames返回一个数组，包含对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）。

4. Object.getOwnPropertySymbols(obj)
Object.getOwnPropertySymbols返回一个数组，包含对象自身的所有Symbol属性。

5. Reflect.ownKeys(obj)
Reflect.ownKeys返回一个数组，包含对象自身的所有属性，不管是属性名是Symbol或字符串，也不管是否可枚举。

以上的5种方法遍历对象的属性，都遵守同样的属性遍历的次序规则。

- 首先遍历所有属性名为数值的属性，按照数字排序。
- 其次遍历所有属性名为字符串的属性，按照生成时间排序。
- 最后遍历所有属性名为Symbol值的属性，按照生成时间排序。

## js大数据展现
用定时器分时加载，利用document.createfragment()方法创建一个文档片段，节点添加和事件绑定都在该片段上操作，最后再把这个文档片段append到页面中。能有效防止大数据循环频繁的dom操作开销。

## js内存
http://www.cnblogs.com/mliudong/p/3635294.html

## 浏览器输入url到整个页面显示出来经历的过程
大致流程如链接文章所示:http://www.jianshu.com/p/19fde9ed9fc6，主要是
1. 客户端域名解析，去哪里拿什么文件，跟服务端三次握手建立连接；
2. 客户端发送http请求，告诉服务端我要什么资源，服务端根据http响应头和响应体返回资源；
3. 客户端拿到资源之后开始渲染
每个过程都涉及到非常繁多的细节，不展开了。

## angular脏检查原理
参见这篇文章，讲的比较详细了:http://www.webnpm.com/60.html
$watch：
接受以下两个函数形成一个监听器：
- 一个监控函数，用于指定所关注的那部分数据。
- 一个监听函数，用于在数据变更的时候接受提示。
监控scope上的表达式，如scope.userName。通常在数据绑定，指令的属性，或者JavaScript代码中指定，它被Angular解析和编译成一个监控函数。
当Scope上发生变更时，监听器会收到提示。

$digest:
一次digest过程，遍历所有在作用域上注册过的监听器。调用监听器上的监控函数，并且比较它返回的值和上一次返回值的差异。如果不相同，监听器就是脏的，它的监听函数就应当被调用。

## 浏览器的repaint和reflow
repaint(重绘) ，repaint发生更改时，元素的外观被改变，且在没有改变布局的情况下发生，如改变outline,visibility,background color,opacity，不会影响到dom结构渲染。

reflow(渲染)，与repaint区别就是他会影响到dom的结构渲染，他会改变他本身与所有父辈元素(祖先)和相邻元素，这种开销是非常昂贵的，导致性能下降是必然的，页面元素越多效果越明显。

每个页面加载的时候必会reflow和repaint，页面加载完成后，用户的一些操作、脚本的一些操作都会导致浏览器发生这种行为，比如
- DOM元素的添加、修改（内容）、删除( Reflow + Repaint)
- 仅修改DOM元素的字体颜色（只有Repaint，因为不需要调整布局）
- 应用新的样式或者修改任何影响元素外观的属性
- CSS3 动画（animation）和过渡（transition）: 动画的每一 frame 都会触发 Reflow
- Resize浏览器窗口、滚动页面
- 读取元素的某些属性（offsetLeft、offsetTop、offsetHeight、offsetWidth、scrollTop/Left/Width/Height、clientTop/Left/Width/Height、getComputedStyle()、currentStyle(in IE))

常用避免办法
1. 避免使用inline style和table布局
inline style 会在 html 下载完后进行一次额外的 Reflow
2. 避免复杂动画
运用动画的元素尽量是 position:absolute 或 position:fixed 的，这样会让他们脱离文档流，不去影响其他的元素。但是手机端通过定位的动画会不太流畅，权衡考虑之。
3. 善用display:none
display:none不会除法reflow和repaint,因此可以将元素的display设置为”none”，完成修改后再把display修改为原来的值
4. 使用createDocumentFragment
如果需要创建多个DOM节点，可以使用document.createDocumentFragment创建完后一次性的加入document
5. 批量更新元素
如下的代码中，每一次赋值都会造成浏览器重新渲染，可以采用cssText或者className的方式
```js
element.style.width=”80px”;  //reflow
element.style.height=”90px”; //reflow
element.style.border=”solid 1px red”; //reflow
```
可以把这几个样式写到到一个class上，然后修改dom的className，或者用cssText
```js
element.style.cssText=”width:80px;height:80px;border:solid 1px red;”; //reflow
```
6. 缓存Layout属性值 
对于Layout属性中非引用类型的值（数字型），如果需要多次访问则可以在一次访问时先存储到局部变量中，之后都使用局部变量，这样可以避免每次读取属性时造成浏览器的渲染。
```js
var width = el.offsetWidth;
var scrollLeft = el.scrollLeft;
```