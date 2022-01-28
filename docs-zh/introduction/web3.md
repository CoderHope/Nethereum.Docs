# Web3

Web3 提供了一个简单的交互封装来访问以太坊客户端提供的 RPC 方法，这些方法按照相似的功能进行分类。

它还通过将合约输入/输出的 ABI 编码/解码与 Eth RPC 请求相结合，提供了一种与合约交互的简化方式。

有两种类型的 RPC 调用：

### 标准调用[Ethereum JSON RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC)

* `Eth`
* `Net`
* `Shh`

### 管理相关RPC调用.

* `Admin`
* `Personal`
* `Debug`

了解所提供的不同 RPC 方法的最佳方式是参考 [以太坊官方RPC API文档](https://github.com/ethereum/wiki/wiki/JSON-RPC) 或者 [Geth官方管理APi文档](https://github.com/ethereum/go-ethereum/wiki/Management-APIs)
所以我们不会在这里详细介绍。

## Web3 构造函数

Web3 接受以下构造函数： 
- 将用作默认 RPC 客户端的 url，或者，
- 可以是 IPC 客户端的 IClient，或者，
- 一个客户自定义的RPC客户端

无参数构造函数使用默认地址“http://localhost:8545/”，这是以太坊客户端用来接受 RPC 请求的默认端口。

### 无参数构造函数

```C#
var web3 = new Nethereum.Web3.Web3();
```

### Url 构造函数

```C#
var web3 = new Nethereum.Web3.Web3("https://myclient.com:8545");
```
### IPC 客户都构造函数

```C#
var ipcClient = new Nethereum.JsonRpc.IpcClient.IpcClient("./geth.ipc");
var web3 = new Nethereum.Web3.Web3(ipcClient);
```
## 属性/方法概述

充当主要交互点角色的 Web3 提供了2种类型的属性/方法 RPCClientWrappers 并对核心实用程序服务进行访问。

### RPCClientWrappers
RPC 客户端包装器只是以太坊客户端特定功能的通用包装器。

我们目前有：

Eth、Net、Miner、Admin、Personal 和 DebugGeth。 Eth、Net 如前所述是标准 Eth 的通用性，Miner、Admin、Personal 和 DebugGeth 
属于管理 RPC的Api。

Eth 被细分为更多的包装器，以便以更简单的方式组织不同的 RPC 调用。

```C#
web3.Eth.Transactions.GetTransactionReceipt;
web3.Eth.Transactions.Call;
web3.Eth.Transactions.EstimateGas;
web3.Eth.Transactions.GetTransactionByBlockHashAndIndex;
web3.Net.PeerCount;
web3.Eth.GetBalance;
web3.Eth.Mining.IsMining;
web3.Eth.Accounts;
```
每个对象都是一个 RPC 命令，可以执行异步操作：

```C#
await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);
```

### 实用程序
Web3 还提供了几个实用程序来简化交互。

#### 单位转换

客户通过Convert对该方法进行访问

```C#
Convert.ToWei
Convert.FromWei
```

更多示例可以在 [转换单元测试](https://github.com/Nethereum/Nethereum/blob/master/tests/Nethereum.Util.UnitTests/ConversionTests.cs) 中找到

#### 离线交易签名

"OfflineTransactionSigning" 无需直接与客户端交互即可启用交易签名、获取发件人地址或验证已签名的交易。
这非常方便，因为轻客户端可能无法存储整个链，但更愿意使用他们的私钥签署交易并将签署的原始交易广播到网络。

要“离线”签署交易，您可以使用：
```Nethereum.Web3.Accounts.AccountOfflineTransactionSigner``` 或直接``web3.Eth.TransactionManager.SignTransactionAsync```，都需要你使用一个用私钥和chainId实例化Account。

这两者都检测 ```transactionInput``` 提供的特定交易类型，并将选择特定的签名者（eip1559、legacy 等）。

#### 地址校验与格式化
还有一些实用程序可以验证和格式化地址

可以再 [编码和解码可以再这里找到测试源码](https://github.com/Nethereum/Nethereum/blob/master/src/Nethereum.ABI.Tests/AddressEncodingTests.cs)


### 对离线签名交易拦截器的交易请求

对离线签名交易拦截器的 web3 交易请求，提供了一种机制来拦截所有交易并自动离线签名它们并使用预配置的私钥发送原始交易。

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";

var web3 = new Web3();
var transactionInterceptor = new TransactionRequestToOfflineSignedTransactionInterceptor(senderAddress, privateKey, web3);
web3.Client.OverridingRequestInterceptor = transactionInterceptor;
```

拦截器需要私钥、对应的地址和 web3 的实例。 一旦配置了 web3 rpc 客户端，所有的请求都是一样的。

```C#
var txId = await web3.Eth.DeployContract.SendRequestAsync(abi, contractByteCode, senderAddress, new HexBigInteger(900000), 7);
```
