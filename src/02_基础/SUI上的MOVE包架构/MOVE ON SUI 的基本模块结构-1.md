# MOVE ON SUI 的基本结构模块-1

学习如何设置 Move on Sui 包以及 Move on Sui 包的重要组件如何工作是一项伟大的工作。现在，在本课中，我们将探讨 Sui Move 中智能合约的结构。 Sui move 中完整的智能合约由哪些不同组件组成？

##  `module` 关键字

在 Move on Sui 中，我们使用 `module` 关键字声明智能合约，就像我们在 Solidity 中使用 `contract` 关键字声明智能合约一样。以下是声明智能合约的完整语法。

```ceylon
module package_name::module_name {
		// module code goes here
}
```

让我们了解一下 `package_name` 和 `module_name` 这里代表什么。

- `package_name` 是您在创建包时为包指定的名称，例如，在 sui move new hello_world 命令中，包名称为 `hello_world` 。
- `module_name` 是您在包中创建的模块的名称。一个包中可以有多个模块，这就是为什么显式提及模块名称很重要。

让我们看看它如何看待我们的“Hello World”示例。

```cpp
module hello_world::hello_world {
		// module code goes here
}
```

合约的所有内容都在 Move on Sui 中的 `module` 内。甚至是 import 语句，与 Solidity 编程语言不同。

##  `use` 关键字

在 Move on Sui 中， `use` 用于导入模块内的任何模块。这是 `use` 的完整结构。

```ruby
use <Address/Alias>::<ModuleName>;
```

让我们了解一下 `<Address/Alias>` 和 `<ModuleName>` 这里代表什么。

- `<Address/Alias>` 代表我们要在模块中使用的模块的地址。为了简单起见，我们还可以在此处使用别名而不是地址，并在 `Move.toml` 文件中提及别名，正如我们在上一课中讨论的那样。
- `<ModuleName>` 仅表示模块名称。例如，如果 `sui` 包中有一个我们想要在我们的模块中使用的 `transfer` 模块，那么我们将执行如下操作：

```arduino
use sui::transfer
```

### 一些重要的 Move on Sui 模块

让我们探讨一下我们在大多数智能合约中使用的一些重要的 Move on Sui 模块。

- ```
  use std::string;
  ```

  - 这个导入语句帮助我们在模块中使用字符串。

- ```
  use sui::transfer;
  ```

  - 该语句帮助我们编写将对象从一个帐户地址转移到另一个帐户地址的功能。

- ```
  use sui::object;
  ```

  - 此行有助于从 `sui` 框架导入 `object` 模块。该模块有助于在我们的模块中创建一个对象。

- ```
  use sui::tx_context;
  ```

  - 这有助于从 `sui` 框架导入 `tx_context` 。 `tx_context` 帮助我们识别交易的基本信息，例如发送者地址、签名者地址、 `tx` 的纪元号等。

Move on Sui 中还有多个其他模块用于不同的目的。目前来说，学习这些就足够了。

## 小结

我们讨论了帮助我们在 Move 中创建模块的两个基本重要关键字。其中 `module` 关键字在 Sui Move 中创建模块， `use` 关键字在导入任何模块中发挥作用。接下来，我们将探讨有助于在 Sui Move 中创建模块的更重要术语。

## quiz

### 在 Move on Sui 中，如何使用 module 关键字声明智能合约，声明中的 package_name 和 module_name 有何意义？