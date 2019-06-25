教程地址: https://beeth0ven.github.io/RxSwift-Chinese-Documentation
## 1. RxSwift能帮助我们做什么?
### 1.1 Target Action
在iOS开发中, 我们经常给UIbutton或者UIView添加交互事件, 引入RxSwift之后, 我们可以换一种方式了. 这种方式无需使用Target Action, 这样使代码逻辑更加清晰.

```swift
redButton.rx.tap.subscribe(onNext:{
    print("redbutton")
}).disposed(by: disposeBag)
```
### 1.2 代理
传统的代理, 需要实现代理方法, 在代理方法中处理逻辑代码, 引入RxSwift后, 直接就能得到想要的结果. 常用的UIscrollView, UITableView代理在RxSwift中都有相关的实现, 我们在声明自己代理时候, 可以使用类似的方法. 

```swift
scrollView.rx.contentOffset.subscribe { (contentOffset) in
    print(contentOffset)
}.disposed(by: disposeBag)
```
### 1.3 闭包回掉
使用回掉RxSwift中订阅代理回掉闭包, 在订阅中处理相关的网络状态.

```swift
URLSession.shared.rx.data(request: URLRequest(url: url))
.subscribe(onNext: { data in
    print("Data Task Success with count: \(data.count)")
}, onError: { error in
    print("Data Task Error: \(error)")
})
.disposed(by: disposeBag)
```
### 1.4 通知
使用RxSwift订阅通知, 无需管理观测者的声明周期, 这样可以有更多的精力去关注业务逻辑代码.

```
NotificationCenter.default.rx
	.notification(.UIApplicationWillEnterForeground)
	.subscribe(onNext: { (notification) in
	    print("Application Will Enter Foreground")
	}).disposed(by: disposeBag)
```
### 1.5 KVO
RxSwift可以直接使用KVO, 在subscribe中监听相关属性的变化.

```
scrollView.rx.observe(CGRect.self, #keyPath(UIScrollView.frame))
    .subscribe(onNext: { (frame) in
        print(frame?.debugDescription)
}).disposed(by: disposeBag)
```
### 1.6 多个任务之间有依赖关系
使用RxSwift实现多个任务的依赖关系, 无需嵌套多层, 容易维护.

```
/// 用 Rx 封装接口
enum Api {
    /// 通过用户名密码取得一个 token
    static func token(username: String, password: String) -> Observable<String> { ... }

    /// 通过 token 取得用户信息
    static func userInfo(token: String) -> Observable<UserInfo> { ... }
}
/// 通过用户名和密码获取用户信息
Api.token(username: "beeth0ven", password: "987654321")
    .flatMapLatest(Api.userInfo)
    .subscribe(onNext: { userInfo in
        print("获取用户信息成功: \(userInfo)")
    }, onError: { error in
        print("获取用户信息失败: \(error)")
    }).disposed(by: disposeBag)
```

### 1.7 等待多个并发任务完成后处理结果
并发任务, 可以通过zip方法来统一调用.

```
/// 用 Rx 封装接口
enum Api {

    /// 取得老师的详细信息
    static func teacher(teacherId: Int) -> Observable<Teacher> { ... }

    /// 取得老师的评论
    static func teacherComments(teacherId: Int) -> Observable<[Comment]> { ... }
}
/// 同时取得老师信息和老师评论
Observable.zip(
      Api.teacher(teacherId: teacherId),
      Api.teacherComments(teacherId: teacherId)
    ).subscribe(onNext: { (teacher, comments) in
        print("获取老师信息成功: \(teacher)")
        print("获取老师评论成功: \(comments.count) 条")
    }, onError: { error in
        print("获取老师信息或评论失败: \(error)")
    }).disposed(by: disposeBag)
```

## 2. 一个简单的输入验证dmeo

