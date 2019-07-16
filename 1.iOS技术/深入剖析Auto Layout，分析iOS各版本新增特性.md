[深入剖析Auto Layout，分析iOS各版本新增特性](https://www.jianshu.com/p/ae53304de925)

上面文章转载自戴铭的博客，详情请参考戴铭的博客，这里只做简单的学习记录。http://www.starming.com/ 


Auto Layout实际上并不是苹果搞出来的东西，而是1997年其它三名科学家发布论文中提到的`Cassowary`算法，此算法专门用来解决用户界面布局问题，它通过将布局问题抽象成线性等式和不等式约束来进行求解。



Auto Layout的生命周期： 在得到自己的layout之前Layout Engine会将Views，约束，Priorities（优先级），instrinsicContentSize（主要是UILabel,UIImageView等）通过计算转换成最终的效果。在Layout Engine里会有约束变化到Deferred Layout Pass再到应用Run Loop再回到约束变化这样的循环机制。

**约束变化：**设置新的约束，改变约束的优先级、添加删除视图都会触发约束的变化。当Layout管理器遇到约束变化后会重新计算layout，计算完毕后会调用`superview.setNeedsLayout()`, 以此来更新其所有的子视图。

**推断约束变化(Deferred Layout Pass)**：在这时候主要是做些容错处理，更新约束有些没有确定或者缺失布局声明的视图会在这里处理。接着从上到下调用layoutSubviews()来确定视图各个子视图的位置，这个过程实际上是把subview的frame从layout engine里拷贝出来。这里要注意重写layoutSubviews()或者执行类似layoutIfNeeded这样可能会立刻唤起layoutSubviews()的方法，如果要这样做需要注意手动处理的这个地方自己的子视图布局的树状关系是否合理。



tag:`Layout控制器`,`Auto Layout`