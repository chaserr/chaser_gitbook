在 swift 协议中需要通过associatedtype关键字来实现泛型，其他如 class, struct, enums都可以通过参数化类型表达泛型

协议在Swift中有两个目的，第一个目的是用来实现多继承(swift语言被设计为单继承的），第二个目的是强制实现者必须准守自己所指定的泛型约束。关键字associatedtype是用来实现第二个目的的。


