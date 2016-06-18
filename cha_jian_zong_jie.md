# 插件总结

一款类似cocoapod管理第三方工具的管理工具，用来管理常用的插件:[Alcatraz](https://github.com/wawsc5354524/Alcatraz)。

##卸载Alcatraz

```ruby
~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin

rm -rf ~/Library/Application\ Support/Alcatraz
```

## 安装Alcatraz
```ruby
curl -fsSL https://raw.github.com/supermarin/Alcatraz/master/Scripts/install.sh | sh
```
xcode更新之后插件失效的解决办法
```
defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID
```
重启xcode

##我正在使用的插件
- BackLight // 定位当前编辑行数
- BBUDebuggerTuckAway // 显示错误的全部信息
- BBUFulllssueNavigator // 同上
- CodePilot // 搜索代码功能
- DerivedDataExterminator // 删除DerivedData
- DXXcodeConsoleUnicodePlugin // 在控制台讲- Unicode转换为中文
- FuzzyAutocomplete // 自动补充代码
- GitDiff // 自动跟踪git上的代码
- HOStringSense // 一款方便String输入的插件
- KSImageNamed // 查找图片名
- OMColorSense // 可视化颜色选择
- Peckham // 可以在任何地方进行import
- VVDocumenter-Xcode // 注释插件，猫神作品
- XAlign // 规范化代码
- FastStub // 快速生成简单代码集合 cont+cmd+k
- RTImageAssets  // 自动根据@3x图片填充@2x图片