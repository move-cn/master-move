# 编写 Dex 智能合约

欢迎回来！到目前为止你做得很好！我们已经完成了货币的创建。因此，让我们开始写一些深刻而实际的内容吧！

##  Dex 智能合约

 导航到 `sources/dex.move` 。

该合约实现了去中心化交易（DEX）系统，允许用户在 Sui 区块链上交易以太坊（ETH）和 USDC（稳定币）。

DEX 使用 DeepBook 协议，这是一个用于管理买卖订单的中央限价订单簿（CLOB）系统。现在将 DeepBook 想象为一个中间实体，为您管理买卖订单。

例如，如果买家提交以 1,800 美元买入 1 ETH 的订单，并且有相应的以 1,800 美元或更低价格卖出 1 ETH 的订单，则 DeepBook 协议将匹配这些订单并执行交易。买方将收到 1 ETH，卖方将收到指定金额的 USDC（在本例中为 1,800 美元）。

现在让我们深入研究代码！

```rust
module dex::dex {

  // Import necessary modules and types
  use std::option;
  use std::type_name::{get, TypeName};
  use sui::transfer;
  use sui::sui::SUI;
  use sui::clock::{Clock};
  use sui::balance::{Self, Supply};
  use sui::object::{Self, UID};
  use sui::table::{Self, Table};
  use sui::dynamic_field as df;
  use sui::tx_context::{Self, TxContext};
  use sui::coin::{Self, TreasuryCap, Coin};
  use deepbook::clob_v2::{Self as clob, Pool};
  use deepbook::custodian_v2::AccountCap;
  use dex::eth::ETH;
  use dex::usdc::USDC;
```

- 该代码首先定义一个名为 `dex::dex` 的 Move 模块。
- 导入各种模块和类型，包括来自 Sui 和 DeepBook 库的模块和类型。

```rust
 // Constants
  const CLIENT_ID: u64 = 122227;
  const MAX_U64: u64 = 18446744073709551615;
  const NO_RESTRICTION: u8 = 0;
  const FLOAT_SCALING: u64 = 1_000_000_000;
  const EAlreadyMintedThisEpoch: u64 = 0;
```

接下来，我们有常数，让我为你分解一下，

- CLIENT_ID：u64 = 122227；这是用于在 DeepBook 协议中下订单的初始客户端 ID。合约下的每个订单都会有一个唯一的客户 ID。 MAX_U64：u64 = 18446744073709551615；这是无符号 64 位整数的最大值。在下达无限期限价单时，它用作“无限”时间戳的占位符。
- 无限制：u8 = 0；该常数表示下限价单时没有任何限制。它用作没有任何特定限制的订单参数的占位符。
- 浮动缩放：u64 = 1_000_000_000； // 1e9 该常量表示浮点运算的缩放因子。它用于在合约中的十进制值和整数表示之间进行转换。
- EAlreadyMintedThisEpoch: u64 = 0;这是 mint_coin 函数中使用的错误代码。用于表示用户在当前纪元已经铸造了一枚币。

```rust
  //One-time witness to create the DEX coin
  struct DEX has drop {}
```

现在我们声明一个具有 drop 能力的 struct DEX，这种模式称为一次性见证模式（OTW）。 Move编程语言中的一次性见证（OTW）是一种特殊的结构体，用于控制某些资源的初始化或创建，确保此类操作只能执行一次。

```rust
struct Data<phantom CoinType> has store {
    cap: TreasuryCap<CoinType>,
    faucet_lock: Table<address, u64>,
}
```

- 定义了一个通用的 `Data` 结构，它存储与特定硬币类型相关的数据。
- 它包含一个 `TreasuryCap` 和一个表（ `faucet_lock` ）来存储用户地址及其最后的铸造纪元。

```rust
struct Storage has key {
    id: UID,
    dex_supply: Supply<DEX>,
    swaps: Table<address, u64>,
    account_cap: AccountCap,
    client_id: u64,
}
```

- `Storage` 结构体是用关键能力和 UID 定义的。
- 它有以下字段：
  - `id` ：表示 UID（唯一标识符），它是该结构体实例的唯一标识符。
  - `dex_supply` ：代表 DEX 代币的供应量，类型为 `Supply<DEX>` 。
  - `swaps` ：表示一个名为 `swaps` 的表，其中包含地址键和 `u64` 值。该表用于跟踪每个地址执行的交换次数。
  - `account_cap` ：代表 `AccountCap` ，与合约上下文中的账户能力相关。
  - `client_id` ：表示名为 `client_id` 的 `u64` 字段，用于跟踪合约中的客户端 ID。