```
private func setupViewModel() {
    
    let usernameValid = username.rx.text.orEmpty.map{$0.count >= 6}.share(replay: 1)
    usernameValid.bind(to: password.rx.isEnabled).disposed(by: disposeBag)
    usernameValid.bind(to: usernameNotice.rx.isHidden).disposed(by: disposeBag)
    
    let passwdValid = password.rx.text.orEmpty.map{$0.count >= 6}.share(replay: 1)
    passwdValid.bind(to: passwordNotice.rx.isHidden).disposed(by: disposeBag)
    
    
    let everythingValid = Observable.combineLatest(usernameValid, passwdValid) { $0 && $1 }.share(replay: 1)    //??
    everythingValid.bind(to: confirmButton.rx.isEnabled).disposed(by: disposeBag)
    
    confirmButton.rx.tap.subscribe(onNext: {[weak self] in
        self?.showAlert()
    }).disposed(by: disposeBag)
}
    
private func showAlert() {
    let alertController = UIAlertController(title: "Rx Helloworld", message: nil, preferredStyle: .alert)
    let action = UIAlertAction(title: "confirm", style: .default, handler: nil)
    alertController.addAction(action)
    present(alertController, animated: true, completion: nil)
}
```

## 3. 函数响应式编程
**函数式编程:**是一种编程范式, 他需要我们将函数作为参数传递, 或者作为返回值返还. 我们可以通过组合不同的函数得到想要的结果. 函数式编程更加的灵活/高复用性/简洁/易维护/适应各种需求变化.

我们通过不同的构建函数来创建所需要的数据序列. 最后通过适当的方式来响应这个序列. 这就是**函数响应式编程**.

在RxSwift中有个比较重要的概念就是**数据绑定(订阅)**. 就是指可被监听的序列绑定到观察者上. 

## 4. RxSwift核心
1. Observable - 产生事件
2. Observer - 响应事件
3. Operator - 创建变化组合事件
4. Disposable - 管理绑定(订阅)的生命周期
5. Schedulers - 线程队列调配

### 4.1.1 Observable - 可被监听的序列
所有的事物都是序列. Observable可以用来描述元素异步产生的序列. 我们生活中许多事物都可以通过它来表示, 例如: 温度变化的序列(Observable<Double>), 网络请求JSON序列(Observable<JSON>). 

**Event - 事件**

```
public enum Event<Element> {
    case next(Element)
    case error(Swift.Error)
    case completed
}
```

* next - 序列产生一个新的元素;
* error - 创建序列时候产生一个错误,导致序列终止;
* completed - 序列的所有元素都已经成功产生,整个序列已经完成;

**特征序列**

RxSwift中, Observable也存在一些特征序列, 这些特征序列可以帮助我们更准确的描述序列. 并且他们还可以给我们提供语法糖, 让我们使用更优雅的方式书写代码.分别有: Single, Completable, Maybe, Driver, ControlEvent. 

### 4.1.2 Single
Singles=使Observable的另一个版本. 不像Observable可以发出多个元素, 它要么只发出一个元素, 要么产生一个error事件.

* 发出一个元素, 或者一个error事件
* 不会共享状态变化

一个常见的例子使执行http请求, 然后返回一个应答或者错误. 不过你**可以使用Single来描述任何只有一个元素的序列**.

`SingleEvent`的枚举:

```swift
public enum SingleEvent<Element> {
    case success(Element)
    case error(Swift.Error)
}
```

### 4.1.3 Completable
Completable是Observale的另一个版本. 它要么产生一个completed事件, 要么产生一个error事件. 

* 发出零个元素
* 发出一个completed事件或者一个error事件
* 不会共享状态变化

Completable适用于那种只关心任务是否完成, 而不需要在意任务返回值的情况. 他和Observable<Void>有点相似.

**CompletableEvent的枚举:**

```
public enum CompletableEvent {
    case error(Swift.Error)
    case completed
}
```

### 4.1.4 Maybe
Maybe是Observable的另一个版本. 介于Single和Completable之间, 它要么只能发出一个元素, 要么产生一个completed事件, 要么产生一个error事件.

* 发出一个元素或者一个completed事件或者一个error事件
* 不会共享状态变化

