# \_\_covariant

`__covariant` - 协变性，子类型可以强转到父类型（里氏替换原则） `__contravariant` - 逆变性，父类型可以强转到子类型（WTF?）

```swift
@interface Stack<__contravariant ObjectType> : NSObject

- (void)testExample {
    // This is an example of a functional test case.
    // Use XCTAssert and related functions to verify your tests produce the correct results.
    ContravariantTest<NSString *> *contravariantTestStr;
    ContravariantTest<NSMutableString *> *contravariantTestMulStr;
    contravariantTestStr = contravariantTestMulStr; // 子类强制转化为父类， 这里会报警告 @warining Incompatible pointer types assigning to 'ContravariantTest<NSString *> *' from 'ContravariantTest<NSMutableString *> *'
    contravariantTestMulStr = contravariantTestStr; // 父类强制转化为子类
}
```

