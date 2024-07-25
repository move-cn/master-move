# Move on Sui 的基本模块结构-2

现在您已经了解了 Sui 模块结构的主要两个组件，让我们更深入地了解哪些其他东西可以构成完整且安全的 Sui 模块。

##  move对象

在本课程中，我们将向您快速介绍在 Sui 上MOVE对象，并且我们将在接下来的课程中进一步探讨它们。在 Move on Sui 中，对象携带有关模块的重要信息，例如您的帐户地址、钱包中的当前余额或您想要转账的金额。

您可以使用 `struct` 关键字创建对象，但情况有所不同。与 Go 等其他编程语言不同，您只需使用 `struct` 来声明一个 Move 对象，而在 Move on Sui 中，您不能简单地使用 `struct` 关键字来创建 Move 对象。必须将其与 `key` 配对才能使其成为 move 对象。这是结构的样子。不用担心！我们将在接下来的课程中详细讨论 `key` 的含义。

```rust
struct StructName has key {
		// struct contents goes here
}
```

对于 Move on Sui 中的每个 `module` 和 `struct` - 拥有一个有助于识别创建该对象的帐户地址的 ID 非常重要。以下是您可以如何做到这一点。

```rust
use sui::object::UID;

struct StructName has key {
    id: UID,
    // other struct contents go here
}
```

不用担心！在介绍基础知识后，我们将在接下来的课程中详细讨论对象。现在我们只关注一个简单的 `module` 结构。

## Sui 上的move 构造函数（模块初始化器）

在 Move on Sui 中，构造函数在模块发布时被调用。这意味着构造函数只被调用一次。因此，构造函数帮助我们做一些我们只想做一次的事情，例如，定义安全性并确保只有模块的创建者可以更改或拥有对象。构造函数还有助于初始化对象并确保它们拥有正确的帐户地址。它具有以下属性。

1. 函数名称必须是 `init` 。
2. 它必须具有以下类型的最后一个参数之一。
   1. `&TxContext`
   2. `&mut TxContext`
3. 它不能有任何返回值。
4. 它可以具有可选参数，例如任何对象或任何类型的变量。

我们来看看它的基本编码结构。

```rust
fun init(optional_parameter: anyDataType, ctx: &mut TxContext) {	
    // init code goes here
}
```

让我们看一个示例代码以更好地理解 `init` 。

```move
module examples::init {
    use sui::transfer;
    use sui::object::{Self, UID};
    use sui::tx_context::{Self, TxContext};

    // Defining the ExampleObject
    public struct ExampleObject has key {
        id: UID
    }

    // init will be only called once at the time of publishing the module
    // Here, we are restricting the object so that only the owner can own 
    // the object
    fun init(ctx: &mut TxContext) {	
        transfer::transfer(ExampleObject {
            id: object::new(ctx),
        }, tx_context::sender(ctx))
    }
}
```

这里， `init` 采用一个参数 ( `ctx: &mut TxContext` ) 并使用 `transfer::transfer()` 将对象的所有权转移到发送者地址。不用担心，如果您还不太理解，我们将在接下来的课程中详细研究传输操作和对象所有权。

注意： `init` 在 `module` 中不是必需的，当您需要返回任何内容时，您也可以将函数定义为构造函数的形式。

## 完整Move on Sui合约的基本结构

因此，我们已经介绍了 Move on Sui 合约的基本组成部分。这些并不是有助于在 Move on Sui 中构建合约的全部内容，但它是理解我们构建合约所需的基本概念的良好开端。完整的结构如下所示。

```move
module package_name::module_name {
		use sui::object::UID;

		public struct StructName has key {
		    id: UID,
		    // struct contents goes here
		}

		fun init() {
		     // constructor code goes here
		}

		// module code goes here

}
```

## 模块创建完整合约所需的其他基本内容

与任何其他编程语言一样，我们需要学习如何编写、创建和使用以下不同的编码方面：

1.  变量
2.  控制流程
3. 表达式和运算
4.  功能
5. 高级对象概念
6.  和许多其他

在接下来的课程中，我们将详细介绍所有这些概念。

## 小结

现在，您知道 Move on Sui 中合约的基本结构是什么样的了。接下来，我们将介绍 Move on Sui 的基础知识，从变量、数据类型、控制流以及我们可以在 Move on Sui 中执行的不同操作开始



## quiz

### 在 Move on Sui 中，如何创建对象，这个过程中 struct 关键字与 key 关键字配对的意义是什么？