### 4.1.5 Driver
Driver是一个精心准备的特征序列. 它主要是为了简化UI层代码. 不过如果你遇到的序列具有以下特征, 你也可以使用它:

* 不会产生error事件
* 一定会在MainScheduler监听(主线程监听)
* 共享状态变化

这些都是驱动UI的序列所具有的特征.

**示例代码:**

```
/// 模拟根据shu用户输入,产生一个网络请求,网络请求结果绑定到UI上,一个UI空间展示数量,另一个产生结果
private func testDriver() {
    let results = username.rx.text.asDriver().distinctUntilChanged()        // 将普通序列转换为 Driver
        .throttle(0.03)
        .flatMapLatest { query in
            return self.fetchAutoCompleteItems(keyword: query).asDriver(onErrorJustReturn: [])
    }
    
    results.map{"\($0.count)"}.drive(usernameNotice.rx.text).disposed(by: disposeBag)
    
    results.map{"\($0.description)"}.drive(passwordNotice.rx.text).disposed(by: disposeBag)
}
    
private func fetchAutoCompleteItems(keyword: String?) -> Observable<[String]> {
    return Observable<[String]>.create({ (observable) -> Disposable in
        DispatchQueue.global().asyncAfter(deadline: DispatchTime.now() + 0.5, execute: {
            observable.onNext([keyword!])
        })
        return Disposables.create()
    })
}
```
任何被监听的序列都可以呗转换为Driver, 只要满足三个条件: 不会产生error事件;一定在MainScheduler监听(主线程监听);共享状态变化;

drive方法只能被Driver调用. 这意味着, 如果你发现代码存在driver, 那么这个序列不会产生错误事件并且一定在主线程监听. 这样你可以安全的绑定UI元素.

### 4.1.6 ControlEvent
ControlEvent专门用于描述UI控件所产生的事件, 它具有以下特征:

* 不会产生error事件
* 一定在MainScheduler订阅(主线程订阅)
* 一定在MainScheduler监听(主线程监听)
* 共享状态变化

### 4.2 Observer - 观察者
观察者是用来监听事件, 然后它需要这个事件做出响应. 例如: 弹出提示框就是观察者, 它对点击按钮这个事件做出响应.

**如何创建观察者**

和Observable一样, 框架已经帮我们创建好很多的观察者. 例如: view是否隐藏, button是否可点击, label当前文本, imageView的当前图片等.

另外, 有一些自定义的观察者是需要我们自己创建的. 例如:

```
tap.subscribe(onNext: { [weak self] in
    self?.showAlert()
}, onError: { error in
    print("发生错误： \(error.localizedDescription)")
}, onCompleted: {
    print("任务完成")
})
```

创建观察者最直接的方法就是Observable的subscribe方法后面的描述, 事件发生时, 需要如何做出响应. 而观察者就是由后面的onNext, onError, onCompleted等这些闭包构建出来的.

### 4.2.1 AnyObserver
AnyObservable可以用来描述任意一种观察者.

**根据逻辑判断是否显示usernameNotice:**

```
usernameValid.bind(to: usernameNotice.rx.isHidden).disposed(by: disposeBag)

相当于下面代码:

let usernameNoticeObserver: AnyObserver<Bool> = AnyObserver { [weak self] (event) in
    switch event {
    case .next(let isHidden):
        self?.usernameNotice.isHidden = isHidden
    case .error(let error):
        print(error)
    case .completed:
        print("completed")
    }
}
    
usernameValid.bind(to: usernameNoticeObserver).disposed(by: disposeBag)
```

**对网络请求结果的处理:**

```
URLSession.shared.rx.data(request: URLRequest(url: url))
    .subscribe(onNext: { data in
        print("Data Task Success with count: \(data.count)")
    }, onError: { error in
        print("Data Task Error: \(error)")
    })
    .disposed(by: disposeBag)
    
相当于下面拆分后的代码:

let observer: AnyObserver<Data> = AnyObserver { (event) in
    switch event {
    case .next(let data):
        print("Data Task Success with count: \(data.count)")
    case .error(let error):
        print("Data Task Error: \(error)")
    default:
        break
    }
}

URLSession.shared.rx.data(request: URLRequest(url: url))
    .subscribe(observer)
    .disposed(by: disposeBag)
```

