# 部署忠诚度 DApp

在最后一节中编写忠诚度智能合约非常出色。在本节中，让我们部署我们的合约。

## 更新 `loyalty` 合约 `Move.toml` 文件

要成功部署 Dex Sui 项目，需要更新 `Move.toml` 文件。需要此更新以确保可以轻松安装和使用某些依赖项。在合同的实施过程中，您可能已经注意到一些库的使用。要安装和使用这些库，请使用以下信息更新 `Move.toml` 文件。

注意：请记住使用与创建 Move on Sui 项目时使用的包名称相同的包名称。

```rust
[package]
name = "loyalty"
version = "0.0.1"

[dependencies]
MoveStdlib = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/move-stdlib", rev = "testnet-v1.18.0" }
Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "testnet-v1.18.0" }
Dex = { local = "../dex/contracts" }
DeepBook = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/deepbook", rev = "testnet-v1.18.0" }

[addresses]
loyalty = "0x0"
std = "0x1"
sui = "0x2"
deepbook = "0xdee9"
dex = "0xa1dce324bcf781692a358adb27bd105844231d35863b5c99f94e54801d653788"
```

在这里，我们使用 Dex dApp 并提供本地 Dex 应用程序路径。因此，您一定已经在本 Sui 学习路径的最后课程中开发了 Dex dApp。请在此处提供本地 Dex dApp 路径。确保预先部署 Dex 以在忠诚度应用程序中使用它（如果您有 100 个 Sui 测试网代币，否则您可以使用我们的合约）。在我们的例子中，路径如下所示。

```abnf
Dex = { local = "/Users/username/Desktop/metaschool/Sui/dex-app/dex" }
```

在 `[addresses]` ，你一定注意到我们使用的是 dex 合约地址。使用此地址并且不要更改它，因为该合约已经创建了一个池，该池需要运行 dex 和忠诚度 dApp 所需的 100 个 Sui 代币。如果您与创建的池有 dex 合约，请随意在此处使用它。

使用现有的合约地址将有助于忠诚度 dApp 不再发布 dex 合约，而是使用现有的已发布地址。

注意：请使用更新版本的软件包以及 dex dApp 中使用的相同版本。否则，忠诚度 dApp 会报错。

## 更新 `dex` 合约 `Move.toml` 文件

正如您所注意到的，我们正在向忠诚度 dApp 添加本地 dex dApp 路径，我们需要更新 dex `Move.toml` 文件以添加 dex dApp 的适当发布地址。否则，你的忠诚就会给你带来错误。请使用以下 `Move.toml` 文件。

```rust
[package]
name = "Dex"
version = "0.0.1"
published-at = "0xa1dce324bcf781692a358adb27bd105844231d35863b5c99f94e54801d653788"

[dependencies]
MoveStdlib = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/move-stdlib", rev = "testnet-v1.18.0" }
Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "testnet-v1.18.0" }
DeepBook = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/deepbook", rev = "testnet-v1.18.0" }

[addresses]
dex = "0xa1dce324bcf781692a358adb27bd105844231d35863b5c99f94e54801d653788"
std = "0x1"
sui = "0x2"
deepbook = "0xdee9"
```

我们在 `[package]` 字段下添加了 `published-at` 地址，在 `[addresses]` 字段下添加了 `dex` 地址。

## **在 Sui 上构建并发布忠诚度 dApp**

按照以下步骤在区块链上构建并发布您的 Sui 项目。

###  步骤1

首先，我们必须进入 `loyalty` 目录，因为它包含我们的合约。

```rust
cd loyalty
```

###  第2步

现在，让我们使用以下命令构建移动文件。这将创建一个新的 `build` 文件夹和一个 `Move.toml` 文件。

```rust
sui move build
```

输出如下所示。

![output-1.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/sui-loyalty-dapp/Deploying%20the%20Loyalty%20DApp/output-1.png?raw=true)

###  步骤3

您现在需要在 Sui 中设置开发环境，以便使用以下命令部署在 Sui 测试网上：

```rust
sui client new-env --alias=testnet --rpc https://fullnode.testnet.sui.io:443
sui client switch --env testnet
```

###  步骤4

运行以下命令，最终将Move项目发布到测试网：

```rust
sui client publish --skip-dependency-verification --gas-budget 90000000
```

我们将有一个很长的输出，但滚动到输出的开头并复制交易摘要：

![output-2.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/sui-loyalty-dapp/Deploying%20the%20Loyalty%20DApp/output-2.png?raw=true)

- 注意：如果您遇到“资金不足”错误，则可以前往 Sui Testnet faucet discord 频道并粘贴“!faucet [YOUR_ADDRESS]”以接收 10 个 Sui 代币。

###  步骤5

前往 https://suiexplorer.com/?network=testnet。将交易摘要粘贴到搜索栏中，以在 Sui Explorer 上查找您的交易：

![output-3.png](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/sui-loyalty-dapp/Deploying%20the%20Loyalty%20DApp/output-3.png?raw=true)

## 小结

在本课程中，您在 Sui 区块链上部署了忠诚度智能合约。祝您未来部署顺利！

接下来，让我们将合约与前端集

## quiz

### 搜索您的合约并与我们分享 Sui explorer 的屏幕截图。