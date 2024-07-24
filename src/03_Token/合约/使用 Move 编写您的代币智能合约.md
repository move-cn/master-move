# 使用 Move 编写您的代币智能合约

欢迎回来！这样您就完成了环境设置。你做得真棒！好吧，做好准备，因为在本课中，您将学习如何编写token。你兴奋吗？让我们开始吧！

##  编写代码

导航到 `sources/pepe.move` 。让我们逐行查看您将要编写的代码：

首先， `metaschool` 是包名称。它应该与我们使用命令 `sui move new metaschool` 初始化 Sui 工作区的文件夹名称相同。因此，如果您使用了任何其他名称，请务必在此处替换它。此外， `pepe` 是模块名称。因此，如果您将其命名为 `pepe` 之外的其他名称，请务必在此处更新它。

```rust
module metaschool::pepe {
```

- 此行定义了一个名为 `metaschool::pepe` 的模块，它将包含 Move 令牌实现。模块是一种组织代码并将相关功能分组在一起的方法。

```rust
    use std::option;
    use sui::coin;
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};
```

- 导入包含要在代码中使用的预构建函数和类型的模块（ `std::option` 、 `sui::coin` 、 `sui::transfer` 和 `sui::tx_context` ） 。

```rust
    struct PEPE has drop {}
```

- 使用 `has drop` 属性定义一个名为 `PEPE` 的新结构。

```rust
    fun init(witness: PEPE, ctx: &mut TxContext) {
        let (treasury, metadata) = coin::create_currency(witness, 9, b"PE", b"PEPE", b"", option::some(url::new_unsafe_from_bytes(b"https://silver-blushing-woodpecker-143.mypinata.cloud/ipfs/Qmed2qynTAszs9SiZZpf58QeXcNcYgPnu6XzkD4oeLacU4")), ctx);
        transfer::public_freeze_object(metadata);
        transfer::public_transfer(treasury, tx_context::sender(ctx))
    }
```

- 定义一个名为 `init` 的函数，它是模块初始值设定项。
- `init` 函数采用以下内容：
  - Move 使用见证设计模式，规定传递的类型只能启动一次。需要立即消耗或丢弃见证资源，以防止创建给定对象的多个实例。
  - 一次性见证人（OTW）是 Move on Sui 中的一种特殊类型，始终只有一个实例。它用于任何我们希望某些操作发生一次的地方。例如，一次性创造一枚硬币。您可以在这里探索有关 OTW 的更多信息：https://docs.sui.io/concepts/sui-move-concepts/one-time-witness
  - 所以我们传递了具有放置功能的 `PEPE` 结构。
  - 我们还传递了对 `TxContext` 的可变引用。
- 在函数内部，调用 `coin::create_currency` 函数来创建新的 PEPE 货币：
  - 将 `PEPE` 见证人作为第一个参数传递。
  - 所需的小数位数作为第二个参数。例如，对于 9 作为十进制值；假设我们要铸造100个代币，那么我们需要传递100*10^9。
  - 我们的代币符号是 `PE` 。
  - 令牌名称是 `PEPE` ，这是第四个参数。
  - 接下来，添加一个空图标 URL，以及一个可选的元数据。
- `coin::create_currency` 函数返回一个包含 `treasury` 和 `metadata` 的元组，它们分别是 `TreasuryCap` 和 `CoinMetadata` 对象。
  - `TreasuryCap` 就像一个管理器，控制对 `mint` 和 `burn` 方法的访问。
- 调用 `transfer::public_freeze_object` 冻结元数据，这样硬币元数据一旦发布就没有人可以更改
- 最后，使用 `transfer::public_transfer` 将 `treasury` 传输给交易的发送者。

```rust
    public entry fun mint(
        treasury: &mut coin::TreasuryCap<PEPE>, amount: u64, recipient: address, ctx: &mut TxContext
    ) {
        coin::mint_and_transfer(treasury, amount, recipient, ctx)
    }
}
```

- 定义一个名为 `mint` 的公共入口函数。
- `mint` 函数采用对 `coin::TreasuryCap<PEPE>` 的可变引用，类型为 `u64` 的 `amount` ，类型为 `recipient` `address` ，以及对 `TxContext` 作为参数的可变引用。
- 在函数内部，调用 `coin::mint_and_transfer` 函数来铸造新代币并将其转移到指定的 `recipient` 。

##  完整代码

完整的代码如下，您可以将其粘贴到在 Move Studio 中创建的文件中。

```rust
module metaschool::pepe{
    use std::option;
    use sui::coin;
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};
    use sui::url::{Self, Url};

    // Name matches the module name but in UPPERCASE
    public struct PEPE has drop {}

    // Module initializer is called once on module publish.
    // A treasury cap is sent to the publisher, who then controls minting and burning.
    fun init(witness: PEPE, ctx: &mut TxContext) {
        let (treasury, metadata) = coin::create_currency(witness, 9, b"PE", b"PEPE", b"", option::some(url::new_unsafe_from_bytes(b"https://silver-blushing-woodpecker-143.mypinata.cloud/ipfs/Qmed2qynTAszs9SiZZpf58QeXcNcYgPnu6XzkD4oeLacU4")), ctx);
        transfer::public_freeze_object(metadata);
        transfer::public_transfer(treasury, tx_context::sender(ctx))
    }

    public entry fun mint(
        treasury: &mut coin::TreasuryCap<PEPE>, amount: u64, recipient: address, ctx: &mut TxContext
    ) {
        coin::mint_and_transfer(treasury, amount, recipient, ctx)
    }
}
```

## 小结

唷！在理解和创建 Move on Sui 上的第一个代币方面做得非常出色。您可以在此处探索在 Sui 中创建硬币：https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/sui-framework/coin.md

在下一课中，您最终将在 Sui 区块链上部署代币。



## quiz

### 与我们分享代码的屏幕截图