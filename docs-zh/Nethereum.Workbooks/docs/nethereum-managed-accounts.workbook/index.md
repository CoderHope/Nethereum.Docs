# 使用 Nethereum 管理账户

该文档是一个Workbook，一个可以在其中运行代码的交互式文档。 要本地运行Workbook，您可以：

* [安装运行时](https://docs.microsoft.com/en-us/xamarin/tools/workbooks/install)

* [下载本文档的原始文件](http://docs.nethereum.com/en/latest/Nethereum.Workbooks/docs/nethereum-managed-accounts.workbook/index.workbook) 

在[这里](https://github.com/Nethereum/Nethereum.Workbooks)，可以找到完整的 Nethereum Workbook

有关于Nethereum的文档，可以在以下链接查阅: <https://docs.nethereum.com>

首先，让我们下载与您的环境匹配的测试链： <https://github.com/Nethereum/Testchains>

启动Geth客户端 (geth-clique-linux\\, geth-clique-windows\\ , geth-clique-mac\\)使用  **startgeth.bat** (Windows) 或 **startgeth.sh** (Mac/Linux). 该链是通过权威证明共识(POA)建立的，并将立即开始Mining过程。

**注意:** 以下代码依赖于消息体 (在本文的上下文中，他们嵌入在 [smartContractData](./smartContractData.csx)). 如果你还不熟悉 Nethereum中的消息体, 您可以在 ["Getting started with Smart Contracts"](Nethereum.Workbooks/docs/nethereum-smartcontrats-gettingstarted.workbook)中查阅

```C#
#load "smartContractData.csx"
#r "Nethereum.Web3"
#r "Nethereum.Accounts"
```

```C#
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using Nethereum.Web3.Accounts.Managed;
using Nethereum.Hex.HexConvertors;
using Nethereum.Hex.HexTypes;
using Nethereum.ABI.FunctionEncoding.Attributes;
using Nethereum.Contracts.CQS;
using Nethereum.Util;
using Nethereum.Hex.HexConvertors.Extensions;
using Nethereum.Contracts;
using Nethereum.Contracts.Extensions;
using System.Numerics;
```

## The use for Managed accounts

在解锁账户时，客户端使用解密文件和密码来检索账户的私钥信息(如果他们存储在Keystore 文件夹内)，或者还可以使用`personal_sendTransaction`方法在发送交易时通过提供密码来完成以上操作。

解锁账户存在一定的安全性问题，所以，首选方案依旧是使用`personal_sendTransaction`方法.

Nethereum.Web3 使用 ManagedAccount 封装了此功能，管理帐户存储的帐户地址和密码信息。

实例 ManagedAccount 对象可以通过提供账户地址以及密码来非常简单的调用“sender”方法:

```C#
var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
var password = "password";
var account = new ManagedAccount(senderAddress, password);
var web3 = new Nethereum.Web3.Web3(account);
```

当与Web3结合使用时，可以像“Account”对象一样使用相同的方式进行：

### 部署一个合约

在这篇文章的上下文中，要部署的合约是 `StandardTokenDeployment`(您可以在[smartContractData](./smartContractData.csx)找到它)

```C#
var deploymentMessage = new StandardTokenDeployment
{
    TotalSupply = 100000
};
```

```C#
var deploymentHandler = web3.Eth.GetContractDeploymentHandler<StandardTokenDeployment>();
var transactionReceipt1 = await deploymentHandler.SendRequestAndWaitForReceiptAsync(deploymentMessage);
var contractAddress1 = transactionReceipt1.ContractAddress;
```

### 与合约进行交互

当我们部署了一个合约，我们可以开始与其进行交互。

#### 查询账户的余额

要检索地址的余额，我们可以创建 BalanceFunction 消息模型的实例并将参数设置为我们的帐户“地址”，因为我们是令牌的“所有者”，所以我们会查询到该合约的所有余额信息。

```C#
var balanceOfFunctionMessage = new BalanceOfFunction()
{
    Owner = account.Address,
};

var balanceHandler = web3.Eth.GetContractQueryHandler<BalanceOfFunction>();
var balance = await balanceHandler.QueryAsync<BigInteger>(contractAddress1, balanceOfFunctionMessage);
```

#### 转账

最后，让我们执行Token转账，在这种情况下，我们需要使用'TransferFunction'定义创建一个'TransactionHandler'。

在 transfer message消息实体中,我们将包含接收方地址'To'，以及要转账的金额：'TokenAmount'。

最后一步是发送请求，等待收据被“mined”并获取转账Hash值:

```C#
var receiverAddress = "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe";
var transferHandler = web3.Eth.GetContractTransactionHandler<TransferFunction>();
var transfer = new TransferFunction()
{
    To = receiverAddress,
    TokenAmount = 100
};
var transactionReceipt2 = await transferHandler.SendRequestAndWaitForReceiptAsync(contractAddress1, transfer);
var transactionHash = transactionReceipt2.TransactionHash;
```
