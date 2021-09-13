# 分离.a文件的架构模式

// 终端进入.a文件目录 查看.a文件的架构 lipo -info libWeiboSDK.a // 分离.a文件的架构 lipo libWeiboSDK.a -thin armv7 -output libWeiboSDK.armv7.a

// 分离出.386架构 lipo -extract\_family i386 -output libWeiboSDK\_i386.a libWeiboSDK.a

// 合并.a文件 lipo -create libWeiboSDK.arm64.a libWeiboSDK.armv7.a -output libWeiboSDK.a

