title: ife-2016s task3笔记
date: 2016-03-28 20:46:51
tags: [前端,总结,技术]
---

## 题目要求 [传送门](http://ife.baidu.com/task/detail?taskId=3)
    实现三栏式布局，要求左右两宽度固定，中间一栏填充父元素宽度

## 关键实现方法
    页面结构主要是由一个wrap包裹住所有模块，内含left、right、middle三个部分，其中left、right的float属性分别为left、right，middle的html代码一定要写在最后。然后固定左右两部分的宽度，设置中间栏的左右margin，差不多就可以完成三栏式的基本布局了。
<!--more-->
    其次就是一些细节的修改和调整了。
    注意到左右两栏都是浮动的，因此不占用元素空间，父元素的高度就是中间栏的高度，这显然不行。因此，可以对middle添加伪元素`.middle:after`（注：参考资料1），用`clear:both`清除浮动。这样父元素的高度就是三栏的最大高度，但是中间一栏就不会是本身的高度，而会与最高的一致了。
    因此，本来是不想再增加多余的html结构的，但是（暂时）只能利用一个空div标签清除浮动，实现题目中的效果了。（后来发现可以在wrap上增加`overflow:auto`，而不用增加多余的html结构也能达到这个效果！注：参考资料4）

## Tips
1、模块之间的视觉间隔的实现方法：可以是padding，也可以是margin，在设置border的情况下，可以看到，实际显示宽度＝width＋padding。
2、在不设置宽度的情况下，默认会填充父元素的宽度，此时不可以再设置`width:100%; padding:20px`，这样会造成元素过宽，撑出滚动条。
3、right栏logo的间距：类似这样的同类元素的样式，可以统一先设置所有元素（如：`margin:10px 0`），然后针对特殊的元素（如头尾两个），使用伪类进行特殊处理（如：对第一个同类元素应用`:first-of-type`，使其`margin-top:0`），好酷炫的赶脚～😁

## 参考资料
1、[伪类和伪元素的区别](https://segmentfault.com/a/1190000000484493)
2、[CSS选择器](http://www.w3school.com.cn/cssref/css_selectors.asp)
3、[清除浮动黑科技](http://stackoverflow.com/questions/211383/which-method-of-clearfix-is-best)
4、[清除浮动](http://zh.learnlayout.com/clearfix.html)
