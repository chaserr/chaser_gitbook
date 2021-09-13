# swift+alomfire+realm

Q：使用realm继承的object不能实现归档解档？

```text
required init?(coder aDecoder: NSCoder) {
        self.init()
        self.name = aDecoder.decodeObjectForKey("name") as! String
        self.updated = aDecoder.decodeObjectForKey("updated") as! NSDate
    }
```

当实现上面的方法的时候xcode会强制性的向你提出还需要实现其他几个`init`方法，对，这没错，但是当你实现了

```text
     required init(realm: RLMRealm, schema: RLMObjectSchema) {
        fatalError("init(realm:schema:) has not been implemented")
    }

    required init(realm: RLMRealm, schema: RLMObjectSchema) {
        fatalError("init(realm:schema:) has not been implemented")
    }

    required init() {
        fatalError("init() has not been implemented")
    }
```

上面这三个方法之后，然后run，就会crash了。

解决办法：去掉你自己自定义的init方法，使用`convenience`来定义你的init方法 上面的解决方法是：在`required`前面添加`convenience`，不知道`convenience`是干什么的同学，自己去补充swift基础知识

```text
    convenience required init?(coder aDecoder: NSCoder) {
        self.init()
        self.name = aDecoder.decodeObjectForKey("name") as! String
        self.updated = aDecoder.decodeObjectForKey("updated") as! NSDate
    }
```

继承了realm的Object对象之后，我们不需要再自己定义新的init（）方法了，但是我们可以使用convenience创建一个便利构造器，来方便的根据我们的需求定义创建init\(\)方法，这也是工厂模式的好处。 [关于realm的更多坑](https://github.com/realm/realm-cocoa/issues)

