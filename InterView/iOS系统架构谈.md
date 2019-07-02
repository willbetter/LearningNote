问题:

1. 网络层如何设计？设计网络层要考虑什么问题？对网络层做优化时候，可以从哪些地方入手？

2. 页面的展示调用组织都有哪些方案？我们做这些方案时候要考虑哪些问题？

3. 本地持久化层的设计方案有哪些？优劣势都是什么？不同方案间要注意的问题分别都是什么？

4. 要实现动态部署，都有哪些方案？不同方案之间的优劣点，他们的侧重点是什么？



一个糟糕的View层设计，主要有以下几个原因：

1. 代码混乱不规范；
2. 过多的继承导致的复杂依赖关系；
3. 模块化程度不够高，组件粒度不够细；
4. 横向依赖；
5. 架构设计失去传承；



通过AOP代替公共父类，但是存在问题是，所有App中类型都会被Hook，即使你不想hook也会被hook。



## MVC

MVC是最老牌的软件设计思想，其中Model就是作为数据管理者、View作为数据展示者、Controller作为数据加工者。Model和View又是由Controller来根据业务需求调配，所以Controller还负担一个数据流的调配功能。

## MVCS

苹果自身就是采用这种架构思路，这是基于MVC衍生出来的一套架构。从概念上来说，它拆分的是Model部分，拆出来一个Store。这个Store专门负责数据存取。

这是瘦Model的一种方案，它专门用于表达数据，然后存储、数据处理都交给外面来做。

## MVVM

MVVM本质上也是从MVC中派生出来的思想，MVVM着重想要解决的问题是尽可能地减少Controller的任务。MVVM认为Controller做了太多数据加工的事情，所以MVVM把数据借工的任务从Controller中解放出来，使得Controller只专注于数据调配的工作，ViewModel则去负责数据加工并通过通知机制让View响应ViewModel的改变。

不适用ReactiveCocoa也能实现MVVM，但是使用ReactiveCocoa能够更好的体现MVVM的精髓。

MVVM架构还是需要ViewController，只不过ViewController的作用只是用来建立View和ViewModel的关联，不再负责处理数据。