```rust
#[allow(unused_function)]
  fun init(witness: DEX, ctx: &mut TxContext) {
    let (treasury_cap, metadata) = coin::create_currency<DEX>(
      witness,
      9,
      b"DEX",
      b"DEX Coin",
      b"Coin of SUI DEX",
      option::none(),
      ctx
    );

    transfer::public_freeze_object(metadata);

    transfer::share_object(Storage {
      id: object::new(ctx),
      dex_supply: coin::treasury_into_supply(treasury_cap),
      swaps: table::new(ctx),
      account_cap: clob::create_account(ctx),
      client_id: CLIENT_ID,
    });
  }
```

- 该代码定义了一个名为 `init` 的函数，该函数具有 `#[allow(unused_function)]` 属性，表明它有意允许未使用的函数。
- `init` 函数采用两个参数： `DEX` 类型的 `witness` 和对名为 `ctx` 的 `TxContext` 的可变引用> .
-  函数内部：
  - 它使用 `coin::create_currency` 函数创建 DEX 货币。该函数负责使用特定参数（例如符号、名称和元数据）初始化 DEX 代币。
  - `coin::create_currency` 的结果是一个包含 `treasury_cap` （DEX 的 TreasuryCap）和 `metadata` 的元组。
  - 它与 Sui 网络共享 `metadata` 并使用 `transfer::public_freeze_object` 使其不可变。
  - 它与 Sui Network 共享 `Storage` 对象。 Treasury Cap 转化为供应量以铸造 DEX 代币。
    - 它创建一个具有以下字段的新 `Storage` 对象：
      - `id` ：使用 `object::new(ctx)` 创建，表示新的唯一标识符。
      - `dex_supply` ：使用 `coin::treasury_into_supply` 从 `treasury_cap` 转换而来。
      - `swaps` ：使用 `table::new(ctx)` 创建新表。
      - `account_cap` ：使用 `clob::create_account(ctx)` 创建。
      - `client_id` ：用常量 `CLIENT_ID` 初始化。
    - `Storage` 对象使用 `transfer::share_object` 与 Sui Network 共享。

注意：这里，DEX 货币是我们 DEX 的原生代币，您在 DEX 上每成功兑换 2 次即可获得该代币。在下一门课程中，我对这个令牌有一个有趣的想法。敬请关注！

```rust
public fun user_last_mint_epoch<CoinType>(self: &Storage, user: address): u64 {
    let data = df::borrow<TypeName, Data<CoinType>>(&self.id, get<CoinType>());

    if (table::contains(&data.faucet_lock, user)) return *table::borrow(&data.faucet_lock, user);

    0
  }
```

- 该代码在 `Storage` 结构的上下文中定义了一个名为 `user_last_mint_epoch` 的公共函数。
- 它是一个视图函数，用于检索用户的最后铸造纪元。它从存储中加载硬币数据并检查用户是否使用了水龙头。
- 该功能是通用的 `CoinType` ，表明它可以使用不同类型的硬币。
- 它需要两个参数：
  - `self` ：对 `Storage` 结构体的引用，指示该函数对该结构体的实例进行操作。
  - `user` ： `address` 表示函数检索其最后铸造纪元的用户。
-  函数内部：
  - 它使用 `df::borrow` 使用 `self.id` 从 `Storage` 对象的动态字段访问与指定 `CoinType` 关联的数据。
  - 它通过使用 `table::contains` 验证其地址是否存在于关联 `Data` 的 `faucet_lock` 表中来检查 `user` 是否曾经使用过水龙头。 。
  - 如果用户使用了水龙头，该函数将使用 `table::borrow` 返回为该用户存储的最后一个纪元。
  - 如果用户没有使用过水龙头，函数返回 `0` 作为默认值。

注意：这里， `df` 是动态字段。 Sui 中的动态字段是一种在对象中存储数据的方法，不受结构声明中预定义字段的限制。它们允许您在运行时动态添加和删除字段，与常规对象字段相比具有更大的灵活性。

在我们的代码中，通过使用动态字段，Storage 对象可以存储与不同货币类型（ETH 和 USDC）相关的数据，而无需在 Storage 结构中为每种类型定义单独的字段。这允许更大的灵活性和可扩展性，因为可以通过简单地创建具有相应类型名称的新动态字段来添加新的硬币类型。

