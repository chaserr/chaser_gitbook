#字符串
 [SE-0163](https://github.com/apple/swift-evolution/blob/master/proposals/0163-string-revision-1.md)
 1. 直接在String对象上进行迭代
```swift
let galaxy = "Milky Way"
for char in galaxy {
    print(char)
}

galaxy.count
galaxy.isEmpty
galaxy.dropFirst()
String(galaxy.reversed())

galaxy.filter({
    char in
    let isASCII = char.unicodeScalars.reduce(true, {$0 && $1.isASCII})
    return isASCII
})

```

2. StringProtocol