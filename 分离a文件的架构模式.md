// 终端进入.a文件目录
查看.a文件的架构
lipo -info libWeiboSDK.a
// 分离.a文件的架构
lipo libWeiboSDK.a -thin armv7 -output libWeiboSDK.armv7.a
