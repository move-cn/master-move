# 在 Sui 上部署您的第一个代币合约

欢迎回来！这样您就完成了可替代代币的编写。你做得很好！在本课程中，您将深入了解将代币部署到 Sui 区块链。你兴奋吗？让我们开始吧！

##  代码

这是我们在上一课中编写的合约的完整代码，以防您错过：

```rust
module metaschool::pepe {
    use std::option;
    use sui::coin;
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};

    // Name matches the module name, but in UPPERCASE
    public struct PEPE has drop {}

    // Module initializer is called once on module publish.
    // A treasury cap is sent to the publisher, who then controls minting and burning.
    fun init(witness: PEPE, ctx: &mut TxContext) {
        let (treasury, metadata) = coin::create_currency(witness, 9, b"PE", b"PEPE", b"", option::none(), ctx);
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

##  部署您的合约

###  步骤1

首先，我们必须进入 `metaschool` 目录，因为它包含我们的合约。

```rust
cd metaschool
```

###  第2步

现在，让我们使用以下命令构建移动文件。这将创建一个新的 `build` 文件夹和一个 `Move.toml` 文件。

```rust
sui move build
```

###  步骤3

现在，您需要在 Sui 中设置开发环境，以便使用以下命令部署在 Sui 测试网上：

```rust
sui client new-env --alias devnet --rpc https://fullnode.devnet.sui.io:443
sui client switch --env devnet
```

###  步骤4

现在是公开您的合同的时候了。为此，请右键单击 `pepe.move` 的绝对路径，然后选择 VS 代码上的 `copy path` 选项。获得路径后，您现在可以使用以下命令进行发布，方法是将 `[YOUR_PATH]` 替换为您刚刚复制的路径：

注意：如果您使用 VS code 以外的 IDE，请尝试查找 `pepe.move` 文件的确切完整路径。

```rust
sui client publish --gas-budget 100000000 [YOUR_PATH]
```

###  步骤5

现在您已经发布了合约，您将获得一个很长的输出。滚动并复制交易摘要。您可以使用此交易摘要通过以下链接使用 Sui Explorer 搜索您的交易：

https://suiexplorer.com/?network=devnet

![sui-explorer-gif.gif](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assests_for_sui_c3/sui-explorer-gif.gif?raw=true)

## 小结

在本课程中，您在 Sui 区块链上部署了代币。祝您未来部署顺利！

接下来，我们就来总结一下本次课程吧。



## quiz

### 与我们分享成功铸造交易的屏幕截图