
# 测试链：入门指南

有关 Nethereum 的文档可在以下位置找到： <https://docs.nethereum.com>

区块链开发通常需要运行本地区块链客户端（AKA：TestChain）。这是为了确保您的工作保持私密，并且任何已发送的交易都能得到快速响应。

为了加快这个过程，这个 repo 包含了在几分钟内启动本地 TestChain 所需的一切。这些链中的每一个都使用 PoA（权威证明）作为更快响应的共识模型。它们都使用提供的脚本启动，自动提供帐户和密码。

首先，让我们从 <https://github.com/Nethereum/Testchains> 下载与您的环境匹配的测试链

使用 **startgeth.bat** (Windows) 或 **startgeth.sh** (Mac) 启动 Geth 链（geth-clique-linux\\、geth-clique-windows\\ 或 geth-clique-mac\\） /Linux）。该链是通过权威证明共识建立的，并将立即开始挖掘过程。

首先，我们需要为 localhost:8545 新建一个 web3 对象（这里使用 RPC）

```C#
var web3 = new Nethereum.Web3.Web3();
```

列出开发链中的所有预定义账户

```C#
var accounts = await web3.Eth.Accounts.SendRequestAsync();
```

获取默认账户的余额

```C#
var balance = await web3.Eth.GetBalance.SendRequestAsync("0x12890d2cce102216644c59dae5baed380d84830c");
```
