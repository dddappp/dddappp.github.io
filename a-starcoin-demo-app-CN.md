# Dapp 低代码平台的概念验证 : 开发一个 Starcoin Demo 应用

## 关于 Dapp-LCDP（低代码开发平台）

见：https://www.dddappp.org

## 本 Demo：一个演示性的域名系统

### Demo 的目的

从大的方面说，我们其实试图探讨一种可以极大地降低传统应用开发者迈入 Dapp 领域的门槛的开发模式。理想中的“极低门槛”应该是这样的：开发人员只需要构建领域模型，编写（存粹的）业务逻辑代码。这些代码理应可以在 L1/L2、链上/链下、中心化数据库/去中心化账本等不同的技术基础设施之间迁徙而不需要开发人员手动修改。

这显然是一个极具挑战性的目标。因为不同的区块链有不同的特性，我们的低代码平台的设计是否可以有效地应对基础设施的多样性所带来的挑战？对此我们当然是有极大的信心的，我们将通过开发一个 Demo 域名系统来证明这一点。

我们知道，之前 Move（Move 虚拟机）没有类似 Solidity（以太坊虚拟机）的 Mapping 这样的数据结构；就算接下来 Move 会支持 Table（Mapping），它也不应该被滥用。

因为对 Mapping 的滥用会造成所谓的区块链状态膨胀的问题。在 L1 链上的 Mapping 中存储大量的状态数据不是一个值得推荐的实践，它们应该存储在链下（或者说 L1 链外），但同时这些数据又需要在链上可以被验证和使用。

所以，在不使用 Table（Mapping）的前提下，在 Move 链上构建一个域名系统是一件具有相当复杂度的开发工作；接下来我们可以看到基于 DDD 和 DSL 的开发模式（低代码平台）可以如何极大地减轻开发者完成这项工作的负担。


### 如何证明

第一步，我们手动编写这个 Demo 应用的实现代码（已完成）；它们应该解耦为如下三部分：

1. 可以重用的和应用领域（域名系统）的业务逻辑无关的库。
2. 可以从（DSL 描述的）模型生成的样板代码（boilerplate code）。
3. 纯粹的业务逻辑代码。需要说明的是，这个 Demo 的业务逻辑是使用 Move 编写、在 Starcoin链（L1）上执行的，但在此基础上很有潜力演化出更复杂的实现。

第二步，我们制作代码生成工具，把上面的第 2 部分代码模板化（待完成）。然后，我们可以删除上面所说的第 2 部分代码，使用模型（加上代码模板）重新生成这部分代码，应用应该可以正常编译和运行。

需要说明的是，尽管我们还没有完成第二步，但是任何有经验的开发人员都可以通过阅读我们在第一步完成的源代码得到肯定的结论：真正的业务逻辑代码（它们确实需要开发人员编写）非常有限，源代码中的 90% 都是可以从模型生成的样板代码。

#### Demo 的源代码

源代码见这里：

* On-chain 合约: https://github.com/wubuku/Dapp-LCDP-Demo/tree/main/starcoin-contracts
* Off-chain 服务: https://github.com/wubuku/Dapp-LCDP-Demo/tree/main/starcoin-go-service


### “域名系统”的功能

为便于在有限的时间内完成概念验证，我们生造一些简单但基本可以说明问题的“域名系统”的功能需求如下：

* 支持域名的注册与续费。提交域名注册的交易需要传入域名状态的不存在证明（Non-Membership Proof）；提交续费域名的交易需要传入域名状态的存在证明（Membership Proof）。

* 只需要支持二级域名。这是为了演示实体 ID 不是一个“基本类型”，而是有两个字段的“复杂”值对象的情况。

### Demo 系统的架构

Demo 域名系统由三部分组成：

