#概念

1. Tips
>RxSwift中做数据绑定有三种
利用BindTo方法
利用Driver(强烈建议使用这个，)
利用KVO来手动绑定（很少用到）

2. 
>时刻牢记，使用RxSwift，尽量把所有的任务（可以理解为方法）抽象成Obserable（序列）和Obserable创建者，监听者
能用数据绑定的（bindTo和Driver）的就不要手动绑定
一定要熟练RxSwift提供的操作符，要会自定义操作符