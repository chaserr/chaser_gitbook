###Swift获取命名空间
``` objc 

//获取命名空间 
let spaceName = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
```

2. 比较元组大小会按照从左到右、逐值比较的方式，直到发现有两个值不等时停止。如果所有的值都相等，那么这一对元组我们就称它们是相等的。例如：

```c
(1, "zebra") < (2, "apple") //true 因为1<2
(3, "apple") < (3, "bird") // true apple < bird
(4, "dog") == (4, "dog") // true

```

> Swift 标准库只能比较七个以内元素的元组比较函数。如果你的元组元素超过七个时，你需要自己实现比较运算符

3. 空合运算符 （a ?? b）
空合运算符（a ?? b）将对可选类型 a 进行空判断，如果 a 包含一个值就进行解封，否则就返回一个默认值 b。表达式 a 必须是 Optional 类型。默认值 b 的类型必须要和 a 存储值的类型保持一致。