-- 华米
1. 格式化代码
利用clang format来格式化

1. 下载xcode的扩展程序
https://github.com/mapbox/XcodeClangFormat
2. 自定义。clang-format文件规则
https://www.cnblogs.com/PaulpauL/p/5929753.html 中英文参照

##### 2018.6.13  项目pod化还是carthage化讨论
carthage化问题：二（进制，crash没有源码）
Q:遇到错误无法跟进修改
A:如果拿到百度，高德sdk等二进制库，出现错误了怎么处理，那么carthage化后就该怎么处理（找到这个库的维护者）

carthage化的好处
针对公司的每个库的每个管理者，要求库的稳定性，开发人员对代码的严格性

从项目工程角度来说