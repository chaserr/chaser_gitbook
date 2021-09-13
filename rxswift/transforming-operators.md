RxSwift中四种转换操作符：

- map
- flatMap
- flatMapLatest
- scan

####map
通过使用一个闭包函数将原来的Observable序列转换为一个新的Observable序列。
![](https://images2015.cnblogs.com/blog/1202156/201707/1202156-20170721165135324-900984769.png)

```swift
Observable.of(1,2,3).map({
            return 10 * $0
        }).subscribe({print($0)}).dispose()
```
打印结果
```swift
next(10)
next(20)
next(30)
completed
```
####flatMap
将一个Observable序列转换为另一个Observable序列，并且合并两个Observable序列。会按时间顺序接收两个序列发出的元素。

```swift
struct Player {
    var score: Variable<Int>
    
}

let man = Player (score: Variable(80))
let woman = Player (score: Variable(90))
        
let player = Variable(man) 
player.asObservable() // 拆箱转成可被监听的 sequence
.flatMap({
    $0.score.asObservable() // flatMap拆包，$0本来应该是一个BehaviorSubject类型，但是直接访问 score,所以猜想flatMap对behaviorSubject进行了onNext拆包取数据
    .subscribe(onNext: { print($0) })
}).subscribe(onNext: {
       print($0)
   }).disposed(by: disposeBag)
        
man.score.value = 85
player.value = woman //更换了value，相当于又添加了一个sequence，两个sequence都可以接收
man.score.value = 95
woman.score.value = 100
        
```

打印结果
```swift
80
85
90
95
100
```

####flatMapLatest
flatMapLatest同flatMap一样，也是将一个序列转换为另一个序列，flatMapLatest只会从最近的序列中发出事件。

flatMapLatest = map + switchLatest
```swift
        let man = Player (score: Variable(80))
        let woman = Player (score: Variable(90))
        
        let player = Variable(man)
        player.asObservable().flatMapLatest({
            $0.score.asObservable()
        }).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
        
        man.score.value = 85
        player.value = woman
        man.score.value = 95
        woman.score.value = 100
        
```

打印结果
```swift
80
85
90
100
```

####scan
scan就是提供一个初始化值，然后使用计算闭包不断将前一个元素和后一个元素进行处理，并将处理结果作为单个元素的Observable序列返回。

```swift
Observable.of(10,100,1000).scan(2) { (aggregateValue, newValue) in
            aggregateValue + newValue
        }.subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```
打印结果

```swift
12
112
1112
```

