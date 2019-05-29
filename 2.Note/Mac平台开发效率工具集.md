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





