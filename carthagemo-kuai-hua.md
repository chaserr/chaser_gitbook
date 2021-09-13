# Carthage模块化

关于pod的制作就不细说了，这里主要说说carthage的模块化

其实carthage模块化比pod化更简单，这里只是梳理一下流程以及对工程配置的注意点

这里讲解的demoSDK是依赖了pod库和其他carthage库的一个demo,也是最复杂的一个依赖类型

## 1. 新建一个framework工程

1. 新建一个`Cocoa Touch FrameWork`类型的工程，

   > 这个时候CarthageMoudlesSDK只有一个xcodeproj工程，并没有工作区域，因为后面我们还要为demoSDK配置一个demo,理应先新建一个workspace，这里为了方便，通过引入pod库的方式自动创建一个workspace\(因为demoSDK本来就需要依赖pod库\)

2. 新建`Podfile`文件，按照正常的流程先添加几个库，然后执行`pod install`。将workspace文件生成出来

podfile文件内容：

```swift
gitConfigUserName   = %x{git config --get user.name}
gerritName          = gitConfigUserName.split("\n")[0]
gerritURL           = "ssh://#{gerritName}@internal.smartdevices.com.cn:29418/apps/Specs-iOS"
platform :ios, '9.0'
inhibit_all_warnings!

## 这里配置的是私有库source
source gerritURL 
source 'https://github.com/CocoaPods/Specs.git'

target 'CarthageMoudlesSDK' do
    pod 'ZFPlayer', '3.1.6'
    pod 'ZFPlayer/AVPlayer', '3.1.6'
    pod 'ZFPlayer/ControlView', '3.1.6'
    pod 'KTVHTTPCache', '~> 1.1.5'

end
```

## 2. 新建一个demo工程