```rust
  public fun user_swap_count(self: &Storage, user: address): u64 {
    if (table::contains(&self.swaps, user)) return *table::borrow(&self.swaps, user);

    0
  }
```

- 该代码在 `Storage` 结构的上下文中定义了一个名为 `user_swap_count` 的公共函数。
- 它是一个检索用户交换计数的视图函数。它检查用户是否曾经交换过并返回计数。
- 该函数有两个参数：
  - `self` ：对 `Storage` 结构体的引用，指示该函数对该结构体的实例进行操作。
  - `user` ： `address` 表示函数检索其交换计数的用户。
-  函数内部：
  - 它通过使用 `table::contains` 验证其地址是否存在于 `Storage` 对象的 `swaps` 表中来检查指定的 `user` 是否曾经执行过交换。
  - 如果用户执行了交换，该函数将使用 `table::borrow` 返回为该用户存储的交换计数。
  - 如果用户未执行任何交换，该函数将返回 `0` 作为默认值。

```rust
  public fun entry_place_market_order(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>,
    account_cap: &AccountCap,
    quantity: u64,
    is_bid: bool,
    base_coin: Coin<ETH>,
    quote_coin: Coin<USDC>,
    c: &Clock,
    ctx: &mut TxContext,
  ) {
    let (eth, usdc, coin_dex) = place_market_order(self, pool, account_cap, quantity, is_bid, base_coin, quote_coin, c, ctx);
    let sender = tx_context::sender(ctx);
    transfer_coin(eth, sender);
    transfer_coin(usdc, sender);
    transfer_coin(coin_dex, sender);
  }
```

------

- 该代码在 `Storage` 结构的上下文中定义了一个名为 `entry_place_market_order` 的公共函数。
- 可以调用该函数在DEX（去中心化交易所）下市价单。
- 它需要几个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `pool` ：对 ETH 和 USDC 的 `Pool` 的可变引用，在其中下达市价订单。
  - `account_cap` ：对与用户帐户关联的 `AccountCap` 的引用。
  - `quantity` ： `u64` 代表市价单的数量。
  - `is_bid` ： `bool` 指示订单是否为出价（买入）。
  - `base_coin` ：代表基础货币（ETH）的 `Coin` 对象。
  - `quote_coin` ：代表报价货币（USDC）的 `Coin` 对象。
  - `c` ：对 `Clock` 对象的引用，可能表示区块链上的时间戳。
  - `ctx` ：对 `TxContext` 的可变引用，提供事务上下文信息。

 函数内部：

- 它调用另一个名为 `place_market_order` 的函数来执行市价订单，获得三个结果代币： `eth` 、 `usdc` 和 `coin_dex` 。
- 它使用 `tx_context::sender(ctx)` 检索发件人的地址。
- 它调用 `transfer_coin` 函数三次，将生成的硬币（ `eth` 、 `usdc` 和 `coin_dex` ）转移到发送者的地址。

```rust
public fun place_market_order(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>,
    account_cap: &AccountCap,
    quantity: u64,
    is_bid: bool,
    base_coin: Coin<ETH>,
    quote_coin: Coin<USDC>,
    c: &Clock,
    ctx: &mut TxContext,    
  ): (Coin<ETH>, Coin<USDC>, Coin<DEX>) {
  let sender = tx_context::sender(ctx);  

  let client_order_id = 0;
  let dex_coin = coin::zero(ctx);

  if (table::contains(&self.swaps, sender)) {
    let total_swaps = table::borrow_mut(&mut self.swaps, sender);
    let new_total_swap = *total_swaps + 1;
    *total_swaps = new_total_swap;
    client_order_id = new_total_swap;

    if ((new_total_swap % 2) == 0) {
      coin::join(&mut dex_coin, coin::from_balance(balance::increase_supply(&mut self.dex_supply, FLOAT_SCALING), ctx));
    };
  } else {
    table::add(&mut self.swaps, sender, 1);
  };
  
  let (eth_coin, usdc_coin) = clob::place_market_order<ETH, USDC>(
    pool, 
    account_cap, 
    client_order_id, 
    quantity,
    is_bid,
    base_coin,
    quote_coin,
    c,
    ctx
    );

    (eth_coin, usdc_coin, dex_coin)
  }
```

