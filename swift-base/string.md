1. 访问首元素和尾元素

```swift
var str = "Hello, playground"
print("\(str[str.startIndex])")
```
> endIndex是最后一个元素后边的那个元素，因此不能直接访问，否则会崩溃。 
访问最后一个元素的正确方式 :

```swift
let index = str.index(before: str.endIndex)
print("\(str[index])")

```

> 为什么不能像oc那样直接用下边访问呢？苹果是这样解释的： 
**如上所述，不同的字符可能需要不同数量的内存来存储，因此为了确定哪个Character位于特定位置，您必须从每个Unicode标量的开始或结尾处遍历String。因此，Swift字符串不能用整数值索引。**




>不同的 Unicode 字符以及相同 Unicode 字符的不同表示方式可能需要不同数量的内存空间来存储。所以 Swift 中的字符在一个字符串中并不一定占用相同的内存空间。因此字符串的长度不得不通过迭代字符串中每一个字符的长度来进行计算。如果您正在处理一个长字符串，需要注意countElements函数必须遍历字符串中的字符以精准计算字符串的长度。 另外需要注意的是通过countElements返回的字符数量并不总是与包含相同字符的NSString的length属性相同。NSString的length属性是基于利用 UTF-16 表示的十六位代码单元数字，而不是基于 Unicode 字符。为了解决这个问题，NSString的length属性在被 Swift 的String访问时会成为utf16count。
