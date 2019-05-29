**Chrome快捷键：**
cmd+L: 光标移动到地址栏并选中所有的地址；
cmd+R：刷新页面；
cmd+T：打开一个新的Tab；


**Google搜索引擎**
* google搜索引擎对英文大小写不敏感,god, GOD在google中的搜索结果是相同的;
* 作为一个整体查询时，最好全他们加上引号。如“电子商务”。如果直接输入电子商务就会自动分为电子和商务两个。


**google搜索命令:**
1. site命令: 指定搜索某个网站的内容,如:swift site:stackoverflow.com.      ([应用名字] site:itunes.apple.com 通过这种方法,准确的在应用商店搜索到了应用)
2. filetype命令: 指定搜搜某个格式的文件,如:swift filetype:doc
3. 双引号: 表示完全匹配, 使关键词不分开, 顺序都不改变,如:"swift closure"
4. -命令: 减号与前一个关键词之间要有一个空格,与后一个关键词之间一定不能有空格.匹配结果是,匹配前一个关键词但不包含后一个关键词.如:swift -closure
5.and(+)命令: 逻辑与, 这个命令我们一直在用,只是没有意识到,一般用空格代替
6.intitle命令: 在搜索结果的title中包含关键词,一次只能搜索一个关键词.如:frend intitle:love
7.inurl命令: 指在搜索结果的url中包含关键词,一次只能包含一个关键词.如:frend inurl:savage
8.intext命令:在结果的正文中包含关键词.如:friend intext:love
9.allintitle命令:在结果的标题中包含多个关键词.如:frend allintitle:love dead
10.allinurl命令:在结果的url中包含多个关键词.如:frend allinurl:savage dead
11.allintext命令:在结果的正文中包含多个关键词,排他性指定,如:friend allintext:love right
12.define命令:查询关键词的词义,起到词典的作用.如:define:痴人说梦
13.weather命令:查询某一个地区或者城市的天气.如:weather:beijing
14.\*命令:通配符,可以匹配任何字符串(笑口\*开的结果可能包含笑口常开,也包含笑口巨开的的页面),如:笑口\*开
15.".."命令:表示一个数值范围:如:手机2000..3000元
16."|"逻辑或命令:A|B,结果中要么只包含A,要么只包含B,不可能包含A和B两个.如:friend|love
17.查询这个url链接的页面,使用-site之后结果更可靠,因为这样扫除了本身链接到本身的情况.如:linkdomain:github.com -site:github.com