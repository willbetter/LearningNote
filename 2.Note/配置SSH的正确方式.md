# 1. 生成密钥对

首先你需要用命令行生成密钥对.

```sh
#用以生成密钥对,你可以用以下命令去生成密钥对, 默认使用rsa加密算法, 之后会有一步一步的提醒, 按照提醒操作即可.
#如果你需要使用其它加密算法, 你需要使用`-t`参数指定你使用的加密算法, 例如:ssh-keygen -t dsa.
#ssh-keygen命令还可以配置很多其它参数, 详情可以通过`--help`查看
ssh-keygen
```

一般情况下, 使用Mac自带的ssh-keygen命令就能满足日常的需要, 但是如果你需要使用更多其它类型的加密算法, 你可以安装`openssl`, openssl支持更多的功能(openssl是基于ssl加密算法的开源具体实现).

```shell
#安装openssl
sudo apt-get install openssl
```

# 2. 配置`config`文件

如果你只需要连接一个github账号, 那么只需将`id_rsa`,`id_rsa.pub`放在.ssh目录即可, 程序会自动从目录下读取对应的公钥和私钥. 但是如果你使用了github, 又使用了gitlab, 那么你需要配置两对密钥对, 这时候你就需要一个`config`文件了.

## 2.1 同一台电脑配置多个密钥对

事实上, 在同一台电脑上是可以配置多个密钥对的, 只需创建一个config文件即可.

```shell
#如果.ssh目录中不存在config文件, 则创建config文件
touch config

#config中配置多个网址使用多个不同的私钥
#配置公司工作邮箱(***@***.com) 
Host git.czb365.com
HostName git.czb365.com
User git 
IdentityFile ~/.ssh/id_rsa

#个人新github账号(***@gmail.com)
Host github.com
HostName github.com
User git 
IdentityFile ~/.ssh/id_rsa__devgithub

#iphone6s
Host 10.0.21.23
HostName 10.0.21.23
User root 
IdentityFile ~/.ssh/id_iphone6s
```

## 2.2 同一台电脑配置两个github账号

有时候我们需要在一台电脑上使用两个github账号, 一个用来记录自己的项目, 用另一个记录笔记或者其他任何内容.



## 2.3 github常见操作

如果你电脑上安装了git工具, 那么git工具的全局配置文件存储在`~/.gitconfig`文件中, 你可以直接去查看你的全局配置. 另外你也可以通过`git config --list`命令查看你的全局git配置文件.

```shell
#查看全局git配置文件
git config --list
#配置全局的用户名和邮箱
git config --global user.name=[UserName]
git config --global user.email=[UserEmail]
#取消全局配置的用户名和邮箱
git config --global --unset user.name
git config --global --unset user.email
#为每个项目的repo指定自己的用户名和邮箱
git config  user.email "xxxx@xx.com"
git config  user.name "suzie"
```



# 其它

## .ssh下known_host文件的作用

当你使用ssh连接到新的服务器时候, ssh回让你确认服务器信息(域名,IP,公钥), 如果你确认了, 就会写到known_hosts中去. 当下次访问相同计算机时，OpenSSH会核对公钥。如果公钥不同，OpenSSH会发出警告， 避免你受到DNS Hijack之类的攻击。















