# 解决方案：使用 Move on Sui 创建计算器合约

所以我希望您已经完成了该解决方案，但让我们讨论该解决方案并将其也部署在 Sui 上。

##  完整代码

计算器的完整代码是：

```rust
module calculator::calculator{
    use sui::object::{Self, UID};
    use sui::tx_context::{Self, TxContext};
    use sui::transfer;

    struct Output has key, store{
        id: UID,
        result: u64,

    }

    public entry fun start (ctx: &mut TxContext){
       let output = Output{
            id: object::new(ctx),
            result: 0,
        }; 

        transfer::public_transfer(output, tx_context::sender(ctx));  

    }

    public entry fun add (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a + b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx));  
    }
    
    public entry fun sub (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a - b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx)); 
    }

    public entry fun mul (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a * b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx));
    }

    public entry fun div (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a / b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx));
    }

}
```

##  解释

让我们看一下代码：

```rust
module calculator::calculator{
    use sui::object::{Self, UID};
    use sui::tx_context::{Self, TxContext};
    use sui::transfer;
```

 导入模块：

- 我们首先导入为我们的程序提供工具和功能的必要模块。
- `use sui::object::{Self, UID};` ：这一行导入了与对象相关的模块，包括Self和UID类型，这有助于我们管理对象的唯一标识符。
- `use sui::tx_context::{Self, TxContext};` ：在这里，我们导入用于事务上下文处理的模块，包括 Self 和 TxContext 类型。
- `use sui::transfer;` ：这会导入传输模块，使我们能够传输对象。

```rust
    struct Output has key, store{
        id: UID,
        result: u64,

    }
```

 定义结构体：

- 我们使用 `struct` 关键字定义一个名为 `Output` 的结构体。
- `struct` 有两个字段：
  - `id` ：它是 `Output` 对象的每个实例的唯一标识符 (UID)。
  - `result` ：该字段存储表示计算结果的64位无符号整数（u64）。

```rust
    public entry fun start (ctx: &mut TxContext){
       let output = Output{
            id: object::new(ctx),
            result: 0,
        }; 

        transfer::public_transfer(output, tx_context::sender(ctx));  

    }
```

定义 `start` 函数：

- 我们创建一个名为 `start` 的公共入口函数，它将对 `TxContext` 的可变引用作为参数。
- 在 `start` 函数内部：
  - 我们创建一个名为 `output using `的新 `Output` 对象 let output = Output { id: object::new(ctx), result: 0 };`
  - 我们将 `output` 对象的 `id` 字段设置为使用 `object::new(ctx)` 生成的唯一标识符。
  - 我们将 `result` 字段初始化为 0，就像我们从计算器设置为零开始一样。
  - 最后，我们使用 `transfer::public_transfer` 将 `output` 对象传输给某人，并将发送者指定为 `tx_context::sender(ctx)` 。

```rust
    public entry fun add (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a + b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx));  
    }
```

定义 `add` 函数：

- 这是一个名为 `add` 的公共入口函数。
- 它需要两个 64 位无符号整数（ `a` 和 `b` ）作为输入以及对 `TxContext` （ `ctx` ）的可变引用处理事务上下文。
- 用途：此函数旨在将两个数字相加并将结果存储在 `Output` 对象中。

 函数内部：

- ```rust
  let output = Output { id: object::new(ctx), result: a + b };
  ```

  - 我们创建一个名为 `output` 的新 `Output` 对象。
  - 我们使用 `object::new(ctx)` 为 `output` 对象分配一个唯一标识符 (UID)。
  - 我们计算 `a` 和 `b` 的总和并将其存储在 `output` 对象的 `result` 字段中。

- ```
  transfer::public_transfer(output, tx_context::sender(ctx));
  ```

  - 我们将“输出”对象传输给某人，并将发送者指定为 `tx_context::sender(ctx)` 。

```rust
    public entry fun sub (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a - b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx)); 
    }
