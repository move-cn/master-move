# 编写 USDC 智能合约

欢迎再次回来！让我们立即开始创建 USDC 智能合约。

##  USDC 智能合约

首先，导航到 `sources/usdc.move` 并开始编写以下代码：

首先，我们从定义合约并导入我们将在代码中使用的基本包开始。

```rust
module dex::usdc {
  use std::option;

  use sui::url;
  use sui::transfer;
  use sui::coin;
  use sui::tx_context::{Self, TxContext};
```

接下来，我们将定义名为 `USDC` 的结构。它是创造电流的一次性见证者。它具有掉落能力，因此无法转移或存储。

```rust
struct USDC has drop {}
```

然后，我们创建一个 `init` 函数，该函数在创建模块时运行一次，并接受一次性 `witness` 作为第一个参数。

我们创建了一个具有 1 种能力的 `witness` 结构体， `drop` ，它保证整个网络中只有一个，因为你只能在 `init` 中获取它功能。

在 `init` 函数内，我们调用 `create_currency` 。创建货币需要一次性 `witness` 以确保它是唯一的硬币，并且只有 `TreasuryCap` 的持有者才被允许铸造和销毁该硬币。

```rust
fun init(witness: USDC, ctx: &mut TxContext) {
      let (treasury_cap, metadata) = coin::create_currency<USDC>(
            witness, // One time witness
            9, // Decimals of the coin
            b"USDC", // Symbol of the coin
            b"USDC Coin", // Name of the coin
            b"A stable coin issued by Circle", // Description of the coin
            option::some(url::new_unsafe_from_bytes(b"https://s3.coinmarketcap.com/static-gravity/image/5a8229787b5e4c809b5914eef709b59a.png")), // An image of the Coin
            ctx
        );

      // We send the treasury capability to the deployer
      transfer::public_transfer(treasury_cap, tx_context::sender(ctx));
      // Objects defined in different modules need to use the public_ transfer functions
      transfer::public_share_object(metadata);
  }
```

最后，我们添加了将在接下来的课程中实施的测试。该测试将帮助我们验证代码并检查它是否正常工作

```rust
// ** Test Functions

  #[test_only]
  public fun init_for_testing(ctx: &mut TxContext) {
    init(USDC {}, ctx);
  }
}
```

##  完整代码

为了您的方便，这里是完整的代码。

```rust
module dex::usdc {
  use std::option;

  use sui::url;
  use sui::transfer;
  use sui::coin;
  use sui::tx_context::{Self, TxContext};

  // ** Structs

  // One Time Witness to create a Current in Sui
  // This struct has the drop ability so it cannot be transferred nor stored. 
  // It allows the Network to know it is a unique type

  struct USDC has drop {}

  // The init function runs once on Module creation and accepts a one-time witness as the first argument
  // Witness is a struct with 1 ability, drop, it guarantees that there is only one in the entire network as you can only get it in the init function
  
fun init(witness: USDC, ctx: &mut TxContext) {
      // We call the create_currency
      // Creating a currency requires a one-time witness to ensure it is a unique coin
      // Only the holder of the TreasuryCap is allowed to mint and burn this coin
      // Metadata holds all the information about the coin, so other applications query it
      
			let (treasury_cap, metadata) = coin::create_currency<USDC>(
            witness, // One time witness
            9, // Decimals of the coin
            b"USDC", // Symbol of the coin
            b"USDC Coin", // Name of the coin
            b"A stable coin issued by Circle", // Description of the coin
            option::some(url::new_unsafe_from_bytes(b"https://s3.coinmarketcap.com/static-gravity/image/5a8229787b5e4c809b5914eef709b59a.png")), // An image of the Coin
            ctx
        );

      // We send the treasury capability to the deployer

      transfer::public_transfer(treasury_cap, tx_context::sender(ctx));

      // Objects defined in different modules need to use the public_ transfer functions
      
			transfer::public_share_object(metadata);
  }

  // ** Test Functions

  #[test_only]
  public fun init_for_testing(ctx: &mut TxContext) {
    init(USDC {}, ctx);
  }
}
```

##  小结

USDC 合约将帮助我们创建并用 USDC 代币填充我们的余额。这样我们就可以轻松地将USDC币兑换成ETH或者ETH兑换USDC币。接下来，我们将实现 Dex 智能合约，它将帮助我们交换 ETH 和 USDC 硬币并记录我们的代币余额。

## quiz

### 解释一下transfer::public_share_object函数的用途。

