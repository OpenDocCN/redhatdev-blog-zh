# 改进你的代码:没有调试器的禁闭的故事

> 原文：<https://developers.redhat.com/blog/2020/07/07/improve-your-code-tales-from-confinement-without-a-debugger>

我一直对那些不用调试器就能凑合的开发人员印象深刻，并且常常希望我能更像他们。我依稀记得一位同事说他从未使用过调试器，偏爱`printf`胜过`gdb`。同样，在我初出茅庐的那几年，我清楚地记得和一个使用`objdump`的朋友一起追踪一个内核 bug，以及一个更老的内核的来源:

> “理查德，你至少应该使用正确的来源吧？”
> “咩...他们离得够近了。”
> 

我仍然印象深刻。

现在我编码已经有几年了，我注意到我已经养成了一些坏习惯。过度依赖调试器就是其中之一。我经常把它当作一个强大的拐杖，经常让我陷入堆栈跟踪，很少停下来思考问题。我迷失在 20 层递归中，不知道为什么一个不相关的变量被逗乐了。

当然，调试器有很多好的用途，但是我的好用途比例只有 40%。我的使用通常开始是良性的，但后来退化成恶性抽象。因此，在我的第 20 个 GNU 编译器集合(GCC)黑客周年纪念日，我决定给自己一个月没有调试器的挑战。故事是这样的。

## 我是如何来到这里的

当我刚开始黑 GCC 的时候，Red Hat 的新手被安排在旧的工具链支持岗位上。十有八九，那些漏洞已经在上游被修复了。我非常擅长运行两个并行的`gdb`,单步执行直到我发现代码中的差异，并最终找到修复 bug 的补丁。我的技术是有效的，但是对于我正在“修复”的潜在问题，我学到的很少。

现在，我是一个头发花白的老顽固，我已经数不清我在垃圾收集器上放了多少个断点来找出是谁创建了一块内存，只是为了节省分析给定优化的 *where* 的时间。我甚至有一个尴尬的宏来做这件事:

```
define who_created_me
break ggc-page.c:2683 if result == $arg0

```

(是的，对于不同的 GCC 版本，我有不同的宏。那条源线不可避免地会改变。)

我在本地树中也有一系列的`strcmp`变量，用于比较 GNU 编译器集合内部(GIMPLE)、汇编输出和其他 GCC 内部状态。我经常被看到为特定的模式设置断点，因为我非常努力地避免深入的工作。这里有一个例子:

```
(gdb) break some_function if !strcmp_gimple(stmt, "var_3 = foo ();")

```

不幸的是，这些很酷的派对小把戏诱使我相信他们是省时者。事实上，它们很快就退化为无处可去的洞穴探险。通常，我探险的根本原因是我不理解代码。

## 没有调试器的生活

最初，有很多“废话，如果我不能解决这个问题呢？”如果我的整个*编程都依赖于调试器怎么办？如果这是需要调试器的 *one* bug，而我只能在每周状态报告中沮丧地报告，那该怎么办？然而，在最初的恐慌之后，我进入了一种节奏。一旦发生这种情况，我发现我的思维过程和编码实践有了显著的改进:*

*   我注意到我的很多代码都是为了调试体验而优化的，而不是为了可读性或可维护性。我避免在一行中放一个以上的语句，而是把事情分开，以便更容易设置断点。这样做有时会产生更干净的代码，但并不总是如此。
*   我想是 Kernighan 说的，"[调试比一开始写程序难两倍](https://www.defprogramming.com/quotes-by/brian-w-kernighan/)"我从一开始就面对这个冷酷无情的事实，并意识到浏览意大利面条式的代码是多么省时。然而，我也很快注意到，我在具体问题上花费了这么多时间，因为代码很糟糕(在一个共享项目中，这不完全是我的错)。结果，在我努力少调试多修复的过程中，我的功能变得更小，更不相互依赖，也更容易理解。我也很擅长在编译器中重构别人的代码，让事情更容易理解。总体来说，双赢！
*   我明智地在整个代码中添加了更多的断言，以便更早地发现问题。
*   我的转储变得更具可读性。令人惊讶的是，GCC 中有多少转储往好了说是令人困惑，往坏了说是毫无用处。是的，我贡献了我那份不可读的转储。
*   我保存了导航堆栈帧和设置断点的击键，而不是编写精心制作的`printf` s，我确保它们会保存到转储文件中。这样做为后续的“调试”会话节省了我大量的时间。
*   我两次食言，把调试器拿出来，都是因为太累了，没把问题想透。我希望随机输入`up`、`down`和`print`能神奇地解决我的问题。这两次，我需要的是休息(没有双关语)和第二天的新开始。

## 经验教训

为了自我展示，我应该说我不会放弃使用调试器。它们是我武器库中的伟大工具。在出现问题的第一个迹象时，我可能更不愿意本能地拿出一个调试器，但是有很好的理由使用它。在我无拘无束的日子里，我特别怀念调试器的这些用途:

*   调试器有助于事后分析进程死亡的原因。在检查崩溃时，上下堆栈跟踪和检查变量是非常宝贵的。
*   没有什么真正的方法可以替代`gdb`-检查一个挂起的进程，看看它在哪里卡住了。上个月我不得不调试其中的一些，我不希望任何人遭受这种痛苦。
*   我在添加一堆只是模拟一个`gdb`会话的`printf`上划了一条线。如果你要通过代码中的`printf`重新发明一个调试器，看在克苏鲁的份上，请使用现有的工具。
*   当你没有机会重构别人的意大利面时，调试器可能是一种快速提高速度的方法——只是不要过度使用它。在某些时候，你应该坐下来理解大局。

## 结论

在没有调试器的一个月里，我学到了很多东西。我的代码变得更小，更容易理解，我也更善于坐下来从全局的角度进行分析。对没有调试器而停滞不前的恐惧让我首先引入了更少的 bug。我更多地考虑了我写的东西，减少了对单步分析算法的依赖。总的来说，这是一次很好的经历。

如果你花更多的时间关注森林，而不是树上的小蚂蚁，你就更有可能永远解决这个问题。调试器，按照设计，帮助你查看树上的蚂蚁。不要误会我；微焦点是有用的。但这不应该是你分析问题的主要方式。过去的一个月很可怕，但这是值得的。

*Last updated: July 1, 2020*