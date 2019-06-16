# 写在前面的
> 在iOS原生应用中嵌入的H5网页, 如果H5包含支付功能, webView需要对`alipay://`, `wexin://`两个scheme做系统级的转发, 原生应用是可以正常打开微信或者支付宝进行支付的, 但是支付完成之后并不能够正常的回掉到原生的App中. 

对用户来说, 在App中支付, 最终回掉到safria浏览器中的用户体验极差, 事实上, 我们可以通过一些小技巧来让支付宝和微信回掉到原生的App中. 

# 支付宝支付回掉问题
支付宝回掉问题处理起来相对简单. 如下代码所示是h5调起支付宝支付时候的参数, 根据参数名字`fromAppUrlScheme`我们猜测, 其值应该是用来控制回掉URI的. 

```
alipay://alipayclient/?{"requestType":"SafePay","fromAppUrlScheme":"alipays","dataString":"h5_route_token=\"RZ114oCHb6whkNX9aJ8yTIbiXeOPJOmobilecashierRZ11\"&is_h5_route=\"true\""}
```

我们将其值`alipays`改成我们自己的App中配置过的scheme, 发现可以正常回掉回原生App. 问题解决 ✌🏻.

# 微信支付回掉问题
有了解决支付宝支付回掉的经验, 我们使用相似的方法先拿到网页端发起微信支付的路由, 如下所示:

```
weixin://wap/pay?prepayid%3Dwx13184252063215ee7146ef601943283700&package=1054478582&noncestr=1560422573&sign=f4933e3c7d6a8464d928e54906f7f8fb
```
这里并不存在关于支付回掉相关的字段, 而支付完成后, 微信能够跳转到浏览器并打开我们配置在微信开放平台的支付结果网页. 因此猜测微信支付的回掉地址可能是微信从微信服务器自动获取的.

实际上解决问题过程中, 我们拿到了微信SDK支付跳转的URI, 其中包含了微信支付回掉相关的信息, 我们试图将相同的参数拼接到网页发起支付的URI上, 不管用; 我们试图将H5支付参数拼成SDK支付的方式调起微信, 同样不管用. 

最终我们从网上找到了一种解决方案(不记得从哪里看到了解决方案, 忘记了).

**解决**

H5微信支付中, 微信URI转发请求之前, 需要我们手动配置Request的请求头信息`Referer`, 我们可以在这里做文章. 原本Referer的值是我们配置在微信开放平台的支付url(也就是回掉到浏览器中的url), 我们只需要将Referer的值稍作修改即可.

假如我们配置在微信开放平台的支付url是`https://baidu.com/baidupay`, 那么我们可以将Referer的值修改为`xxx.baidu.com://`即可, `xxx`不限长度和内容, 可以是任意的内容, 可以是没有注册过的子域名; 然后我们需要在`Target>Info>URL Types`中配置相同的`URL Scheme`即可.(假如支付域名是`https://images.baidu.com/baidupay`, 那么Referer值应该是`xxx.images.baidu.com://`, 配置的Referer的值一定要是支付URl的值域名).

至此, 问题解决! ✌🏻













