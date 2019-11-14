InjectionIII在网上的资料并不是很多哎。

下面简单做一下总结吧！

> 整体来看，在真实项目中还是可以应用一下的，在手写代码调试UI样式的时候，用起来应该还是可以的。
>
> 如果要实践的话，可以再研究一下：[Vaccine](https://github.com/zenangst/Vaccine)。

# 1.安装

Github地址：https://github.com/johnno1962/InjectionIII

1.前往AppStore下载安装InjectionIII或者github上下载源代码编译。

2.在`applicationDidFinishLaunching`方法中添加如下代码。

Xcode 10.2 and later (Swift 5+):

```swift
#if DEBUG
Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/iOSInjection.bundle")?.load()
//for tvOS:
Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/tvOSInjection.bundle")?.load()
//Or for macOS:
Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/macOSInjection.bundle")?.load()
#endif
```

3.如果在新版本的xcode中不好使了，去github的release上下载最新的版本试试！

**局限性：**

* 只支持模拟器，不支持真机。有时候并不能触发变化，暂时没找到规律。
* 不支持swift类型添加或者移除方法（导致崩溃）。但是可以再类扩展中添加或者移除方法。
* swift工程不支持添加文件。（测试不开启全模块优化依然不行啊）
* 方法派发必须通过vtable方式而不是直接派发的方式进行调用才能够被监听。
* 开启全模块优化后可能不好使，已知不支持添加新文件。因为injection需要知道怎么编译单个的swift文件。

**可用性：**

* 支持@objc方法和普通方法监听。

**删除插件：**

```
rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/InjectionPlugin.xcplugin
```



# 2.InjectionIII原理

Mac上安装的InjectionIII会监听源代码文件的变化，如果文件被改动了，Injection Server就会执行rebuildClass重新进行编译、打包成动态库，也就是.dyld。编译打包成动态库后使用writeString方法通过Socket通知运行的App。

<p>Server 会在后台发送和监听 Socket 消息，实现逻辑在 <code>InjectionServer.mm</code> 的 runInBackground 方法里。Client 也会开启一个后台去发送和监听 Socket 消息，实现逻辑在 <code>InjectionClient.mm</code>里的 runInBackground 方法里。</p>
<p>Client 接收到消息后会调用 inject(tmpfile: String) 方法，运行时进行类的动态替换。inject(tmpfile: String) 方法的具体实现代码，你可以点击<a href="https://github.com/johnno1962/InjectionIII/blob/master/InjectionBundle/SwiftInjection.swift">这个链接</a>查看。</p>
原理图如下所示：

![](../resourse/images/4f49ea2047d2dd2d5c4646b0ba55b8c9.png)



原理介绍: [iOS热重载背后的黑魔法](https://mp.weixin.qq.com/s?__biz=MjM5NTQ2NzE0NQ==&mid=2247483999&idx=1&sn=bc88d37b6f819bd6bd7d8b76e9787620&chksm=a6f958b9918ed1af9a084ce2c2732aaee715193e37fdb830dc31d8f0174c0314b22dc5c0dd1e&mpshare=1&scene=1&srcid=0612tT8PS1pePiL5EmqMr9HH#rd)

# 3.踩坑

1. 必须手动打开Mac端App后，然后运行项目才能够正常的链接上。
2. 打开InjectionIII工具，选择项目代码所在的目录时候，必须选择你的`.xcodeproj`文件所在的目录。
3. InjectionIII的File Watcher选项要保持选中状态，连接成功后，InjectionIII的图标颜色会由蓝变黄。
4. InjectionIII工具只对模拟器有效，真机无效















