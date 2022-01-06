---
title: 清明节全网灰色主题实现原理
date: 2022-01-05 10:49:52
tags:
---
属性写法：

``` bash
filter: grayscale();
```

filter是 滤镜 属性，grayscale是 灰度 属性值，grayscale括号里面可以是0、1或者百分比、小数or something？
括号里的数值越大，灰得越彻底。数值越小，原本的五颜六色保留得越好。

``` bash
filter: grayscale(100%);    
-webkit-filter: grayscale(100%);    //兼容chrome和safari和2013年后Opera
-moz-filter: grayscale(100%);     //兼容Firefox
-ms-filter: grayscale(100%);     //兼容IE、Edge
-o-filter: grayscale(100%);       //兼容2013年前的Opera

```

#### 代码严谨进阶
filter是CSS3的属性，我们知道一些较低版本的IE对CSS3的兼容性不是很好，但是老版本的IE中我们依然可以实现滤镜的效果：

``` bash
filter: url(data:image/svg+xml;utf8,#grayscale);         //兼容IE10、IE11
filter: progid:DXImageTransform.Microsoft.BasicImage(grayscale=1); //兼容IE6～9
```