### 4.2.2 Binder
和Observable一样, Observer也存在特征观察者, 例如: `Binder`.

Binder主要有以下两个特征:

* 不会处理错误事件
* 确保绑定都是在给定Scheduler上执行(默认MainScheduler)

一旦产生错误事件, 在调试环境下将执行fatalError, 在发布环境将打印错误信息.

如上`4.2.1-根据逻辑判断是否显示usernameNotice`代码所示, 观察者是一个UI观察者, 所以它在响应事件时候, 只会处理next事件, 并且更新UI的操作需要在主线程上执行. 因此更好的方案就是使用Binder:

```
let observer: Binder<Bool> = Binder.init(usernameNotice) { (usernameNotice, isHidden) in
    usernameNotice.isHidden = isHidden
}
usernameValid.bind(to: observer).disposed(by: disposeBag)
```

Binder只处理next事件, 并且保证响应next事件的代码一定在Scheduler上执行, 这里采用默认的MainScheduler.

由于页面是否隐藏式一个常用的观察者, 所以应该让所有的UIView都提供这种观察者:

```
extension Reactive where Base: UIView {
    fileprivate var isVisible: Binder<Bool> {
        return Binder(self.base, binding: { (view, hidden) in
            view.isHidden = hidden
        })
    }
}
```

### 4.3 Observable & Observer既是可被监听的序列也是观察者
我们所遇到的事务中, 有一部分非常特别, 他们既是可被监听的序列, 也是由外部文本序列来控制的当前显示内容的观察者. 许多的UI控件都有这种特性, 比如switch开关的状态, segmentControl的选中索引, datePicker的选中日期等等.

RxSwift中定义了一些辅助类型, 他们既是可被监听的序列, 也是观察者. 使用合适的辅助类型, 能够帮助你更加准确的描述事物的特征: AsyncSubject, PublishSubject, ReplaySubject, BehaviorSubject, Variable, ControlProperty.

### 4.3.1 AsyncSubject
AsyncSubject将在源Observable产生完成事件后, 发出最后一个元素(仅仅只有最后一个元素), 如果源Observable没有发出任何元素, 只有一个完成事件. 那么AsyncSubject也只有一个完成事件.

它会对随后的观察者发出最终元素. 如果源Observable产生了一个error事件而终止, AsnycSubject就不会发出任何元素, 而是将这个error事件发送出来.

```
private func testAsyncSubject() {
    let subject = AsyncSubject<String>()
    subject.subscribe(onNext: { (element) in
        print(element)
    }, onError: { (error) in
        print(error)
    }, onCompleted: {
        print("on completed")
    }).disposed(by: disposeBag)
    
    subject.onNext("111")
    subject.onNext("222")
    subject.onNext("333")
    subject.onCompleted()   //只会发出最后一个元素 "333", 调用onCompleted才会发出元素,不调用这不会发出任何元素.
}
```

### 4.3.2 PublishSubject
PublishSubject将对观察者发送订阅后产生的元素, 而在订阅前发出的元素将不会发送给观察者. 如果你希望观察者接受所有的元素, 你可以通过Observale的create方法来创建Observale或者使用ReplaySubject.

如果Observable因为产生一个error事件而终止, 则PublishSibject就不会发出任何元素, 而将这个error事件发送出来.

### 4.3.3 ReplaySubject
ReplaySubject将对观察者发送全部的元素, 无论观察者何时进行订阅的.

这里有多个版本的ReplaySubject, 有的只会将最新的n歌元素发送给观察者, 有的只会将限定时间段内最新的元素发送给观察者.

如果把ReplaySubject当做观察者来使用, 注意不要再多个县城调用onNext, onError或者onCompleted. 这样会导致无需调用, 造成意想不到的结果.

