# Move on Sui 中的对象基础知识

我为您走到这一步并完成 Move on Sui 的所有基础知识感到非常自豪。由于您已经了解了这些对象以及如何声明和初始化它们，现在我们将从头开始详细学习它们，并进一步深入以更好地理解它们。

## 我们回想一下，Move on Sui 上的对象是什么？

简单来说，Move on Sui 中的对象就像一个保存数据的容器。

正如我们之前讨论的，要在 Move on Sui 中创建对象，您需要使用称为 `struct` 的东西来定义其结构。 `struct` 就像一个蓝图，描述了对象将保存哪些信息。例如， `struct Sum` 具有 `number_1` 和 `number_2` 值的字段。

```rust
struct Sum {
    number_1: u8,
    number_2: u8,
}
```

你发现这里的错误了吗？你是对的。这不是 Move on Sui 对象。为了使其成为 Move on Sui 对象，我们需要添加能力 `key` 和 ID，使用 `sui::object::UID` 为每个对象提供唯一的 ID，以帮助识别 Sui 网络上的对象。现在，让我们的 `Sum` 结构成为 Move on Sui 对象。

```rust
use sui::object::UID;

struct SumObject has key {
    id: UID,
    number_1: u8,
    number_2: u8,
}
```

还记得吗，我们需要在 Move on Sui 中将所有代码写在 `module` 内？您的任务是编写 `module` 内的所有代码并查看其外观。

## 在 Move on Sui 中创建对象

现在您知道如何在 Move on Sui 中定义对象了，但是如何初始化它呢？我们在之前的课程中也已经介绍过这一点，但让我们回想一下，因为记住有关这些物体的所有信息很重要，就好像我们的生活依赖于它们一样。

因此，要创建一个对象，我们必须使用我们在对象内部定义的值填充所有字段。为此，首先，我们需要为 `id` 分配一个值。由于 `UID` 定义了 `id` 的数据类型，因此我们需要用唯一的 ID 填充 `id` 。这就是 `sui::object` 和 `sui::tx_context` 模块派上用场的地方。 `sui::object` 帮助我们调用 `new()` 函数，该函数使用 `sui::tx_context` 给我们的值实例化新的 id。我们先看一个例子，然后我们会进一步理解这一点。

```move
module sum::sum {
		use sui::object;
		use sui::tx_context::TxContext;

		// Defining the SumObject
		public struct SumObject has key {
		    id: UID,
		    number_1: u8,
		    number_2: u8,
		}
		// Constructor function used to iniatlize the SumObject
		fun new(num_1: u8, num_2: u8, ctx: &mut TxContext): SumObject {
				SumObject {
		        id: object::new(ctx),
		        number_1: num_1,
				    number_2: num_2,
		    }
		}
}
```

您是否注意到 `TxContext` 在此示例中如何发挥作用？是的，您在前面的示例中也看到过这种语法，但是让我们再看一遍，因为这就像蛋糕中的面粉一样。没有面粉，我们就无法制作蛋糕。我们认为这个例子不好，但你的想法对吗？

因此， `TxContext` 为我们提供了交易信息， `&mut` 使其可变，然后我们将 `ctx` 传递给 `object::new()` 函数创建 `UID` 的新实例。

## 将对象存储在 Move on Sui 中

那么，您已经初始化了该对象，但是它存储在哪里呢？当您在构造函数内部初始化对象时，它会存储在本地，这意味着它可以从当前函数返回，作为参数传递给另一个函数，或存储在另一个结构内部。但是，如果我们想从另一个模块访问该对象或在全局级别（例如在资源管理器上）查看它，该怎么办？由于Sui中没有打印系统，我们需要找到一个解决方法。

因此，为此我们使用 `transfer` 模块。 `transfer` 模块帮助将对象添加到持久存储中。它具有有助于做到这一点的所有功能。以下是使用 `transfer` 模块的格式。

```rust
transfer::function_call(object, recipient)
```

这是使用 `transfer` 模块的最简单表示。我们在函数内部使用这一行，并将对象添加到全局存储中，其中接收者是对象的所有者，所有者可以是地址、另一个对象或共享对象。不用担心！我们将在接下来的课程中详细讨论对象所有权。

该模块的常见用途是将对象的所有权转移给调用合约的人，这里我们称之为当前交易的发送者或签名者，例如铸造一个你拥有的 NFT。那么，我们怎样才能获取发件人的信息呢？在 Move on Sui 中，获取信息的唯一方法是使用入口函数，该函数的最后一个参数是事务上下文 `ctx: &mut TxContext` ，它为我们提供了当前事务的所有信息。

因此，为了获取当前的交易签名者，我们可以这样调用： `tx_context::sender(ctx)` 。让我们通过以下示例来了解传输模块的完整工作原理。

```move
 module sum::sum {
		use sui::object;
		use sui::tx_context::TxContext;

		// Defining the SumObject
		public struct SumObject has key {
		    id: UID,
		    number_1: u8,
		    number_2: u8,
		}
		// Constructor function used to iniatlize the SumObject
		fun new(num_1: u8, num_2: u8, ctx: &mut TxContext): SumObject {
				SumObject {
		        id: object::new(ctx),
		        number_1: num_1,
				    number_2: num_2,
		    }
		}

		// create function is used to call the constrcutor function and 
		// transfer the ownership of the object and make it public using
		// `transfer` module and function.
		public entry fun create(red: u8, green: u8, blue: u8, ctx: &mut TxContext){
			let Color = new(red, gree, blue, ctx);
			transfer::transfer(color_object, tx_context::sender(ctx))
		}
}
```

在这里，我们使用 `the transfer::transfer(color_object, tx_context::sender(ctx))` 行将 `SumObject` 公开，并将其所有权分配并转移到 `sender` 地址。

您知道为什么我们在“Hello World”程序中使用这一行吗？

## 小结

您在本课程中已经介绍了对象的基础知识。现在，您已经非常了解如何在 Move 中声明和使用对象。

完成挑战将帮助您更快地掌握所学知识。请务必完成挑战，并在完成挑战后与我们分享代码的屏幕截图。在下一课中，我们将探索使用 Move on Sui 对象的更高级概念，因此首先练习基础知识非常重要。

##  quiz

所以，这是给你的一个小挑战。编写一个 `animal` 合约，其中包含 `AnimeObject` 以及 `name` 、 `no_of_legs` 和 `favorite_food` 属性。创建构造函数，然后将 `AnimeObject` 对象的所有权转移到 `sender` 地址。

创建合约后，尝试将其部署在 Sui 区块链上并探索会发生什么。