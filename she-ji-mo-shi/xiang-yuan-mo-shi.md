# 享元模式

概论：通过共享已存在的对象，减少创建对象内存开销的设计模式被称作享元模式

享元模式Flyweight和单例模式Singleton像是一对孪生兄弟，二者的表现方式非常相似，但二者的存在目的却不一样

* 单例模式

（两种创建方法）：

```text
class UserInfo {



 static let sharedInfo = UserInfo()

 private init() {}

}



// 第二种方法

let singleton = UserInfo()

class UserInfo {

 class var sharedInfo : UserInfo {

 return singleton

 }

}
```

保证了整个应用声明周期内，同一个对象只会存在一份内存，并且任何时间都能被访问使用。

单例保证了数据在应用运行期间的唯一性，减少了重复内存的损耗，但如果单例本身内存占用过大时，又是一种负担。另一方面，单例的访问也存在着多线程安全的问题，这需要我们合理的使用线程锁来保证单例的稳定性。

![](http://jbcdn2.b0.upaiyun.com/2016/10/f1b0881badc991421d64921129d67b82.jpeg)

* 享元模式

如果存在可以复用的对象，那么对象将被共享而不是创建新的对象

![](http://jbcdn2.b0.upaiyun.com/2016/10/1c532653dbbfd92397a49968cee5b9f5.jpeg)

在iOS开发中，享元模式的最佳实践就是UITableView的复用机制——超出屏幕外的单元格统一被回收放到一个复用队列之中，等待着需要新的单元格时进行复用。

