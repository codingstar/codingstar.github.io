title: ife-2016s task4笔记
date: 2016-05-16 16:46:51
tags: [前端,总结,技术]
---

## 题目要求 [传送门](http://ife.baidu.com/task/detail?taskId=4)
实现灰色元素水平垂直居中，有两个四分之一圆位于其左上角和右下角。

## 关键实现方法
由于需要在任何情况下都能使元素水平垂直居中，因此考虑将body设置为fixed，然后用绝对定位定位灰色元素，其中的黄色扇形由`position: absolute`控制使其相对于灰色矩形位于左上角和右下角。

## Tips
1、相对（relative）定位元素的默认位置还是被元素自身占用，别的元素是无法占用的。也就是说相对定位元素的位移是相对于`元素自身`的边缘进行位移。
2、绝对（absolute）定位元素的位置是相对于最近的一个设置为`相对 或 绝对`定位的祖先元素的。
3、固定（fixed）定位元素相对于浏览器窗口，并且不会随滚动条进行滚动（不兼容IE6）。
<!--more-->
4、关于清除浮动：
    这个在task3中也有记录。不过最近接了个活，在做的过程中多次遇到清楚浮动和定位的问题，也终于对这个问题越发清晰了。之前一直不太敢用float，因为会有元素塌陷的问题而又不太会处理，因此有时就用`inline-block`替代，但是发现inline-block虽然可以使元素在同一行出现，但是在元素margin为0的情况下还会有间距，这个也很麻烦。。。。因此现在看来用float其实方便很多，只要能够很好地清除浮动～
    主要有两种方式：
            a. 在父元素上应用 `overflow: auto; *zoom:1;`
            b. 在父元素上应用清除浮动类clearfix，然后在css中应用如下代码，这样就可以使父元素中的元素也可以到父元素外面去。
                 ```
.clearfix:before,
.clearfix:after {
  content: "";
  display: table;
}
.clearfix:after {
  clear: both;
}
.clearfix {
  *zoom: 1;
}
                 ```

## 参考资料
1、[HTML和CSS定位详解](http://www.w3cplus.com/css/advanced-html-css-lesson2-detailed-css-positioning.html)
