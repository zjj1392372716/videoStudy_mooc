
# vue组件化学习

## 慕课网的学习项目


### 1、项目构建


```
- JDFinance  这个目录是一个vue的自定义构建模版

github地址： https://github.com/yjjdick/JDFinance
```

html的模块化、css的模块化，以及js的模块化，这三者我们称为（web）前端模块化。我们先学习css模块化。

### 2、css模块化学习


```
1.设计原则

a、可复用、能继承、要完整
b、周期性迭代

2、设计方法

a、先整体后部分再颗粒化
b、先抽象再具体

```
3、具体实现

如：

[从css谈模块化](https://www.jianshu.com/p/6ce8619dc674)

#### 经验总结：

**1、公共css库、存放我们的公共样式以及公共模块**

```
// common.css
body {
  background: #fff;
  color: #333;
  font-size: 16px;
}

.box ... {
  background: #333;
  color: #fff;
  ...
}

.another-box ... {
  ...
}
```

存在的问题：

1、假设我们这个项目非常大，大概有20个页面这么多，那么我们每做一个页面就会往common里面补充3～4个公共样式/模块，那么在这个项目开发完成以后，common的体积可能要比其他css的体积都大；

2、由于common越写越大，它所占用的命名就越多，那么我们在引入common的时候，即使我们页面还什么都没有，但已经默认被占用了很多的命名，使得我们在某个页面的可用命名变少，而且是越来越少；

3、总之问题主要是：`一、冗余`；`二、污染`。


**2、命名规范**

我们规定页面由且只由几种基本结构体构成：`框架`、`模块`，`以及元件`。其他零散的元素，除了是作为`模块的辅助类`，否则不能独立于这三者存在。

* 框架

以 g 开头

指构成页面的基础结构，它是一个页面的筋骨

```css
index页面  .g-index
about页面  .g-about

头部  g-header (g-hd简写更好但是不易读懂)
底部  g-footer
主体  g-body
```

* 模块

以 m 开头

它是代码复用的主体部分，是一个个按照功能划分的区域，如导航栏、轮播图、登录窗口、信息列表等等，模块之间相互独立，分布在页面上，嵌在框架的各个位置上，组成一个丰富多彩的页面
```css
导航栏模块     m-nav
新闻列表      m-news
出版声明模块   m-copy
```

* 元件

以 u 开头

元件是独立的、可重复使用的，并且在某些情况下可以作为模块的组成部分的一种细颗粒。比如一个按钮，一个logo等等。某种意义上说，它其实可以等同于模块，因为它们两者的区别只是规模不同而已。模块更强调一个功能完整的整体，而元件则更强调独立性。
```css
logo       u-logo
登陆按钮    u-login_btn
```


> 前缀与名称之间用 ` - ` 连接，而名称之间的若干单词以 ` _ ` 连接，组合单词除外，如`side-menu_title`；


* 工具类样式

以 z 开头

z-开头表示状态，如z-active、z-succ、z-disabled等等

```css
z-active
z-disabled
z-checked
z-on
z-hover
z-clear
```

上面就是我们的`gmuz`规范。



**3、改写common**

```css
/* common.css */

body {
  background: #fff;
  color: #333;
  font-size: 16px;
}

.m-nav { ... }

.m-news { ... }

.m-copy_right { ... }
```

ok，现在我们一定程度上缓解了“污染”的问题，至少按照命名规范，我们的common构成由原来笼统的一类，变成了现在gmuz四类，变得更加可管理且“没那么容易冲突”了，但是这还远没有解决“污染”。


但是这样我们的`common`中和`页面css`中都会出现`m-`等开头的命名，这样其实还是有存在命名冲突的可能的，因此我们可以为`common`单独定义前缀，比如说在`common`中使用： `cm-`.在其他的页面中使用 `gmuz`。

```css
/* common.css */

body {
  background: #fff;
  color: #333;
  font-size: 16px;
}

.cm-nav { ... }

.cm-news { ... }

.cm-copy_right { ... }
```

```css
/* index.css */

.g-index {
  background: #fff;
  color: #333;
  font-size: 16px;
}

.m-nav { ... }

.m-news { ... }

.m-copy_right { ... }

```

------------

> 总结： 其实上面的方法仍然没有做到最完美，所以我们怎么划分模块，并做合理的处理是我们真正实现css模块化的关键之处。


#### 注意事项

* 1. 只对外暴露一个类名；


一个（组）样式相对独立且完整的类。
```css
/* nav */
.m-nav {
  color: #ccc;
  background: #666;
  font-size: 14px;
}

.m-nav .u-logo { ... }

.m-nav .list { ... }

.m-nav .list .item { ... }
```


* 2. 继承实现
```
1、在css中并写两个类，如.cm-nav, .m-nav，我们知道，这相当于让两个（组）类共享一套样式，然后我们再单独对.m-nav进行补充，实现继承和定制；
2、在class属性里并写两个类，如<img class="u-logo logo">，这样我们只需要在页面css中单独对.logo类进行补充，就可以实现定制；
3、在页面css中直接对类进行引用，然后补充样式，实现定制，如.cm-nav { margin-bottom: 20px; }；
```


-----------

> 总结： 我们已经定制了一套基于规范的css模块化生产方式，虽然并不完美，但这却也是一套简单的、零工具、零成本的解决方案，适用于任何项目。可以看出，`我们这里所谓的模块化，其实是规范化的子集，通过制定了一套规范，才产生了模块`。所以css的模块化过程其实是css规范化的过程。


上面仅仅是简单的普通css模块化处理。只从css预处理器出现之后，css模块化就变得更加丰富了。

下面我们继续去学习吧！


[css预处理语言的模块化实践](https://www.jianshu.com/p/3461c1cefe5c#)

## css预处理语言的模块化总结

我们使用sass预处理

```
-css
  - reset.scss
  - layout.scss
  - element.scss
```

```scss
layout.scss
// 记录各种布局公用样式
/* flex布局 */
@mixin flex($direction:column, $inline:block) {
    display: if($inline == block, flex, inline-flex);
    flex-direction: $direction;
    flex-wrap: wrap;
}
```

```scss
element.scss
// 记录各种元素公用样式
@import "./layout.scss";

@mixin btn($size:14px,$color:#fff,$bgcolor:#F04752,$padding:5px,$radius:5px) {
  padding: $padding;
  background-color: $bgcolor;
  border-radius: $radius;
  border: 1px solid $bgcolor;
  font-size: $size;
  color: $color;
  text-align: center;
  line-height: 1;
  display: inline-block;
}

@mixin list($direction:column) {
  @include flex($direction);
}

@mixin panel($bgcolor:#fff,$padding:0,$margin:20px 0,$height:112px,$txtPadding:0 32px,$color:#333,$fontSize:32px) {
  background: $bgcolor;
  padding: $padding;
  margin: $margin;
  >h4{
    height: $height;
    line-height: $height;
    padding: $txtPadding;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    text-align: center;
    color: $color;
    font-size: $fontSize
  }
}

```