1. 打开`CarthageMoudlesSDK.xcworkspace`文件，然后`cmd+shift+N` 新建一个`Single View App`,填好demo名称，点击next,注意，这里一定要将新建的project添加到`CarthageMoudlesSDK.xcworkspace`工作区域 ![&#x6DFB;&#x52A0;project&#x5230;workspace](http://wx3.sinaimg.cn/mw690/937882b5gy1fyk64msyxtj20ys09mwez.jpg)
2. 完成之后的工程目录应该是这样的 ![&#x5DE5;&#x7A0B;&#x76EE;&#x5F55;](http://wx3.sinaimg.cn/mw690/937882b5gy1fyk683l5p7j21bu0cs0xr.jpg)

ok,如果这个demo不需要依赖其他pod库的话那就这样。

## 3. 添加其他pod库依赖

如果demo需要其他pod库支持或者carthage库支持的话，那么就需要修改`Podfile`文件,其中指定workspace和project

> 在模块化的过程中，这种情况很正常，因为你的模块是从主项目剥离出来，但是你的项目只依赖A库，可以要想整个demo运行起来，并且可以跑单元测试，那么就需要依赖主项目的网络库和kiwi等库文件,

修改后的podfile文件内容：

```swift
gitConfigUserName   = %x{git config --get user.name}
gerritName          = gitConfigUserName.split("\n")[0]
gerritURL           = "ssh://#{gerritName}@internal.smartdevices.com.cn:29418/apps/Specs-iOS"
platform :ios, '9.0'
inhibit_all_warnings!

## 这里配置的是私有库source
source gerritURL 
source 'https://github.com/CocoaPods/Specs.git'

##设置workspace文件
workspace 'CarthageMoudlesSDK.xcworkspace'

target 'CarthageModulesDemo' do
    project 'CarthageModulesDemo/CarthageModulesDemo.xcodeproj'
    pod 'AFNetworking', '~> 3.0'

end

target 'CarthageModulesDemoTests' do
    project 'CarthageModulesDemo/CarthageModulesDemoTests.xcodeproj'
    pod 'Kiwi'
    pod 'OCMock', '3.4'
end

target 'CarthageMoudlesSDK' do
    project 'CarthageMoudlesSDK.xcodeproj'
    pod 'ZFPlayer', '3.1.6'
    pod 'ZFPlayer/AVPlayer', '3.1.6'
    pod 'ZFPlayer/ControlView', '3.1.6'
    pod 'KTVHTTPCache', '~> 1.1.5'

end
```

执行`pod install`成功，目前情况是： 你的`CarthageMoudlesSDK`依赖`ZFPlayer`库 你的`CarthageModulesDemo`依赖 `AFNetworking`来支撑运行 你的`CarthageModulesDemoTests` 依赖`Kiwi`来支撑

上面我们已经搞定了pod的依赖库 现在我们缺少的就是CarthageMoudlesSDK依赖的carthage库，和CarthageModulesDemo依赖的carthage库

## 4. 添加其他carthage库依赖

首先说一下，carthage库会被clone到主项目的本地目录，然后通过carthage/Checkouts/.\*中clone下来的cartfile文件去自动fetch/pull相关的库，而我们的demo依赖的cartfile库又不需要主项目去fetch/pull,不然会浪费时间去拉取一些不必要的库，所以这里就需要提到`cartfile.private`

官文:

```swift
Frameworks that want to include dependencies via Carthage, but do not want to force those dependencies on parent projects, can list them in the optional Cartfile.private file, identically to how they would be specified in the main Cartfile.

Anything listed in the private Cartfile will not be seen by dependent (parent) projects, which is useful for dependencies that may be important during development, but not when building releases—for example, test frameworks.
```

大致意思就是说上游的项目不需要去关注的依赖可以列在private文件中，也就是说你制作的SDK中如果有不需要被上有项目用到，但是你的demo有用到就可以使用cartfile.private文件,例如单元测试。

关于Cartfile.private的官文地址：

> [https://github.com/Carthage/Carthage/blob/9d1a67f47c7231eb2f1c33a719634ec4281b65c1/Documentation/Artifacts.md\#cartfileprivate](https://github.com/Carthage/Carthage/blob/9d1a67f47c7231eb2f1c33a719634ec4281b65c1/Documentation/Artifacts.md#cartfileprivate)

所以这里为了测试，再次加入一些单元测试的库

### 4.1. 编写cartfile文件

1. 生成Cartfile.private和Cartfile文件

Cartfile：

```ruby
github "CoderMJLee/MJRefresh" == 3.1.15
```

Cartfile.private：

```ruby
github "rs/SDWebImage" == 4.1.0
github "specta/specta" ~> 1.0
```

> `specta`的官文也推荐了使用Cartfile.private,这里不仅仅适用于测试库，比如你的SDKDemo和主工程都需要依赖同一个库B，但是最终你的SDK需要集成进主工程，这时候库B就应该放在`Cartfile.private`中

编写完执行`carthage update --platform iOS`

到现在为止sdk和sdkdemo该依赖的库都基本完成，接下来是内部依赖

## 5. framework工程配置

### 5.1. SDK工程配置

1. 工程配置 ![SDK&#x5DE5;&#x7A0B;&#x914D;&#x7F6E;](http://wx1.sinaimg.cn/mw690/937882b5gy1fyk7ab6mp9j217w0awdjp.jpg)

   > 这里这样写，这就要求你`carthage update`之后你的carthage文件夹必须保持和project在同一个目录路径下 注意修改你的工程配置`Valid Architectures`, 保持和主工程目录一直，不然集成进去会报arm相关的错误

2. public头文件的暴露
3. framework依赖处理

### 5.2. SDKDemo工程配置

1. SDKDemo工程配置和集成Carthage库一样， 在`build Phases`中新建一个`Run Script Phases` ![](http://wx1.sinaimg.cn/mw690/937882b5gy1fyk7obca35j21c40mgq6l.jpg)
2. 然后从Carthage/Build/iOS文件夹中将几个库一一对应的拖入到`Linked Frameworks and Libraries`
3. 保证SDK的demo能够成功运行

### 5.3.分享SDK的scheme

1. 将SDK的scheme分享出来 ![](http://wx1.sinaimg.cn/mw690/937882b5gy1fyk7utfguoj218a0fe0xd.jpg)
2. workspace的根目录下执行`carthage build --no-skip-current --platform iOS`
3. 上传项目到远仓库，打上tag

## 6. 集成SDK

1. 在主工程的cartfile文件中加入

```ruby
github "chaserr/CarthageModulesDemo" "master"
```

1. 执行`carthage update CarthageModulesDemo --platform iOS`

   ![](http://wx4.sinaimg.cn/mw690/937882b5gy1fyk8hi2dqoj21600cwad4.jpg)

> 主工程会主动从clone到Carthage/Checkouts/CarthageModulesSDK文件中去读取相应的cartfile文件和podfile文件，不需要我们去关心

