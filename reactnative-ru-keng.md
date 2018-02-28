http://bbs.reactnative.cn/topic/15/react-react-native-%E7%9A%84es5-es6%E5%86%99%E6%B3%95%E5%AF%B9%E7%85%A7%E8%A1%A8
https://github.com/zhengweikeng/blog/blob/master/posts/2016/%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0%E4%B8%ADthis%E7%9A%84%E7%94%A8%E6%B3%95.md


1. watchman 问题
使用下面的方法进行手动安装就可以
```js
git clone https://github.com/facebook/watchman.git  
cd watchman  
git checkout v4.9.0  # the latest stable release  
./autogen.sh  
./configure  
make  
sudo make install  
```
2. `RCTReconnectingWebSocket.h`文件的`#import <fishhook/fishhook.h>` 显示`error: 'fishhook/fishhook.h' file not found`

解决：在`package.json`文件中添加如下代码：
```js
  "scripts": {
    "postinstall": "sed -i '' 's#<fishhook/fishhook.h>#\"fishhook.h\"#g' ./node_modules/react-native/Libraries/WebSocket/RCTReconnectingWebSocket.m"
  },
```

3. Unable to find a specification for 'yoga (=0.50.4.React)' depended upon by 'React/Core'
  看了 `react-native`的issue 好像这个库还没有更新版本，所以我把 react 的版本降低到如下就 OK：
  ```js
  "dependencies": {
    "react": "16.0.0-beta.5",
    "react-native": "0.49.5",
  },
```

4. 警告`BatchedBridge is deprecated`
在 `podfile` 里将`react` 的 `BatchedBridge` 替换成 `CxxBridge`



###错误5 `undefined is not an object (evaluating 'Orientation.lockToPortrait')`
这是关于设备的横竖屏适配的问题
解决方案：https://github.com/yamill/react-native-orientation/issues/43

###错误6 使用自定义字体的时候出现失败

这里以华康海报体 w12 为例：
解决方案：
0x1. 先把导入的字体资源改为 fontName:DFPHaiBaoW12
0x2. 把 xcode 里 plist 文件里的字体也改为DFPHaiBaoW12
0x3. 设置 js 里的样式`fontFamily`
```js
btn: {
    color: "white",
    fontSize: 18,
    fontFamily: 'DFPHaiBaoW12'
  }
```
> 字体的fontFamily可以把下载的 ttf文件直接双击安装，然后在系统自带的字体册里看到关于这个字体的一切信息
>https://github.com/forJrking/FontZip这是一个字体提取器，用来瘦身项目



###错误7 `Native module cannot be null`
类似问题：https://github.com/react-navigation/react-navigation/issues/460

错误场景：外部Xcode导入 reactNative.而不是运行 react自带的 ios 项目
错误描述：当引入`react-navigation`并将其作为出口register 的时候会报错：Native module cannot be null
错误原因：react-navigation会生成一个原生的RCTLinkingIOS库，外部引用的需要手动导入，内部的自动链接
解决方法：在 podfile 中加入：`RCTLinkingIOS`
```js
pod 'React', :path => '../../MathsGameRN/node_modules/react-native', :subspecs => [
    'Core',
    'RCTActionSheet',
    'RCTGeolocation',
    'RCTImage',
    'RCTNetwork',
    'RCTPushNotification',
    'RCTSettings',
    'RCTText',
    'RCTVibration',
    'RCTWebSocket',
    'CxxBridge',
    'RCTLinkingIOS'
    ]
```

###错误8 swift 通过 RCTRootView 的  initPor传值不被识别，OC 可以

###错误9 Undefined is not an object(evaluating ‘_react2.PropTypes.func’)
导入 propTypes 错误
正确方法：
```js
//import PropTypes from 'react'; wrong

import PropTypes from 'prop-types'; //right

```



#流程
1）组件导入区： 所有用到的组件都需要事先进行导入，包括样式也需要进行导入
2）核心代码区：所有逻辑代码编写的地方
3）组件样式区：render() 方法中用到的组件的样式，可以集中在这里编写
4）注册启动组件：组件只有注册后才能运行。这里用到的 AppRegistry 也需要在组件导入区进行导入


#问题

1. 如何调试
2. 如果使用 native 的导航，那么原生可不可以拿到 native 的一些页面来做某些事情或者说，还是用原生的导航，然后在 native 中注册多个入口来实现