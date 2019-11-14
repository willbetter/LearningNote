iOS加固方式：

1. 代码混淆；
2. 防止动态ptrace注入；
3. 防止tweak注入；
4. 防止Reveal查看视图；
5. 破壳加固；
6. 判断签名；



## 判断应用是否被重签名

首先在Appstore下载或者自己编译的app，在.app里面都会存在`embedded.mobileprovision`这个文件，你可以使用vim工具查看此文件的内容：`vi Example.app/embedded.mobileprovision`。实际上里面使用xml格式存储应用的信息的。

每个证书都会哟一个Team ID，我们可以根据`Team Identifity`来判断你签名的证书和应用中的证书是否一致，如果不一致就代表被篡改了。

具体代码实现也是巨简单的，详情如下所示：

```swift
struct SignatureVerify {
    
    /// 从bundle中获取签名的TeamID
    ///
    /// - Returns: 返回获取到的签名TeamID
    static func teamIdentifity() -> String? {
        guard let path = Bundle.main.path(forResource: "embedded", ofType: "mobileprovision"),
            let content = try? String(contentsOfFile: path, encoding: .ascii) else {
                return nil
        }
        let pattern = #"<key>com.apple.developer.team-identifier</key>[\w\W]*?</string>"#
        let teamIdRegion = content.matchingResult(pattern: pattern)
        var teamID = teamIdRegion?.matchingResult(pattern: #"<string>.*?</string>"#)
        teamID?.removeLast(9)
        teamID?.removeFirst(8)
        return teamID
    }
}

fileprivate extension String {
    /// 返回正则匹配结果
    func matchingResult(pattern: String) -> String? {
        let regex = try? NSRegularExpression(pattern: pattern, options: [])
        let matchingRange = NSRange(location: 0, length: count)
        if let checkingResult = regex?.matches(in: self, options: [], range: matchingRange).first {
            return (self as NSString).substring(with: checkingResult.range)
        }else{
            return nil
        }
    }
}	
```



