# 使用Nethereum管理随机数(Nonces)

## 什么时随机数 (nonces)?

nonce 是交易的重要组成部分，它是地址的属性，表示该地址发送的交易数量。 随机数充当跟踪帐户发送的交易数量的计数器。

随机数有两个功能：
1- 允许选择执行交易的顺序。
2- 避免重放攻击。

在功能 1 中，nonce 可以通过简单地分配反映我们希望它们处理的顺序的nonce来选择执行事务的顺序（第一个为 0，第二个为 1，等等）。

在功能 2 中，nonce 会阻止攻击者复制我们的交易并重新发送它，直到帐户被耗尽（重放攻击）。 随机数使每笔交易都是独一无二的：只有一个交易具有特定的随机数，一旦确认就不能“重放”。

有关交易和随机数的更多详细信息，我们推荐[这篇文章](https://github.com/ethereumbook/ethereumbook/blob/develop/06transactions.asciidoc#the-transaction-nonce)
(更通俗的[Ethereum Book](https://github.com/ethereumbook/ethereumbook))

## 使用 nonce 时的常见错误

每个节点都会根据其 nonce 的值以严格的顺序处理来自特定帐户的交易，因此 nonce 值需要精确递增。

如果所有交易都来自处理帐户的单一来源/钱包，那么跟踪 nonce 很简单，但如果帐户由并发进程管理，事情可能会变得复杂。
当多个钱包处理同一个账户的交易时，可能会发生重复和缺口，导致交易被取消或推迟。

当 Geth 或 Parity 客户端更新其待处理事务队列的速度太慢时，也会发生错误。

随机数可能会出现两个主要错误：

错误 1/ 重用 nonce：如果我们从同一个账户发送两个具有相同 nonce 的交易，则两者中的一个将被拒绝。

错误 2/ 间隔: i如果我们在归因于两个连续交易的随机数之间留下一个间隔, 在执行了间隔的交易之前，不会处理最后一笔交易。

让我们举一个例子，第一笔交易的随机数为`123`，第二笔交易的随机数为`126`。 在该示例中，只有随机数为`124`和`125`的交易被发送后，才会处理随机数为`126`的交易。

## Nethereum 如何帮助管理随机数

借助`NonceService`，Nethereum 简化了 nonce 管理。
`NonceService` 跟踪待处理的交易，从而防止上面提到的错误，下面演示了如何利用它。

## 前提条件:

为了运行本文档中包含的代码，我们建议进行以下设置：
首先，从 <https://github.com/nethereum/testchains> 下载与您的环境匹配的测试链

使用 **startgeth.bat** (Windows) 或 **startgeth.sh** 启动 Geth 链（geth-clique-linux\、geth-clique-windows\ 或 geth-clique-mac\）(Mac /Linux)。 该链是通过权威证明共识(POA)建立的，并将立即开始Mining过程。

然后我们需要添加 `using` 语句：

```C#
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using Nethereum.Web3.Accounts.Managed;
using Nethereum.Signer;
using Nethereum.Hex.HexConvertors.Extensions;
using Nethereum.KeyStore;
using Nethereum.Hex.HexConvertors;
using Nethereum.Hex.HexTypes;
using Nethereum.RPC.NonceServices;
using Nethereum.RPC.TransactionReceipts;
using System.Threading.Tasks;
using Nethereum.RPC.Eth.Transactions;
using Nethereum.RPC.Eth.DTOs;
```

## 应用

在大多数情况下，Nethereum 会自动增加`nonce`（除非您需要手动签署原始交易，我们将在下一章中解释）。

将私钥加载到您的帐户后，如果使用该帐户实例化 Web3，则所有交易都将使用 `TransactionManager` 进行，合同部署或功能将使用最新的 nonce 离线签名。

例子:
这个例子展示了当我们使用 Nethereum 账户发送交易时，`nonce` 值会发生什么变化：

我们首先需要创建一个帐户的实例，然后用它来实例化一个 `web3` 对象。

让我们首先声明我们的`Account`：

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
var account = new Nethereum.Web3.Accounts.Account(privateKey);
```

* `web3` 是使用新 `account` 作为构造函数的 Web3 实例

```C#
var web3 = new Web3(account);
```

我们现在可以创建一个 NonceService 的实例来帮助我们跟踪事务。\
请注意：使用 TransactionManager 时，NonceService 会自动启动。 以下主要是为了演示。

```C#
account.NonceService = new InMemoryNonceService(account.Address, web3.Client);
```

现在让我们看看在我们发送交易之前和之后，`nonce` 值会发生什么变化：

### 在发送交易之前：

`NonceService` 跟踪所有交易，包括那些仍然未决的交易，从而可以轻松地将正确的随机数分配给即将发送的交易。

以下是如何返回我们之前声明的`Account`的当前交易数量：

```C#
var currentNonce = await web3.Eth.Transactions.GetTransactionCount.SendRequestAsync(account.Address, BlockParameter.CreatePending());
```

`actualNonce` 包括交易总数，这其中包含已提交但尚未确认的待处理交易。

也可以返回下一个需要分配给未来交易的 nonce，这个 nonce 将由 `NonceService` 使用当前 nonce 加上我们帐户发送的待处理交易来确定：

```C#
var futureNonce = await account.NonceService.GetNextNonceAsync();
```

现在，让我们发送一个简单的交易，正确的 nonce 将由 `TransactionManager` 自动分配给它：

```csharp
var recipientAddress = "0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae";
var transaction = await web3.TransactionManager.SendTransactionAsync(account.Address, recipientAddress, new HexBigInteger(1));
```

#### 交易发送后

最后，使用 NonceService，我们可以检查您的事务计数是否已变更：

```C#
currentNonce = await web3.Eth.Transactions.GetTransactionCount.SendRequestAsync(account.Address, BlockParameter.CreatePending());
```

如上面的代码所示，由于使用了 `TransactionManager`，`nonce` 会自动增加。

## 使用任意随机数发送交易

在某些情况下，我们可能想要手动提供 Nonce，例如，如果我们想要完全离线签署交易。 以下是验证帐户发送的交易数量的方法：

让我们首先创建一个 `TransactionSigner` 的对象实例

```C#
var OfflineTransactionSigner = new TransactionSigner();
```

我们现在可以为即将到来的交易声明一个代表下一个随机数的变量：

```C#
futureNonce = await account.NonceService.GetNextNonceAsync();
```

最后，让我们离线签署我们的交易：

```C#
var encoded = OfflineTransactionSigner.SignTransaction(privateKey, recipientAddress, 10,futureNonce);
```

最后，发送我们的交易：

```C#
var txId = await web3.Eth.Transactions.SendRawTransaction.SendRequestAsync("0x" + encoded);
```
