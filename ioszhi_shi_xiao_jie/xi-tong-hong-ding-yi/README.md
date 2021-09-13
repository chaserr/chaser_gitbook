# 系统宏定义

在继承中,凡是要求子类重写父类的方法必须先调用父类的这个方法进行初始化操作;建议:父类的方法名后面加上NS\_REQUIRES\_SUPER; 子类重写这个方法就会自动警告提示要调用这个super方法,示例代码

```c
// 注意:父类中的方法加`NS_REQUIRES_SUPER`,子类重写才有警告提示
- (void)prepare NS_REQUIRES_SUPER;
```

