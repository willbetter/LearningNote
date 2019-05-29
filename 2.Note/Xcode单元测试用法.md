## 1.整体测试
`Command + U`, 或者`Xcode -> Product -> Test`可以运行整个单元测试代码.

一般一个测试文件包含如下几个部分:

```
class CZBNetworkModuleTests: XCTestCase {
    
    override func setUp() {
        super.setUp()
        // Put setup code here. This method is called before the invocation of each test method in the class.
        //这是一个用来进行初始化的方法，运行每一个测试用例之前，都会先运行这个方法，所以一般会把一些重复的代码放到这里来初始化。
    }
    
    override func tearDown() {
        // Put teardown code here. This method is called after the invocation of each test method in the class.
        //这个方法是每个测试用例运行完成后执行的，可以用来进行释放资源等操作
        super.tearDown()
    }
    
    func testExample() {
        // This is an example of a functional test case.
        // Use XCTAssert and related functions to verify your tests produce the correct results.
        //这是一个自动生成的测试用例方法，每一个测试用例都需要以test开头，后面可以跟上测试的具体内容，在每个test方法所在的行号旁边，都有一个菱形的图标，点击它就可以运行相应的测试用例。同样，在当前类上也有一个这样的图标，点击的话会运行该类里所有的测试用例。
    }
    
    func testPerformanceExample() {
        // This is an example of a performance test case.
        //也是一个测试用例，它内部通过一个 self.measureBlock 闭包来测试代码的性能。把要测试的代码写到这个闭包里，就可以得出这段代码的运行时间。
        self.measure {
            // Put the code you want to measure the time of here.
        }
    }
}
```







## 测试结果断言:

```
//1.生成一个失败的测试
XCTFail(<#T##message: String##String#>)
//2.为空判断,exp为空时通过,反之不通过
XCTAssertNil(<#T##expression: Any?##Any?#>, <#T##message: String##String#>)
//3.不为空判断,exp不为空时候通过
XCTAssertNotNil(<#T##expression: Any?##Any?#>, <#T##message: String##String#>)
//4.布尔判断,为true时候通过测试
XCTAssert(<#T##expression: Bool##Bool#>, <#T##message: String##String#>)
//5.布尔判断,为true时候通过测试
XCTAssertTrue(<#T##expression: Bool##Bool#>, <#T##message: String##String#>)
//6.布尔判断,为false时候通过测试
XCTAssertFalse(<#T##expression: Bool##Bool#>, <#T##message: String##String#>)
//7.判断相等
XCTAssertEqual(<#T##expression1: Equatable##Equatable#>, <#T##expression2: Equatable##Equatable#>, <#T##message: String##String#>)
//8.判断不等
XCTAssertNotEqual(<#T##expression1: Equatable##Equatable#>, <#T##expression2: Equatable##Equatable#>, <#T##message: String##String#>)
//9.发生异常时候通过测试,反之不通过测试
XCTAssertThrowsError(<#T##expression: T##T#>, <#T##message: String##String#>, <#T##errorHandler: (Error) -> Void##(Error) -> Void#>)
//10.没有异常时候通过测试
XCTAssertNoThrow(<#T##expression: T##T#>, <#T##message: String##String#>)
```