![Demo Domain Name System Architecture](https://www.dddappp.org/DemoDomainNameSysArch.jpeg)

- 客户端。客户端向链上合约提交交易前，先向链下服务请求获取实体（域名）的状态以及其状态证明。
- 链上合约。链上合约不保存所有域名的“当前状态”，只保存着所有域名状态的SMT Root。链上合约执行交易时，先验证客户端提交过来的状态和状态证明，然后再执行业务逻辑、更新 SMT Root。
- 链下服务。链下服务通过从链上拉取事件，在本地数据库中构建出所有域名的“当前状态”。任何人都可以运行链下服务的实例，且无法作恶（伪造状态信息），这就保证了系统的去中心化。

需要说明的是，Demo 系统的实现代码实际上使用了 Starcoin 二层/分层方案中提到的富状态交易（StateFullTransaction） 模式。那么，如何确定交易（Transaction）涉及的状态（State）的边界，DDD 的“聚合”概念是一个非常强大的思维武器。

### 需要开发者手动完成的工作

首先我们需要开发者使用 DSL（DDDML）描述 Demo 系统的领域模型。

这一步得到的领域模型可能如下：

```yaml
aggregates:
  DomainName:
    id:
      name: DomainNameId
      type: DomainNameId
    properties:
      ExpirationDate:
        type: u64
      Owner:
        type: AccountAddress
    methods:
      Register:
        parameters:
          Account:
            type: signer
            eventPropertyName: Owner
          RegistrationPeriod:
            type: u64
        eventName: Registered
        isCreationCommand: true
      Renew:
        parameters:
          Account:
            type: signer
          RenewPeriod:
            type: u64
        eventName: Renewed
        
valueObjects:
  DomainNameId:
    properties:
      TopLevelDomain: # TLD
        type: string
      SecondLevelDomain: # SLD
        type: string
```

- DomainNameId：一个描述域名 ID 的值对象。

- DomainName：一个表示“域名”的聚合，聚合内只有一个同名的聚合根实体（DomainName），它有两个方法：

- - Register，注册域名的方法。
- Renew，域名续费的方法。

然后，开发者需要编写链上合约中的业务逻辑代码。

这个 Demo 系统的链上合约代码的目录和文件结构如下：

```txt
./move-contracts/src/modules
├── domain-name # 领域（域名系统）相关代码
│   ├── DomainName.move # 数据模型，DomainNameId、DomainNameState 等
│   ├── DomainNameAggregate.move # “域名”聚合的粘合代码
│   ├── DomainNameRegisterLogic.move # 注册域名的业务逻辑
│   ├── DomainNameRenewLogic.move # 域名续费的业务逻辑
│   └── DomainNameScripts.move # 脚本（script）函数入口
└── smt # Sparse Merkle Tree 相关代码，用于验证交易传入的状态证明
    ├── SMTHash.move
    ├── SMTProofUtils.move
    ├── SMTProofs.move # 对证明（Proof）进行验证的方法
    ├── SMTUtils.move
    └── SMTreeHasher.move
```

这里需要特别说明的是，如果有代码生成工具，应该只有这两个文件是需要开发人员手动编写的“业务逻辑”代码：

* DomainNameRegisterLogic.move
* DomainNameRenewLogic.move

除此之外的其他代码都是可以重用的库（smt 目录中的代码），或者是可以由（DSL描述的）模型生成的代码。

我们需要开发人员手写“注册域名”的业务逻辑（Move 代码）大致如下：

```Move
address 0x18351d311d32201149a4df2a9fc2db8a {
module DomainNameRegisterLogic {
//…

public fun verify(
    account: &signer,
    _domain_name_id: &DomainName::DomainNameId,
    registration_period: u64,
): (
    address, // Owner
    u64, // RegistrationPeriod
) {
    let amount = Account::withdraw<STC::STC>(account, 1000000);
    Account::deposit(DomainName::genesis_account(), amount);
    let e_owner = Signer::address_of(account);
    let e_registration_period = registration_period;
    (e_owner, e_registration_period)
}

public fun mutate(
    domain_name_id: &DomainName::DomainNameId,
    owner: address,
    registration_period: u64,
): DomainName::DomainNameState {
    let domain_name_state = DomainName::new_domain_name_state(
        domain_name_id,
        Timestamp::now_milliseconds() + registration_period,
        owner,
    );
    domain_name_state
}
```

* verify 函数验证客户端提交的交易参数，如果验证通过，返回“事件”的属性。事件会被 DomainNameAggregate emit 出来（使用“廉价”的链上事件存储而不是昂贵的状态存储）。
* mutate 应该是个纯函数，接受事件参数，返回“修改”后的（这里指新创建的）域名状态。

需要开发人员手写“域名续费”的业务逻辑大致如下：

```Move
address 0x18351d311d32201149a4df2a9fc2db8a {
module DomainNameRenewLogic {
//…

public fun verify(
    account: &signer,
    _domain_name_state: &DomainName::DomainNameState,
    renew_period: u64,
): (
    address, // Account
    u64, // RenewPeriod
) {
    let amount = Account::withdraw<STC::STC>(account, 1000000);
    Account::deposit(DomainName::genesis_account(), amount);
    let e_account = Signer::address_of(account);
    let e_renew_period = renew_period;
    (e_account, e_renew_period)
}

public fun mutate(
    domain_name_state: &DomainName::DomainNameState,
    _account: address,
    renew_period: u64,
): DomainName::DomainNameState {
    let updated_domain_name_state = DomainName::new_domain_name_state(
        &DomainName::get_domain_name_state_domain_name_id(domain_name_state),
        DomainName::get_domain_name_state_expiration_date(domain_name_state) + renew_period,
        DomainName::get_domain_name_state_owner(domain_name_state),
    );
    updated_domain_name_state
}
```

* 与域名注册的两个函数相比这两个函数有一点区别。由于域名续费是对一个已有的域名执行续费，所以在输入参数列表中有表示旧状态的参数。
* mutate 方法接收旧状态和事件数据并返回新状态。

---

以上，就是**所有**需要手写的代码：一个模型文件、两个业务逻辑代码文件！

### 应该由工具自动完成的工作

#### 链下服务的代码

Demo 系统的链下服务的代码是使用 Go 编写的。项目的目录和文件结构见下：

```txt
./off-chain-service
├── README.md
├── client # 域名系统的 Client SDK for Go
│   ├── client.go
│   └── client_test.go # 单元测试代码
├── contract # 链上合约的查询接口
│   ├── contract.go
│   └── contract_test.go # contract 包的单元测试代码
├── db
│   ├── bcs.go # 数据模型的 BCS 序列化/反序列化代码
│   ├── db.go # 数据库常量和接口代码
│   ├── db_test.go # db 包的单元测试代码
│   ├── models.go # 数据模型，DomainNameId、DomainNameEvent 等
│   ├── mysqldb.go # 数据访问层的 MySQL 实现
│   └── smt_test.go # 关于 SMT 的单元测试代码
├── events
│   ├── events_test.go # 单元测试代码
│   ├── lib.go # 从 serde-format/events.yaml 生成的事件结构和 BCS 序列化/反序列化代码
│   └── libext.go # 对 SerdeGen 工具生成的事件代码做的一些扩展
├── go.mod
├── go.sum
├── handlers.go # 使用 Gin 实现 RESTful API 的 handlers，依赖 starcoinmanager.go
├── main.go # 链下服务的程序入口
├── manager
│   ├── starcoinmanager.go # 拉取链上事件、更新链下状态，监控和处理链的分叉等
│   └── starcoinmanager_test.go # 单元测试代码
├── serde-format
│   └── events.yaml # 描述链上事件的格式的 YAML 文档，SerdeGen 工具可使用它们生成代码
├── tools # 一些工具类代码
│   ├── restclient.go # REST client 代码
│   ├── starcoinutil.go # 对 Starcoin Go SDK 做的一些包装和扩展
│   └── util.go # 其他工具类代码
├── transactions # （改变链上状态的）链上交易相关的代码
│   ├── lib.go # （改变链上状态的）链上交易的编码方法
│   ├── transactions_test.go # 单元测试代码
│   └── util.go # 一些关于链上交易的工具代码
└── vo
    └── vo.go # RESTful API 使用的参数和返回值的类型（View Objects）
```

- 定时任务和 RESTful API 的实现可以从这里看起： starcoinmanager.go
- 仔细审视这些代码，我们可以发现：**所有**（整个链下服务的）代码其实都是可以由模型（DSL）生成的。

#### 各种客户端 SDK、前端应用以及更多

毫无疑问，我们完全可以制造自动化的工具，从 DDD 领域模型生成各种语言的 Client SDK，包括 Java Client SDK、JavaScript Client SDK、Go Client SDK，任意你能想到的编程语言的 Client SDK。

工具甚至能直接从领域模型生成有用户界面的前端应用，包括 Web 前端应用（这个在 Web 2.0 时代我们真的做过）、手机 App、命令行客户端应用等。也许你觉得这过于乐观，那么，最少你可以相信生成前端应用的脚手架代码是毫无问题的。

#### Demo 的代码行数

我们对这个 Demo 系统的代码行数做了一个粗略的统计如下：

```txt
github.com/AlDanial/cloc v 1.92  T=0.08 s (604.0 files/s, 97263.5 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
Go                              24            459            390           4374
Move                            12            255            225           1323
JSON                             3              0              0            509
Markdown                         2             58              0            211
XML                              5              0              0            118
YAML                             2              5              0             66
TOML                             2             24              0             34
-------------------------------------------------------------------------------
SUM:                            50            801            615           6635
-------------------------------------------------------------------------------
```

### Demo 的结论

通过这个 Demo，我们可以得出大致结论如下：

* Demo 揭示了我们提倡的低代码开发模式可以高效地开发运行在不同的链上的 Dapp。虽然我们是使用 Move 语言（基于 Starcoin 链）进行的验证，但说明使用 Solidity（基于以太坊或其他 EVM 兼容链）也是可行的，因为 Solidity 也同样支持结构体，以太坊同样支持事件/日志机制。也就是说，这个概念验证的 demo 所利用的 Move 语言和 Starcoin 链的特性，在以太坊上都有。
* “低代码”可以很好地屏蔽技术基础设施使用的复杂性。Demo 为了解决状态膨胀问题，把状态存储在链下（在 L1 链之外），这是一个比较复杂的架构，但是应用的开发者只需要编写业务逻辑，完全不需要感知其中的复杂性。
* “低代码”对 Dapp 开发效率的提升令人惊叹。将“低代码”开发运用在适当的地方，开发效率可以提升十倍以上。以 Demo 系统为例，整个项目的代码量有数千行，但是（等有了低代码平台的帮助后）开发人员需要手动编写的代码不过两三百行。

需要说明的是，Demo 其实没有完全展示出低代码开发的威力。我们可以借助 DSL（DDDML）的表现力，构建相当复杂的领域（对象）模型：值对象（非基本类型）嵌套值对象；包含多个（多级）实体的聚合等。在真实的“传统”应用的开发中，我们使用 DSL 构建过比 Demo 要复杂得多的聚合对象模型。


