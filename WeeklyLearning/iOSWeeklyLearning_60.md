# iOS 摸鱼周报 #60 | 2022 Apple 高校优惠活动开启

![](https://cdn.zhangferry.com/Images/moyu_weekly_cover.jpeg)

### 本期概要

> * 本期话题：2022 Apple 高校优惠活动开启
> * 本周学习：Swift 5.7 中的 opaque parameter 和 primary associated types
> * 内容推荐： WWDC22 内参推荐 
> * 摸一下鱼：推荐收集到的各种图标网站
> * 岗位推荐：上海 Espressif 招聘全栈工程师

## Apple 资讯

### [2022 Apple 高校优惠活动开启](https://www.apple.com.cn/cn-edu/shop/back-to-school "2022 Apple 高校优惠活动开启")

[@远恒之义](https://github.com/eternaljust)：炎热的暑假来袭，Apple 为你“降降温”。一年一度的 Apple 高校优惠活动正式开启，为你的高校生活购买 Mac 或 iPad，不仅省钱，还送 AirPods，更有 Apple Pencil 和智能键盘优惠、八折加购 AppleCare+ 服务计划及更多惊喜等着你。此次优惠活动面向在读及新录取的高校学生，以及各级教师和教职员工，选购前需要[通过 UNiDAYS 资格验证](https://www.myunidays.uk/CN/zh-CN/partners/appleeducationstore/spotlight/online "通过 UNiDAYS 资格验证")。活动限制查看 [2022 中国大陆高校优惠活动的条款和条件](https://www.apple.com.cn/cn-edu/shop/open/back_to_school/terms_conditions "2022 中国大陆高校优惠活动的条款和条件")。

## 本周学习

整理编辑：[Hello World](https://juejin.cn/user/2999123453164605/posts)

### Swift 5.7 中的 opaque parameter 和 primary associated types

熟悉 Swift 的读者都知道，如果你将存在关联类型或者 `Self` 的协议当做类型使用，编译器会报错 `Protocol 'X' can only be used as a generic constraint because it has Self or associated type requirements.`。表示该协议只能用作泛型协议。

Swift 5.1 为了解决这个问题引入了不透明返回类型的概念，即在函数返回值的位置使用 `some` 修饰协议，整体作为一个类型使用。这也是支持 SwiftUI 的核心特性之一。现在 Swift 5.7 扩展了这一功能。

####  opaque parameter

现在 `some` 关键字不仅可以用在函数返回值位置，也支持用来修饰函数参数。表示的含义和修饰返回值类型时是一致的。示例如下：

```swift
class BookRender {
    ...
    func bookArticles(_ articles: [Article]) {
        ...
    }
}
```

`BookRender` 是一个渲染文章的对象，`bookArticles `接收一个文章数组来渲染。上文代码中入参仅支持数组类型，如果我们想同时支持 `Array` 和` Set`类型，Swift 5.7 之前我们一般使用泛型来处理：

```swift
func bookArticlesGeneric<T: Collection>(_ articles: T) where T.Element == Article {}
```

通过泛型来约束入参为集合类型，这样写是没有问题的，但更简洁的编写方式在 Swift 5.7 中出现了，我们可以使用 `some` 修饰参数入参从而实现将 `Collection`协议用做类型约束的目的。如下：

```swift
func bookArticlesOpaque(_ articles: some Collection) {}
```

这样编写的代码同样支持入参为集合类型 `Array` 和 `Set`。Swift 5.7 允许我们使用 `some` 修饰存在关联类型或者 `Self`的协议直接当做参数类型使用，而不仅限于不透明返回类型。这一特性可称为不透明参数。更详细可以参考 [SE-0341](https://github.com/apple/swift-evolution/blob/main/proposals/0341-opaque-parameters.md "SE-0341")。

对比以上泛型和 `some` 两种实现方式可以发现，不透明参数写法暂时还不能完全等价于泛型的方式。原因在于泛型函数不仅限制了入参类型为 `Collection` 集合类型，同时限制了元素 `Element` 类型为 `Article` 。而 `some`仅仅是限制了 `Collection`集合类型，对于元素类型却没有限制。这其实是不完整的功能替换，所以 Swift 5.7 中又新增了另一项特性来解决该问题。就是接下来的 **primary associated types**。

#### primary associated types

[SE-0346](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fapple%2Fswift-evolution%2Fblob%2Fmain%2Fproposals%2F0346-light-weight-same-type-syntax.md "SE-0346") 中引入了更简洁的语法来实现特定场景下指明协议关联类型的需求。该特性是对泛型协议能力的扩展。继续上文的示例，如果我们仍然想用 `some`替代泛型，同时保留指明 `Collection` 元素类型的需求。那么我们不得不在 `Collection` 协议本身上下功夫。

```swift
func bookArticlesOpaque(_ articles: some Collection) where Collection.Element == Article {} // Error
```

我们没有办法使用类似上面代码中的 `where`来约束关联类型，因为这里的 `Collection` 代表的仍然是协议而非是具体类型。所以我们的实际需求转为了 “需要在使用协议时，有一种途径可以指明约束的关联类型”。这就是 **primary associated types**。

Swift 5.7 中 `Collection` 的定义由 `public protocol Collection : Sequence {}` 变为了 `public protocol Collection<Element> : Sequence {}`，注意对比，这里多出的 `<Element>`实际就是所谓的 primary associated types。它即像协议又类似泛型的语法。

之所以叫做 **primary**，是因为并不是所有的关联类型都应该在这里声明。相反。应该只列出最关心的那些关联类型，剩余的关联类型仍然由编译器推断决定。

在使用该协议时，可以直接通过类似泛型的语法来指明该关联类型的具体类型。例如我们上面的例子：

```swift
func bookArticlesOpaque(_ articles: some Collection<Article>) {}
```

此时通过 `some` 实现的 `bookArticlesOpaque` 才和泛型的函数 `bookArticlesGeneric`完全等价。

Swift 标准库的部分协议已经改写为 **primary associated types**，同样这一特性也支持我们自定义的协议，语法是相同的。

> 另外相关联的特性还包括泛型和 `some`、`any`之间的实现异同。以及如何取舍的问题。

* [What’s new in Swift 5.7](https://www.hackingwithswift.com/articles/249/whats-new-in-swift-5-7  "What’s new in Swift 5.7")
* [What are primary associated types in Swift 5.7?](https://www.donnywals.com/what-are-primary-associated-types-in-swift-5-7/  "What are primary associated types in Swift 5.7?")

## 内容推荐

> 对于国内的 iOSer 来说，WWDC 内参是一个较好的了解 WWDC 新内容途径，目前超过三成的文章已经出炉，今年的内参质量更上一层楼。今年我们部分摸鱼编辑参与其中，你可以查看[WWDC22内参参与体验](https://mp.weixin.qq.com/s/1x6JaHxb-bT3NtDb56BHDw)，今天还有机会获得邀请码呦。

1、[使用 LLDB 调试 Swift](https://xiaozhuanlan.com/topic/4809126537 "使用 LLDB 调试 Swift") -- 来自：WWDC22内参

[@夏天](https://juejin.cn/user/3298190611456638)：使用 LLDB 调试 Swift 代码时，有时候有点力不从心，有时候找不到源码，有时候指令失效等。文章介绍了几个解决方案来解决命令失效的问题。文章最后还介绍了如何正确的为打包 Framework 设置参数，确保 LLDB 能够正常运行。

2、[Safari 和 WebKit 新特性介绍](https://xiaozhuanlan.com/topic/1560743928 "Safari 和 WebKit 新特性介绍") -- 来自：WWDC22内参

[@夏天](https://juejin.cn/user/3298190611456638)：苹果关于 Safari 和 WebKit 的更新，可能是其为了追平某些功能在各个浏览器上相同或相似的体验。文章介绍了一些前端的概念，以及几个有助于前端开发的网站 [Can I use](https://caniuse.com/) 和 [MDN](https://developer.mozilla.org/en-US/)。

3、[在 SwiftUI 中组合各种自定义布局](https://xiaozhuanlan.com/topic/1507368249 "在 SwiftUI 中组合各种自定义布局") -- 来自：WWDC22内参

[@夏天](https://juejin.cn/user/3298190611456638)：Grid 是一个十分强大的网格排版工具，极大地改善了 SwiftUI 的版式控制能力。除了 Grid， 文章还介绍了 `ViewThatFits` 以及使用 `AnyLayout` 在不同的布局类型之间平滑地过渡。

4、[快速链接：优化构建和启动耗时](https://xiaozhuanlan.com/topic/1509638472 "快速链接：优化构建和启动耗时") -- 来自：WWDC22内参

[@夏天](https://juejin.cn/user/3298190611456638)：一篇关于实现更快构建和优化 APP 提交和缩短启动耗时的文章。文章介绍了静态链接和动态链接相关的概念，并引申其原理内容。构建和启动相关的知识，一直是 iOS 开发中较为深奥的一部分，文章介绍的内容无论你是学习还是准备面试，都有一定的作用。

5、[Swift 新特性介绍](https://xiaozhuanlan.com/topic/2498765013 "Swift 新特性介绍") -- 来自：WWDC22内参

[@夏天](https://juejin.cn/user/3298190611456638)：是一篇让你快速了解今 Swift 更新内容的文章，为你提供了最近几年 Swift 发展的概览，介绍了今年更新的内容。如果你需要对今年 Swift 更新内容有一个了解，不失为一种途径。

7、[探索 In-App Purchase 集成和迁移](https://xiaozhuanlan.com/topic/8024563197 "探索 In-App Purchase 集成和迁移") -- 来自：WWDC22内参

[@夏天](https://juejin.cn/user/3298190611456638)：IAP 可能是部分国内开发者上架 App Store 的一种`阻碍`。去年 Apple 对 IAP 大拆大建，今年也新增了部分功能。如果你近期有关于 IAP 相关的内容，可以回顾最近两年的内参，对你有不小的帮助。

6、[Swift 编程语言](https://www.cnswift.org/ "Swift 编程语言")--来自：cnswift

[@Hello World](https://juejin.cn/user/2999123453164605/posts)：比 SwiftGG `更快` 的 Swift 中文版本。

## 摸一下鱼

整理编辑：[CoderStar](https://mp.weixin.qq.com/mp/homepage?__biz=MzU4NjQ5NDYxNg==&hid=1&sn=659c56a4ceebb37b1824979522adbb15&scene=18)

![](https://cdn.zhangferry.com/Images/20220714233011.png)

- [iconpark](https://iconpark.oceanengine.com/home "iconpark")：字节出品的一款图标网站；
- [iconfont](https://www.iconfont.cn/ "iconfont")：估计这个大家都知道，就不介绍了；
- [icons8](https://icons8.com/animated-icons "icons8")：Icons8 推出的动态图标网站；
- [openmoji](https://openmoji.dashgame.com/#/ "openmoji")：面向设计师、开发人员和其他人的开源表情符号！

自己做`Side Project`的时候用的上哦！

## 岗位推荐

公司名：Espressif（乐鑫科技）

城市：上海

Job Description & Summary: 

A career within ESPRESSIF Digital Solutions team, will provide you with the opportunity to digitally transform the company by building elegant web/mobile systems backed by latest technologies. As a member of our team, the candidate will join talented professionals with diverse backgrounds and skills. 

Skills Required

· Bachelor's degree in Computer Science, Information Technology or related area
· 2-6 years of hands-on experience developing full-stack software applications
· Experienced with relational databases (MySQL, MSSQL) with the ability of database design and optimization
· Strong expertise in Java/Scala/Python programming language with Spring/Play/Django framework experience
· Deep understanding of front-end technologies such as JavaScript, CSS, HTML5, React or Vue etc.

Skills Desired

· Understanding of CI/CD pipeline and related tools such as Git, Jenkins, Docker, Kubernetes
· Experienced with any NoSQL databases e.g. MongoDB and distributed cache e.g. Redis 
· Experience with ELK (ElasticSearch, LogStash, Kibana) stack
· Experience with Azure or AWS cloud
· Fluent oral English communication

联系方式：简历发送到邮箱：fanbaoying@espressif.com 或加微信：fzhanfei（备注：摸鱼周报）。

## 关于我们

iOS 摸鱼周报，主要分享开发过程中遇到的经验教训、优质的博客、高质量的学习资料、实用的开发工具等。周报仓库在这里：https://github.com/zhangferry/iOSWeeklyLearning ，如果你有好的的内容推荐可以通过 issue 的方式进行提交。另外也可以申请成为我们的常驻编辑，一起维护这份周报。另可关注公众号：iOS成长之路，后台点击进群交流，联系我们，获取更多内容。

### 往期推荐

[iOS 摸鱼周报 #59 | DevOps 再理解 ](https://mp.weixin.qq.com/s/LJNCo0Eg11shGZN75-TZcg)

[iOS 摸鱼周报 #58 | 极客风听歌网站，纯文字音乐播放器](https://mp.weixin.qq.com/s/KwqFraJk40f9bEy0eKa8Kw)

[iOS 摸鱼周报 #57 | 周报改版，WWDC22 讲座集锦](https://mp.weixin.qq.com/s/e4ZbFBPqclgy7KyfxVyQZA)

[iOS 摸鱼周报 #56 | WWDC 进行时](https://mp.weixin.qq.com/s/5chb-a9u7VMdLis1FG6B6Q)

![](https://cdn.zhangferry.com/Images/WechatIMG384.jpeg)
