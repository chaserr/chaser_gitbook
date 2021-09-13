# ScrollerView

1、几个已知的滑动或者滑动开始结束的方法： onScroll：在滚动过程中, 每帧最多调用一次此函数, 调用的频率可以用scrollEventThrottle属性来控制. onMomentumScrollEnd：当一帧滚动完毕时调用. onScrollAnimationEnd ：ios上的当滚动动画结束时调用. 2、还有其他的一些事件如下，触摸事件里面有携带event，大家可以再下面的方法里面更改一些view操作就可以打印出来这些event携带的信息了

1、onScrollBeginDrag：一个子view滑动开始拖动开始时触发，注意和onMomentumScrollBegin的区别

2、onScrollEndDrag：一个子view滚动结束拖拽时触发，注意和onMomentumScrollEnd的区别

3、onTouchStart：按下屏幕时触发

4、onTouchMove：移动手指时触发

5、onTouchEnd：手指离开屏幕触摸结束时触发

6、onMomentumScrollBegin：当一帧滚动开始时调用.

7、onMomentumScrollEnd：当一帧滚动完毕时调用.

8、onStartShouldSetResponder：触摸开始时是否成为响应者

9、onStartShouldSetResponderCapture：防止子视图在触摸开始时成为应答器

10、onScrollShouldSetResponder：滚动时是否成为响应者

11、onResponderGrant：开始响应时触发

12、onResponderRelease：手指释放后，视图成为响应者

13、onResponderReject：另一个响应已经被激活, 响应者不会释放它到该视图

14、onScroll：滚动时触发，会触发多次

