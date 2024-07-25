#  Sui MOVE 中的控制流

让我们继续学习如何在 SUI MOVE中使用不同的条件。在本课程中，我们将探讨 if-else 条件以及如何像其他编程语言一样使用它们。此外，我们将回顾如何在SUI MOVE中使用变量，这将帮助您理解 if-else 条件背后的逻辑。

值得注意的是，Move on Sui 避免使用 for-while 循环，因为它们可能导致非终止或无限执行，这违背了该语言确定性和可预测性的目标。相反，迭代逻辑是使用递归和模式匹配来实现的。让我们仔细看看。

##  `if` 表达式

`if` 表达式允许您在指定的某些条件成立的情况下运行代码块。如果条件为假，它将移动到另一个代码块。 `if` 是一个表达式，因此它应该以分号结尾。其语法如下：

```rust
if (<bool_expression>) {
	<expression> 
} else {
	<expression>
};
```

让我们看一个使用 `if` 表达式的实际示例：

```move
module examples::if {
	use sui::object::UID;
	use sui::tx_context::{Self, TxContext};

	// Declaring the ExampleObject
	public struct ExampleObject has key {
		id: UID,
		num: u8,
	}

	// Initializing the constructor
	fun main(ctx: &mut TxContext) {

		// Try switching to false
		let a = true;
		let b = if (a) {
			10
		} else {
			20
		};
		
		let obj = ExampleObject{
			id: object::new(ctx),
			num: b,
		}

	}
}
```

在上面的示例中，如果 `a` 为 `true` ，则变量 `b` 设置为 `10` 并且如果 `a` 是 `false` ，变量 `b` 设置为 `20` 。这是使用 `if` 表达式实现的。最后，我们将 `ExampleObject` 字段的 `num` 设置为 `b` 的值。

注意：if-else中的返回类型必须相同。否则，它将导致错误，并且变量 b 将可以选择为其他类型或未定义。

您还可以单独使用 `if` - 不带 `else` 条件的 `if` 。以下是如何在代码中使用它。

```move
module examples::if {
	use sui::object::UID;
	use sui::tx_context::{Self, TxContext};

	// Declaring the ExampleObject
	public struct ExampleObject has key {
		id: UID,
		num: u8,
	}

	// Initializing the main function
	fun main(ctx: &mut TxContext) {

		// Try switching to false
		let a = true;
		let b = if (a) {
			10
		};

		let obj = ExampleObject{
			id: object::new(ctx),
			num: b,
		}

    }
}
```

请注意，使用单独的 if 条件将 `false` 值分配给 `a` 可能会导致 `b` 未定义，这可能会导致代码出现问题。因此，建议不要使用单独的 `if` 条件。

## 使用内置的 `assert` 函数

`assert` 功能在 Move on Sui 中非常方便。当我们想要在不满足某个条件时中止程序时使用它。这是编写 `assert` 函数的基本结构。

```rust
assert!(condition, 0);
```

以下是如何在代码中使用它。

```rust
module examples::assert {
	fun main(a: bool) {

		assert!(a == true, 0);
		// code here will be executed if (a == true)
    }
}
```

程序将首先检查条件 `a == true` 是否满足，如果为 true，则其余条件将满足，如果不满足，程序将中止。

## Move on Sui 中的递归

因此，正如我们之前告诉过您的那样，Move on Sui 不使用 for-while 循环，而是使用递归函数。递归函数不允许您无限次运行程序。它必然满足一定的条件并结束程序。让我们看看如何在 Move on Sui 中计算数字的阶乘。

```move
module examples::assert {
	fun factorial(n: u64): u64 {
		if (n == 0) {
			return 1;
		} else {
			return n * factorial(n - 1);
		}
	}
}
```

该函数使用 if-else 语句来检查基本情况 `(n == 0)` 和递归情况 ( `n * factorial(n - 1)` )。这确保了该函数始终会终止并返回一个值。

## 小结

了解在 SUI move中处理不同的条件使您有机会满足编写智能合约所需的不同条件。它还允许您编写开发游戏等所需的多个数学条件。例如，在 Sui Move 上构建您自己的随机数生成器。接下来，我们将探索您可以在 Move on Sui 中执行的某些表达式和运算符。



## quiz

### 提供编码示例，说明 if 表达式、单独的 if 条件和内置断言函数的使用。