# 使用 Infura 开始 Nethereum


有关 Nethereum 的文档可在以下位置找到：<https://docs.nethereum.com>

该示例将引导您完成连接到 [Infura](https://www.infura.io) 的步骤，从主网（实时以太坊）检索账户余额以及检查链 ID 和最新区块号.


INFURA 运行以太坊节点并通过 api 提供对它们的访问，以消除运行和更新您自己的基础设施的需要。

使用 INFURA 的第一步是[注册](https://infura.io/register) 并获取 API 密钥。下一步是选择要连接的以太坊网络——要么是主网，要么是 Kovan、Goerli、Rinkeby 或 Ropsten 测试网络。这两个都将用于我们用于初始化 Nethereum 的 url，格式为：`https://<network>.infura.io/v3/YOUR-PROJECT-ID`。

对于此示例，我们将使用特殊的 API 密钥`7238211010344719ad14a89db874158c`，但对于您自己的项目，您需要自己的密钥。

```C#
using Nethereum.Web3;
```
让我们用主网的 infura url 创建一个 Web3 实例。

```C#
var web3 = new Web3("https://mainnet.infura.io/v3/7238211010344719ad14a89db874158c");
```

使用 Eth API，我们可以为我们选择的帐户异步执行 GetBalance 请求。在这种情况下，所选帐户属于以太坊基金会。 “0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae”

```C#
var balance = await web3.Eth.GetBalance.SendRequestAsync("0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae");
```

返回的金额以最低的价值单位 Wei 为单位。我们可以使用转换实用程序将其转换为以太币：

```csharp
var etherAmount = Web3.Convert.FromWei(balance.Value);
```

使用 Net API，我们可以调用并找出我们连接到的网络。这将根据我们之前选择连接的网络而改变。例如，Kovan 将返回`42`，主网将返回`1`。

```C#
var networkId = await web3.Net.Version.SendRequestAsync();
```

接下来，使用 Eth API，我们将调用以获取已在该网络中挖掘的最新块。

```C#
var latestBlockNumber = await web3.Eth.Blocks.GetBlockNumber.SendRequestAsync();
var latestBlock = await web3.Eth.Blocks.GetBlockWithTransactionsHashesByNumber.SendRequestAsync(latestBlockNumber);
```

这是将 Nethereum 与 INFURA 结合使用的快速演示。使用 Infura 等托管基础设施时要知道的一件重要事情是它不存储任何私钥，因此必须在本地完成任何签名，然后将原始交易传递给服务。 Nethereum 使用 `Account` 对象使这一切变得容易。有关详细信息，请参阅[使用帐户对象](https://docs.nethereum.com/en/latest/Nethereum.Workbooks/docs/nethereum-using-account-objects/#sending-a-transaction)。

注意：如果 INFURA 的 API 和您的应用程序无法就使用的 TLS 版本达成一致，INFURA 可能会出现一些通信错误。 .Net 4.5 及更早版本将默认使用 TLS v1，如果 TLS v1.2 包含在框架中，则会停用它。 （在 .Net 4.6.* 中，v1.2 是默认设置。）
要在 4.5.2 中启用 v1.2，您可以使用：
`System.Net.ServicePointManager.SecurityProtocol |= SecurityProtocolType.Tls12;` 请注意使用 |= 打开 1.2 而不会影响其他协议（这样您仍然可以利用未来可能成为默认值的 TLS 版本.NET 更新）。
