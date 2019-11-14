# Get started with SwiftUI

# SwiftUI上手教程

Hands-on code to help you get moving fast.

[Paul Hudson](https://www.hackingwithswift.com/about)    October 14th 2019     [@twostraws](https://twitter.com/twostraws)

![img](https://www.hackingwithswift.com/uploads/ios-13-1.jpg)

SwiftUI is Apple's incredible new user interface framework for building apps for iOS, macOS, tvOS, and even watchOS, and brings with it a new, declarative way of building apps that makes it faster and safer to build software.

If you're already using UIKit there's a bit of a speed bump at first because we’re all so programmed to think in terms of UIKit’s flow, but once you’re past that – once you start thinking in the SwiftUI mindset – then everything becomes much clearer.

SwiftUI是苹果公司新发布的用于构建UI界面的框架，它可以用于搭建iOS、macOS、tvOS、watchOS系统的用户界面。它采用声明式的方式进行构建，相比UIKit，使用SwiftUI搭建App更快、更安全。

如果你用过UIKit，刚上手使用SwiftUI可能会不习惯，因为我们已经习惯了用UIKit搭建界面的流程，但是一旦你习惯了SwiftUI声明式的方式，习惯了SwiftUI的思考方式，你一定会爱上SwiftUI。

At the launch of SwiftUI at WWDC19, Apple described it as being four things:

- **Declarative**, meaning that we say what we want rather than how we get there.
- **Automatic**, meaning that it takes care of many things we had to do by hand previously.
- **Compositional**, meaning that we build small things and combine them together into larger things.
- **Consistent**, meaning that we don’t see strange fault lines between Swift and Objective-C like we did previously, or very old APIs mixed up with very new ones.

WWDC2019苹果介绍SwiftUI时候，说它有四个特点：

* **声明式的：**我们只需声明我们想要的样式，并不需要像UIKit那样描述如何实现我们的样式。
* **自动化的：**自动化意味着它可以省去很多之前我们必须手动完成的事情。
* **可组合的：**可组合意味着我们可以构建很多小的UI元素，然后将小的元素组合在一起，形成复杂的UI界面。
* **稳定的：**稳定意味着在不同版本的系统中SwiftUI搭建的界面表现是相同的。

Having now written tens of thousands of lines of code with SwiftUI I can tell you they missed one important point off: *concise*. Your SwiftUI code will be maybe 10-20% of what your UIKit code was – almost all of it disappears because we no longer repeat ourselves, no longer need to handle so many lifecycle methods, and more.

Let’s dig in to how SwiftUI works…

 

- **Update:** I've released a massive, free guide to SwiftUI here: [SwiftUI by Example](https://hackingwithswift.com/quick-start/swiftui) – it contains a huge number of code samples and solutions for common SwiftUI problems, plus a long video showing you how to build your first complete project.
- You can also now follow my free [100 Days of SwiftUI](https://www.hackingwithswift.com/100/swiftui) curriculum to learn SwiftUI in a hands-on way.

目前为止我已经使用SwiftUI写了成千上万行的代码，我可以明确的告诉你， 他们漏了很重要的一点：**简洁**。你使用SwiftUI搭建界面的代码量可能只有UIKit代码量的10-20%。之所以会减少这么多，是因为我们不需要再写重复的UI元素，也不用处理生命周期相关的方法等等。

让我们一起来看看SwiftUI到底是如何工作的：

* 更新：我已经发布了大量的SwiftUI免费教程：[SwiftUI示例](https://hackingwithswift.com/quick-start/swiftui)。里面有很多代码示例和SwiftUI常见问题的解决方案，外加一个长视频叫你怎么搭建你的第一个完整SwiftUI项目。
* 现在你还可以关注我的[100天SwiftUI入门](https://www.hackingwithswift.com/100/swiftui)课程，手把手叫你写SwiftUI。

### What is a View?

### View是什么

In SwiftUI, `View` is more or less what we had with `UIView`, with two major differences:

1. It’s a protocol rather than a class, so we don’t get stuck in inheritance problems.
2. Our view must always return one view to render. That view might internally contain other views, but it’s still just one parent view going back.

在SwiftUI中，`View`的功能类似于`UIView`，但是有两个重要的不同点：

1. View是协议类型而不是class类型，因此不会出现集成问题。
2. 我们的view必须返回唯一的一个view，这个view内部可以包含多个其他的view，但是我们只能返回多个view的superview。

Apple’s default sample code gives us this view:

苹果给的代码示例如下所示：

```swift
struct ContentView: View {
    var body: some View {
        Text("Hello World")
    }
}
```

You’ll notice a few things there:

1. It returns `some View`, which is an opaque type – [watch my video on opaque return types in Swift 5.1](https://youtu.be/DvHkeUxiwYY) if you haven’t seen these before.
2. It returns exactly one thing: a `Text` view with the text “Hello World” – that’s equivalent to a `UILabel` in UIKit terms.
3. It doesn’t use the `return` keyword – that’s another Swift 5.1 change, so you might want to [watch my video](https://youtu.be/EkvxlrYFUkc) for that too.

你可能会注意到这些事情：

1. 上述View的返回值是`some View`，其实some view是一个透明类型。如果你没见过这种用法，你可以参考我的文章：[透明返回类型](https://www.youtube.com/watch?v=DvHkeUxiwYY&feature=youtu.be)。
2. 它实际上返回的是一个包含文本“Hello World”的Text视图，Text等价于UIKit中的UILabel。
3. 这里并没有使用`return`关键字，这是Swift5.1的另一个新特性，你可能想看[我的讲解视频](https://www.youtube.com/watch?v=EkvxlrYFUkc&feature=youtu.be)。

### Creating layout stacks

### 创建布局堆栈

Remember, our views must return precisely one thing inside them – not 0, 2, or 200. So, if you want to show multiple labels you need to place them inside a container such as a `HStack`, a `VStack`, or a `ZStack` – effectively horizontal stack views, vertical stack views, or a stack view that positions one thing on top of another.

So, if you wanted to place three labels above each other you’d write this:

前面提到，我们的View必须返回唯一的一个东西，不是0个、2个或者200个。因此如果你想要展示多个标签，你必须将他们放到一个容器里面，比如`HStack`、`VStack`、`ZStack`，分别对应水平布局、垂直布局、堆叠布局。

因此，如果你想要垂直排列三个label，你需要像下面这么写：

```swift
var body: some View {
    VStack {
        Text("Hello World")
        Text("Hello World")
        Text("Hello World")
    }
}
```

By default these stacks take up only the space they need, so you’ll see those three text views bunched up in the center of your screen. If you want to force them apart, you can insert a flexible space control using `Spacer`, like this:

默认情况下，这些label只会占据他们需要的空间，所以你将会在屏幕中央看到这三个label。如果你想让他们分开，你需要使用`Spacer`插入一个可变的控件，如下所示：

```swift
var body: some View {
    VStack {
        Text("Hello World")
        Text("Hello World")
        Spacer()
        Text("Hello World")
    }
}
```

Now you’ll see the first two text views at the top of your screen, then a large gap, then the final text view at the bottom of your screen. For a more subtle effect, use `Divider`rather than `Spacer` – this creates a horizontal rule between elements, giving a little visual space between them without pushing them far apart.

现在你将会看到前两个label在你屏幕的顶部，然后是一个空隙，然后是最后一个view在屏幕的底部。为了产生更加微妙的效果，你可以使用Divider，这个可以用来创建元素水平上的空隙，而不会将他们推开。

### Creating lists of content

Everyone who has used UIKit knows the monotony of creating table views: we need to register cell prototypes, tell UIKit how many items we have, dequeue and configure cells, and more – it’s tedious, and it’s repetitive.

*And it’s all gone with SwiftUI.*

Tables in SwiftUI use a new `List` view, which can show static or dynamic content. For example, we might have a struct that defines users, like this:

```swift
struct User {
    var firstName: String
    var lastName: String
}
```

If we wanted to show that inside rows in a list, we’d start by defining what one row looks like. We know it needs to work with a `User`, so we would add that as a property. And we know it needs to show their names, so we’d make our view body return a text view.

In SwiftUI, it’s this:

```swift
struct UserRow: View {
    var user: User

    var body: some View {
        Text("\(user.firstName) \(user.lastName)")
    }
}
```

Finally, we can update our `ContentView` struct so that it creates a couple of users then puts them in a list:

```swift
struct ContentView: View {
    var body: some View {
        let user1 = User(firstName: "Piper", lastName: "Chapman")
        let user2 = User(firstName: "Gloria", lastName: "Mendoza")

        return List {
            UserRow(user: user1)
            UserRow(user: user2)
        }
    }
}
```

While that works fine for simple lists, for anything more advanced you’re going to want to use dynamic content – to pass in an array of data and have SwiftUI figure out how many rows it needs, create them all, and show them all.

To make that happen, we must make `User` conform to the `Identifiable` protocol so that SwiftUI knows which person is which uniquely. Although it *could* check for this by querying every single property, that’s slow and likely to lead to false positives. So, instead we need to give our structs a unique `id` value that we know won’t be duplicated by other items in the same list.

So, we could update `User` to have a `id` property like this:

```swift
struct User: Identifiable {
    var id: Int
    var firstName: String
    var lastName: String
}
```

You can use anything you like for your identifier – strings, UUIDs, or whatever, are all fine.

Now that we have that we can create an array of our users – which could just as easily come from some `Codable` input you’ve downloaded from the internet – and pass that into the list as we create it. We then pass a closure to run that configures individual rows in the list, like this:

```swift
struct ContentView: View {
    var body: some View {
        // create some example data
        let user1 = User(id: 1, firstName: "Piper", lastName: "Chapman")
        let user2 = User(id: 2, firstName: "Gloria", lastName: "Mendoza")
        let users = [user1, user2]

        // show that data
        return List(users) { user in
            UserRow(user: user)
        }
    }
}
```

Just to be clear: about half that code is just me creating some example data – the actual SwiftUI part to create and show many list rows is only three lines of code, and that’s only if you include the closing brace on a line by itself.

In fact, if you’re just showing rows like that, you can collapse it down even further:

```swift
return List(users, rowContent: UserRow.init)
```

**Boom.**

Seriously, SwiftUI will demolish so much of your code.

Before we’re done with lists, there’s one more thing: we can change `UserRow` freely without worrying about how it’s used elsewhere. Remember, SwiftUI is designed for composability: we pass a user into `UserRow` and let *it* figure out how it’s displayed, rather than always having our main views control everything like we did with UIKit.

So, if we wanted `UserRow` to show two labels stack vertically, with one large and one small, and with both labels aligned to their leading edge, we’d write this:

```swift
struct UserRow: View {
    var user: User

    var body: some View {
        VStack(alignment: .leading) {
            Text(user.firstName)
                .font(.largeTitle)
            Text(user.lastName)
        }
    }
}
```

Beautiful.

### Showing a detail screen

Showing just one screen isn’t very interesting, so let’s create another.

First, we’ll say this screen should show just the last name of the user that was selected – nice and big, and in a red color:

```swift
struct DetailView: View {
    var selectedUser: User

    var body: some View {
        Text(selectedUser.lastName)
            .font(.largeTitle)
            .foregroundColor(.red)
    }
}
```

Next we want to embed the list in `ContentView` inside a navigation view, which is equivalent to a navigation controller in UIKit:

```swift
struct ContentView: View {
    var body: some View {
        let user1 = User(id: 1, firstName: "Piper", lastName: "Chapman Yay")
        let user2 = User(id: 2, firstName: "Gloria", lastName: "Mendoza")
        let users = [user1, user2]

        return NavigationView {
            List(users, rowContent: UserRow.init)
        }
    }
}
```

By default navigation bars don’t have a title, so you should attach a title to your list like this:

```swift
List(users, rowContent: UserRow.init)
    .navigationBarTitle("Users")
```

Of course, what we *really* want is for tapping items in our cells to show our detail screen, passing in whichever user was tapped.

To make that happen we need to wrap our user rows inside a `NavigationLink`. This gets created with a handful of parameters, but here we’re going to use only two: where to send the user when the item is tapped, and a trailing closure specifying what to put inside the navigation item. In our case, that’s our user row with whatever user is being shown.

So, replace the navigation view with this:

```swift
return NavigationView {
    List(users) { user in
        NavigationLink(destination: DetailView(selectedUser: user)) {
            UserRow(user: user)
        }
    }.navigationBarTitle("Users")
}
```

Now two things have happened. First, if you click the small play button at the bottom-right corner of the preview area, you’ll find you can now tap on list rows to show the detail view we made. Second, you might also have noticed that our list rows have disclosure indicators – that’s what Apple meant about SwiftUI being automatic.

### Where next?

Once you've spent some time with SwiftUI it's fair to say that going back to UIKit feels like going back to Objective-C – methods like `viewDidLoad()` that we took for granted now seem just a bit alien, and you start to resent writing boilerplate code that effectively disappears in SwiftUI.

If you'd like to learn much more about SwiftUI, I published a [massive, free SwiftUI tutorial](https://www.hackingwithswift.com/quick-start/swiftui) called SwiftUI By Example, which walks you through a complete project from scratch then dives into well over 100 solutions for common problems you'll face – how do you create text fields? How do you work with Core Data? How do you animate changes? All that and more are covered, and it's online for free.

I’d love to hear your views on SwiftUI – send me a tweet at [@twostraws](https://twitter.com/twostraws) and let me know what you think!