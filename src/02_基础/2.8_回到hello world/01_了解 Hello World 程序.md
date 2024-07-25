# 了解 Hello World 程序

我们已经运行了 Hello World 程序并与它进行了交互。现在我们已经完成了基础知识，我们可以深入了解它的完整代码。

##  完整代码

Hello World程序的完整代码为：

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

##  解释

让我们深入了解代码的逐行解释：

```rust
// Copyright (c) 2022, Sui Foundation
// SPDX-License-Identifier: Apache-2.0
/// A basic Hello World example for Sui Move, part of the Sui Move intro course:
/// https://github.com/sui-foundation/sui-move-intro-course
/// 
```

- 代码开头的注释表明其版权（Sui Foundation）和 SPDX-License-Identifier（指定 Apache-2.0 许可证）。
- `module hello_world::hello_world {` ：代码在 `hello_world` 命名空间内定义了一个名为 `hello_world` 的模块。

```rust
    use std::string;
    use sui::object::{Self, UID};
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};
```

- 该代码使用 `use` 关键字导入各种模块，包括：
  - `std::string` ：用于使用标准库中的字符串数据类型。
  - `sui::object::{Self, UID}` ：这会从 `sui::object` 模块导入“Self”和“UID”类型。
  - `sui::transfer` ：这会导入“transfer”模块，用于传输对象。
  - `sui::tx_context::{Self, TxContext}` ：从 `sui::tx_context` 模块导入“Self”和“TxContext”类型，用于处理事务上下文。

```rust
    struct HelloWorldObject has key, store {
        id: UID,
        /// A string contained in the object
        text: string::String
    }
```

- 在模块内，定义了一个名为 `HelloWorldObject` 的结构。该结构代表一个具有以下属性的对象：
  - `id` ：对象的唯一标识符（UID）。
  - `text` ：包含一些任意文本的字符串。

```rust
public entry fun mint(ctx: &mut TxContext) {
        let object = HelloWorldObject {
            id: object::new(ctx),
            text: string::utf8(b"Hello World!")
        };
        transfer::public_transfer(object, tx_context::sender(ctx));
    }
```

- 定义了一个名为 `mint` 的公共入口函数。该函数采用对 `TxContext` 的可变引用作为参数。
- 在 `mint` 函数内部：
  - 它创建一个新的 `HelloWorldObject` 对象。
  - 使用 `object::new` 函数初始化对象的 `id` 字段，生成新的唯一标识符。
  - 对象的 `text` 字段使用文本“Hello World!”进行初始化。使用 `string::utf8(b"Hello World!")` 。
  - 最后，它使用 `transfer::public_transfer` 函数将 `HelloWorldObject` 传输到发送者。这表明 `mint` 函数负责创建和传输该对象。

你一直做得很棒！我确信现在您可以轻松打印您想要的任何文本。那么为什么不尝试打印你的名字呢？构建和学习！

## 小结

综上所述，我们分析了 Sui Move 中的“Hello World”程序，包括模块导入、结构体创建和函数定义。现在，您已准备好修改代码并进行实验。请记住，实践是学习的关键。快乐编码！



## quiz

### 讨论 HelloWorldObject 结构中文本字段的初始化。字符串“Hello World!”是怎样的？表示，为什么使用 string::utf8 函数？