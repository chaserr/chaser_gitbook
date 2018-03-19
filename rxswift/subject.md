Subject是一个代理，它既是 Observer,也是 Observable
所以它既可以订阅一个或者多个 Observable,也可以被其他的 Observer 订阅。
它可以传递或者转发作为 Observer 收到的值，也可以主动发射值

Subject在 Rxswift 中的实现有4种：
- PublishSubject
- ReplaySubject
- BehaviorSubject
- Variable

##### PublishSubject
代理
我们先以PublishSubject为例来解释Subject是一个代理的含义。

```swift
let subject = PublishSubject<Int>()
        subject.subscribe { (event) in
            print("Event: \(event)")
        }
        
        subject.onNext(1)
        subject.onNext(2)
        subject.onCompleted()
```


Subject作为一个Observable提供了subscribe等方法。在订阅之后，我们调用了onNext()，向Observer发射了1、2，以及onCompleted()。

我们可以调用Subject的on系列方法主动给Observer发送值。
Subject可以作为代理转发订阅到的结果。例如：

```swift
let subject = PublishSubject<Int>()
        subject.subscribe { (event) in
            print("Event: \(event)")
        }
        
        
        let reveseSubject = Observable<Int>.create { (observer) -> Disposable in
            observer.onNext(1)
            observer.onNext(2)
            observer.onCompleted()
            return Disposables.create()
        }

        reveseSubject.subscribe(subject)
```
subject订阅了reveseSubject，并将结果转发给了Observer。



Observer订阅 subject时不会收到订阅之前subject的值。如下：
```swift
let subject = PublishSubject<Int>()
subject.onNext(0)
subject.subscribe({ (event) in
    print("Event:\(event)")
})
    
subject.onNext(1)
subject.onNext(2)
subject.onCompleted()
```
observer无法接收到0这个值。



##### ReplaySubject
ReplaySubject和PublishSubject不同的是，Observer有可能接收到订阅之前的值。
```swift
let subject1 = ReplaySubject<Int>.create(bufferSize: 1)
        subject1.onNext(0)
        subject1.subscribe { (event) in
            print("Event:\(event)")
        }
        subject1.onNext(1)
        subject1.onNext(2)
        subject1.onCompleted()
```

ReplaySubject具有重放(replay)的功能，replay的个数可以通过参数指定。我们可以将其理解为缓存的效果。


一般我们使用ReplaySubject的时候，都是先发射，后订阅，然后通过指定缓存的大小，可以获取对应的值。(注意：不考虑Error和Completed)。
```swift
let subject1 = ReplaySubject<Int>.create(bufferSize: 1)
        subject1.onNext(0)
        subject1.onNext(1)
        subject1.onNext(2)

        subject1.subscribe { (event) in
            print("Event:\(event)")
        }
        subject1.onNext(3)
        subject1.onCompleted()
        
```

打印结果：
```swift
Event:next(2)
Event:next(3)
Event:completed
```
上述代码，我们指定了bufferSize等于1，所以只缓存了最新的值。

##### BehaviorSubject
BehaviorSubject类似于ReplaySubject具有缓存能力，但是略有不同。
- 只缓存一个最新值，类似ReplaySubject.create(bufferSize: 1)
- 需要提供默认值

```swift
let behaviorSubject = BehaviorSubject<Int>(value: 1)
        behaviorSubject.subscribe { (event) in
            print("Event:\(event)")
        }
```

使用BehaviorSubject有一点好处，我们可以确定当Observer订阅时，至少可以收到最新的一个值。


##### Variable
Variable和BehaviorSubject又很相似。
Variable是BehaviorSubject的一个封装，同样具备了缓存最新值和提供默认值的能力。但是Variable没有on系列方法，只提供了value属性。
直接对value进行set等同于调用了onNext()方法。

这表明了Variable不会发射error也不会发射completed

在Variable被销毁的时候会调用发射completed给Observer

在订阅Variable的时候，我们无法直接调用subscribe方法，需要先调用asObservable()方法。
```swift
let variable = Variable<Int>(1)
variable.asObservable().subscribe { (event) in
    print("Event:\(event)")
}
variable.value = 2
```
上述打印结果：
```swift
Event:next(1)
Event:next(2)
Event:completed
```

Variable可以用来储存数据，因为我们可以拥有value的get和set方法。比如：

```swift
let variable = Variable(1)
print("Value: \(variable.value)")
variable.value = 2
print("Value: \(variable.value)")
```
这是BehaviorSubject不具备的


