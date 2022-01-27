# 使用Nethereum估算交易成本

有关 Nethereum 的文档可在以下位置找到: <https://docs.nethereum.com>

此示例的目的是估计简单交易的 gas 成本并修改 `gas` 和 `gasprice` 的值。

## 以太坊的Gas费: 入门

Gas 是用于在以太坊中运行交易或合约的定价系统。

Gas系统与使用 kW-h 测量家庭用电量没有太大区别。 与实际能源市场的一个不同之处在于，交易的发起者设定了矿工可以接受或不接受的gas价格，
这导致了围绕gas的市场的出现。 您可以在 <https://etherscan.io/chart/gasprice> 看到 gas 价格的变动。

设置每笔交易或每份合约的 gas 价格是为了应对以太坊的图灵完备特性及其 EVM（以太坊虚拟机代码）——这个想法是为了防止无限循环。 如果账户中没有足够的以太币来执行交易或消息，那么它被认为是无效的。 这个想法是阻止来自无限循环的拒绝服务攻击，提高代码效率——并让攻击者为他们使用的资源付费，从带宽到 CPU 计算再到存储。

以下是定义交易的 **gas** 成本所需的术语：

* **Gas limit** 指您愿意在特定交易上花费的最大天然气量。

* **Gas price** 指你愿意为每单位gas支付的以太币数量，通常以“Gwei”为单位。

如果不知道他们的 gas 成本，就很难发送交易，幸运的是，以太坊提供了在发送交易之前获得 gas 估计的方法。

以下文章解释了如何通过返回估算值来预测未发送交易的成本。

##### 一个警告

由于 EVM 的图灵完备性，很容易编写函数来采用不同的代码路径，并具有截然不同的 gas 成本。 例如，一个函数可以根据某个全局状态变量的值选择不同的代码路径。 直到事务执行时间才知道函数中采用的真实代码路径。 因此，gas 估计只能给出交易实际成本的近似值。


!!! 注意
您可以通过以下链接使用 Nethereum 的 Playground 直接在浏览器中运行以下代码：[Smart Contracts: Smart Contracts Deployment, Querying, Transactions, Nonces, Estimating Gas, Gas Price](http://playground.nethereum.com/csharp/id/1007)

## 快速环境设置

```C#
using Nethereum.Web3;
using Nethereum.ABI.FunctionEncoding.Attributes;
using Nethereum.Contracts.CQS;
using Nethereum.Util;
using Nethereum.Web3.Accounts;
using Nethereum.Hex.HexConvertors.Extensions;
using Nethereum.Contracts;
using Nethereum.Contracts.Extensions;
using System.Numerics;
```

### Web3

Web3 为以太坊客户端提供了一个简单的交互封装。 要创建 Web3 的实例，我们需要提供我们的 Account 和 Ethereum 客户端的 RPC uri。
在这种情况下，我们将使用 Nethereum 的公共测试链，默认 RPC uri “http://testchain.nethereum.com:8545”

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
var account = new Nethereum.Web3.Accounts.Account(privateKey);
var web3 = new Web3(account,"http://testchain.nethereum.com:8545");
```
## 设置和部署合约
首先让我们声明我们的智能合约 BYTECODE、函数定义并部署我们的合约。 

```C#
   public static string BYTECODE = "0x60606040526040516020806106f5833981016040528080519060200190919050505b80600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005081905550806000600050819055505b506106868061006f6000396000f360606040523615610074576000357c010000000000000000000000000000000000000000000000000000000090048063095ea7b31461008157806318160ddd146100b657806323b872dd146100d957806370a0823114610117578063a9059cbb14610143578063dd62ed3e1461017857610074565b61007f5b610002565b565b005b6100a060048080359060200190919080359060200190919050506101ad565b6040518082815260200191505060405180910390f35b6100c36004805050610674565b6040518082815260200191505060405180910390f35b6101016004808035906020019091908035906020019091908035906020019091905050610281565b6040518082815260200191505060405180910390f35b61012d600480803590602001909190505061048d565b6040518082815260200191505060405180910390f35b61016260048080359060200190919080359060200190919050506104cb565b6040518082815260200191505060405180910390f35b610197600480803590602001909190803590602001909190505061060b565b6040518082815260200191505060405180910390f35b600081600260005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008573ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925846040518082815260200191505060405180910390a36001905061027b565b92915050565b600081600160005060008673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561031b575081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505410155b80156103275750600082115b1561047c5781600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff168473ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a381600160005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505403925050819055506001905061048656610485565b60009050610486565b5b9392505050565b6000600160005060008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506104c6565b919050565b600081600160005060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561050c5750600082115b156105fb5781600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a36001905061060556610604565b60009050610605565b5b92915050565b6000600260005060008473ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054905061066e565b92915050565b60006000600050549050610683565b9056";

    public StandardTokenDeployment() : base(BYTECODE){}

    [Parameter("uint256", "totalSupply")]
    public BigInteger TotalSupply { get; set; }
}

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

var deploymentMessage = new StandardTokenDeployment
{
    TotalSupply = 100000
};

var deploymentHandler = web3.Eth.GetContractDeploymentHandler<StandardTokenDeployment>();
var transactionReceipt = await deploymentHandler.SendRequestAndWaitForReceiptAsync(deploymentMessage);
string contractAddress = transactionReceipt.ContractAddress;
```

### 设置发送者地址

```C#
var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";

```

## 发送代币

进行转账会改变区块链的状态，所以在这个场景中，我们需要使用 TransferFunction 定义创建一个 TransactionHandler。

在转账消息中，我们将包含接收方地址`To`和要转账的`TokenAmount`。

最后一步是发送请求，等待交易被“打包”并包含在区块链中。

```C#
var receiverAddress = "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe";
var transferHandler = web3.Eth.GetContractTransactionHandler<TransferFunction>();
var transfer = new TransferFunction()
{
    To = receiverAddress,
    TokenAmount = 100
};
var transactionReceipt = await transferHandler.SendRequestAndWaitForReceiptAsync(contractAddress, transfer);
```

### Gas Price

如果没有使用客户端的“GasPrice”调用，Nethereum 会自动设置 GasPrice，该调用提供之前区块的平均 gas 价格。

如果您想对 GasPrice 进行更多控制，可以在 `FunctionMessages` 和 `DeploymentMessages` 中进行设置。

GasPrice 设置为以太坊中最低的单位`Wei`，因此如果我们习惯了通常的`Gwei`单位，则需要使用 Nethereum Conversion 实用程序进行转换。

```C#
  transfer.GasPrice =  Nethereum.Web3.Web3.Convert.ToWei(25, UnitConversion.EthUnit.Gwei);
```

### 估算 Gas

Nethereum 通过使用“CallInput”在内部调用`EthEstimateGas”`自动估计进行函数交易所需的总Gas。

如果需要，这可以手动完成，使用 TransactionHandler 和“transfer”交易 FunctionMessage。

```C#
 var estimate = await transferHandler.EstimateGasAsync(contractAddress, transfer);
 transfer.Gas = estimate.Value;
```

现在,交易将获得正确的`gasprice`和正确数量的`gas`:

```C#
var secondTransactionReceipt = await transferHandler.SendRequestAndWaitForReceiptAsync(contractAddress, transfer);
var receiptHash = secondTransactionReceipt.TransactionHash;
```
