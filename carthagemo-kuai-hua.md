# 如何制作一个依赖pod库和其他carthage库的carthage库

关于pod的制作就不细说了，这里主要说说carthage的模块化

其实carthage模块化比pod化更简单，这里只是梳理一下流程以及对工程配置的注意点

这里讲解的demoSDK是依赖了pod库和其他carthage库的一个demo,也是最复杂的一个依赖类型

1. 新建一个`Cocoa Touch FrameWork`类型的工程，

> 这个时候CarthageMoudlesSDK只有一个xcodeproj工程，并没有工作区域，因为后面我们还要为demoSDK配置一个demo,理应先新建一个workspace，这里为了方便，通过引入pod库的方式自动创建一个workspace(因为demoSDK本来就需要依赖pod库)

2. 新建`Podfile`文件，按照正常的流程先添加几个库，然后执行`pod install`。将workspace文件生成出来


