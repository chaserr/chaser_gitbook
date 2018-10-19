#CAShapeLayer之strokeStart和strokeEnd属性研究
![](/assets/galshir-todo-list-swipe.gif)

最近利用闲暇在研究上面的这个动画，因为本身对动画没有太深入的研究。
该动画是来自一个设计师的设计作品：https://dribbble.com/shots/3959132-Todo-List-Swipe-To-Check
觉得很nice

在研究这个动画的过程中涉及到了UIBezierPath，CAKeyframeAnimation，CAShapeLayer，CATransaction等系统类

先不表这个动画的思路

本文单开是为了介绍CAShapeLayer的strokeStart和strokeEnd属性
> 官方文档
- strokeStart
The relative location at which to begin stroking the path. Animatable.
- strokeEnd
The relative location at which to stop stroking the path. Animatable.

一下情况keytimes=[@"0", @"1"];
1 keyPath = strokeStart  动画的fromValue = 0，toValue = 1 （从有到无）
strokeEnd默认为1，strokeStart从 0 到 1 ，strokeStart = 0 时有一条完整的路径，strokeStart = 1 时 路径消失。效果就是一条从路径起点到终点慢慢的消失

2 keyPath = strokeStart  动画的fromValue = 1，toValue = 0 (从无到有)
strokeEnd默认为1，strokeStart从 1 到 0 ，strokeStart = 1 时无路径，strokeStart = 0 时 画出一条完整的路径。效果就是一条从路径终点到起点慢慢的出现

3 keyPath = strokeEnd  动画的fromValue = 0，toValue = 1
strokeStart默认为0，strokeEnd从 0-1，strokeEnd=0 时，无路径，strokeEnd=1 时，一条完整路径。效果就是一条路径起点到终点慢慢的出现

4 keyPath = strokeEnd  动画的fromValue = 1，toValue = 0
strokeStart默认为0，strokeEnd从 1-0，strokeEnd=1 时，有路径，strokeEnd=0 时，无路径。效果就是一条路径终点到起点慢慢的消失

![strokeStart，strokeEnd研究矢量图](/assets/QQ20181019-125702-HD.gif)

> 动画的0-1（fromValue = 0，toValue = 1） 或1-0 （fromValue = 1，toValue = 0） 表示执行的方向 和路径的范围。

放出研究到一半的动画：
![](/assets/QQ20181019-130017-HD.gif)