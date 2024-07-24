# 部署 Dex DApp

在为 Move on Sui 设置开发环境方面做得非常出色。在本课程中，我们将学习在 Sui 区块链上运行和部署 `dex` Move 项目，并在资源管理器中对其进行探索。

##  更新 `Move.toml` 文件

要成功部署 Dex Sui 项目，需要更新 `Move.toml` 文件。需要此更新以确保可以轻松安装和使用某些依赖项。在合同的实施过程中，您可能已经注意到一些库的使用。要安装和使用这些库，请使用以下信息更新 `Move.toml` 文件。

注意：请记住使用与创建 Move on Sui 项目时使用的包名称相同的包名称。

```rust
[package]
name = "dex"
version = "0.0.1"

[dependencies]
MoveStdlib = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/move-stdlib", rev = "testnet-v1.14.0" }
Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "testnet-v1.14.0" }
DeepBook = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/deepbook", rev = "testnet-v1.14.0" }

[addresses]
dex = "0x0"
std = "0x1"
sui = "0x2"
deepbook = "0xdee9"
```

## 在 Sui 上构建并发布

现在让我们使用以下命令构建 Move 文件。请记住使用 `cd dex` 命令移动到 `dex` 文件夹。

```rust
sui move build
```

这将生成如下输出：

![deploy-1.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/4.%20Deploy%20the%20DApp/assets/deploy-1.png?raw=true)

注意：忽略所有警告并继续前进。

运行命令后，您的项目目录将包含一个 `build` 文件夹和一个 `Move.lock` 文件。

![deploy-2.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/4.%20Deploy%20the%20DApp/assets/deploy-2.png?raw=true)

### 设置开发环境

要在 Sui Testnet 上部署，请执行以下命令。

```rust
sui client new-env --alias=testnet --rpc https://fullnode.testnet.sui.io:443
sui client switch --env testnet
```

###  发布合同

运行以下命令，最终将Move项目发布到测试网：

```rust
sui client publish --skip-dependency-verification --gas-budget 90000000
```

注意：当您在生产环境中部署时，我们建议不要使用 `--skip-dependency-verification` 标志

我们将有一个很长的输出，但滚动到输出的开头并复制交易摘要：

![deploy-3.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/4.%20Deploy%20the%20DApp/assets/deploy-3.png?raw=true)

- 注意：忽略所有警告并继续前进。但如果您遇到“资金不足”错误，那么您可以前往 [Sui Testnet faucet discord ](https://discord.com/channels/916379725201563759/1037811694564560966) 频道并粘贴“!faucet [YOUR_ADDRESS]”以接收 10 个 SUI 代币。

前往 https://suiexplorer.com/?network=testnet。将交易摘要粘贴到搜索栏中，以在 Sui Explorer 上查找您的交易：

![deploy-4.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/4.%20Deploy%20the%20DApp/assets/deploy-4.png?raw=true)

呼！你做得很好，但等等！现在您需要创建池。池可以用代币填充你的 dex 应用程序。为此，我们需要运行 `sources/dex.move` 文件中的以下函数。

1. `create_pool`
2. `create_state`
3. `fill_pool`

这是 `sources/dex.move` 文件中这些函数的屏幕截图。

![pool-code.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/4.%20Deploy%20the%20DApp/assets/pool-code.png?raw=true)

为了运行这些功能，我们的测试网钱包地址中需要 100 个 SUI 代币。即使是测试网帐户，也几乎不可能免费获得 100 个 SUI 代币。

因此，为了解决这个问题，我们已经在以下地址为您创建了一个池。这是我们 DEX 合约的 Package ID

注意：如果您希望在 Sui 上构建端到端产品，请联系 Metaschool 团队，我们将帮助您获得 100 个 Sui 代币。

```dns
0xa1dce324bcf781692a358adb27bd105844231d35863b5c99f94e54801d653788
```

下面是DEX存储ID，我们将在下一课中使用它。

```x86asm
0x6912d83e2c4868386511fe3f6f18aff9399b9ad5cae2d97943766e2ff160ab25
```

## 小结

总之，本课介绍了在 Sui 上运行智能合约。我们学习了如何创建 Move 工作区、构建和发布 Move 文件、创建 Sui 帐户以及在 Sui 测试网上部署合约。恭喜您完成本课！

## quiz

### 分享您部署的 Sui Dex dApp 地址的屏幕截图