# UI小技巧开发

1.在使用view的缩放的时候，layer.border.width随着view的放大，会出现锯齿化的问题，解决这个问题需要设置这个属性。

self.layer.allowsEdgeAntialiasing = YES;

2.instrument中time profile 中的self, #self,%self各代表什么 ?

![instrument.png](http://ww3.sinaimg.cn/small/937882b5gw1f5bufg4y6ej20cc0853zu.jpg)

下面引用了一下网上的具体内容

Self is "The number of times the symbol calls itself." according to the Apple Docs on the Time Profiler.

From the way the numbers look though, it seems self is the summed duration of samples that had this symbol at the bottom of its stack trace. That would make:

self: the number of samples where this symbol was at the bottom of the stack trace
% self: the percent of self samples relative to total samples of currently displayed call tree
(eg - #self / total samples).
So this wouldn't tell you how many times a method was called. But it would give you an idea how much time is spent in a method or lower in the call tree.