- 该代码在 `Storage` 结构的上下文中定义了一个名为 `place_market_order` 的公共函数。
- 该功能负责在 DEX（去中心化交易所）下市价单。
- 它需要几个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `pool` ：对 ETH 和 USDC 的 `Pool` 的可变引用，在其中下达市价订单。
  - `account_cap` ：对与用户帐户关联的 `AccountCap` 的引用。
  - `quantity` ： `u64` 代表市价单的数量。
  - `is_bid` ： `bool` 指示订单是否为出价（买入）。
  - `base_coin` ：代表基础货币（ETH）的 `Coin` 对象。
  - `quote_coin` ：代表报价货币（USDC）的 `Coin` 对象。
  - `c` ：对 `Clock` 对象的引用，可能表示区块链上的时间戳。
  - `ctx` ：对 `TxContext` 的可变引用，提供事务上下文信息。

 函数内部：

- 它使用 `tx_context::sender(ctx)` 检索发件人的地址。
- 它将 `client_order_id` 初始化为 `0` 并使用 `coin::zero(ctx)` 创建一个 `dex_coin` 作为零值硬币。
- 它通过使用 `table::contains` 验证发送者的地址是否存在于 `Storage` 对象的 `swaps` 表中来检查发送者是否曾经执行过交换。
  - 如果发送方之前执行过交换，则会增加总交换计数器并更新 `client_order_id` 。
  - 如果总交换次数为偶数，则为用户铸造 1 个 DEX 代币。这涉及增加 DEX 供应，将其转化为代币，并将其与零价值的 DEX 代币结合起来。
  - 如果发送者之前没有执行过任何交换，它会通过向 `swaps` 表添加一个条目来注册帐户。
- 它调用 `clob::place_market_order` 函数将市价单放入 DEX 池中，获得 `eth_coin` 和 `usdc_coin` 。
- 它返回一个包含生成的硬币的元组： `(eth_coin, usdc_coin, dex_coin)` 。

```rust
  public fun create_pool(fee: Coin<SUI>, ctx: &mut TxContext) {
    clob::create_pool<ETH, USDC>(1 * FLOAT_SCALING, 1, fee, ctx);
  }
```

- 该代码在合约上下文中定义了一个名为 `create_pool` 的公共函数。
- 该函数负责使用 `clob::create_pool` 函数在Deep Book中创建一个池。
- 它需要两个参数：
  - `fee` ： `Coin<SUI>` 代表创建池所需支付的费用。
  - `ctx` ：对 `TxContext` 的可变引用，提供事务上下文信息。

 函数内部：

- 它调用 `clob::create_pool` 函数在Deep Book中创建ETH-USDC池。
- 创建的池将与 Sui Network 共享。
- 池的刻度大小设置为 `1 USDC - 1e9` 。
- 没有为池指定最小批量。
- 创建池的费用指定为 `fee` 参数，即 `Coin<SUI>` 。
- `1 * FLOAT_SCALING` 参数可能代表池中 ETH 和 USDC 的初始流动性或数量。
- 该功能有助于创建池，允许用户在 Deep Book 平台上交易资产。

```rust
  public fun fill_pool(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>,
    c: &Clock,
    ctx: &mut TxContext
  ) {
    create_ask_orders(self, pool, c, ctx);
    create_bid_orders(self, pool, c, ctx);
  }
```

- 该代码在合约上下文中定义了一个名为 `fill_pool` 的公共函数。
- 仅当指定池中不存在现有订单时才调用该函数。
- 它需要几个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `pool` ：对 ETH 和 USDC 的 `Pool` 的可变引用，代表连续限价订单簿 (CLOB) 池。
  - `c` ：对 `Clock` 对象的引用，可能表示区块链上的时间戳。
  - `ctx` ：对 `TxContext` 的可变引用，提供事务上下文信息。

 函数内部：

- 它包含一条注释，指示仅当池中没有现有订单时才应调用该函数。
- 它调用两个函数： `create_ask_orders` 和 `create_bid_orders` 。
- `create_ask_orders` 使用参数 `self` 、 `pool` 、 `c` 和 `ctx` 进行调用。
  - 该功能负责在 DeepBook 中存入资金、下限价卖单以及允许其他用户购买代币。
- `create_bid_orders` 使用相同的参数调用。
  - 该功能负责在 DeepBook 中存入资金、下限价买单以及允许其他用户出售代币。

此功能的目的是用初始订单填充池，提供流动性并为其他用户提供交易。

```rust
  public fun create_state(
    self: &mut Storage, 
    eth_cap: TreasuryCap<ETH>, 
    usdc_cap: TreasuryCap<USDC>, 
    ctx: &mut TxContext
  ) {

    df::add(&mut self.id, get<ETH>(), Data { cap: eth_cap, faucet_lock: table::new(ctx) });
    df::add(&mut self.id, get<USDC>(), Data { cap: usdc_cap, faucet_lock: table::new(ctx) });
  }
```

