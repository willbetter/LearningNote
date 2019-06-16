# 17. 远超你想象的多线程的那些坑
多线程技术有两个常见的坑: **常驻线程**, **并发问题**.
### 常驻线程
常驻线程就是将线程添加到runloop中, 一直存在的线程. 但是如果滥用常驻线程, 比如30各库, 每个都有自己的常驻线程, 那么**不但不能提高cpu的利用率, 反而会降低程序的执行效率**. 这样做不但不会提高cpu效率, 反而浪费了cpu资源. 

如果在程序中确实需要线程存活一段时间, 可以使用: `runUntilDate:`, `runMode:beforeDate`. 

`AFNetworking 2.0`中使用了常驻线程来完成网络请求和回掉. 这是因为其中用到了`NSURLConnection`, 而`NSURLConnection`在设计上有些缺陷. NSURLConnection发起请求后, 所在线程需要一直存活, 以等待接收回掉结果, 而网络返回时间不确定, 因此必须使用常驻线程.

![](images/02c4b5f2f0a9a2d0cef55b9e5420e148.png)

### 并发
并发是多线程的第二个坑. 

在进行读写操作时, 总是需要一段时间来等待磁盘响应, 如果此时通过GCD发起一个任务, 那么GCD本着最大化利用CPU的原则, 在等待磁盘的空挡回再创建新的线程来保证能够充分利用cpu. 

如果这些新任务都是类似这些数据存储这样需要等待磁盘响应的人物, 那么随着人物数量的增加, 回导致内存资源越来越紧张. 结果导致内存占用过多.

FMDB使用了`FMDatabaseQueue`这个核心类, 将数据的读写等数据库操作都放到一个串行队列中, 避免线程创建过多造成系统资源紧张的问题.


# 18. 怎么减少App电量消耗

1. cpu是耗电的大头, 对cpu的使用要精打细算, 要避免让cpu做多余的事情. 对于大量复杂的运算, 应该把数据传给服务器去处理.
2. 除了cpu, IO操作也是耗电的原因之一. 任何的IO操作都会破坏低功耗状态. 针对IO优化, 我们可以嫁给你碎片化的数据进行聚合, 在内存中进行存储. 在指定的时机(比如App进入后台/挂起)统一将数据进行存储.
3. 苹果的电量优化指南: [Energy Efficiency and the User Experience](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/EnergyGuide-iOS/)

/# 33. iOS系统内核`XNU`:App如何加载

/# 34. iOS黑魔法`Runtime Method Swizzing`背后的原理