### 4.3.4 BehaviorSubject
当观察者对BehaviorSubject进行订阅时候, 它会将源Observable中最新的元素发送出来(如果不存在就发出默认元素). 然后将随后产生的元素发送出来.

如果源产生一个error事件而终止, BehaviorSubject就不会发出任何元素, 而是将这个error事件发送出来.

### 4.3.5 Variable
即将废弃, 使用`BehaviorRelay`代替.

### 4.3.6 ControlProperty
ControlProperty专门用来描述UI控件的, 具有以下特征:

* 不会产生error事件
* 一定在MainScheduler订阅
* 一定在MainScheduler监听
* 共享状态变化

### 4.4 Operator - 操作符
操作符可以帮助我们创建新的序列, 或者变化组合原有的序列, 从而生成一个新的序列. 例如map和combineLatest都是操作符, 它们可以帮助我们构建所需要的序列.

* filter: 过滤, 符合条件的元素生成新的序列;
* map: 变形, 将序列中所有的元素进行变形操作;
* zip: 配对, 将两个或者多个Observable序列匹配起来, 例如等待两个事情完成后处理结果;

Rx提供了充分的操作符帮助我们来创建序列. 如果内置操作符无法满足你的需求, 你也可以自定义操作符.

如果你不知道该如何选择操作符, 可以参考决策树: [如何选择操作符?](https://beeth0ven.github.io/RxSwift-Chinese-Documentation/content/decision_tree.html)

### 4.5 Disposable - 可被清除的资源
通常来说, 一个**序列如果发出了error或者completed事件, 那么所有的内部资源都会被释放**. 如果你需要提前释放那些资源或取消订阅的话, 那么你可以对返回的`可被清除的资源`调用dispose方法.

```
self.disposable = textField.rx.text.orEmpty
        .subscribe(onNext: { text in print(text) })
self.disposable?.dispose()
```

调用dispose方法后, 订阅将被取消, 并且内存资源会被释放. 通常情况下, 你是不需要手动调用dispose方法的. 推荐使用清楚包(DisposeBag)或者takeUntil操作符来管理订阅的声明周期.

Swift中我们习惯使用ARC管理内存. 我们可以使用ARC来管理订阅的声明周期. 在实例对象中声明DisposeBag类型的属性, 使用disposeBag属性管理订阅的声明周期.

另一种实现自动取消订阅的方法就是使用takeUntil操作符, 这会使得订阅一直持续到控制器的dealloc事件产生为止.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    ...

    _ = usernameValid
        .takeUntil(self.rx.deallocated)
        .bind(to: passwordOutlet.rx.isEnabled)
}
```

### 4.6 Schedulers - 调度器
Schedulers是Rx实现多线程得核心模块, 它主要用于控制任务在哪个线程或者队列运行.

**GCD实现多线程实例:**

```
DispatchQueue.global(qos: .userInitiated).async {
    let data = try? Data(contentsOf: url)
    DispatchQueue.main.async {
        self.data = data
    }
}
```

**RxSwift实现多线程示例:**

```
let rxData: Observable<Data> = ...

rxData
    .subscribeOn(ConcurrentDispatchQueueScheduler(qos: .userInitiated))
    .observeOn(MainScheduler.instance)
    .subscribe(onNext: { [weak self] data in
        self?.data = data
    })
    .disposed(by: disposeBag)
