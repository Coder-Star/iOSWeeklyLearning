# iOS 摸鱼周报 #52 | 如何规划个人发展

![](https://cdn.zhangferry.com/Images/moyu_weekly_cover.jpeg)

### 本期概要

> * 话题：互联网行业不景气，个人该如何发展
> * 面试模块：load方法为什么耗时？
> * 优秀博客：扩展视野，了解其他领域的动态
> * 学习资料：Python 最佳实践指南
> * 开发工具：`Bartender`是一款很棒的菜单栏管理工具，有效解决当屏幕比较小时，顶部菜单栏显示不全的问题；

## 本期话题

### 互联网行业不景气，个人该如何发展

[@zhangferry](https://zhangferry.com)：本期话题来源于上周参加的亚东组织的一次分享会，分享者是做猎头的Firda，正常分享持续了3个小时且质量非常高，我简单总结下这次听讲的收获。

互联网行业多个大厂从股票暴跌到不断裁员，行业不景气为何有的人被迫转行？有的人却还能激流勇进？不同人的发展轨迹不同，其中很重要的一点就是他们做的选择不同，我们应该做的是：**做对的选择，正确努力**。

前半句是做选择，要做对的选择就需要拥有这些选择的足够信息，了解越多，越不容易犯错。我们需要做到以下几点，外部：

* 能分析外部环境市场&机会
* 能判断当下自己在公司内所处局势
* 能判断在什么时候可以看看机会

内部：

* 自知，较为清晰自己在市场中的能力水平

这里着重提一下内部因素这一点，找到市场中同工龄的人应具备哪些能力，应达到什么样的标准。这个可以通过跟领导的交流、招聘网站或者一些大牛的个人成长经历分享中获取。注意这些应该是相对优秀的人，然后以他们为标杆进行学习。我现在回想自己成长比较快的几个阶段，都是因为遇到了比自己优秀很多的同事，然后学习他们好的习惯。

另外一点是正确的努力，只努力不行，方向要正确。职场不同于学校，它要求的不是单一能力，而是综合能力。如何学习新知识、如何更好的协作、如何复盘、如果应对复杂的业务需求、如何构建自己的高效工作流、如何带团队、如何向上管理等等，在努力自我更新的同时也要注意这个更大的环境所发生的变化，结合自己的需求去往不同方向延展。以上每个点都有很大往里探索的空间，职场成长也应是个人成长，在努力做好工作的同时我们也在努力塑造一个更强大的自己。

回过头来，互联网不景气了，个人该如何发展呢？大环境基本无法控制，只能是从自身出发，自己如果做到无可替代，那就无惧外接环境变化。找准定位，正确的努力。

## 面试解析

整理编辑：[JY](https://juejin.cn/user/1574156380931144)

### load方法为什么耗时？

我们都知道启动优化的时候，减少`+load`方法能够减少启动时间。

如果`+load` 方法里的内容很简单，会影响启动时间么？比如这样的一个`+load `方法？

```C++
+ (void)load 
{
    printf("123");
}
```

这段代码编译完之后，这个函数会储存在`Mach-O`中的`TEXT`两个段中，`__text`存函数二进制，`cstring`存储字符串 123

要执行`printf`函数，首先需要访问`__text`触发一次`page In` 读入物理内存，为了要打印字符串，还需要访问`cstring`，还会触发一次`page In`

有很多同学不了解`page In`，这里介绍一下，首先先要知道`mmap`

`mmap` 的全称是 `memory map`，是一种内存映射技术，可以把文件映射到虚拟内存的地址空间里，这样就可以像直接操作内存那样来读写文件。

当读取虚拟内存，其对应的文件内容在物理内存中不存在的时候，会触发一个事件：` Page In`，把对应的文件内容读入物理内存中。

`Page In`又做了哪些事情呢？

* MMU找到空闲的物理内存页面
* 触发磁盘IO，把数据读入到物理内存
* 如果是TEXT段的页，要进行解密（iOS13之后不需要解密）
* 对解密后的页，进行签名验证

 为了执行这个函数，系统付出了两个`page In`的代价，所以一旦`load`方法过多，会影响启动速度


## 优秀博客

> 本期将介绍一些有特点的中文技术博客。虽然其中大多的内容与 iOS 或 Swift 关系不大，但对于开扩视野、了解其他领域的动态很有帮助。

整理编辑：[东坡肘子](https://www.fatbobman.com)

1、[漩涡的博客](https://xuanwo.io "漩涡的博客") -- 来自：Xuanwo

[@东坡肘子](https://www.fatbobman.com/)：作者是一名受雇于开源项目 datafuselabs 的全职开发者，工作中主要使用的是 Rust 语言。每周他都会介绍当周的项目进展，包括：技术分享、开源运营、开发感受等。从中可以对商业运营的开源项目的参与和运作有所了解。

2、[科技爱好者周刊](https://www.ruanyifeng.com/blog/ "科技爱好者周刊") -- 来自：阮一峰

[@东坡肘子](https://www.fatbobman.com/)：内容包罗万象，包含技术分享、软件推荐、科技动态、奇闻异事等等。最大的特点是读者的参与度高，留言踊跃。

3、[codedump的网络日志](https://www.codedump.info "codedump的网络日志") -- 来自：codedump

[@东坡肘子](https://www.fatbobman.com/)：作者对存储引擎、分布式开发、缓存服务等内容情有独钟，撰写了大量的相关文章。近期开始从 C 转向 Rust ，估计今后有关 Rust 的内容也会逐渐增多。

4、[体验碎周报](https://www.ftium4.com "体验碎周报") -- 来自：龙爪槐守望者

[@东坡肘子](https://www.fatbobman.com/)：作者是一个交互设计师，每周的博文都会汇总大量有关交互设计的文章、案例、动态、评论、分析等内容。即使你从事的工作与交互无关，从中也能有所收获。

5、[13 的 Apple 开发者周报](https://ethanhuang13.substack.com "13 的 Apple 开发者周报") -- 来自：Ethan Huang

[@东坡肘子](https://www.fatbobman.com/)：以 Twitter 上的信息汇总为主，内容包括：苹果官方消息、媒体报道、技术资源、趣事小梗等内容，并且每周还会制作一个由苹果开发者参与的 podcast。对了解对岸开发者的开发状态有一定的帮助。

## 见闻

> 这一周阅读/浏览到的有趣的资讯。

1、[你真的了解二维码吗？](https://mp.weixin.qq.com/s/Y4A8hPumwP_Vwdj3J0toBA) -- 来自公众号：中兴文档
[@远恒之义](https://github.com/eternaljust/)：如今二维码和我们的生活息息相关，扫码加好友扩大社交圈，线下扫微信和支付宝购物，出行查看行程码与健康码，这些都是最常见的使用场景。那么，有如此广泛用途的二维码你真的了解吗？文中详细的介绍了二维码的组成区域和工作原理，回答了二维码是否会被耗尽以及怎样才能安全地扫描二维码等相关问题。

2、[AI 绘画能代替真人么？我们和几位设计大咖聊了聊](https://mp.weixin.qq.com/s/nPH-FqlOZ7o93gSGouxXmQ) -- 来自公众号：设计青年实验室
[@远恒之义](https://github.com/eternaljust/)：如果你关注艺术和设计，那么最近也许会被一款外网爆火的 [Disco Diffusion](https://github.com/alembics/disco-diffusion "Disco Diffusion Github 地址") AI 刷屏。Disco Diffusion 是一款可以根据描述场景的关键词渲染出对应图像的 AI 图像生成程序（你说我画）。面对功能如此强大的 AI 绘画神器，作者非常好奇关于它背后的一些故事：Disco Diffusion 的创作的逻辑是什么？生成的画作版权归属怎么算？以及大家讨论度最高的话题，Disco Diffusion 的出现是否意味着人工绘画会被电脑代替？

3、[WebAssembly完全入门——了解wasm的前世今身](https://zhuanlan.zhihu.com/p/68048524 "WebAssembly完全入门——了解wasm的前世今身") -- 来自知乎：SH的全栈笔记

[@zhangferry](zhangferry.com)：我开始对 WebAssembly 一直不理解，直到阅读到这篇文章才恍然大悟。 看[官网的定义](https://webassembly.org/ "WebAssembly")：

> WebAssembly (abbreviated *Wasm*) is a binary instruction format for a stack-based virtual machine. Wasm is designed as a portable compilation target for programming languages, enabling deployment on the web for client and server applications.

我们再把WebAssembly 拆开看：Web + Assembly，Web 格式的汇编！对，这就是 WebAssembly 的精髓。它并不是高级的编程语言，而是一种类汇编的、可移植的、兼容 Web 的二进制格式（format。有时也把它成为语言，我感觉按格式来理解更清晰一些）。更进一步来说 WebAssembly 就类似是 Java 中的 Bytecode，iOS 中的 BitCode，它是一种中间码。

那为什么会出现这个东西了？它是用于解决什么问题的？

![](https://cdn.zhangferry.com/Images/20220427235534.png)

这个是 Mircsoft Edge 开源的 ChakraCore 的执行流程，可以看出对于 JavaScripts 这种解释性语言的执行，调用过程过程要进行解析和生成 Bytecode 的操作是比较耗时的。那能不能想编译型语言一样，直接给它一个“编译文件”，将编译任务提前做好，给到解释器呢？可以，这个工作就是 WebAssembly 诞生的目的。它不光是能节省 JavaScripts 的执行时间，还凭借其可移植性，可以将一些用 C/C++/Rust 等创建应用直接移植到Web端，而无需再搞一遍 JavaScripts实现。现在 AutoCAD、GoogleEarth、Unity 等一些较大型的项目都借助于 WebAssembly 移植到了 Web 端。

4、[Doodle Icons](https://khushmeen.com/icons.html "Doodle Icons")

[@zhangferry](zhangferry.com)：一款涂鸦风的图标网站，有 400+ 可用 ICON，除了ICON 还有一些涂鸦插画，个用和商用都免费。相对于标准规范的流水线图标，涂鸦风格能给人带来一种更加轻松欢快的感觉，在一些特定场合我们可以将图标换成这类风格试一下。

![](https://cdn.zhangferry.com/Images/20220428102206.png)

5、[星链 Starlink](https://satellitemap.space/ "星链 Starlink")

![](https://cdn.zhangferry.com/Images/20220428132650.png)

Space X 的星链用一种更直观的方式可以更好的感受它的存在，上图是官网给出的星链卫星分布图。可以看到星链已经覆盖了很多地方，图片左下角的数字区域表示，在 4 月 28 这一天，一共有 2336 颗卫星，正在使用的是 1730颗，损毁的有 211，其余是未激活状态。2300 多颗是什么概念，这已经达到我国历史发射卫星总量的 3 倍。

星链的目的是提供全球卫星互联网系统，19 年马斯克借助星链成功发送了 twitter，目前星链提供的能力已经覆盖仅30 个国家。

## 学习资料

整理编辑：[Mimosa](https://juejin.cn/user/1433418892590136)

### Python 最佳实践指南

**地址**：https://pythonguidecn.readthedocs.io/zh/latest/

这是一份关于 Python 的实践指南，该指南目前持续不断地更新与完善，在 Github 上有 5.8k 的 Stars。它旨在为 Python 初学者和专家提供一个关于 Python 安装、配置和日常使用的最佳实践手册。涵盖各种平台的 Python 安装、优秀的模块推荐、配合不同的 web 框架和工具、如何写出优雅的 Python 代码等内容。链接中的这份是该指南的中文版。

![](https://cdn.zhangferry.com/Images/52-python.png)

## 工具推荐

整理编辑：[CoderStar](https://mp.weixin.qq.com/mp/homepage?__biz=MzU4NjQ5NDYxNg==&hid=1&sn=659c56a4ceebb37b1824979522adbb15&scene=18)

### Bartender

**地址**：https://www.macbartender.com/

**软件状态**：免费

**软件介绍**：可以免费试用4周，购买费用 $15;

`Bartender`是一款很棒的菜单栏管理工具，有效解决当屏幕比较小时，顶部菜单栏显示不全的问题；

![Bartender](http://cdn.zhangferry.com/20220427102101.png)


## 关于我们

iOS 摸鱼周报，主要分享开发过程中遇到的经验教训、优质的博客、高质量的学习资料、实用的开发工具等。周报仓库在这里：https://github.com/zhangferry/iOSWeeklyLearning ，如果你有好的的内容推荐可以通过 issue 的方式进行提交。另外也可以申请成为我们的常驻编辑，一起维护这份周报。另可关注公众号：iOS成长之路，后台点击进群交流，联系我们，获取更多内容。

### 往期推荐

[iOS 摸鱼周报 #51 | 游戏版号恢复发放](https://mp.weixin.qq.com/s/ogjhELipiVFRaYJkT2NQwA)

[iOS 摸鱼周报 第五十期](https://mp.weixin.qq.com/s/6IS0RlytWxjeRHyh0f2fXA)

[iOS 摸鱼周报 第四十九期](https://mp.weixin.qq.com/s/6GvVh8_CJmsm1dp-CfIRvw)

[iOS摸鱼周报 第四十八期](https://mp.weixin.qq.com/s/br4DUrrtj9-VF-VXnTIcZw)

![](https://cdn.zhangferry.com/Images/WechatIMG384.jpeg)
