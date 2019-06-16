>  问题描述: 在原生App中嵌入html网页, 网页h5需要微信支付宝支付. 按照h5对接微信支付宝的方案对接支付后, App端可以正常跳转到目标App, 但是支付完成或者取消后无法跳转回原App. 



# 1.支付宝支付的解决方案

支付宝的解决方案比较简单, 在`webView:decidePolicyForNavigationAction:`方法中拦截scheme为`alipay:`的请求, 转发出去之前, 将url中key为`fromAppUrlScheme`的值(默认是`alipays`)改为自己App中定义过的scheme即可. 在支付宝中支付完成后, 支付宝会调用这个scheme打开你的App, 你也可以添加自定义的参数来标记这个支付来自哪个订单等等.

代码大概如下所示:

```objective-c
+ (NSURL *)changeAlipayUrlScheme:(NSString *)lastUrlString {
    NSMutableString *newUrlStr = [[NSMutableString alloc]initWithString:lastUrlString];
    if ([lastUrlString containsString:@"fromAppUrlScheme"] && [lastUrlString containsString:@"alipays"]) {
        NSRange range = [newUrlStr rangeOfString:@"alipays"];
        [newUrlStr replaceCharactersInRange:range withString:[[DadaWebManager shared] platformScheme]];
    }
    NSURL *newUrl = [NSURL URLWithString:newUrlStr];
    return newUrl;
}
```

# 2. 微信支付的解决方案

微信支付的解决就没有那么简单了. 

经过不断查找,研究,测试发现, 微信会使用一个叫做`Referer`的参数来控制回掉的地址. 在h5支付过程中需要在webView中配置request的请求头.` [Referer:value]`. 我们可以将配置在App中的scheme设置为微信支付的Referer的value. 

这个scheme不能是任意的scheme, 必须是配置在微信开放平台支付功能以及域名域名的子域名. 比如我们配置在开放平台的域名是`http://weidongfang.com` , 那么配置的scheme必须是`xxx.weidongfang.com`. "xxx"可以是任意字符串, 但是后面的weidongfang.com一定要与你配置在开发开放平台的以及域名保持一致

代码如下所示

```objective-c
if ([navigationAction.request.URL.absoluteString hasPrefix: @"weixin"]) {
        if ([[UIApplication sharedApplication] canOpenURL:navigationAction.request.URL]) {
            [[UIApplication sharedApplication] openURL:navigationAction.request.URL];
            return false;
        }
    }

    NSMutableDictionary *requestHeaders = [navigationAction.request.allHTTPHeaderFields mutableCopy];
    if (requestHeaders[@"Referer"] == nil) {
        requestHeaders[@"Referer"] = [[DadaWebManager shared] platformScheme];
        [navigationAction.request setValue:requestHeaders forKey:@"allHTTPHeaderFields"];
    }
    return true;
```

# 3.总结

综上所述, `webView:decidePolicyForNavigationAction:`中, 处理h5微信支付正常回掉的代码大概应该是这样的:

```objective-c
+ (BOOL)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction {
    if ([navigationAction.request.URL.absoluteString hasPrefix: @"weixin"]) {
        if ([[UIApplication sharedApplication] canOpenURL:navigationAction.request.URL]) {
            [[UIApplication sharedApplication] openURL:navigationAction.request.URL];
            return false;
        }
    }
    if ([navigationAction.request.URL.absoluteString hasPrefix: @"alipay"]) {
        NSURL *url = [DadaWebManager changeAlipayUrlScheme: navigationAction.request.URL.absoluteString];
        if ([[UIApplication sharedApplication] canOpenURL:url]) {
            [[UIApplication sharedApplication] openURL:url];
            return false;
        }
    }

    NSMutableDictionary *requestHeaders = [navigationAction.request.allHTTPHeaderFields mutableCopy];
    if (requestHeaders[@"Referer"] == nil) {
        requestHeaders[@"Referer"] = [[DadaWebManager shared] platformScheme];
        [navigationAction.request setValue:requestHeaders forKey:@"allHTTPHeaderFields"];
    }
    return true;
}

+ (NSURL *)changeAlipayUrlScheme:(NSString *)lastUrlString {
    NSMutableString *newUrlStr = [[NSMutableString alloc]initWithString:lastUrlString];
    if ([lastUrlString containsString:@"fromAppUrlScheme"] && [lastUrlString containsString:@"alipays"]) {
        NSRange range = [newUrlStr rangeOfString:@"alipays"];
        [newUrlStr replaceCharactersInRange:range withString:[[DadaWebManager shared] platformScheme]];
    }
    NSURL *newUrl = [NSURL URLWithString:newUrlStr];
    return newUrl;
}
```











