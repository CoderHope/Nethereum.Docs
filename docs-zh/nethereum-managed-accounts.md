# 使用 Nethereum 管理账户

关于 Nethereum 的文档，您可以再以下链接查阅: <https://docs.nethereum.com>

**注意:** 以下代码依赖于消息。 如果您不熟悉 Nethereum 中消息的使用，请参阅 ["智能合约入门"](Nethereum.Workbooks/docs/nethereum-smartcontrats-gettingstarted.workbook)


## Managed accounts用途

客户端使用为解密文件而提供的密码检索帐户的私钥（如果存储在其密钥库文件夹中）。 这是在解锁帐户时完成的，或者如果使用带密码的 `personal_sendTransaction`方法，则仅在发送交易时完成。

解锁账户会有一定的安全问题，因此我们推荐使用 rpc 方法 `personal_sendTransaction`。

Nethereum.Web3 使用 ManagedAccount 封装了此功能，管理和存储帐户地址和密码信息。

可以使用`sender`帐户公共地址及其密码简单地声明实例“ManagedAccount”对象。

本文介绍了如何使用Managed accounts来完善私钥管理和提升交易安全。

## 快速环境设置

```C#
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using Nethereum.Web3.Accounts.Managed;
using Nethereum.Hex.HexTypes;
using Nethereum.Hex.HexConvertors;
using Nethereum.Hex.HexConvertors.Extensions;
using Nethereum.ABI.FunctionEncoding.Attributes;
using Nethereum.Contracts.CQS;
using Nethereum.Util;
using Nethereum.Contracts;
using Nethereum.Contracts.Extensions;
using System.Numerics;
```

### Web3

Web3 为以太坊客户端提供了一个简单的交互封装。 要创建 Web3 的实例，我们需要提供我们的 Account 和 Ethereum 客户端的 RPC uri。 在这种情况下，我们建议安装一个 [Testchains](https://github.com/Nethereum/TestChains) 这是一个建立在POA共识机制下交易非常迅速的选择.  

```C#
var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
var password = "password";
var account = new ManagedAccount(senderAddress, password);
var web3 = new Nethereum.Web3.Web3(account. "http://127.0.0.1:8545");
```

当与Web3结合使用"Account"时，您可以:

### 部署一个合约

在这篇文章的上下文中，要部署的合约是 `StandardTokenDeployment`


```C#
public class StandardTokenDeployment : ContractDeploymentMessage
{

            public static string BYTECODE = "0x60606040526040516020806106f5833981016040528080519060200190919050505b80600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005081905550806000600050819055505b506106868061006f6000396000f360606040523615610074576000357c010000000000000000000000000000000000000000000000000000000090048063095ea7b31461008157806318160ddd146100b657806323b872dd146100d957806370a0823114610117578063a9059cbb14610143578063dd62ed3e1461017857610074565b61007f5b610002565b565b005b6100a060048080359060200190919080359060200190919050506101ad565b6040518082815260200191505060405180910390f35b6100c36004805050610674565b6040518082815260200191505060405180910390f35b6101016004808035906020019091908035906020019091908035906020019091905050610281565b6040518082815260200191505060405180910390f35b61012d600480803590602001909190505061048d565b6040518082815260200191505060405180910390f35b61016260048080359060200190919080359060200190919050506104cb565b6040518082815260200191505060405180910390f35b610197600480803590602001909190803590602001909190505061060b565b6040518082815260200191505060405180910390f35b600081600260005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008573ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925846040518082815260200191505060405180910390a36001905061027b565b92915050565b600081600160005060008673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561031b575081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505410155b80156103275750600082115b1561047c5781600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff168473ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a381600160005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505403925050819055506001905061048656610485565b60009050610486565b5b9392505050565b6000600160005060008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506104c6565b919050565b600081600160005060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561050c5750600082115b156105fb5781600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a36001905061060556610604565b60009050610605565b5b92915050565b6000600260005060008473ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054905061066e565b92915050565b60006000600050549050610683565b9056";

    public StandardTokenDeployment() : base(BYTECODE){}

    [Parameter("uint256", "totalSupply")]
    public BigInteger TotalSupply { get; set; }
}

```

现在让我们定义我们将在本教程中使用的函数消息:


### 部署消息实体

```C#
var deploymentMessage = new StandardTokenDeployment
{
    TotalSupply = 100000
};
```

### 余额消息实体

```C#
[Function("balanceOf", "uint256")]
public class BalanceOfFunction : FunctionMessage
{
    [Parameter("address", "_owner", 1)]
    public string Owner { get; set; }
}

### 转账消息实体

[Function("transfer", "bool")]
public class TransferFunction : FunctionMessage
{
    [Parameter("address", "_to", 1)]
    public string To { get; set; }

    [Parameter("uint256", "_value", 2)]
    public BigInteger TokenAmount { get; set; }
}
```

### 合约部署

```C#
var deploymentHandler = web3.Eth.GetContractDeploymentHandler<StandardTokenDeployment>();
var transactionReceipt1 = await deploymentHandler.SendRequestAndWaitForReceiptAsync(deploymentMessage);
var contractAddress1 = transactionReceipt1.ContractAddress;
```

### 与合约交互

一旦我们部署了合约，我们就可以开始与它进行交互。.

#### 查询账户余额

要检索地址的余额，我们可以创建 BalanceFunction 消息的实例并将参数设置为我们的帐户“地址”，因为我们是令牌的“所有者”，所以我们查询得到所有合约余额数量。

```C#
var balanceOfFunctionMessage = new BalanceOfFunction()
{
    Owner = account.Address,
};

var balanceHandler = web3.Eth.GetContractQueryHandler<BalanceOfFunction>();
var balance = await balanceHandler.QueryAsync<BigInteger>(contractAddress1, balanceOfFunctionMessage);
```

#### 转账

最后让我们执行令牌转账。 在这种情况下，我们需要使用 TransferFunction 定义创建一个 TransactionHandler。

在转账消息中，我们将包含接收方地址“To”，以及要转账的“TokenAmount”。

最后一步是发送请求，等待收据被“Mined”并获取其Hash地址：

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
