# Error Handing Operators

RxSwift中的错误处理，包括：

* catchErrorJustReturn
* catchError
* retry
* retry\(\_:\)

## catchErrorJustReturn

遇到error事件的时候，返回一个值，并且结束。[更多详情](http://reactivex.io/documentation/operators/catch.html)

```swift
let sequenceFial = PublishSubject<String>()
        sequenceFial.catchErrorJustReturn("😁").subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
        sequenceFial.onNext("😝")
        sequenceFial.onNext("😓")
        sequenceFial.onError(error)
```

打印结果

```swift
😝
😓
😁
```

## catchError

捕获error进行处理，可以返回另一个sequence进行订阅。[更多详情](http://reactivex.io/documentation/operators/catch.html)

```swift
let sequenceThatFails = PublishSubject<String>()
let recoverySequence = PublishSubject<String>()

sequenceThatFails.catchError {
        print("Error:", $0)
        return recoverySequence
    }.subscribe { print($0) }.disposed(by: disposeBag)

sequenceThatFails.onNext("😬")
sequenceThatFails.onNext("😨")
sequenceThatFails.onNext("😡")
sequenceThatFails.onError(TestError.test)

recoverySequence.onNext("😊")
```

运行结:

```swift
next(😬)
next(😨)
next(😡)
Error: test
next(😊)
```

## retry

遇见error事件可以进行重试，比如网络请求失败，可以进行重新连接。

```swift
var count = 1

let retrySequence = Observable<String>.create { (observer) -> Disposable in
    observer.onNext("🍎")
    observer.onNext("🍐")

    if count == 1 {
        observer.onError(TestError.test)
        print("Error encountered")
        count += 1
    }

    observer.onNext("🐶")
    observer.onNext("🐱")

    return Disposables.create()
}

retrySequence.retry().subscribe(onNext: {print($0)}).disposed(by: disposeBag)
```

结果：

```swift
🍎
🍐
Error encountered
🍎
🍐
🐶
🐱
```

## retry\(\_:\)

与retry相同，只是指定了重试次数。

```swift
var count = 1

let sequenceThatErrors = Observable<String>.create { observer in
    observer.onNext("🍎")
    observer.onNext("🍐")
    observer.onNext("🍊")

    if count < 5 {
        observer.onError(TestError.test)
        print("Error encountered")
        count += 1
    }

    observer.onNext("🐶")
    observer.onNext("🐱")
    observer.onCompleted()

    return Disposables.create()
}

sequenceThatErrors.retry(3).subscribe(onNext: { print($0) }).disposed(by: disposeBag)
```

结果

```swift
🍎
🍐
🍊
Error encountered
🍎
🍐
🍊
Error encountered
🍎
🍐
🍊
Error encountered
Received unhandled error: Playground.xcplaygroundpage:41:__lldb_expr_18 -> test
```

