简单翻译一下 Svelte 3 作者Rich Harris 发布时的blog
原文链接 https://www.sveltejs.cn/blog/svelte-3-rethinking-reactivity

## Svelte 3: 重新思考响应式(Rethinking reactivity)
######  它终于来了
***
[Rich Harris](https://twitter.com/Rich_Harris) MON APR 22 2019


在离开了几个月之后，我们即将发布 Svelte3 稳定版。这是一个重要的发布，凝聚了 Svelte 社区的众多开发者成百上千小时的付出和努力，包括来自beta测试者们的及其珍贵的反馈，在完成最终版本的道路上的每一步都提供了帮助。

我们相信你会喜欢上 Svelte3 。

### Svelte 是什么？

Svelte 是一个组件框架，类似于React、Vue，但是有一个重要的区别。传统框架允许开发者编写 *声明式* 状态驱动代码，但是这样有一个坏处，浏览器必须去做额外的工作来将响应式结构转换成DOM操作，例如使用[virtual DOM diffing](https://www.sveltejs.cn/blog/virtual-dom-is-pure-overhead)技术，增加框架的内存占用和浏览器垃圾回收的负担。

与之相反的是，Svelte 运行在构建阶段，将组件转换成如同外科手术般更新DOM的高效命令式代码。这样的好处是，我们可以编写更复杂且性能表现更出色的应用。

第一版的Svelte是聚焦于验证一个假设 —— 一个专门的构建编译器可以生成更安全可靠的代码，带来更好的用户体验。第二版是一个把项目整理清除的小升级。

版本3是一次意义重大的改造。在过去的五六个月里，我们专注于如何提供一个出色的开发者体验。现在已经实现比在其他任何框架少的代码去编写组件。尝试我们的[新教程](https://www.sveltejs.cn/tutorial/basics)就能发现我们所言非虚，如果你熟悉其他框架，我们认为会让你感到惊喜。

为了实现这一点，我们首先要重新思考现代UI框架的核心概念 —— 响应式(reactivity)

### 把响应式结合到开发语言中

在旧版本的Sevlte中，你需要调用 ` this.set ` 方法将state的变化传递给程序（原文为computer）：
```javascript
const { count } = this.get();
this.set({
  count: count + 1
});
```
这样就可以实现组件响应式更新。说到这里，` this.set ` 几乎是和React类组件（在hooks之前）中的` this.setState` 一样：
```javascript
const { count } = this.state;
this.setState({
  count: count + 1
});
```
这两者是有一些关键的技术实现的差异（如同我在上面的视频中说到，React并不是响应式的），不过从概念上讲它是一样的。

但是随着[hooks](https://reactjs.org/docs/hooks-intro.html)以一种完全不同的方式处理state的出现，一切都发生了改变。许多框架都开始尝试实现自己的hooks，但是我们很快得出了结论——hooks不是我们追寻的方向。hooks有一些有趣的特性，但是仍然是需要添加一些不自然的代码（unnatural code）和增加了垃圾收集器额外的工作，这对于一个用于嵌入式设备以及重度动画交互的框架并不好。

因此我们后退一步同时对自己发问——什么样的API对我们是有用的。最终我们意识到最好的API就是完全不使用API（额外的）。我们仅仅使用编程语言本身。更新` count ` 的值以及所有依赖于它的东西应该像下面这么简单：

```javascript
 count += 1;
```

因为我们是一个编译器，我们可以在幕后通过检测赋值做到这一点

```javascript
count += 1; $$invalidate('count', count);
```

重要的是，我们可以在没有代理或者访问器的开销和复杂性的情况下实现这一切。

### 全新面貌

你的组件不是唯一进行整容手术的部分，Svelte本身也有一个崭新的面孔和感觉，感谢 Achim Vedam 令人赞叹的设计工作，他为我们设计了全新的logo和网站，现在网站已经迁移到了[svelte.dev](https://svelte.dev/)。

同事，我们将宣传语从 **'The magical disappearing UI framework'** 更改为 **'Cybernetically enhanced web apps'** 。Svelte有很多优秀的特性——出色的性能表现，更小的打包体积，可访问性，内置样式封装（built-in style encapsulation），声明式转换，易用性，实际上还是个编译器等等——甚至将注意力集中到其中任何一项上都是对其他项的不公平。**'Cybernetically enhanced'**旨在表明Svelte最重要的设计哲学——我们的工具应当作为我们自己的一个智能拓展，希望是一个复古的、William Gibson-esque风格（赛博风格）。

### 从 Svelte 2 升级

如果你是一名 Svelte 2 用户，这其中会涉及到一些需要手动升级的部分。在接下来的几天里，我们会发布一个迁移指引和一个尽可能提供自动化迁移过程的 [svelte-upgrade](https://svelte.dev/) 的更新版本，但是这是一个重大而版本更新，并非所有的内容都能被自动化处理。

我们并没有轻视这个问题：希望一旦你使用过一次 Svelte 3 ，你就会明白为什么我们觉得与过去彻底决裂是必要的。

### 仍将继续

尽管这次发布历尽艰难险阻，但是我们还没有到达终点，我们还有大量的构思去构建更智能、更健壮的代码，以及一份非常长的待实现的特性清单。[Sapper](https://sapper.svelte.dev/)是我们的 Next.js 风格的应用框架，目前仍在使用 Svelte 3 更新的途中。[Svelte Native](https://svelte-native.technology/) 社区项目允许开发者使用 Svelte 构建 Android 和 iOS 应用，正在取得稳定坚实的进展，但是仍需要核心团队的更全面的支持。我们至今还没有其他框架所具备的编辑器拓展，语法高亮，组件库，开发者工具等等，我们需要时间解决这个问题。我们迫切想要增加一流的 TypeScript 支持。

但与此同时，我们认为 Svelte 3 是目前构建Web应用的最佳实践方式。花费一小时浏览我们的[教程](https://www.sveltejs.cn/tutorial/basics)，我们希望能够说明你认同这个说法。无论哪种方式，我们非常乐意在我们的 [Discord chatroom](https://discord.gg/yy75DKs) 或者 [GitHub](https://github.com/sveltejs/svelte)上看到您——欢迎所有人，特别是您！
