wireshark - 网络抓包工具.



## 0.Mac功能集锦
### 0.1 屏幕共享
两个mac之间共享屏幕(远程控制), 只需搜索"屏幕共享", 打开相关的软件, 然后输入对方的appleid, 等待对方同意即可.

## 1.iTerm2(终端利器)
* **官网:**https://www.iterm2.com
* **安装:**可以直接去官网下载安装即可.
* **功能:**提供众多的快捷键,可以更加方便的使用终端,开发者不可多得的终端利器.
* **快捷键:**

cmd+w:关闭标签.
cmd+左箭头(右箭头):切换标签.
cmd+enter:切换全屏.
cmd+d:垂直分屏.
cmd+shift+d:水平分屏.
cmd+shift+h:查看剪切板历史.
ctrl+u:清楚当前行.
ctrl+l:清屏.
ctrl+a:到行首.
ctrl+e:到行尾.

* **配置代理:**

编辑`vim ~/.zshrc`,增加下面配置(使用的shadowsocks):
```
# proxy list
alias proxy='export all_proxy=socks5://127.0.0.1:1086'
alias unproxy='unset all_proxy'
```

iterm2需要新建标签页,才会有效果:

```
$ proxy		#打开代理
$ curl ip.cn
当前 IP：185.225.14.5 来自：美国

$ unproxy		#关闭代理
$ curl ip.cn
当前 IP：115.236.186.130 来自：浙江省杭州市 电信
```


## 2.Go2Shell
**官网**:https://zipzapmac.com/Go2Shell
**安装**:在官网直接下载安装即可.
**功能:**在finder中打开当前目录的终端装口,对于一个开发者来说是一个非常有用的App.

打开设置界面, 选择使用自己喜欢的终端盒打开方式, 点击安装到Findle即可. 

![](http://7xv47b.com1.z0.glb.clouddn.com/18-10-22/35190341.jpg)

*我第一次使用时候这样安装并不能成功,可以在应用程序文件夹直接拖动Go2Shell到Finder中即可*

![](http://7xv47b.com1.z0.glb.clouddn.com/18-10-22/18978490.jpg)

## 3.zsh



zsh自动建议填充zsh-autosuggestions; 语法高亮提醒zsh-syntax-highlighting; 
Manico试什么东西?


## 4.cloc代码统计工具
可以使用brew直接下载安装.

```
//1.统计当前项目的代码量
cloc ./

//2.排除目录
cloc ./ --exclude-dir=libs,cache

//3.排除扩展
cloc ./ --exclude-ext=css,js
```

## 5. Print Friendly & PDF
一款Chrome上保存网页为PDF的神器, 一个Chrome插件: [Print Friendly & PDF](https://chrome.google.com/webstore/detail/print-friendly-pdf/ohlencieiipommannpdfcmfdpjjmeolj?hl=zh-CN)

此插件的强大只处在于:

1. 对于计数博客文章, 自动抓取主文章内容区域, 自动忽略非文章区域;
2. 抓取后的页面可以预览, 对于多余的部分可以手动删除;
3. 可直接保存成PDF并下载或者电子邮件发送到指定邮箱;
4. 保存的PDF自动根据文章内容生成标题;

## 6. 爱思助手
1. 可用来检测iPhone设备各种配置参数等信息, 检测硬件是否原装/生产厂商等;
2. 一键越狱/刷机. 
3. 电视寿命以及循环情况.
4. 屏蔽iOS更新,制作铃声,抹除数据等.
5. 高清壁纸,铃声都还不错.

## 7. XtraFinder: 增强型的Finder.


## 8. Alfred: 便捷搜索,工作流.


## 9.破解百度网盘
突破百度网盘网速限制的方法. 好使.
https://github.com/Dev-Dongfang/BaiduNetdiskPlugin-macOS

## 10.typora-markdown编辑器
官网:https://www.typora.io 
一款可视化的Markdown编辑器, 笔记后马上可以看到效果, 支持不同主题的预览格式, 非常的实用.



