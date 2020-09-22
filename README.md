# README

## 关于本笔记

OverNote 系列笔记是我在平时一些学习时间阅读书籍博客、观看视频后整理出来的一套体系化的笔记，着重将一个整套知识体系由浅入深的记录下来，并做适当的、持续的扩展。

由于笔记刚刚开始整理，许多地方仍然没有细化，笔误依然很多，敬请期待不断校正后的正式版。正式版会以类似 gitbook 的形式开放，便于阅读。

为何是笔记形式，而不是独立博客形式？

```txt
因为我个人不是很喜欢在类似博客这样的平台上分享零碎的知识点，因为独立博客的内容目录极度不清晰，久而久之只能成为一个自己也不会问津的垃圾收集器。

我觉得知识点应该是一套循序渐进的整体，方便我日后随时查阅！像 OverNote 这样主次目录划分好，以后看到了好的内容就可以基于 Git 在原来的基础上进行增删改，可以不断的优化！
```

## 新时代前端的学习方向要点

**需要深入的技术点**：

- 三大框架：Angular/Vue/React 的深入理解，精通其原理、周边生态！
- TypeScript：优秀的 JS 超集，可以体验一下面向接口设计的理念，当然 TS 也会因为 vue3.0 的普及而持续上升份额。
- 小程序：小程序已经很多年了，不算新，Taro、Uni-app 等第三方多端框架都可以试试。
- NodeJS：笔者认为：无 Node 不前端，o(╯□╰)o。
- 计算机基础：所有开发者都逃不掉的三大件：算法、操作系统、计算机网络

**需要实践的技术点**：

- Nest.js：NodeJS 后端服务框架，拥有完善 IOC 机制，可以提升前端的架构视野，当然阿里的 midway 也不错，这 2 个框架也是直接使用 TS 书写，可以加强 TS 学习
- Flutter：笔者认为是具有全新意义的跨平台框架
- GraphQL：API 规范相关
- Electron：快速构建跨平台应用的利器
- 持续集成：docker，CI/CD，甚至可以了解 k8s、Serverless

**可以简单了解的技术点**：

- PWA：简单关注即可，其技术原理也极其简单
- WASM：目前火热的 rust、go 等语言已经支持，可以尝试
- IOT：物联网相关
- TensorFlow.js：AI 相关

## 资料

> 被列入其他书籍的部分，并不是这些书籍不够优秀，而是因为其内容或年代久远、或已有更好的替代品。笔者推荐每个技术尽量精读 1-3 本即可。

### 网页书籍 CSS

笔者推荐直接阅读新时期的一些著作：

