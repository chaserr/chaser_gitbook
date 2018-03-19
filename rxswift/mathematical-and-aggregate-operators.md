RxSwift中数学和集合操作符。
在RxSwift中包括了：
- toArray
- reduce
- concat

####toArray
将一个Observable序列转化为一个数组，并转换为一个新的Observable序列发射，然后结束。

```swift
Observable.of(1,2,3,4,5).toArray().subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```

打印结果
```swift
[1,2,3,4,5]
```

####reduce
使用一个初始值和一个操作符，对Observable序列中的所有元素进行累计操作，并转换成单一事件信号。(PS:和map有的区别就是：map针对单个元素进行操作，reduce针对所有元素累计操作)

```swift
Observable.of(1,20,100).reduce(1, accumulator: +).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```
结果：
```swift
122
```

####concat
将两个Observable序列合并成一个Observable序列，当一个Observable序列中的所有元素成功发射完成之后，才会发射另一个Observable序列中的元素。
在第一个Observable发射完成之前，第二个Observable发射的事件都会被忽略，但会接收第一个Observable发射完成前第二个Observable发射的最后一个事件。

```swift
        let subject1 = BehaviorSubject (value: "🍎")
        let subject2 = BehaviorSubject (value: "🐶")
        let variable = Variable(subject1)
        variable.asObservable().concat()
        .subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
        
        subject1.onNext("🍐")
        subject1.onNext("🍊")
        variable.value = subject2
        subject2.onNext("I would be ignored")
        subject2.onNext("🐶")
        subject1.onCompleted()
        subject2.onNext("🐭")
```
打印结果：
```swift
🍎
🍐
🍊
🐶
🐭
```

解释：在subject1发射完成事件之前

```swift
variable.value = subject2
subject2.onNext("I would be ignored")
subject2.onNext("🐭")
```
subject2发射的这些事件都会被忽略，但是又会接收subject2发射的最后一个事件，因此会打印onnext(🐭)。