- 该代码在合约上下文中定义了一个名为 `create_state` 的公共函数。
- 该函数旨在在部署期间调用，并且只应调用一次，因为它会初始化 ETH 和 USDC 与 TreasuryCaps 的合约状态。
- 它需要三个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `eth_cap` ： `TreasuryCap<ETH>` 代表 ETH 货币的 TreasuryCap。
  - `usdc_cap` ： `TreasuryCap<USDC>` 代表 USDC 货币的 TreasuryCap。
  - `ctx` ：对 `TxContext` 的可变引用，提供事务上下文信息。

 函数内部：

- 它包含一条注释，指示该函数只能在部署期间调用。
- 它使用 `df::add` 函数将大写字母保存在具有动态对象字段的 `Storage` 对象内。
- `df::add` 函数向 `Storage` 对象添加一个动态字段，该字段具有基于 `get<ETH>()` 和 `get<USDC>()` 类型的唯一标识符。
-  对于以太币：
  - 它添加了一个带有键 `get<ETH>()` 的动态字段和一个包含 `eth_cap` 的 `Data` 结构以及一个新的空水龙头锁表。
-  对于 USDC：
  - 它添加了一个带有键 `get<USDC>()` 的动态字段和一个包含 `usdc_cap` 的 `Data` 结构以及一个新的空水龙头锁表。

该函数的目的是在部署期间初始化 ETH 和 USDC 与 TreasuryCaps 的合约状态。

```rust
  public fun mint_coin<CoinType>(self: &mut Storage, ctx: &mut TxContext): Coin<CoinType> {
    let sender = tx_context::sender(ctx);
    let current_epoch = tx_context::epoch(ctx);
    let type = get<CoinType>();
    let data = df::borrow_mut<TypeName, Data<CoinType>>(&mut self.id, type);

    if (table::contains(&data.faucet_lock, sender)){
      let last_mint_epoch = table::borrow(&data.faucet_lock, tx_context::sender(ctx));
      assert!(current_epoch > *last_mint_epoch, EAlreadyMintedThisEpoch);
    } else {
      table::add(&mut data.faucet_lock, sender, 0);
    };

    let last_mint_epoch = table::borrow_mut(&mut data.faucet_lock, sender);
    *last_mint_epoch = tx_context::epoch(ctx);
    coin::mint(&mut data.cap, if (type == get<USDC>()) 100 * FLOAT_SCALING else 1 * FLOAT_SCALING, ctx)
  }
```

- `mint_coin` 函数设计为使用 ETH 或 USDC 类型进行调用。
- 如果类型为 USDC，则每个纪元铸造 100 USDC；如果类型为 ETH，则每个纪元铸造 1 ETH。
- 该函数有两个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `ctx` ：对 `TxContext` 的可变引用，提供事务上下文信息。

 函数内部：

- 它从交易上下文中检索发送者的地址和当前纪元。
- 它使用 `get<CoinType>()` 函数根据通用参数 ( `CoinType` ) 确定硬币类型。
- 它使用 `df::borrow_mut` 加载与指定 `CoinType` 关联的 `Data` 结构。
- 它检查发送者在水龙头锁定表中是否有记录。如果是，它会检查发送者是否有资格在当前时期进行铸造。
  - 如果符合条件，则继续进行；否则，它会引发错误（ `EAlreadyMintedThisEpoch` ）。
- 如果发送方在表中没有记录，则会添加一条默认纪元为 0 的新记录。
- 它借用了对最后铸造纪元的可变引用并将其更新为当前纪元。
- 它使用 `coin::mint` 函数铸造一枚硬币（100 USDC 或 1 ETH）。

```rust
fun create_ask_orders(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>, 
    c: &Clock,
    ctx: &mut TxContext
  ) {

    let eth_data = df::borrow_mut<TypeName, Data<ETH>>(&mut self.id, get<ETH>());

    clob::deposit_base<ETH, USDC>(pool, coin::mint(&mut eth_data.cap, 60000000000000, ctx), &self.account_cap);

    clob::place_limit_order(
      pool,
      self.client_id,
     120 * FLOAT_SCALING, 
     60000000000000,
      NO_RESTRICTION,
      false,
      MAX_U64,
      NO_RESTRICTION,
      c,
      &self.account_cap,
      ctx
    );

    self.client_id = self.client_id + 1;
  }
```

