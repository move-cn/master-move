# 为什么创建Sui上的MOVE语言

让我们从了解 Sui 开始， Sui上的MOVE语言 和 Move语言 之间的区别，以及为什么 Sui上的MOVE语言 是区块链中的游戏规则改变者

## 让我们回想一下，Sui区块链

Sui 是一个去中心化的第 1 层区块链，它改变了我们在区块链中定义资产所有权的方式以及交易的处理方式。

![sui-icon.jpeg](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Why%20Move%20on%20Sui%20was%20Created/sui-icon.jpeg?raw=true)

Sui 在 Move on Sui 中引入了可组合对象。这些对象可以是单一拥有的，也可以是共享拥有的。对于单一所有者的简单交易，如转移代币或 NFT，Sui 放弃了共识。来自不同用户的这些交易是相互独立的，因此 Sui 以并行方式实现这些交易，称为并行交易。这意味着，每笔交易都可以在给定时间同时处理。这成倍地提高了 Sui 的可扩展性。好吧，我们将在课程后面讨论这个问题。

对于共享所有者对象，Sui 实现了一种称为 Delegated Proof of Stake 的共识机制。简单来说，这意味着当提交交易时，验证者投票决定是否批准交易。

## Move到底是什么？

您一定已经学习过“Sui Chain简介”课程中的Move on Sui。但是，让我们退后一步。

在 Move on Sui 构建之前，就已经存在 Move 编程语言，我们通常称之为 Core Move。Core Move是一种开源编程语言，主要用于构建和操作智能合约。The Move 支持跨不同区块链的通用库、工具和开发者社区。Move 也具有适应性，这意味着我们可以对其进行多项增强以执行不同的操作。而这正是Move on Sui正在做的事情。它使用核心 Move 并对其进行增强，以制作与 Sui 兼容的 Move。

## Sui上的MOVE

Move on Sui 与其他区块链上的核心 Move 或 Move 有一些重大区别。Move on Sui 受益于 Move 的灵活性和安全性，并在一定程度上增强了它，提高了吞吐量、减少了延迟，并使 Move 编程语言更平易近人、更适合使用。让我们来看看 Move on Sui 的一些独特功能。



## move的主要区别

以下是Sui  MOVE的主要区别：

1、Sui 有其以对象为中心的存储

2、Sui 中的地址表示对象 ID

3、Sui 中的每个对象在全局范围内都有唯一的 ID

4、Sui 有一个模块初始值设定项 （init）

5、Sui 具有通过引用将对象作为输入的入口点

让我们简要讨论一下这些差异。

### 以对象为中心的存储

Sui 链有自己的 Sui 存储，而不是全局存储。核心 Move 中的全局存储会导致可扩展性问题，Sui 链通过保留 Sui 链上的地址所拥有的对象和模块来解决这个问题。Sui 链中进行的所有交易都通过唯一标识符表示，以允许链并行运行交易。

### Sui 中的地址表示对象 ID

好吧，在 move 中，Sui 使用 32 字节的地址标识符来表示帐户地址。每笔交易都由账户地址（发送者）签名，以访问交易的所有信息。在 Move on Sui 中，账户地址存储在 id： UID 中，即对象中的必填字段。

### 具有关键能力的对象，全局唯一 ID

在 Sui 中，使用键能力对于将简单的结构体转换为 Sui 对象至关重要。具有键能力的对象必须具有 id： UID 字段，该字段存储对象的唯一地址。

### 模块初始值设定项

模块初始值设定项的工作方式类似于 Move on Sui 中的构造函数。正如我们所讨论的，如果您在 Sui 存储中发布模块，初始值设定项有助于初始化对象字段的初始值。初始值设定项在运行时执行，这意味着当您发布模块时，初始值设定项只会执行一次。

### 入口点通过引用将对象作为输入

在 Move on Sui 中，您可以将对象作为输入传递给公共函数，这些函数可由 Sui 事务调用。有多种方法可以做到这一点。您可以通过引用、可变引用或值传递它们。在这里，按值传递的对象可以被删除或转移给另一个所有者，而您可以更改通过可变引用传递的对象的数据，而无需更改所有权或创建任何新实例。

在 move 中，您还可以调用入口函数，即使它们是私有的，只要其他非入口函数没有使用它们的输入

不用担心！我们将在接下来的课程中详细讨论上述所有主题。

## 总结

 move在许多方面都是不同和独特的。它提供了许多独特的功能，帮助我们扩展应用程序。因此，与 Move on Sui 合作将帮助您部署可扩展的独特智能合约。我知道这似乎很多！但是我已经找到了你，我将在即将到来的课程中详细讨论所有新概念。现在，让我们继续前进，学习如何设置开发环境来运行 Move on Sui 程序，并学习如何在 Sui 上部署您的第一个程序。

## quiz

### 为什么要创建 Move on Sui，以及它与其他区块链上的 Core Move 或 Move 区分开来的一些关键功能是什么？
