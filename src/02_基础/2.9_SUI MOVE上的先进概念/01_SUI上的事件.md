# SUI上的事件

嘿，学习者，我想向您鞠躬，因为相信我，如果您到目前为止已经仔细学习了所有课程，那么您已经掌握了大量关于如何使用 Move on Sui 进行编码的知识，这太棒了！现在，在本课程中，我们将了解一些可以使用 Move on Sui 执行的高级操作，所以让我们开始吧。

## 为什么需要在SUI事件？

好的，所以每当您与 Sui 区块链上的合约进行交互时，都会有大量的数据流入和流出，并且由于 Move on Sui 是一种以对象为中心的语言，因此 Sui 对象充当与 Sui 通信的媒介区块链，但是如果你想监控数据的流入和流出怎么办？这就是事件发挥作用的地方，您可以根据智能合约发出事件所需满足的某些条件从智能合约发出事件。

例如，您有一个 NFT 合约，其中包含以下函数：

- 总供应量是多少？
- NFT 的代币 ID 是什么？
- 现在，如果您想在有人铸造 NFT 时收到通知该怎么办？

为了解决这个问题，您可以使用事件来发出某些参数，例如铸造 NFT 的代币 ID、带来 NFT 的用户的地址等。让我快速引导您了解如何在 Sui 智能合约中实现事件

注意：Sui 上的事件也表示为 Sui 对象。

## 我们如何使用 Move on Sui 发出事件？

在开始编写代码部分之前，我先带您了解一下 Sui 中事件对象中的不同属性：

- `id` ：包含交易摘要 ID 和事件序列的 JSON 对象。
- `packageId` ：发出事件的包的对象 ID。
- `transactionModule` ：执行事务的模块。
- `sender` ：触发事件的 Sui 网络地址。
- `type` ：发出的事件类型。
- `parsedJson` ：描述事件的 JSON 对象。
- `timestampMs` ：Unix 纪元时间戳（以毫秒为单位）。

我认为理论部分已经足够了，让我们继续使用代码片段来理解这个概念

因此，要使用 Move on Sui 创建事件，第一步是导入。

```rust
use sui::event;
```

让我用代码来解释事件，首先，让我们创建我们的 `Hero` 对象。

```move
module example::events {
	use sui::object::{UID};

 //*Object representing our hero*
	public struct Hero has key{
		id: UID,
		power: u8,
		}
}
```

现在让我们为我们的英雄创造一把剑。

```move
module example::events {
	use sui::object::{UID};
 
	//*Object representing our hero*
	struct Hero has key, store{
		id: UID,
		power: u8,
	}
	 
	//*Object representing our hero's sword*
	struct Sword has key {
	id: UID
	}

}
```

现在我们就来创造一个买剑的乐趣吧。目前，为了保持示例简单，我不会使用英雄。

```move
module example::events {
	use sui::object::{UID};
	use sui::tx_context::{Self, TxContext};
	use sui::transfer;
 
	//*Object representing our hero*
	struct Hero has key, store{
		id: UID,
		power: u8,
	}
	 
	//*Object representing our hero's sword*
	struct Sword has key {
	id: UID
	}

	//function to buy a sword, if the user owns a hero
	pub fun buy_sword (hero: Hero, ctx: &mut TxContext){
		let sword = Sword {
		id: object::new(ctx),
		};
		
	//Transfer the sword to the caller of this function
		transfer::transfer(sword, tx_context::sender(ctx));

	} 

}
```

现在，如果我希望智能合约在有人购买剑时通知我怎么办？为此，我们将使用事件，让我引导您完成它们。

```move
module example::events {
	use sui::object::{UID};
	use sui::tx_context::{Self, TxContext};
	use sui::transfer;
 
	// Object representing our hero
	public struct Hero has key, store{
		id: UID,
		power: u8,
	}
	 
	// Object representing our hero's sword
	public struct Sword has key {
		id: UID
	}
	
	// Object representing an event
	public struct buySwordEvent has copy, drop {
	  owner: address,
	}

	// Function to buy a sword, if the user owns a hero
	pub fun buy_sword (hero: Hero, ctx: &mut TxContext){
		let sword = Sword {
		id: object::new(ctx),
		};
		
	// Emitting an event with the address of the user who brought the sword
	event::emit(buySwordEvent {
      owner: tx_context::sender(ctx),
   });

	//Transfer the sword to the caller of this function
		transfer::transfer(sword, tx_context::sender(ctx));

	} 

}
```

现在，每当携带一把剑时，就会发出一个事件，通知成功购买剑。

## 小结

在本课程中，我们了解了 Sui 上的事件，并了解了如何使用 Move on Sui 来实现事件。希望这对您来说很有趣，下一课对您来说会更加令人兴奋，敬请期待！

## quiz

### 在Sui Move编程中，在智能合约中使用事件的目的是什么？