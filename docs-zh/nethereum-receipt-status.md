# 使用凭证状态


自拜占庭分叉以来，以太坊提供了一种通过检查收据“状态”来了解交易是否成功的方法。 收据状态的值可以是“0”或“1”，它们转换为：

* `0` 交易失败（无论出于何种原因）

* `1` 交易成功。

## 如何使用 Nethereum 检索交易状态

以太坊允许在通过诸如`SendTransactionAndWaitForReceiptAsync`、`SendRequestAndWaitForReceiptAsync`、`TransferRequestAndWaitForReceiptAsync`或`DeployContractAndWaitForReceiptAsync`等方法检索交易收据后检索此属性

我们将演示如何在部署合约和代币转移后使用交易“状态”。

## 前提条件:

```C#
using Nethereum.RPC.Eth.Transactions;
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
using Nethereum.RPC.Eth.DTOs;
```

我们首先需要创建一个帐户实例，然后使用它来实例化一个`web3`对象，以便与以太坊进行交互。

让我们首先声明一个 `Account`

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
var account = new Nethereum.Web3.Accounts.Account(privateKey);
```

### Web3

Web3 为以太坊客户端提供了一个简单的交互包装器。 要创建 Web3 的实例，我们需要提供我们的 Account 和 Ethereum 客户端的 RPC uri。 在这种情况下，我们将使用 Nethereum 的公共测试链 uri：“http://testchain.nethereum.com:8545”

```C#
var web3 = new Web3(account,"http://testchain.nethereum.com:8545");
```

现在让我们部署我们的合约。
在这篇文章的上下文中，要部署的合约是 `StandardTokenDeployment`：

```C#
public class StandardTokenDeployment : ContractDeploymentMessage
{

            public static string BYTECODE = "0x60606040526040516020806106f5833981016040528080519060200190919050505b80600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005081905550806000600050819055505b506106868061006f6000396000f360606040523615610074576000357c010000000000000000000000000000000000000000000000000000000090048063095ea7b31461008157806318160ddd146100b657806323b872dd146100d957806370a0823114610117578063a9059cbb14610143578063dd62ed3e1461017857610074565b61007f5b610002565b565b005b6100a060048080359060200190919080359060200190919050506101ad565b6040518082815260200191505060405180910390f35b6100c36004805050610674565b6040518082815260200191505060405180910390f35b6101016004808035906020019091908035906020019091908035906020019091905050610281565b6040518082815260200191505060405180910390f35b61012d600480803590602001909190505061048d565b6040518082815260200191505060405180910390f35b61016260048080359060200190919080359060200190919050506104cb565b6040518082815260200191505060405180910390f35b610197600480803590602001909190803590602001909190505061060b565b6040518082815260200191505060405180910390f35b600081600260005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008573ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925846040518082815260200191505060405180910390a36001905061027b565b92915050565b600081600160005060008673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561031b575081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505410155b80156103275750600082115b1561047c5781600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff168473ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a381600160005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505403925050819055506001905061048656610485565b60009050610486565b5b9392505050565b6000600160005060008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506104c6565b919050565b600081600160005060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561050c5750600082115b156105fb5781600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a36001905061060556610604565b60009050610605565b5b92915050565b6000600260005060008473ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054905061066e565b92915050565b60006000600050549050610683565b9056";

    public StandardTokenDeployment() : base(BYTECODE){}

    [Parameter("uint256", "totalSupply")]
    public BigInteger TotalSupply { get; set; }
}
```
我们将使用 Nethereum 的 StandardTokenDeployment 函数定义以 100000 的初始供应量部署此合约：

```C#
var deploymentMessage = new StandardTokenDeployment
{
    TotalSupply = 100000
};
```

部署方法会返回一个收据对象，这里是`deploymentTransactionReceipt`

```C#
var deploymentHandler = web3.Eth.GetContractDeploymentHandler<StandardTokenDeployment>();
var deploymentTransactionReceipt = await deploymentHandler.SendRequestAndWaitForReceiptAsync(deploymentMessage);
```

我们现在可以获得事务`status`的值，它将为我们提供部署事务的结果，`1`表示成功，`0`表示失败。

```C#
var deploymentValidityStatus = deploymentTransactionReceipt.Status.Value;
```

我们刚刚部署的智能合约的函数定义如下：

```C#
[Function("balanceOf", "uint256")]
public class BalanceOfFunction : FunctionMessage
{
    [Parameter("address", "_owner", 1)]
    public string Owner { get; set; }
}

[Function("transfer", "bool")]
public class TransferFunction : FunctionMessage
{
    [Parameter("address", "_to", 1)]
    public string To { get; set; }

    [Parameter("uint256", "_value", 2)]
    public BigInteger TokenAmount { get; set; }
}
```

我们还可以评估是否有任何其他交易成功，例如，我们可以评估是否已执行代币转移：

```C#
var receiverAddress = "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe";
var transferHandler = web3.Eth.GetContractTransactionHandler<TransferFunction>();
var transfer = new TransferFunction()
{
    To = receiverAddress,
    TokenAmount = 100
};
var transactionReceipt = await transferHandler.SendRequestAndWaitForReceiptAsync(deploymentTransactionReceipt.ContractAddress, transfer);
var transferValidityStatus = transactionReceipt.Status.Value;
```