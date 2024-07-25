# 部署你的第一个Sui合约

你在为 Move on Sui 设置开发环境方面做得非常出色。在本课中，我们将学习如何在 Sui 区块链上运行和部署“Hello World”智能合约，并在 Explorer 中进行探索。

## 部署您的第一个 Sui 合约

打开您最喜欢的终端，因为我们将运行一些命令来实现我们的目标。

第一步是初始化工作空间环境。这将包含用于运行任何 Move 文件的基本文件。您可以使用以下命令创建工作区;我给我的 `hello_world` 起了个名字：

```
sui move new hello_world
```

该命令将生成一个名为的 `hello_world` 文件夹，它将包含一个文件 `Move.toml` 和一个文件夹 `sources` ：

![deploy-1.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-1.png?raw=true)

P.S.：我正在使用Visual Studio IDE，因为它更好地可视化了我的工作区的结构。

导航到目录 `sources/` 。创建新的移动文件。我正在命名它 `Hello.move` 

![deploy-2.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-2.png?raw=true)

将以下代码粘贴到您刚刚创建的代码中 `Hello.move` ：

```rust
// Copyright (c) 2022, Sui Foundation
// SPDX-License-Identifier: Apache-2.0

/// A basic Hello World example for move, part of the move intro course:
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

注意：本课仅侧重于在 Sui 上运行我们的“Hello World”程序。我们将在即将到来的课程中讨论文件详细信息和解释。所以别担心，我们已经为您准备好了

## 在 Sui 上构建和发布

现在，让我们使用以下命令构建 Move 文件：

```
sui move build
```

这将生成如下所示的输出：

![deploy-3.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-3.png?raw=true)

运行该命令后，项目目录将包含一个 `build` 文件夹和一个 `Move.lock` 文件。

![deploy-4.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-4.png?raw=true)

### 创建您的 Sui 帐户

现在，让我们运行以下命令来创建 Sui 帐户

```
sui client new-address ed25519
```

它将生成如下输出：

![deploy-5.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-5.png?raw=true)

重要提示：请保存恢复短语，我们将在下一课中用到它来设置我们的钱包。

在下面的命令中替换为 `[YOUR_ADDRESS]` 您的地址并运行它。

```
sui client switch --address [YOUR_ADDRESS]
```

您还可以使用以下 `sui client active-address` 命令查看当前活动地址

![deploy-6.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-6.png?raw=true)

最后，前往 [Sui Devnet faucet discord channel](https://discord.com/channels/916379725201563759/971488439931392130)并粘贴“！faucet [YOUR_ADDRESS]”以接收 10 个 Sui 代币。

### 设置 dev 环境

要在 Sui Devnet 上部署，请执行以下命令。

```
sui client new-env --alias devnet --rpc https://fullnode.devnet.sui.io:443
sui client switch --env devnet
```

### 发布合约

首先，复制 `Hello.move` 文件的绝对路径：

![deploy-7.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-7.png?raw=true)

替换为 `[YOUR_PATH]` 文件的绝对路径，然后运行以下命令：

```
sui client publish --gas-budget 10000000 [YOUR_PATH]
```

我们将有一个很长的输出，但滚动到输出的开头并复制交易摘要：

![deploy-8.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-8.png?raw=true)

前往 https://suiexplorer.com/?network=devnet。将 Transaction Digest 粘贴到搜索栏中，以在 Sui Explorer 上查找您的交易

![deploy-9.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Deploy%20Your%20First%20Sui%20Contract/deploy-9.png?raw=true)

喔！你做得很好！我们已经在 Sui 🎉 上部署了我们的第一个“Hello World”程序

## 总结

总之，这节课介绍了在 Sui 上运行智能合约的过程。我们学习了如何在 Sui devnet 上创建 Move 工作区、构建和发布 Move 文件、创建 Sui 帐户以及部署合约。恭喜您完成了这节课！

## quiz

### 分享一下Sui Explorer部署并搜索“Hello World”智能合约后的截图。