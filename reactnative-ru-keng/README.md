# ReactNative 入坑

[http://bbs.reactnative.cn/topic/15/react-react-native-的es5-es6写法对照表](http://bbs.reactnative.cn/topic/15/react-react-native-的es5-es6写法对照表) [https://github.com/zhengweikeng/blog/blob/master/posts/2016/箭头函数中this的用法.md](https://github.com/zhengweikeng/blog/blob/master/posts/2016/箭头函数中this的用法.md)

1. watchman 问题

   使用下面的方法进行手动安装就可以&lt;/span&gt;

   ```javascript
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

```javascript
  "scripts": {
    "postinstall": "sed -i '' 's#<fishhook/fishhook.h>#\"fishhook.h\"#g' ./node_modules/react-native/Libraries/WebSocket/RCTReconnectingWebSocket.m"
  },
```

1. Unable to find a specification for 'yoga \(=0.50.4.React\)' depended upon by 'React/Core' 看了 `react-native`的issue 好像这个库还没有更新版本，所以我把 react 的版本降低到如下就 OK：

   ```javascript
   "dependencies": {
    "react": "16.0.0-beta.5",
    "react-native": "0.49.5",
   },
   ```

2. 警告`BatchedBridge is deprecated` 在 `podfile` 里将`react` 的 `BatchedBridge` 替换成 `CxxBridge`

#### 错误5 `undefined is not an object (evaluating 'Orientation.lockToPortrait')`

这是关于设备的横竖屏适配的问题 解决方案：[https://github.com/yamill/react-native-orientation/issues/43](https://github.com/yamill/react-native-orientation/issues/43)

#### 错误6 使用自定义字体的时候出现失败

这里以华康海报体 w12 为例： 解决方案： 0x1. 先把导入的字体资源改为 fontName:DFPHaiBaoW12 0x2. 把 xcode 里 plist 文件里的字体也改为DFPHaiBaoW12 0x3. 设置 js 里的样式`fontFamily`

```javascript
btn: {
    color: "white",
    fontSize: 18,
    fontFamily: 'DFPHaiBaoW12'
  }
```

> 字体的fontFamily可以把下载的 ttf文件直接双击安装，然后在系统自带的字体册里看到关于这个字体的一切信息 [https://github.com/forJrking/FontZip这是一个字体提取器，用来瘦身项目](https://github.com/forJrking/FontZip这是一个字体提取器，用来瘦身项目) [http://fontstore.baidu.com/static/editor/index.html](http://fontstore.baidu.com/static/editor/index.html)

#### 错误7 `Native module cannot be null`

类似问题：[https://github.com/react-navigation/react-navigation/issues/460](https://github.com/react-navigation/react-navigation/issues/460)

错误场景：外部Xcode导入 reactNative.而不是运行 react自带的 ios 项目 错误描述：当引入`react-navigation`并将其作为出口register 的时候会报错：Native module cannot be null 错误原因：react-navigation会生成一个原生的RCTLinkingIOS库，外部引用的需要手动导入，内部的自动链接 解决方法：在 podfile 中加入：`RCTLinkingIOS`

```javascript
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

#### 错误8 swift 通过 RCTRootView 的  initPor传值不被识别，OC 可以

#### 错误9 Undefined is not an object\(evaluating ‘\_react2.PropTypes.func’\)

导入 propTypes 错误 正确方法：

```javascript
//import PropTypes from 'react'; wrong

import PropTypes from 'prop-types'; //right
```

## 问题

1. 如何调试
2. 如果使用 native 的导航，那么原生可不可以拿到 native 的一些页面来做某些事情或者说，还是用原生的导航，然后在 native 中注册多个入口来实现

&emsp

### &emsp

## 入门姿势

**1. 环境的安装和配置**

* node必备
* `react-native-cli` 用于执行创建，初始化，更新项目，运行打包等任务 （全局安装）
* `Watchman`  [安装出现的问题](./#watchman)

**2. 项目的配置**

1. \[package.json 项目的配置

   \]\([https://www.cnblogs.com/zhangmingcheng/p/7119740.html](https://www.cnblogs.com/zhangmingcheng/p/7119740.html)\)

   > 名称（**项目的入口**），版本，运行脚本，第三方的依赖库（开发依赖和生产环境依赖），类似于 podfile 文件

2. index.js 项目入口

   > 也可以在配置文件中指定入口，AppRegistry 是运行所有 React Native 应用程序的 JS 入口点。应用程序跟组件需要通过 AppRegistry.registerComponent 来注册它们自身，然后本地系统就可以加载应用程序的包，再然后当 AppRegistry.runApplication准备就绪后就可以真正的运行该应用程序了。

reactnative中的组件相当于 MVC 中的 V

**3 一个组件的流程**

1）组件导入区： 所有用到的组件都需要事先进行导入，包括样式也需要进行导入 2）核心代码区：所有逻辑代码编写的地方 3）组件样式区：render\(\) 方法中用到的组件的样式，可以集中在这里编写 4）注册启动组件：组件只有注册后才能运行。这里用到的 AppRegistry 也需要在组件导入区进行导入

**4 一个组件的生命周期**

![](../.gitbook/assets/3-3-component-lifecycle.jpg)

React.Component是一个抽象基类。这意味着直接引用React.Component是毫无意义的。你可以实现一个它的子类，并且至少定义一个render\(\)方法即可使用。

**4.1. 当创建组件的实例并将其插入DOM时，会依次调用这些方法：**

4.1.1 constructor\(\)

> * 构造函数，es5之前所有方法在此函数绑定this,之后用箭头函数自动绑定 this，
> * 在装载组件\(mounting\)之前调用会React组件的构造函数。当实现React.Component子类的构造函数时，应该在任何其他语句之前调用super\(props\)。否则，this.props将在构造函数中未定义，这可能导致错误。

4.1.2 componentWillMount\(\) 4.1.3 render\(\) 4.1.4 componentDidMount\(\)

> 一般在此函数中进行网络请求，然后通过改变state去重新渲染DOM

* es5和 es6语法的区别

this.state = {}; this.props = {};

**4.2. Updating\(更新状态\)**

更新可以由prop或者state的改变引起。在重新渲染组件时依次调用这些方法：

4.2.1 componentWillReceiveProps\(nextProps\)

> 在安装好的组件接收新props之前被调用。 如果你需要更新state用来响应props的更改（例如，重置它），你可以在此方法中比较this.props和nextProps并使用this.setState\(\)来替换并重置state

4.2.2 shouldComponentUpdate\(nextProps, netState\)

> * return bool, 让React知道组件是否受当前state或props变化的影响。 默认true,是在每次state更改时都会去重新渲染DOM，在绝大多数情况下，你应该依赖于这个默认行为。
> * React.PureComponent, 这个组件自动实现了这个方法，利于优化性能，当 state经常改变的时候，可以选择继承

4.2.3 componentWillUpdate\(nextProps, nextState\)

> * 当组件在收到新的props或state时，componentWillUpdate\(\)在渲染之前会立即调用这个方法。 使用这个方法来判断是非需要重新渲染DOM。 第一次渲染DOM不调用此方法。

4.2.4 render\(\) 4.2.2 componentDidUpdate\(prevProps, prevState\)

> * componentDidUpdate\(\)在重新渲染DOM之后被调用。 第一次渲染不调用此方法。
> * 当组件已经重新渲染后，此方法是一个执行DOM操作的好机会，同时也是一个处理网络请求的好地方，前提是你需要比较当前props与之前的props是否相同（例如，如果props没有改变，那么可能不需要进行网络请求）。

**4.3. Unmounting\(卸载组件\)**

当从DOM中删除组件时，将调用此方法： 4.3.1 componentWillUnmount\(\)

> componentWillUnmount\(\)在组件被卸载和销毁之前立即被调用。 此方法可以执行任何有必要的清理工作，例如清理计时器，取消网络请求或清理在componentDidMount\(\)中创建的DOM元素。

**5. 实例属性**

[实例属性](./#reactnative-ru-keng/state-props-he-cheng-yuan-bian-liang.md)