```

定义 `sub` 函数：

- 这是一个名为 `sub` 的公共入口函数。
- 它需要两个 64 位无符号整数（ `a` 和 `b` ）作为输入以及对 `TxContext` （ `ctx` ）的可变引用处理事务上下文。
- 用途：此函数旨在将两个数字相减并将结果存储在 `Output` 对象中。

 函数内部：

- ```
  let output = Output { id: object::new(ctx), result: a + b };
  ```

  - 我们创建一个名为 `output` 的新 `Output` 对象。
  - 我们使用 `object::new(ctx)` 为 `output` 对象分配唯一标识符 (UID)。
  - 我们通过从 `a` 中减去 `b` 并将其存储在 `output` 对象的 `result` 字段中来计算减法。

- 最后，我们将 `output` 对象传输给某人，并使用 `tx_context::sender(ctx)` 指定发送者。

```rust
    public entry fun mul (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a * b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx));
    }
```

定义 `mul` 函数：

- 这是 `mul` 函数，它处理乘法。
- 它需要两个 64 位无符号整数 `a` 和 `b` 以及事务上下文 `ctx` 。

 函数内部：

- 我们创建一个新的 `Output` 对象。
- 我们通过 `a` 和 `b` 相乘来计算结果。
- `output` 对象存储此结果。
- 我们传输 `output` 对象，并使用 `tx_context::sender(ctx)` 指定发送者。

```rust
    public entry fun div (a:u64, b:u64, ctx: &mut TxContext){
        let output = Output{
            id: object::new(ctx),
            result: a / b,
        };
        
        transfer::public_transfer(output, tx_context::sender(ctx));
    }
```

定义 `div` 函数：

- 该函数 `div` 处理除法。
- 它需要两个 64 位无符号整数 `a` 和 `b` 以及事务上下文 `ctx` 。

 函数内部：

- 我们创建一个新的 `Output` 对象。
- 我们通过将 `a` 除以 `b` 来计算结果。
- `output` 对象存储此结果。
- 我们传输 `output` 对象，并使用 `tx_context::sender(ctx)` 指定发送者。

该代码定义了一个计算器程序，其结构体 `Output` 用来存储结果和各种算术运算（加法、减法、乘法和除法）作为公共入口函数。每个函数都会使用结果创建一个 `Output` 对象，并将其传输给使用 `transfer` 的人员。

##  部署合约

将以下命令中的 [YOUR_ADDRESS] 替换为您的帐户地址并运行：

```rust
sui client switch --address [YOUR_ADDRESS]
```

- 确保有足够的汽油。如果您没有，请前往 Sui Devnet faucet Discord 频道并粘贴“!faucet [YOUR_ADDRESS]”以接收 10 个 SUI 代币。

运行以下命令，以便我们可以在 Sui devnet 上部署：

```rust
sui client switch --env devnet
```

要发布合同，请复制 `Hello.move` 文件的绝对路径。将 [YOUR_PATH] 替换为文件的绝对路径并运行以下命令：

```rust
sui client publish --gas-budget 10000000 [YOUR_PATH]
```

我们将有一个很长的输出，但滚动到输出的开头并复制交易摘要。前往 https://suiexplorer.com/?network=devnet。将交易摘要粘贴到搜索栏中，即可在 Sui Explorer 上查找您的交易。

现在点击计算器模块的包地址：

![Frame 3560370.jpg](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Solution:%20Create%20a%20Calculator%20Contract%20using%20Move%20on%20Sui/Frame_3560370.jpg?raw=true)

您将看到如下所示的所有功能：

![Frame 3560370 (1).jpg](https://github.com/0xmetaschool/Learning-Projects/blob/main/assests_for_all/assets_for_sui_c2/Solution:%20Create%20a%20Calculator%20Contract%20using%20Move%20on%20Sui/Frame_3560370_(1).jpg?raw=true)

现在就开始与功能互动并享受吧~

## 小结

在本课程中，我们学习了如何使用 Move 创建计算器合约。我们讨论了代码结构和每个函数的用途。我们还提供了有关在 Sui Devnet 上部署合约的说明。随意探索合约的功能并与之互动。快乐编码！



## quiz

### 分享一下Sui Explorer的功能截图。