- [《HTML & CSS 设计与构建网站》](https://book.douban.com/subject/21338365/)：HTML 与 CSS 零基础入门
- [《精通 CSS 第 3 版）》](https://book.douban.com/subject/30450258/)：CSS 全面了解
- [《CSS 权威指南（第四版）》](https://book.douban.com/subject/33398314/)：经典 CSS 书籍
- [《深入解析 CSS》](https://book.douban.com/subject/35021471/)：新近较好的 CSS 基础与进阶书籍
- [《CSS 揭秘》](https://book.douban.com/subject/26745943/)：重点书籍，CSS 书籍瑰宝！！！

其他优秀书籍：

```txt
《Head First HTML 与 CSS（第 2 版）》
《HTML5 与 CSS3 基础教程（第 8 版）》
《CSS设计指南》
《CSS 禅意花园》
《CSS 实战手册 第四版》
《CSS 选择器世界》
《CSS 世界》
```

### JavaScript

**语法基础**：

- [《Head First JavaScript程序设计》](https://book.douban.com/subject/27120520/)：适合零基础学习使用
- [《JavaScript 高级程序设计》第 4 版](https://book.douban.com/subject/35175321/)：外号红宝书，笔者认为是最好的 JS 入门书籍之一，目前已到第 4 版
- [《JavaScript 语言精髓与编程实践（第 3 版）》](https://book.douban.com/subject/35085910/)：国内出品的 JS 书籍大部头，用深入浅出来形容不为过，推荐观看。

其他：

```txt
《JavaScript 编程精解(第 3 版)：佳作《Eloquent JavaScript》，开源地址：http://eloquentjavascript.net/，但是该书定位为基础学习，但是很多内容与叙述风格不适合零基础学习，且翻译上并不十分优秀
《JavaScript 权威指南》第 6 版：    外号犀牛书，是一部 JS 的百科全书，过于庞大，有红宝书足矣，只适合作为工具书。
《JavaScript 语言精粹》修订版：外号蝴蝶书，是 JS 核心语法的荟萃，短小精悍。该书的优秀的部分是对 JavaScript 糟粕部分的总结，但是这些问题大多在 ES6 中得到的解决，在学习 ES6 时候就会自然明白
```

**DOM 编程**：

- [《JavaScript DOM 编程艺术》第 2 版](https://book.douban.com/subject/6038371/)
- [《锋利的 jQuery》第 2 版](https://book.douban.com/subject/10792216/)
- [《jQuery 技术内幕》](https://book.douban.com/subject/25823709/)
- [《HTML5 秘籍（第 2 版）》](https://book.douban.com/subject/26342322/)

**动画**：

- [《HTML5+JavaScript 动画基础》](https://book.douban.com/subject/24744218/)：目前最好的动画书籍
- [《HTML5 Canvas 核心技术》](https://book.douban.com/subject/24533314/)：目前最好的 canvas 书籍

**ES6**：

- [《深入理解 ECMAScript6》](https://book.douban.com/subject/27072230/)
- [《ECMAScript6 入门》第 3 版](https://book.douban.com/subject/27127030/)

**框架**：

- [《深入浅出 Vue.js》](https://book.douban.com/subject/32581281/)
- [《Vue.js 应用测试》](https://book.douban.com/subject/34998070/)
- [《深入 React 技术栈》](https://book.douban.com/subject/26918038/)
- [《深入浅出 React 和 Redux》](https://book.douban.com/subject/27033213/)
- [《React 状态管理与同构实战》](https://book.douban.com/subject/30290509/)

**JavaScript 提升**：

- [《JavaScript 忍者秘籍》第 2 版](https://book.douban.com/subject/30143702/)：对函数的讲解：闭包、重载、curry 化讲解极好
- [《JavaScript 核心技术开发解密》](https://book.douban.com/subject/30190189/)：短小精悍的介绍了一些难点
- [《你不知道 JavaScript》上卷+中卷](https://book.douban.com/subject/26351021/)：笔者认为目前最好的 JS 深入书籍之一
- [《Effective JavaScript》](https://book.douban.com/subject/25786138/)
- [《JavaScript 面向对象编程指南（第 2 版）》](https://book.douban.com/subject/26302623/)
- [《JavaScript 函数式编程指北》](https://github.com/MostlyAdequate/mostly-adequate-guide)：[中文开源地址为](https://github.com/llh911001/mostly-adequate-guide-chinese)

**综合提升**：

- [《Webpack 实战：入门、进阶与调优》](https://book.douban.com/subject/34430881/)
- [《现代前端技术解析》](https://book.douban.com/subject/27021790/)：前端技术与趋势总览
- [《高效前端：Web 高效编程与优化实践》](https://book.douban.com/subject/30170670/)
- [《高性能 JavaScript》](https://book.douban.com/subject/5362856/)
- [《Web 性能权威指南》](https://book.douban.com/subject/25856314/)
- [《了不起的 JavaScript 工程师：从前端到全端高级进阶》](https://book.douban.com/subject/34788884/)
- [《JavaScript 设计模式与开发实践》](https://book.douban.com/subject/26382780/)
- [《JavaScript 模式》](https://book.douban.com/subject/11506062/)
- [《编写可维护的 JavaScript》](https://book.douban.com/subject/21792530/)
- [《重构》第 2 版](https://book.douban.com/subject/30468597/)

### Node

- [overnote 笔记](https://github.com/overnote/over-javascript/tree/master/04-NodeJS)：入门书籍，笔者认为直接看本笔记即可
- [《Koa 与 Node.js 开发实战》](https://book.douban.com/subject/30404722/)：如果 Web 开发基础薄弱，可以观看此书，否则不需要
- [《深入浅出 Node.js》](https://book.douban.com/subject/25768396/)：Node 书籍的集大成者，虽然一部分内容已过时，但仍然值得精读
- [《Node.js:来一打 C++扩展》](https://book.douban.com/subject/30247892/)：窥探 Node 底层的好书

### 桌面开发

- [《Electron 跨平台开发实战》](https://book.douban.com/subject/34838092/)
- [《Electron 实战：入门、进阶与性能优化》](https://book.douban.com/subject/35069275/)

### WebGL

- [《WebGL 编程指南》](https://book.douban.com/subject/25909351/)
- [《Three.js 开发指南》](https://book.douban.com/subject/26349497/)

## 附

**OverNote**地址：<https://github.com/overnote>  
**笔者的地址**：<https://github.com/ruyuejun>

**OverNote 分类**：

- [Golang](https://github.com/overnote/over-golang)：详尽的 Go 领域笔记：Go 语法、Go 并发编程、GoWeb 编程、Go 微服务等
- [大前端](https://github.com/overnote/over-javascript)：包含 JavaScript、Node.js、vue/react、微信开发、Flutter 等大前端技术
- [数据结构与算法](https://github.com/overnote/over-algorithm)：以 C/Go 实现为主记录数据结构与算法的笔记
- [服务端架构](https://github.com/overnote/over-server)：分布式与微服务笔记，附 Nginx、Mysql、Redis 等常用服务端技术
- [Python 与机器学习](https://github.com/overnote/over-python)：Python 相关笔记，完善中
- [Linux](https://github.com/overnote/over-linux)：计算机组成原理、操作系统、计算机网络、编译原理基础学科笔记
- [大数据](https://github.com/overnote/over-bigdata)：大数据笔记，完善中
- [Flutter](https://github.com/overnote/over-flutter)：完善中
