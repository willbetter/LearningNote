# 如何在Set集合中避免重复元素

[Paul Hudson](https://www.hackingwithswift.com/about)    September 19th 2019     [@twostraws](https://twitter.com/twostraws)

![img](https://www.hackingwithswift.com/uploads/coding-man-2.jpg)

Julian Schiavo写道：我想用Set集合来保证我的Array中元素是唯一的，但是Set集合中每个元素都包含一个Date类型的变量，当两个不同元素仅仅是Date变量不同的时候，实际上Set中可以同时保存这两个元素，这就出现了重复元素。这种问题该怎么解决呢？

这是个好问题，实际上Swift的协议给我们提供了很聪明的解决方案。

首先，我们先看下下面示例代码。结构体`NewsStory`有三个属性：id、title、date：

```swift
struct NewsStory {
    var id: Int
    var title: String
    var date = Date()
}
```

如上代码所示，结构体实例初始化时候会自动将当前时间赋值给date属性。

我们可以用上面的结构体创建三个对象，如下代码所示：

```swift
let story1 = NewsStory(id: 1, title: "What's new in Swift 5.1?")
let story2 = NewsStory(id: 2, title: "What's new in Swift 6.0?")
let story3 = NewsStory(id: 3, title: "What's new in Swift 6.1?")
```

Julian想要保存这些新的对象到一个Set集合而不是数组中，这是一个很明智的选择。因此我们写下如下的代码：

```swift
var stories = Set<NewsStory>()
stories.insert(story1)
stories.insert(story2)
stories.insert(story3)
print(stories)
```

如上代码所示，创建一个保存故事对象的Set，然后将我们创建的对象添加到Set集合中，然后打印这个Set集合。然而上面的代码无法通过编译：为了每个元素在Set中都有唯一的标识，我们需要让`NewStory`对象遵守`Hashable`协议，Hashable协议能够产生唯一的hash值来标识唯一的一个对象。

Swift语言这点做得非常好，我们只需要让一个包含Hashable属性的类型遵守`Hashable`协议即可，Hashable协议会自动帮我们计算这个对象的哈希值。因此我们需要更新`NewStory`结构体如下：

```swift
struct NewsStory: Hashable {
    var id: Int
    var title: String
    var date = Date()
}
```

到现在，我们的代码终于能够正常的跑起来啦！

然后，Julian遇到的问题并没有解决，如下代码所以：

```swift
let story4 = NewsStory(id: 1, title: "What's new in Swift 5.1?")
stories.insert(story4)
print(stories)
```

当我们创建一个和已存在对象相同ID和title的NewStory对象，并添加到set集合中，然后打印集合的内容，你会发现现在集合中包含4个对象，并且其中有一个是重复的。

就像前面写的那样，当一个类型遵守Hashable协议并且其属性也都遵守Hashable协议的时候，Swift会帮我们自动计算这个对象的hash值。计算方法是这样的：获取对象中所有属性的hash值并将它们结合在一起。

因此，我们以为两个对象是相同的，因为他们有相同的ID和title，但是在Swift看来他们是不同的，因为他们的date并不相同。

我们需要做的就是给Swift提供一个自定义的hash计算规则，告诉Swift说"如果两个stories对象的ID和title是相同的，那么他们就是相同的，请忽略date属性。"

为了自定义hash计算规则，我们需要在`NewStory`中实现两个方法：一个是自定义计算hash值，两一个是检查两个对象的唯一标识看是否相等。

第一个方法只使用ID来计算一个story对象的hash值，如下所示：

```swift
func hash(into hasher: inout Hasher) {
    hasher.combine(id)
}
```

第二个方法使用运算符重载来实现一个自定义的`==`方法来比较两个story对象是否相同。

```swift
static func ==(lhs: NewsStory, rhs: NewsStory) -> Bool {
    return lhs.id == rhs.id
}
```

到此为止，完美解决问题！我们实现Hashable版本比Swift自动生成的方法的版本更快，因为我们的hash函数只计算了ID的hash值，而Swift的版本计算了所有属性的hash值。

示例中我们只使用了id这个属性值，但是你在项目中也可以使用更多的属性来保证你的对象是不同的。

最终`NewsStory`代码如下所示：

```swift
struct NewsStory: Hashable {
    var id: Int
    var title: String
    var date = Date()

    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }

    static func ==(lhs: NewsStory, rhs: NewsStory) -> Bool {
        return lhs.id == rhs.id
    }
}
```

在我们的文章结束之前，需要提醒一点， 其实是[Rob Napier](https://twitter.com/cocoaphony/status/1174655727878758400)的提醒：相等意味着可替换——任何两个相等的对象在代码中都可以相互替换。如果你只比较了`id`，那就意味着"如果两个对象有相同的id，但是其它属性是不同的，我不关心其它属性是什么样的，算法可以自由的返回其中的任意一个。"

最后，也是最重要的一点：如果两个对象相等(因为自定义的`==`返回true)，那么Swift会自由选择。Swift可能总是选择第一个对象，也可能总是选择第二个对象，或者每次随机选择两个中的一个——这种表现在未来的Swift版本中可能会发生改变。记住这点，因为我们告诉Swift两个对象是相同的，才会发生这个问题，如果关于对象的选择对你来说很重要，你需要注意这个问题。