```

**subscribeOn:**我们使用subscribeOn来决定数据序列的构建函数在哪个Scheduler上运行. 以上例子中, 由于获取Data需要花费很长时间, 所以subscribeOn切换到后台Scheduler来获取Data. 这样可以避免主线程被阻塞.

**observeOn:**我们用observeOn来决定在哪个Scheduler监听这个数据序列. 上面例子中我们使用observeOn方法切换到主线程来监听并且处理结果.

一个典型的例子就是, 在后台发起网络请求, 然后解析数据, 最后在主线程刷新页面. 你就可以先用subcribeOn切换到后台去发送网络请求并解析数据, 最后使用observeOn切换到主线程更新页面.

* **MainScheduler:** 主线程, 如果你需要执行一些UI相关任务, 就需要切换到该Scheduler运行.
* **SerialDispatchQueueScheduler:** 抽象了串行DispatchQueue, 如果你需要执行一些串行任务, 可以切换到这个Scheduler运行.
* **ConcurrentDispatchQueueScheduler:** 抽象了并行DispatchQueue, 如果你需要执行一些并发任务, 可以切换到这个Scheduler运行.
* **OperationQueueScheduler:** 抽象了NSOperationQueue. 具备NSOperationQueue的一些特定,例如你可以设置maxConcurrentOperationCount来控制同时执行并发任务的最大数量.

### 4.7 Error Handling - 错误处理
一旦序列产生了一个error事件, 整个序列将被终止. RxSwift主要有两种错误处理机制: retry, catch

#### 1. retry - 重试
retry可以让序列在发生错误后重试,如果`query.retry(3)`, 表示当序列发生错误时候, 进行重试操作, 并且最多重试3次.

#### 2. retryWhen
当发生错误时候, 经过一端延时后进行重试. 

```
// 请求 JSON 失败时，等待 5 秒后重试，
let retryDelay: Double = 5  // 重试延时 5 秒

rxJson
    .retryWhen { (rxError: Observable<Error>) -> Observable<Int> in
        return Observable.timer(retryDelay, scheduler: MainScheduler.instance)
    }
    .subscribe(...)
    .disposed(by: disposeBag)
```

#### 3. catchError - 恢复
catchError可以在错误产生时候, 用一个备用元素或者一组备用元素将错误替换掉: 

```
searchBar.rx.text.orEmpty
    ...
    .flatMapLatest { query -> Observable<[Repository]> in
        ...
        return searchGitHub(query)
            .catchErrorJustReturn([])
    }
    ...
    .bind(to: ...)
    .disposed(by: disposeBag)
```

如上所示, 当产生错误时候, 就返回一个空数组, 于是就会显示一个空列表页面.

#### 4. Result
我们可以使用`Result`类型包装序列的结果, 如果发生错误则使用failure包装错误类型, 如果成功则使用success包装需要返回的元素. 这样即使序列产生错误也不会终止这个那个序列. 就算网络请求失败, 整个订阅依然存在. 如果用户再次点击更新按钮, 也是能够发起网络请求进行更新操作的.

```
// 自定义一个枚举类型 Result
public enum Result<T> {
    case success(T)
    case failure(Swift.Error)
}
```

# 5.一些问题以及解答

什么是函数式编程?

> **函数式编程**是一种编程范式, 它需要我们将函数作为参数进行传递, 或者作为返回值返还. 我们可以通过组合不同的函数来得到想要的结果. 举例swift提供的高阶函数:filter,map,reduce,flatMap等.

什么是响应式编程?

> 将
>
> 我们通过不同的构建函数, 来创建所需要的数据序列. 最后通过适当的方式来响应这个序列. 这就是**函数响应式编程**.

使用RxSwift有什么好处?

> 1.更加方便的实现数据绑定，构建清晰的数据流。结合MVVM使用RxSwift实现数据绑定，代码逻辑也更加清晰。
>
> 2.将复杂业务代码分离到ViewModel中，减轻Controller的负担。（通过官方示例Demo来说明）

RxSwift常用变形函数？

> map：变形，flatMapLeatest：只接受最新的一个，比如一次发出2个，只会对最新发出的一个做响应。

bind和subscribe的区别是什么?

> bind需要保证一定不会出错，一定发生在主线程上，主要用来绑定UI视图。

RxSwift内部实现原理是什么？

> 

**相关关键字：**

> 函数式编程、响应式编程、数据绑定、任务依赖、Obervable、Observer、disposable、Single、Completable、Maybe、Driver、bind、subscribe、变形函数、RxDataSources

