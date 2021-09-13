# Swift

[https://www.raywenderlich.com/163857/whats-new-swift-4](https://www.raywenderlich.com/163857/whats-new-swift-4)

## 字符串

[SE-0163](https://github.com/apple/swift-evolution/blob/master/proposals/0163-string-revision-1.md) 1. 直接在String对象上进行迭代，操作Sequence和Collection

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

1. `StringProtocol`

   它声明了以前在String上声明的大部分功能。 这种变化的原因是改善切片如何工作。 Swift 4添加了Substring类型，用于引用String上的子序列。 String和Substring实现了StringProtocol使它们具有几乎相同的功能：

```swift
let endIndex = galaxy.index(galaxy.startIndex, offsetBy: 3)
print(endIndex)
print(galaxy[endIndex])

var milkSubstring = galaxy[galaxy.startIndex...endIndex]
type(of: milkSubstring)

milkSubstring += ""
let milkString = String(milkSubstring)
```

## Dictionary和Set Enhancements

[SE-0165](https://github.com/apple/swift-evolution/blob/master/proposals/0165-dict.md) 1. 基于序列的初始化 列表首先是从一系列键值对（元组）创建一个字典的能力：

```swift
let nearestStarNames = ["Proxima","Centauri", "Alpha Centauri A", "Alpha Centauri B", "Barnard's"]
let nearestStarDistances = [4.24, 4.37, 4.37, 5.96, 7.78]
let starDistanceDict = Dictionary (uniqueKeysWithValues: zip(nearestStarNames, nearestStarDistances))
```

1. 重复键处理

   您现在可以使用重复的键来处理初始化字典的任何方式。 这有助于避免覆盖键值对，而不会有任何问题：

```swift
let favoriteStarVotes = ["Alpha Centauri A", "Wolf 359", "Alpha Centauri A", "Barnard's Star"]
let mergedKeysAndValues = Dictionary(zip(favoriteStarVotes, repeatElement(1, count: favoriteStarVotes.count)), uniquingKeysWith: +)
```

上面的代码使用zip和速记+来通过添加两个冲突的值来解析重复的键。

1. Dictionary和Set现在都可以将结果过滤到原始类型的新对象中：

```swift
let closeStars = starDistanceDict.filter({$0.value <  5.0})

closeStars
```

1. 字典映射

   Dictionary获得了一个非常有用的方法来直接映射其值：

```swift
let mappedCloseStarts = closeStars.mapValues({"\($0)"})
mappedCloseStarts
```

1. 字典默认值

   在Dictionary上访问某个值时，常见的做法是使用nil coalescing运算符给出默认值，以防数值为nil 。 在Swift 4中，这变得更加清洁，

```swift
let siriusDistance = mappedCloseStarts["Wolf 359", default: "unknow"]
var starWordsCount: [String: Int] = [:]
for starName in nearestStarNames {
    let numWords = starName.split(separator: " ").count
    starWordsCount[starName, default: 0] += numWords
}
```

1. 字典分组

   通过字典的 key 值的首字母对 key进行分组

   \`\`\`swift

   let siriusDistance = mappedCloseStarts\["Wolf 359", default: "unknow"\]

   var starWordsCount: \[String: Int\] = \[:\]

   for starName in nearestStarNames {

    let numWords = starName.split\(separator: " "\).count

    starWordsCount\[starName, default: 0\] += numWords

   }

   let starsByFirstLetter = Dictionary\(grouping: nearestStarNames\) { $0.first! }

```text
##私有访问修饰符
[SE-0169](https://github.com/apple/swift-evolution/blob/master/proposals/0169-improve-interaction-between-private-declarations-and-extensions.md)

Swift 4通过在类型和所述类型的任何扩展之间共享相同的访问控制范围来实现原始意图。 这只适用于相同的源文件[SE-0169] ：

```swift
struct SpaceCraft {
    private var warpCode: String
    init(warpCode: String) {
        self.warpCode = warpCode
    }
}


extension SpaceCraft {
    func gotpWarpSpeed(warpCode: String) {
        if warpCode == self.warpCode {
            print("Dp it Soctty")
        }
    }
}

let enterprise = SpaceCraft (warpCode: "KirkIsCool")
enterprise.gotpWarpSpeed(warpCode: "KirkIsCool")
```

在extension文件里面也可以访问private的变量，fileprivate的变量了，

## 新增 API

1. 归档和序列化

到目前为止，在Swift中，为了序列化和归档您的自定义类型，您必须跳过一些环。对于class类型，您需要对NSObject进行子类化并实现NSCoding协议。

像struct和enum这样的值类型需要许多hacks，例如创建一个可以扩展NSObject和NSCoding的子对象。

Swift 4通过将序列化到所有三种Swift类型[SE-0166](https://github.com/apple/swift-evolution/blob/master/proposals/0166-swift-archival-serialization.md)来解决这个问题：

```swift
struct CuriosityLog: Codable {
    enum Discovery: String, Codable {
        case rock, water, martian
    }

    var sol: Int
    var discoveries: [Discovery]

}

let logSol42 = CuriosityLog (sol: 42, discoveries: [.rock, .rock, .rock, .rock])
```

在这个例子中，您可以看到，使Swift类型可Encodable和可Decodable所需的唯一Decodable是实现可编Codable协议。 如果所有属性都是Codable ，则协议实现由编译器自动生成。

要实际编码对象，您需要将其传递给编码器。 Swift编码器正在Swift 4中积极实施。每个编码器根据不同的方案对您的对象进行编码\[SE-0167\] （ 注意：此提案的一部分仍在开发中）：

```swift
let jsonEncoder = JSONEncoder()
// Encode the data
let jsonData = try jsonEncoder.encode(logSol42)
// Create a String from the data
let jsonString = String (data: jsonData, encoding: .utf8)
jsonString
```

这采取了一个对象，并自动将其编码为JSON对象。 确保查看JSONEncoder暴露的属性来自定义其输出。

该过程的最后一部分是将数据解码为具体对象：

```swift
let jsonDecoder = JSONDecoder()
let decodedLog = try jsonDecoder.decode(CuriosityLog.self, from: jsonData)

decodedLog.sol
decodedLog.discoveries
```

1. 键值编码

   对Swift 4来说，令人兴奋的补充是能够引用类型的关键路径来获取/设置实例的基础值[SE-0161](https://github.com/apple/swift-evolution/blob/master/proposals/0161-key-paths.md) ：

   \`\`\`swift

   struct Lightsaber{

    enum Color {

   ```text
    case blue, green, red
   ```

    }

    let color: Color

   }

class ForceUser { var name: String = "" var lightsaber: Lightsaber var master: ForceUser?

```text
init(name: String, lightsaber: Lightsaber, master: ForceUser? = nil) {
    self.name = name
    self.lightsaber = lightsaber
    self.master = master
}
```

}

let sidious = ForceUser\(name: "Darth Sidious", lightsaber: Lightsaber\(color: .red\)\) let obiwan = ForceUser\(name: "Obi-Wan Kenobi", lightsaber: Lightsaber\(color: .blue\)\)

let anakin = ForceUser\(name: "Anakin Skywalker", lightsaber: Lightsaber\(color: .blue\), master: obiwan\)

```text
要创建一个关键路径，您只需使用一个反斜杠后跟您感兴趣的属性：
```swift
// Create reference to the Forceuser.name key path
let nameKeyPath = \ForceUser.name

let obiwanName = obiwan[keyPath: nameKeyPath]
```

在这种情况下，您正在为ForceUser的name属性创建一个关键路径。 然后，通过将其传递给新的下标keyPath来使用此键路径。 默认情况下，此下标现在可用于每种类型。

以下是使用关键路径深入到子对象，设置属性和构建关键路径引用的更多示例：

```swift
let anakinSaberColor = anakin[keyPath: \ForceUser.lightsaber.color]
let masterKeyPath = \ForceUser.master
let anakinMasterName = anakin[keyPath: masterKeyPath]?.name

anakin[keyPath: masterKeyPath] = sidious
anakin.master?.name
```

1. 多行字符串文字

   Swift 4通过在三个引号[SE-0168](https://github.com/apple/swift-evolution/blob/master/proposals/0168-multi-line-string-literals.md)中包装文本来添加这个简单而有用的语法：

```swift
let star = "❤️"
let introString = """
A long time ago in a galaxy far,
far away....

You could write multi-lined strings
without "escaping" single quotes.

The indentation of the closing quotes
below deside where the text line
begins.

You can even dynamically add values
from properties: \(star)
"""

print(introString)
```

1. 无限序列

   当起始索引为可数类型时，它们还允许您定义无限Sequence ：

```swift
var planets = ["Mercury","Venus", "Earth","Mars", "Jupiter", "Saturn", "Uranus"]
// Before
let before = planets[4..<planets.endIndex]
//Now
let outsideAsteroidBelt = planets[4...]
let firstThree = planets[..<4]

var numberedPlanets = Array(zip(1..., planets))
print(numberedPlanets)

planets.append("Pluto")
numberedPlanets = Array(zip(1..., planets))
print(numberedPlanets)
```

1. 单面范围

   为了减少冗长度并提高可读性，标准库现在可以使用单面范围[SE-0172](https://github.com/apple/swift-evolution/blob/master/proposals/0172-one-sided-ranges.md)来推断起始和终点索引。

```swift
var planets = ["Mercury","Venus", "Earth","Mars", "Jupiter", "Saturn", "Uranus"]
// Before
let before = planets[4..<planets.endIndex]
//Now
let outsideAsteroidBelt = planets[4...]
let firstThree = planets[..<4]
```

1. 模式匹配

   单面范围的另一个很好的用途是模式匹配：

```swift
func temperature(planetNumber: Int){
    switch planetNumber {
    case ...2: //anything less than or equal to 2
        print("too hot")
    case 4...: // anything greater than or equal to 4
        print("Too cold")
    default:
        print("Justtttt right")
    }
}
temperature(planetNumber: 3)
```

1. 通用下标 下标是使数据类型以简洁方式可访问的重要组成部分。 为了提高其有用性，下标现在可以是通用的[SE-0148](https://github.com/apple/swift-evolution/blob/master/proposals/0148-generic-subscripts.md)：

   ```swift
   struct GenericDictionary<Key: Hashable, Value> {
    private var data: [Key: Value]
    init(data: [Key: Value]) {
        self.data = data
    }

    subscript<T>(key: Key) -> T?{
        return data[key] as? T
    }
   }
   ```

   在这个例子中，返回类型是通用的。 你可以使用这个通用的下标，如下所示： 会自动判断类型

```swift
var earthData = GenericDictionary(data: ["name": "Earth", "population": 7500000000, "moons": 1])

let name: String? = earthData["name"]
let population:Int? = earthData["population"]
```

返回类型不仅可以是通用的，而且实际的下标类型也可以是通用的：

```swift
extension GenericDictionary {
    subscript<Keys: Sequence>(keys:Keys) -> [Value] where Keys.Iterator.Element == Key {
        var values:[Value] = []
        for key in keys {
            if let value = data[key] {
                values.append(value)
            }
        }
        return values
    }
}

let nameAndMoons = earthData[["moons", "name"]]
let nameAndMoons2 = earthData[Set(["moons", "name"])]
```

在这个例子中，你可以看到，传递两个不同的Sequence类型（ Array和Set ）会导致一个数组有各自的值。

1. 相关类型限制

   您现在可以使用where子句来限制关联类型[SE-0142](https://github.com/apple/swift-evolution/blob/master/proposals/0142-associated-types-constraints.md) ：

```swift
protocol MyProtocol {
    associatedtype Iterator : IteratorProtocol
    associatedtype SubSequence : Sequence where SubSequence.Iterator.Element == Iterator.Element
}
```

使用协议约束，许多associatedtype声明可以直接约束其值，