- `create_ask_orders` 函数负责在交易池中创建卖价订单。
- 卖价订单通常由想要以报价代币 (USDC) 形式以指定价格出售一定数量基础代币 (ETH) 的卖家下达。
- 该函数需要几个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `pool` ：对交易池的可变引用 ( `Pool<ETH, USDC>` )。
  - `c` ：时钟对象（ `Clock` ），用于确定链上的时间戳。
  - `ctx` ：对事务上下文 ( `TxContext` ) 的可变引用。

 函数内部：

- 它使用动态字段访问 ( `df::borrow_mut<TypeName, Data<ETH>>` ) 从存储中检索与 ETH 硬币类型关联的可变数据。
- 它使用 `clob::deposit_base<ETH, USDC>` 函数将 60,000 ETH 存入交易池。该金额是使用 ETH 数据中的 `coin::mint` 铸造的。
- 然后，它在池中放置限价卖单：
  - 该订单是以每 ETH 120 USDC 的价格出售 60,000 ETH。
  - 每个订单的订单 ID ( `client_id` ) 都会递增。
  - 订单没有到期时间戳（ `MAX_U64` 表示没有到期）。
  - 使用 `clob::place_limit_order` 下订单。
- 最后，它增加 `client_id` 为下一个订单做准备。

```rust
fun create_bid_orders(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>, // The CLOB pool
    c: &Clock,
    ctx: &mut TxContext
  ) {
    let usdc_data = df::borrow_mut<TypeName, Data<USDC>>(&mut self.id, get<USDC>());

    clob::deposit_quote<ETH, USDC>(pool, coin::mint(&mut usdc_data.cap, 6000000000000000, ctx), &self.account_cap);

    clob::place_limit_order(
      pool,
      self.client_id, 
      100 * FLOAT_SCALING, 
      60000000000000,
      NO_RESTRICTION,
      true,
      MAX_U64, 
      NO_RESTRICTION,
      c,
      &self.account_cap,
      ctx
    );
    self.client_id = self.client_id + 1;
  }
```

- `create_bid_orders` 函数负责在交易池中创建买单。
- 竞价订单通常由想要以报价代币 (USDC) 形式以指定价格购买一定数量基础代币 (ETH) 的买家下达。
- 该函数需要几个参数：
  - `self` ：对 `Storage` 结构的可变引用，指示该函数可以修改实例。
  - `pool` ：对交易池的可变引用 ( `Pool<ETH, USDC>` )。
  - `c` ：时钟对象（ `Clock` ），用于确定链上的时间戳。
  - `ctx` ：对事务上下文 ( `TxContext` ) 的可变引用。

 函数内部：

- 它使用动态字段访问 ( `df::borrow_mut<TypeName, Data<USDC>>` ) 从存储中检索与 USDC 硬币类型关联的可变数据。
- 它使用 `clob::deposit_quote<ETH, USDC>` 功能将6,000,000 USDC存入交易池。该金额是使用 USDC 数据中的 `coin::mint` 铸造的。
- 然后，它在池中下达限价买单：
  - 该订单是以每 ETH 100 USDC 或更高的价格购买 60,000 ETH。
  - 每个订单的订单 ID ( `client_id` ) 都会递增。
  - 订单没有到期时间戳（ `MAX_U64` 表示没有到期）。
  - 使用 `clob::place_limit_order` 下订单。
- 最后，它增加 `client_id` 为下一个订单做准备。

```rust
fun transfer_coin<CoinType>(c: Coin<CoinType>, sender: address) {
    if (coin::value(&c) == 0) {
      coin::destroy_zero(c);
    } else {
    transfer::public_transfer(c, sender);
    }; 
  }
```

- `transfer_coin` 函数负责将特定类型的硬币（ `CoinType` ）转移到指定的接收者（ `sender` 地址）。
- 它需要两个参数：
  - `c` ： `Coin<CoinType>` 代表要转移的硬币。
  - `sender` ： `address` 表示硬币的接收者。

 函数内部：

- 它使用 `coin::value(&c) == 0` 检查硬币是否具有任何价值。
- 如果硬币的价值为零，它会使用 `coin::destroy_zero(c)` 销毁硬币。
- 如果硬币具有非零值，它将使用 `transfer::public_transfer(c, sender)` 将硬币转移到指定的发送者。

```rust
#[test_only]
  public fun init_for_testing(ctx: &mut TxContext) {
    init( DEX {}, ctx);
  }
}
```

