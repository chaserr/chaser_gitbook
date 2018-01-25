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



#流程
1）组件导入区： 所有用到的组件都需要事先进行导入，包括样式也需要进行导入
2）核心代码区：所有逻辑代码编写的地方
3）组件样式区：render() 方法中用到的组件的样式，可以集中在这里编写
4）注册启动组件：组件只有注册后才能运行。这里用到的 AppRegistry 也需要在组件导入区进行导入


#问题

1. 如何调试