# Observables

每一个 Observable的实例都是一个序列 `Observable` 序列相比于 Swift 序列的关键优势点在于它能够异步的接受元素 这是 `RxSwift` 的精髓所在，其他的一切都是扩展了这个概念

* Observable\(ObservableType\) 等效于 Sequence
* observableType.subscribe\(\_:\)方法等效于 Sequence.makeIterator\(\)
* ObservableType.subscribe\(\_:\)接受一个观察者参数，它将被订阅自动接收由可观察到的序列事件和元素，而不是在返回的生成器上手动调用 `next()`

如果一个 Observable发出一个 next 事件`Event.next(Element)`，它还能够继续发出更多的事件，但是，如果一个 Observable 发出一个 error 事件（Event.error\(ErrorType\)）或者一个 completed 事件（Event.completed）那么这个 Observable 序列就不能给订阅者发送其他的事件了。

## Observables and Observers\(subscribers -- 订阅者\)

除非有订阅者，否则 Observable 不会执行他们的闭包 在下面的例子中，Observable的闭包不会被执行，因为没有订阅者订阅

```swift
_ = Observable<String>.create({ (observingString) -> Disposable in
            print("the code will not be executed")
            observingString.onNext("😆")
            observingString.onCompleted()
            return Disposables.create()
        })
```

在下面的例子中，Observable的闭包将会被执行，因为有订阅者订阅。

```swift
_ = Observable<String>.create({ (observingString) -> Disposable in
            print("the code willbe executed")
            observingString.onNext("😆")
            observingString.onCompleted()
            return Disposables.create()
        }).subscribe({ (event) in
            print(event)
        })
```

subscribe\(\_:\)返回一个一次性的实例，该实例表示一个可使用的资源，如订阅。在前面的简单示例中，它被忽略了，但是应该正常地处理它。这通常意味着将它添加到一个DisposeBag实例中。

## Creating and Subscribing to Observables

有几种方式创建和订阅 Observables

* never

  创建一个永不终止且不发出任何事件的序列,[官方图解](http://reactivex.io/documentation/operators/empty-never-throw.html)

  ```swift
  Observable<String>.never().subscribe { (_) in
            print("this will never be printed")
        }.disposed(by: disposeBag)
  ```

* empty 创建一个只发送 completed 事件的空 Observable 序列 [官方图解](http://reactivex.io/documentation/operators/empty-never-throw.html)
* just 创建一个只有一个元素的Observable序列。[官方图解](http://reactivex.io/documentation/operators/just.html)
* of 创建一个固定数量元素的Observable序列。

  ```swift
  Observable.of("元素1","元素2","元素3","元素4","元素5").subscribe(onNext: { (element) in
            print(element)
        }, onError: nil, onCompleted: nil, onDisposed: nil).disposed(by: disposeBag)
  ```

> 此处用到了subscribe\(onNext:\)的便利构造方法，有部分参数使用的是默认值。

* from

  从一个序列\(如Array/Dictionary/Set\)中创建一个Observable序列。

```swift
Observable.from(["元素1","元素2","元素3","元素4","元素5"]).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```

> 注意：这个例子使用了默认参数$0而不是显式地命名参数。

* create

  创建一个自定义的Observable序列。[更多详情](http://reactivex.io/documentation/operators/create.html)

```swift
let myjust = {(element: String) -> Observable<String> in

            return Observable<String>.create { (observer) -> Disposable in
                observer.on(.next(element))
                observer.on(.completed)
                return Disposables.create()
            }
        }

myjust("篮球").subscribe { (element) in
    print(element)
}.disposed(by: disposeBag)
```

* range

  创建一个Observable序列，它会发出一系列连续的整数，然后终止。更多详情

```swift
Observable.range(start: 1, count: 10).subscribe({print($0)}).disposed(by: disposeBag)
```

* repeatElement

  创建一个Observable序列，它可以无限地释放给定元素。更多详情

```swift
Observable.repeatElement("🏀").take(3).subscribe(onNext: {print($0)}, onError: nil, onCompleted: nil, onDisposed: nil).disposed(by: disposeBag)
```

> 上述例子中take操作符从一个序列开始返回指定数量的元素。

* generate 创建一个Observable序列，只要提供的条件值为true就可以生成值。

  ```swift
  Observable.generate(initialState: 4, condition: {$0<9}, iterate: {$0 + 1}).subscribe(onNext: {print($0)}, onError: nil, onCompleted: nil, onDisposed: nil).disposed(by: disposeBag)
  ```

* deferred 为每一个订阅者创建一个新的Observable序列。[更多详情](http://reactivex.io/documentation/operators/defer.html)

  ```swift
  var count = 1
        let defferedSequen = Observable<String>.deferred {
            print("count = \(count)")
            count += 1
            return Observable.create({ (observable) -> Disposable in
                observable.onNext("🐐")
                observable.onNext("🐗")
                observable.onNext("🐱")
                return Disposables.create()
            })
        }

        defferedSequen.subscribe(onNext: {
            print($0)
        }, onError: nil, onCompleted: nil, onDisposed: nil).disposed(by: disposeBag)
        defferedSequen.subscribe(onNext: {
            print($0)
        }, onError: nil, onCompleted: nil, onDisposed: nil).disposed(by: disposeBag)
  ```

* error

  创建一个不会发送任何条目并且立即终止错误的Observable序列。

  \`\`\`swift

  Observable.error\(TestError.test\).subscribe { print\($0\) }.disposed\(by: disposeBag\)

```text
- do
为每个发出的事件调用一个副作用操作，并返回(通过)原始事件。更多详情

```swift
Observable.of(["元素1","元素2","元素3","元素4","元素5"]).do(onNext: {
            print("next: \($0)")
        }, onError: { (error) in
            print("error: \(error)")
        }, onCompleted: {
            print("complete")
        }).subscribe(onNext: {print($0)}).disposed(by: disposeBag)
```