- `init_for_testing` 函数是一个仅供测试的函数，旨在用于测试目的。
- 它采用对 `TxContext` ( `ctx` ) 的可变引用作为参数。
- 在函数内部，它使用 `DEX` 类型的见证（ `DEX` 结构的空实例）和提供的 `TxContext` 函数> .
- 该测试函数可能用于初始化 DEX 模块以进行测试场景，而不影响主部署。

##  完整代码

`dex.move` 的完整代码是：

```rust
module dex::dex {
  use std::option;
  use std::type_name::{get, TypeName};

  use sui::transfer;
  use sui::sui::SUI;
  use sui::clock::{Clock};
  use sui::balance::{Self, Supply};
  use sui::object::{Self, UID};
  use sui::table::{Self, Table};
  use sui::dynamic_field as df;
  use sui::tx_context::{Self, TxContext};
  use sui::coin::{Self, TreasuryCap, Coin};

  use deepbook::clob_v2::{Self as clob, Pool};
  use deepbook::custodian_v2::AccountCap;

  use dex::eth::ETH;
  use dex::usdc::USDC;

  const CLIENT_ID: u64 = 122227;
  const MAX_U64: u64 = 18446744073709551615;
  const NO_RESTRICTION: u8 = 0;
  const FLOAT_SCALING: u64 = 1_000_000_000; 

  const EAlreadyMintedThisEpoch: u64 = 0;

  struct DEX has drop {}

  struct Data<phantom CoinType> has store {
    cap: TreasuryCap<CoinType>,
    faucet_lock: Table<address, u64>
  }

  struct Storage has key {
    id: UID,
    dex_supply: Supply<DEX>,
    swaps: Table<address, u64>,
    account_cap: AccountCap,
    client_id: u64
  }

  #[allow(unused_function)]
  fun init(witness: DEX, ctx: &mut TxContext) { 

  let (treasury_cap, metadata) = coin::create_currency<DEX>(
            witness, 
            9, 
            b"DEX",
            b"DEX Coin", 
            b"Coin of SUI DEX", 
            option::none(), 
            ctx
        );
    
    transfer::public_freeze_object(metadata);    

		transfer::share_object(Storage { 
      id: object::new(ctx), 
      dex_supply: coin::treasury_into_supply(treasury_cap), 
      swaps: table::new(ctx),
      account_cap: clob::create_account(ctx),
      client_id: CLIENT_ID
    });
  }

  public fun user_last_mint_epoch<CoinType>(self: &Storage, user: address): u64 {
    let data = df::borrow<TypeName, Data<CoinType>>(&self.id, get<CoinType>());

    if (table::contains(&data.faucet_lock, user)) return *table::borrow(&data.faucet_lock, user);

    0 
  }

  public fun user_swap_count(self: &Storage, user: address): u64 {
    if (table::contains(&self.swaps, user)) return *table::borrow(&self.swaps, user);

    0
  }

  public fun entry_place_market_order(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>,
    account_cap: &AccountCap,
    quantity: u64,
    is_bid: bool,
    base_coin: Coin<ETH>,
    quote_coin: Coin<USDC>,
    c: &Clock,
    ctx: &mut TxContext,   
  ) {
    let (eth, usdc, coin_dex) = place_market_order(self, pool, account_cap, quantity, is_bid, base_coin, quote_coin, c, ctx);
    let sender = tx_context::sender(ctx);

    transfer_coin(eth, sender);
    transfer_coin(usdc, sender);
    transfer_coin(coin_dex, sender);
  }

  public fun place_market_order(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>,
    account_cap: &AccountCap,
    quantity: u64,
    is_bid: bool,
    base_coin: Coin<ETH>,
    quote_coin: Coin<USDC>,
    c: &Clock,
    ctx: &mut TxContext,    
  ): (Coin<ETH>, Coin<USDC>, Coin<DEX>) {
  let sender = tx_context::sender(ctx);  

  let client_order_id = 0;
  let dex_coin = coin::zero(ctx);

  if (table::contains(&self.swaps, sender)) {
    let total_swaps = table::borrow_mut(&mut self.swaps, sender);
    let new_total_swap = *total_swaps + 1;
    *total_swaps = new_total_swap;
    client_order_id = new_total_swap;

    if ((new_total_swap % 2) == 0) {
      coin::join(&mut dex_coin, coin::from_balance(balance::increase_supply(&mut self.dex_supply, FLOAT_SCALING), ctx));
    };
  } else {
    table::add(&mut self.swaps, sender, 1);
  };
  
  let (eth_coin, usdc_coin) = clob::place_market_order<ETH, USDC>(
    pool, 
    account_cap, 
    client_order_id, 
    quantity,
    is_bid,
    base_coin,
    quote_coin,
    c,
    ctx
    );

    (eth_coin, usdc_coin, dex_coin)
  }
  
  public fun create_pool(fee: Coin<SUI>, ctx: &mut TxContext) {

    clob::create_pool<ETH, USDC>(1 * FLOAT_SCALING, 1, fee, ctx);
  }

  public fun fill_pool(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>, 
    c: &Clock, 
    ctx: &mut TxContext
  ) {
    
    create_ask_orders(self, pool, c, ctx);
    create_bid_orders(self, pool, c, ctx);
  }

  public fun create_state(
    self: &mut Storage, 
    eth_cap: TreasuryCap<ETH>, 
    usdc_cap: TreasuryCap<USDC>, 
    ctx: &mut TxContext
  ) {

    df::add(&mut self.id, get<ETH>(), Data { cap: eth_cap, faucet_lock: table::new(ctx) });
    df::add(&mut self.id, get<USDC>(), Data { cap: usdc_cap, faucet_lock: table::new(ctx) });
  }

  public fun mint_coin<CoinType>(self: &mut Storage, ctx: &mut TxContext): Coin<CoinType> {
    let sender = tx_context::sender(ctx);
    let current_epoch = tx_context::epoch(ctx);
    let type = get<CoinType>();
    let data = df::borrow_mut<TypeName, Data<CoinType>>(&mut self.id, type);

    if (table::contains(&data.faucet_lock, sender)){

      let last_mint_epoch = table::borrow(&data.faucet_lock, tx_context::sender(ctx));

      assert!(current_epoch > *last_mint_epoch, EAlreadyMintedThisEpoch);
    } else {

      table::add(&mut data.faucet_lock, sender, 0);
    };

    let last_mint_epoch = table::borrow_mut(&mut data.faucet_lock, sender);
    *last_mint_epoch = tx_context::epoch(ctx);
    coin::mint(&mut data.cap, if (type == get<USDC>()) 100 * FLOAT_SCALING else 1 * FLOAT_SCALING, ctx)
  }

  fun create_ask_orders(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>, 
    c: &Clock, 
    ctx: &mut TxContext
  ) {

    let eth_data = df::borrow_mut<TypeName, Data<ETH>>(&mut self.id, get<ETH>());

    clob::deposit_base<ETH, USDC>(pool, coin::mint(&mut eth_data.cap, 60000000000000, ctx), &self.account_cap);

    clob::place_limit_order(
      pool,
      self.client_id,
     120 * FLOAT_SCALING, 
     60000000000000,
      NO_RESTRICTION,
      false,
      MAX_U64,
      NO_RESTRICTION,
      c,
      &self.account_cap,
      ctx
    );

    self.client_id = self.client_id + 1;
  }

  fun create_bid_orders(
    self: &mut Storage,
    pool: &mut Pool<ETH, USDC>,
    c: &Clock,
    ctx: &mut TxContext
  ) {

    let usdc_data = df::borrow_mut<TypeName, Data<USDC>>(&mut self.id, get<USDC>());

    clob::deposit_quote<ETH, USDC>(pool, coin::mint(&mut usdc_data.cap, 6000000000000000, ctx), &self.account_cap);

    clob::place_limit_order(
      pool,
      self.client_id, 
      100 * FLOAT_SCALING, 
      60000000000000,
      NO_RESTRICTION,
      true,
      MAX_U64,
      NO_RESTRICTION,
      c,
      &self.account_cap,
      ctx
    );
    self.client_id = self.client_id + 1;
  }

  fun transfer_coin<CoinType>(c: Coin<CoinType>, sender: address) {
    
    if (coin::value(&c) == 0) {
      coin::destroy_zero(c);
    } else {
    
    transfer::public_transfer(c, sender);
    }; 
  }

  #[test_only]
  public fun init_for_testing(ctx: &mut TxContext) {
    init( DEX {}, ctx);
  }
}
```

您还可以在这里找到完整的 dex 应用程序代码：https://github.com/0xmetaschool/sui-dex-dapp

##  小结

在本课中，我们学习了Dex智能合约及其功能，例如填充交易池、初始化合约状态、创建卖价和买价订单、铸造硬币和转移硬币。总的来说，我们了解了 Dex 智能合约的关键功能。



## quiz

### 解释一下entry_place_market_order函数的作用是什么