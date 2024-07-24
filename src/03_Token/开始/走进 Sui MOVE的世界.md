# 走进 Sui MOVE的世界

大家好，我们很高兴您完成了 Sui 学习路径的前两门课程。在本课中，我们将对 Sui 和 Move 进行一些修改。我们知道您想立即开始编码，但是修改一些概念将帮助您轻松地编写代码。

## 我们来复习一下，SUI中对象的重要性

对象就像任何其他编程语言中的结构一样。每个对象都可以存储任何数据，无论是整数、布尔值还是地址。它们在有效存储大量数据方面发挥着重要作用。每个对象都有所有权，标识谁拥有该对象。所有权告诉我们对象的交易将如何进行。在 Sui 中，有两种类型的对象：

1.  单一所有者对象
2.  共享所有者对象

以下是在  Sui MOVE 中定义对象的方法。

```rust
struct ExampleObject has key {
		 id: UID,
		 data_1: u8,
		 data_2: u8,
}
```

注意：对象定义不定义对象的所有权。我们在初始化和转移所有权过程中定义它。

## 回想一下，SUI MOVE中如何写合约

我们在上一课程中详细介绍了 Move 合约的编写。让我们回顾一下“Hello World”示例代码。

```rust
// Copyright (c) 2022, Sui Foundation
// SPDX-License-Identifier: Apache-2.0

/// A basic Hello World example for Sui Move, part of the Sui Move intro course:
/// https://github.com/sui-foundation/sui-move-intro-course
/// 
module hello_world::hello_world {

    use std::string;
    use sui::object::{Self, UID};
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};

    /// An object that contains an arbitrary string
    struct HelloWorldObject has key, store {
        id: UID,
        /// A string contained in the object
        text: string::String
    }

    public entry fun mint(ctx: &mut TxContext) {
        let object = HelloWorldObject {
            id: object::new(ctx),
            text: string::utf8(b"Hello World!")
        };
        transfer::public_transfer(object, tx_context::sender(ctx));
    }

}
```

让我们花点时间回顾一下这份合约所完成的工作。为了清晰起见，我们将总结要点。

 **模块定义**

您可以使用 `module` 关键字定义合约。以下是您在上面的程序中定义它的方式。

```rust
module hello_world::hello_world
```

 **导入库**

定义模块后，我们导入在 Move on Sui 中创建合约所需的重要库，就像我们在 `hello_world` 合约中所做的那样。

```rust
use std::string;
use sui::object::{Self, UID};
use sui::transfer;
use sui::tx_context::{Self, TxContext};
```

 **定义一个对象**

没有对象，SUI的合约是不完整的。它在 Sui 中定义了具有 `key` 能力和 `id` 的对象。其他能力的出现可以使物体更好地工作。以下是我们在 `hello_world` 合约中定义它的方式。

```rust
/// An object that contains an arbitrary string
struct HelloWorldObject has key, store {
    id: UID,
    /// A string contained in the object
    text: string::String
}
```

**定义 `entry` 函数**

如果您想创建对象并将其转移给所有者，那么使用 `entry` 关键字定义函数非常重要。这是 `hello_world` 合约中的 mint 函数的样子。

```rust
public entry fun mint(ctx: &mut TxContext) {
    let object = HelloWorldObject {
        id: object::new(ctx),
        text: string::utf8(b"Hello World!")
    };
    transfer::public_transfer(object, tx_context::sender(ctx));
}
```

这里的 `mint` 函数调用 `transfer::public_transfer()` 方法并将 `HelloWorldObject` 铸造到 Sui 全局存储中。

## 小结

好了，伙计们，Sui 和 Move 就到此结束了！您还记得如何在 Move on Sui 中定义简单合约。在编写代币合约时，您将复制您的知识并在 Move on Sui 中创建您自己的代币。

在下一课中，您将设置编写自己的合同的环境。在继续之前不要忘记完成任务！

## quiz

### Move on Sui 中如何定义合约？