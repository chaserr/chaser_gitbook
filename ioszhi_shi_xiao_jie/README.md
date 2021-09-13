# iOS知识小结

这部分主要记录关于OC的一些基础知识，包括语法，一些不常见的问题，已经容易混淆的问题。

## Xcodebuild编译工程文件

```ruby
xcodebuild -project YourProject.xcodeproj -scheme YourScheme
```

Or

```ruby
xcodebuild -workspace YourProject.xcworkspace -scheme YourScheme
```

## OC .\(点）与-&gt;\(箭头）用法区别

[http://www.tuicool.com/articles/r6nuIz7](http://www.tuicool.com/articles/r6nuIz7)

## 系统私有文件夹，一般不允许私自访问

[http://bbs.25pp.com/thread-84967-1-1.html](http://bbs.25pp.com/thread-84967-1-1.html)

## 为什么加入-ObjC、-all\_load，可以解决部分编译器错误？

[http://www.cnblogs.com/machao/p/5288460.html](http://www.cnblogs.com/machao/p/5288460.html)

