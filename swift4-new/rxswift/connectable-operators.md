# Connectable Operators

Connectable Observable在订阅时不发射事件消息，而是仅当调用它们的connect\(\)方法时才发射消息，这样就可以等待所有我们想要的订阅者都已经订阅了以后，再开始发出事件消息，这样能保证我们想要的所有订阅者都能接收到事件消息。其实也就是等大家都就位以后，开始发出消息。

在开始学习Connectable Observable之前，让我们来看一个non-connectable operator:

Connectable Observable在RxSwift中包括了：

* publish
* replay
* multicast

## publish

将一个源Observable sequence转变为一个connectable sequence。

```swift
        let intervar = Observable<Int>.interval(1, scheduler:     MainScheduler.instance).publish()
        _ = intervar.subscribe(onNext: {
            print("subscribe:1, event:\($0)")
        })

        _ = intervar.subscribe(onNext: {
            print("subscribe:2, event:\($0)")
        })

        intervar.connect()
```

## replay

将一个正常的sequence转换成一个connectable sequence，然后和replaySubject相似，能接收到订阅之前的事件消息。

```swift
let intSequence = Observable<Int>.interval(1, scheduler: MainScheduler.instance).replay(5)
        DispatchQueue.main.asyncAfter(deadline: .now() + .seconds(2)) {
            _ = intSequence.connect()
        }

        DispatchQueue.main.asyncAfter(deadline: .now() + .seconds(4)) {
            _ = intSequence.subscribe(onNext: {
                print("subscribe:2, event:\($0)")
            })
        }

        DispatchQueue.main.asyncAfter(deadline: .now() + .seconds(8)) {
            _ = intervar.subscribe(onNext: {
                print("subscribe:3, event:\($0)")
            })
        }
```

## multicast

将一个正常的sequence转换成一个connectable sequence，并且通过特性的subject发送出去，比如PublishSubject，或者replaySubject，behaviorSubject等。不同的Subject会有不同的结果。

