#Reveal 调试神器
##1. 安装步骤
###1.1 在 podfile文件添加 Reveal-SDK
首先在你的 podfile 文件里添加Reveal库
```ruby
#podfile
pod 'Reveal-SDK', :git => 'https://github.com/chaserr/Reveal-SDK-4.git', :configurations => ['Debug'] #只在 debug模式下使用

```
>这里的`git`地址是我的 git 地址，因为该版本的 Reveal-SDK 是低版本的，所以我在我的 git 库里存了一分，各位有需要也可以保存在自己的 git 库里

###1.2 在项目中安装 Reveal
添加完成后，在终端执行
```ruby
pod install
```
完成之后，就可以打开你的项目，新建几个控制器和 view。
##2. Reveal 调试
###2.1
调试之前你需要先安装 Reveal 客户端（与 git 库里的 SDK 对应的低版本客户端）
