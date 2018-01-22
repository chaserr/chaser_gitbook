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