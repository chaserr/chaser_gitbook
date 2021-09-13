# swift中的error已经和oc的交互

[https://blockchain520.github.io/2017/12/01/Swift/Swift/Grammar/Error-Handling-%E9%94%99%E8%AF%AF%E5%A4%84%E7%90%86/](https://blockchain520.github.io/2017/12/01/Swift/Swift/Grammar/Error-Handling-%E9%94%99%E8%AF%AF%E5%A4%84%E7%90%86/)

如何将oc方法中的error参数定义在swift中转化成throw类型？

```swift
- (BOOL)initializeEngineWithError:(NSError * _Nullable *)error;
```

必须返回值为bool才会在swift文件中被调用是throw类型

