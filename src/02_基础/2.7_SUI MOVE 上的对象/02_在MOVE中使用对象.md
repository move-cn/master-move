# 在MOVE中使用对象

最后，我们介绍了如何在 Move 中定义、创建 Sui 对象并获取其所有权。现在，我们需要学习如何使用这些对象。我们来详细了解一下。

在Move on Sui中，你只能调用你拥有的对象。要使用这些对象，您需要将它们作为参数传递给 `entry` 函数。现在，我们首先看一下将对象作为参数传递的两种不同方法：通过引用和通过值。

## 通过引用传递对象

有多种方法可以通过引用传递对象。

1. `&T` ：只读引用
2. `&mut T` ：可变引用

这里， `T` 代表我们通过引用传递的任何内容， `&` 符号用于告诉对象是通过引用传递的。让我们看看它们是如何工作的。

###  只读引用

只读引用仅允许您读取对象的数据。它不允许您更改或更新对象的数据。让我们举一个在上一课中使用的示例来阐明如何在代码中使用它。

```rust
module sum::sum {
		use sui::object;
		use sui::tx_context::TxContext;

		// Defining the SumObject
		struct SumObject has key {
		    id: UID,
		    number_1: u8,
		    number_2: u8,
		}

		// Constructor function used to initialize the SumObject
		fun new(num_1: u8, num_2: u8, ctx: &mut TxContext): SumObject {
				SumObject {
		        id: object::new(ctx),
		        number_1: num_1,
				    number_2: num_2,
		    }
		}

		// make_copy function that uses the values data of obj to create a new object
		public entry fun make_copy(obj: &SumObject, ctx: &mut TxContext) {
				let sum_obj = new(obj.num_1, obj.num_2, ctx);
				transfer::transfer(sum_obj, tx_context::sender(ctx))
		}
}
```

在这里，在 `make_copy` 函数中，我们以只读形式使用 `obj` 对象，并使用它来创建和更新新对象 `sum_obj`

###  可变引用

因此，可变引用与只读引用相反。可变引用允许您改变对象中的数据，并更改对象字段的数据。让我们看一个示例代码来了解可变引用的工作原理。

```rust
module sum::sum {	
		use sui::object;
		use sui::tx_context::TxContext;

		// Defining the SumObject
		struct SumObject has key {
		    id: UID,
		    number_1: u8,
		    number_2: u8,
		}

		// copy_into function uses the properties data of obj_1 (non-mutable)
		// to update the data of obj_2 (mutable)
		public entry fun copy_into(obj_1: &SumObject, obj_2: &mut SumObject, ctx: &mut TxContext) {
				obj_2.num_1 = obj_1.num_1;
				obj_2.num_2 = obj_1.num_2;
				transfer::transfer(obj_2, tx_context::sender(ctx))
		}
}
```

这里， `obj_1` 是一个只读对象， `obj_2` 是一个可变引用。我们正在使用 `obj_1` 的属性更新 `obj_2` 的数据属性。更新 `obj_2` 属性后，我们将使用 `the transfer` 模块更改 `obj_2` 的所有权。使用条目修饰符声明函数使得该函数可被事务调用。但我们必须确保交易的发送者必须同时拥有 `obj_1` 和 `obj_2` 。

## 按值传递对象

使用对象的第二种方法是将对象按值传递给 Move `entry` 函数。这使得对象不会存储在 Sui 存储中，代码可以决定对象应该存储在哪里。让我们看看 Move on Sui 中如何按值传递对象。

```rust
module sum::sum {	
		use sui::object;
		use sui::tx_context::TxContext;

		// Defining the SumObject
		struct SumObject has key {
		    id: UID,
		    number_1: u8,
		    number_2: u8,
		}

		// Passing the object by value
		public entry fun update_num_1(num_1: u8, object: SumObject): SumObject {
			let obj = object;
			obj.number_1 = num_1;
			obj
		}
}
```

在这里，我们按值传递 `object` ，创建一个新对象 `obj` ，更新 `number_1` 值，然后返回新对象。

按值传递对象的问题是，当我们传递对象时，它会在内存中创建该对象的一个新实例，并且在我们不自行删除它之前不会被删除。

正如您所知，每个 Move on Sui 对象都应该具有 `UID` ， `UID` 结构不具有 `drop` 功能，这使得 Move on Sui 通常不使用任何地方的 `drop` 能力。那么，如果我们想释放内存存储空间，该怎么办呢？为了处理按值传递对象，我们有两种方法。

1.  删除对象
2.  转移对象

让我们一一回顾一下。

###  删除对象

因此，如果您决定删除该对象，我们需要进行解包。您不能直接删除该对象。您可以做的就是解压对象的字段，将所有字段存储到新变量中，然后为它们分配垃圾值。让我们看看如何进行拆包。

```rust
let SumObject { id, number_1: _, number_2: _ } = object;
```

在这里，我们解压了该对象，并使用 `_` 使 `number_1` 和 `number_2` 值不含任何数据。由于我们无法释放 `id` 字段，因此我们需要一些额外的帮助。我们使用 `object::delete` 函数删除 `UID` 并释放空间。请记住，您只能删除定义该对象的模块内部的对象。现在让我们看一下完整的编码示例。

```rust
module sum::sum {	
		use sui::object;
		use sui::tx_context::TxContext;

		// Defining the SumObject
		struct SumObject has key {
		    id: UID,
		    number_1: u8,
		    number_2: u8,
		}

		// Deleting the object
		public entry fun delete(object: SumObject) {
				let ColorObject { id, red: _, green: _, blue: _ } = object;
        object::delete(id);
		}
}
```

注意：我们只删除按值传递的对象，而不删除按引用传递的对象，因为如果删除引用的对象，它将从全局存储中删除，这是我们无法承受的。但是，按值传递的对象是原始对象的新实例。因此，在我们按值使用对象后清除内存非常重要。

###  转移对象

处理该对象的另一种方法是将其转让给另一个所有者。因此，要传输对象，我们需要定义 `transfer` 函数。让我们看看如何在代码中定义它。

```rust
public entry fun transfer(object: SumObject, recipient: address) {
    transfer::transfer(object, recipient)
}
```

这里， `recipient` 代表我们想要将对象传输到的地址。请记住，您不能在 `entry` 函数之外调用 `transfer::transfer()` 。

## 小结

我为您能走到这一步并学习 Move on Sui 的所有基础知识而感到自豪。接下来，我们将进一步深入了解对象所有权的工作原理以及如何更改它。



## quiz

### 解释为什么删除过程仅限于定义对象的模块以及它与通过引用传递的对象有何不同