官方文档：http://asyncdisplaykit.org/appledocs.html[TOC]##ASDisplayKit的布局/ *
AsyncDisplayKit’s layout system is centered around two basic concepts:1. Layout Specs2. Layout Elements*/
ASDisplayKit的布局主要有两种， 一种是相对布局，一种是绝对布局
###ASLayoutSpecASLayoutSpec主要有四个类型
#### 绝对布局
##### ASAbsoluteLayoutSpec
#### 相对布局
#####ASStackLayoutSpec.hASStackLayoutSpec是一个容器型布局，主要用于描述控件之间左右或者上下位置的关系（兄弟视图）。stack控件分为两个轴，主轴和十字轴，主轴的方向和direction方向平行。下面介绍它的几个重要属性：
###### `ASStackLayoutDirection`属性

>主要用来描述布局方向
>![enter image description here](http://ww2.sinaimg.cn/mw690/937882b5gw1f9xf04nnbyj21220v8q8h.jpg)

- 如果ASStackLayoutDirection方向是水平的，那么主轴方向是自左向右，十字轴方向是自上往下。
- 如果ASStackLayoutDirection方向是垂直的，那么主轴方向是自上往下，十字轴方向是自左向右。
- 控件的布局方向就是按照主轴来逐步计算的，所以主轴不能弄错


###### `ASStackLayoutJustifyContent` 属性
描述主轴方向上子控件的排列方式它是一个枚举值

1. ASStackLayoutJustifyContentStart
> 在主轴方向上，在父控件中居左排列。
> ![enter image description here](http://ww3.sinaimg.cn/mw690/937882b5gw1f9xfhtwb1mj20no0qadi3.jpg)

2. ASStackLayoutJustifyContentCenter> 在主轴方向上，在父空间中居中排列。
> ![enter image description here](http://ww4.sinaimg.cn/mw690/937882b5gw1f9xflwtgl1j20pa0qajtx.jpg)

3. ASStackLayoutJustifyContentEnd> 在主轴方向上，在父空间中居右排列。
4. ASStackLayoutJustifyContentSpaceBetween> 在主轴方向上，在父空间中等间距排。
5. ASStackLayoutJustifyContentSpaceAround> 在主轴方向上，在父空间中每个空间左右间距相等排列。

######`ASStackLayoutAlignItems` 属性
这个属性的作用是描述主轴方向上子控件的排列方式它也是一个枚举值1. ASStackLayoutAlignItemsStart> 在十字轴方向上，居顶排列> ![enter image description here](http://ww1.sinaimg.cn/mw690/937882b5gw1f9xg8hjw1yj20v60tcjv3.jpg)2. ASStackLayoutAlignItemsEnd> 在十字轴方向上，居底排列> ![enter image description here](http://ww1.sinaimg.cn/mw690/937882b5gw1f9xgf396skj20rg0t4adw.jpg)3. ASStackLayoutAlignItemsCenter> 在十字轴方向上，居中排列4. ASStackLayoutAlignItemsStretch> 在十字轴方向上，填充满父空间

######`spacing`属性
这个属性是描述子控件之间的间距官方文档解释：
``` objc/** The amount of space between each child. */@property (nonatomic, assign) CGFloat spacing;```

另外还有其父类中的动态生成的一些属性也放到这里统一
- `flexShrink`，`flexGrow`> 上面这两个属性描述：> 当该控件比父控件大时，则可以在当前主轴方向上缩小或者拉伸自己，直至在当前方向上填满父空间。
- `spacingBefore` ， ` spacingAfter`> 上面这两个属性描述：> 如果是水平方向，before表示前面的间距，after表示后面的间距。> 如果是垂直方向，before表示上面的间距，after表示下面的间距。


#####ASBackgroundLayoutSpec
> 这种布局模式用来描述z轴（在屏幕上展示也就是前后）方向上的两个控件位置关系， background控件会作为背景位于child控件正下方。整个控件的大小和background的大小由child大小决定。

#####ASInsetLayoutSpec
> ASLayoutSpec :用来改变子控件的大小，该控件的大小由子控件大小 + UIEdgeInsets四个方向上的数值。该空间的位置保持子控件的位置不变。

#####ASOverlayLayoutSpec
> 这种控件用来描述z轴（在屏幕上展示也就是前后）方向上的两个空间的位置关系，overlay控件会覆盖于child控件正上方。整个控件的大小和overlay的大小由child大小决定。

#####ASRatioLayoutSpec> ASRatioLayoutSpec :主要用于约束控件的 高宽比。>ratio = 0.5, 空间的宽是高的2倍。>ratio = 2.0, 空间的高是宽的2倍。

#####ASRelativeLayoutSpec
> ASRelativeLayoutSpec :用来方便描述child在父控件中边角特殊位置。例如：左上角，上边界中间，右上角，左边界中间，正中间，右边界中间，左下角，下边界中间，右下角。

##### ASCenterLayoutSpec 
> ASCenterLayoutSpec :是ASRelativeLayoutSpec的子类，专用于描述child在父控件中水平居中，垂直居中，正中。
