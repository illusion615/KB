
# 概述

学习USD的过程中，我发现很多技术大拿的文章，都没有从对象层次和结构上来概述USD是以一种什么样的方式来组织其功能的，而是直接jump in到细节中，这对于像我这样上了年纪却还妄图混技术圈的人而言理解起来就稍显吃力，多花了很多功夫。
所以，关于USD的第二篇文章，我想补上这一篇文章。这一章的目的是让大家能对USD的结构有一个概要的了解，知道  

**“如果你要做……那应该配置……并且按照……的顺序”**。

打开USD的配置其实就可以知道USD中的若干数据对象概念，例如：

* Hosted controls
* Events
* Action call
* Toolbars
* Windows navigation rules
* Entity seaches
* Session lines
* Agent scripts
* Scriptlets

这些对象通过各种错综复杂的关系构成了USD的几乎所有功能。其实我一直觉得如果能有谁做一个所见即所得的配置工具那肯定可以加速USD的价值推广速度。不过我不会做这个事情，因为听说研发团队已经在做了。造轮子的事情交给高收入的人就好。
所以我希望看到在不远的将来开发者将不再需要阅读此类文章而可以使用一种更直觉化的方式来快速上手USD，如果这成为现实，那对USD而言将是一件幸事。

# 什么是USD？

在上篇文章发布之后，有人给我差评，说还以为这篇文章讲的是美元投资指南。

# 核心概念

USD设计的初衷是希望可以实现一种类似面向对象设计的方式来快速实现应用。所以了解这一点就比较容易为这些对象找到参照系。
如果用专业开发者所熟知的OOD来类比，USD的核心对象同样可以分为三个：

## Hosted control

USD的核心，可以类比到面向对象方法中的object（这段话是我从MSDN上抄的，不过那一页就这句话有用，我觉的）

## Action call

可以类比到面向对象方法中的Method（方法）

```text
有坑请注意
action在这里有两层概念，一个是UII action，一个是Action call。
```

**UII action**
是Hosted control的核心，代表了Hosted control可用的方法。但是这些方法自己不会执行，而只能被Action call调用。

**Action call**
是用来调用Hosted control的UII action的，他是独立于Hosted control存在的，其中描述了对不同的Hosted control中的UII action应该以何种方式调用。我们可以理解为对Action call包含的是业务逻辑。例如下图示例了当来电时通过向Account这个Hosted control传递何种信息来调用Open_CRM_Page这个UII action。

## Events

就是面向对象方法中的Evnet（事件）
事件类型是和不同hosted control类型绑定的。例如下图的事件类型就是Hosted control类型为CRM page时具备的事件列表。
而Agent scripting类型的Hosted control所拥有的事件是不同的。
通过绑定Events和Action call，就可以在特定事件触发时执行Action call的调用操作。
parameters就是面向对象方法中的Properties（属性），不过与OOD不同的是parameters是独立于object之外的，也就是说parameters更类似与一个全局的统一属性存储机制，这样跨hosted control的action call都可以访问这些parameters。在debug里面就可以看到它，而且在后面debug的过程中这里会被经常用到且异常有用。

注意：前面带$的代表系统变量，以区别于用户变量。

所以一切都清楚了，Hosted control在特定event时触发action call来执行特定的操作。这就是USD设计的核心概念。

下面以一些具体场景来举例：
场景#1：我希望在登录之后缺省显示“我的工作”
在对象“CRM Global Manager”事件“DesktopReady”调用“Load Dashboard”