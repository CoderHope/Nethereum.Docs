# 在 Nethereum 中使用 Nodesmith

此示例介绍了使用 [Nodesmith](https://nodesmith.io) 连接到以太坊主网或测试网络的过程。 Nodesmith 运行以太坊节点并通过 API 提供对它们的访问，以消除运行和更新您自己的基础设施的需要。

使用 Nodesmith 的第一步是 [注册](https://nodesmith.io) 并获取 API 密钥。下一步是选择要连接的以太坊网络——要么是主网，要么是 Kovan、Goerli、Rinkeby 或 Ropsten 测试网络。这两个都将用于我们用于初始化 Nethereum 的 url，格式为：`https://ethereum.api.nodesmith.io/v1/{network_name}/jsonrpc?apiKey={api_key}`。

对于这个示例，我们将使用特殊的 API 密钥`NETHEREUM_SAMPLE`，但对于您自己的项目，您需要自己的密钥。

```C#
using Nethereum.Web3;
```

让我们首先定义我们想要连接到哪个网络并创建我们的 url。然后使用该 url 创建一个 Web3 实例。

```C#
var networkName = "kovan"; // This can be mainnet, kovan, goerli, rinkeby, or ropsten
var apiKey = "NETHEREUM_SAMPLE"; // For a real application, create your own API key
var url = string.Format("https://ethereum.api.nodesmith.io/v1/{0}/jsonrpc?apiKey={1}", networkName, apiKey);
var web3 = new Web3(url);
```

使用 Net API，我们可以调用并找出我们连接到的网络。这将根据我们之前选择连接的网络而改变。例如，Kovan 将返回 42，而主网将是 1。

```C#
var networkId = await web3.Net.Version.SendRequestAsync();
```

接下来，使用 Eth API，我们将调用以获取已在该网络中挖掘的最新块。

```C#
var latestBlockNumber = await web3.Eth.Blocks.GetBlockNumber.SendRequestAsync();
var latestBlock = await web3.Eth.Blocks.GetBlockWithTransactionsHashesByNumber.SendRequestAsync(latestBlockNumber);
```

这是一个将 Nethereum 与 Nodesmith 结合使用的快速演示。使用像 Nodesmith 这样的托管基础​​设施时要知道的一件重要事情是它不存储任何私钥，因此任何签名都必须在本地完成，然后将原始交易传递给服务。 Nethereum 使用 `Account` 对象使这一切变得容易。详情请参阅[使用账户对象](nethereum-using-account-objects/#sending-a-transaction)。
