> Objective-C和swift中向nil发送消息, 程序是不会崩溃的.
>
> **因为oc函数式通过`objc_msgSend`进行消息发送来实现的, objc_msgSend会通过判断self来决定是否发送消息, 如果self是nil, 那么会直接返回, 所以不会出现问题**. 而向nil发送消息会返回nil(返回值是对象), 0(返回值是基础数据类型), 0x0(返回值是id类型). 
>
> 但是对`[NSNull null]`对象发送消息是会crash的, 因为NSNull对象只有一个null方法. 向null发送消息, 运行时会把null当成一个正常的对象, 而找不到对应的方法则就会导致崩溃. 



更多详细信息可以参考这里: [https://www.jianshu.com/p/11dca953f962](https://www.jianshu.com/p/11dca953f962)

