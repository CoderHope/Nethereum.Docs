# 智能合约与 Nethereum 集成的快速介绍

此示例的目标如下：

* 使用私钥创建帐户

* 部署智能合约（提供的样本是标准的 ERC20 代币合约）

* E估算合约交易的 gas 成本

* 向智能合约发送交易（本实例时对余额进行转账）

* 调用智能合约（本实例时调用合约获取账户余额方法）
        
!!! 注意 
以下文章使用代码，您可以通过以下链接使用 Nethereum 的 Playground 直接在浏览器中运行代码：
[智能合约：部署、调用（查询）、交易](http://playground.nethereum.com/csharp/id/1045)

## 前提条件:

首先，我们将添加 using 语句：

```C#
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using Nethereum.ABI.FunctionEncoding.Attributes;
using Nethereum.Contracts.CQS;
using Nethereum.Util;
using Nethereum.Hex.HexConvertors.Extensions;
using Nethereum.Contracts;
using Nethereum.Contracts.Extensions;
using Nethereum.RPC.Eth.DTOs;
```
### 实例化Web3和Account

要创建 web3 的实例，我们首先提供我们的测试链的 url 和我们帐户的私钥。

这里我们使用 http://testchain.nethereum.com:8545，这是一个Nethereum的测试链地址。

当使用一个私钥实例化的Account时，我们所有的交易都将由 Nethereum 签名。

```C#
        var url = "http://testchain.nethereum.com:8545";
        var privateKey = "0x7580e7fb49df1c861f0050fae31c2224c6aba908e116b8da44ee8cd927b990b0";
        var account = new Account(privateKey);
        var web3 = new Web3(account, url);
```

这是合约字节码（编译可执行文件）和 Abi

```C#
        var contractByteCode =
            "0x60606040526040516020806106f5833981016040528080519060200190919050505b80600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005081905550806000600050819055505b506106868061006f6000396000f360606040523615610074576000357c010000000000000000000000000000000000000000000000000000000090048063095ea7b31461008157806318160ddd146100b657806323b872dd146100d957806370a0823114610117578063a9059cbb14610143578063dd62ed3e1461017857610074565b61007f5b610002565b565b005b6100a060048080359060200190919080359060200190919050506101ad565b6040518082815260200191505060405180910390f35b6100c36004805050610674565b6040518082815260200191505060405180910390f35b6101016004808035906020019091908035906020019091908035906020019091905050610281565b6040518082815260200191505060405180910390f35b61012d600480803590602001909190505061048d565b6040518082815260200191505060405180910390f35b61016260048080359060200190919080359060200190919050506104cb565b6040518082815260200191505060405180910390f35b610197600480803590602001909190803590602001909190505061060b565b6040518082815260200191505060405180910390f35b600081600260005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008573ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925846040518082815260200191505060405180910390a36001905061027b565b92915050565b600081600160005060008673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561031b575081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505410155b80156103275750600082115b1561047c5781600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff168473ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a381600160005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600260005060008673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060003373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505403925050819055506001905061048656610485565b60009050610486565b5b9392505050565b6000600160005060008373ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000505490506104c6565b919050565b600081600160005060003373ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600050541015801561050c5750600082115b156105fb5781600160005060003373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282825054039250508190555081600160005060008573ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828282505401925050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a36001905061060556610604565b60009050610605565b5b92915050565b6000600260005060008473ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005060008373ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060005054905061066e565b92915050565b60006000600050549050610683565b9056";
        var abi =
            @"[{""constant"":false,""inputs"":[{""name"":""_spender"",""type"":""address""},{""name"":""_value"",""type"":""uint256""}],""name"":""approve"",""outputs"":[{""name"":""success"",""type"":""bool""}],""type"":""function""},{""constant"":true,""inputs"":[],""name"":""totalSupply"",""outputs"":[{""name"":""supply"",""type"":""uint256""}],""type"":""function""},{""constant"":false,""inputs"":[{""name"":""_from"",""type"":""address""},{""name"":""_to"",""type"":""address""},{""name"":""_value"",""type"":""uint256""}],""name"":""transferFrom"",""outputs"":[{""name"":""success"",""type"":""bool""}],""type"":""function""},{""constant"":true,""inputs"":[{""name"":""_owner"",""type"":""address""}],""name"":""balanceOf"",""outputs"":[{""name"":""balance"",""type"":""uint256""}],""type"":""function""},{""constant"":false,""inputs"":[{""name"":""_to"",""type"":""address""},{""name"":""_value"",""type"":""uint256""}],""name"":""transfer"",""outputs"":[{""name"":""success"",""type"":""bool""}],""type"":""function""},{""constant"":true,""inputs"":[{""name"":""_owner"",""type"":""address""},{""name"":""_spender"",""type"":""address""}],""name"":""allowance"",""outputs"":[{""name"":""remaining"",""type"":""uint256""}],""type"":""function""},{""inputs"":[{""name"":""_initialAmount"",""type"":""uint256""}],""type"":""constructor""},{""anonymous"":false,""inputs"":[{""indexed"":true,""name"":""_from"",""type"":""address""},{""indexed"":true,""name"":""_to"",""type"":""address""},{""indexed"":false,""name"":""_value"",""type"":""uint256""}],""name"":""Transfer"",""type"":""event""},{""anonymous"":false,""inputs"":[{""indexed"":true,""name"":""_owner"",""type"":""address""},{""indexed"":true,""name"":""_spender"",""type"":""address""},{""indexed"":false,""name"":""_value"",""type"":""uint256""}],""name"":""Approval"",""type"":""event""}]";
```

##### 部署智能合约

该标准 ERC20 智能合约的 Solidity 智能合约构造函数如下：

```js
//SOLIDITY: Constructor
function Standard_Token(uint256 _initialAmount) 
{         balances[msg.sender] = _initialAmount;         
         _totalSupply = _initialAmount;     
}
```
[Smart Contracts: Query ERC20 Smart contract balance](http://playground.nethereum.com/csharp/id/1005)
这意味着我们需要在部署时向构造函数提供参数

```C#
        var totalSupply = BigInteger.Parse("1000000000000000000");
        var senderAddress = account.Address;
```

当使用无类型的智能合约定义时，参数作为 params 对象数组的一部分传递，并使用完整的 json abi 进行识别和映射：

我们首先估算部署交易的成本，这样我们就可以为部署交易提供适量的gas。 提供 abi、字节码和构造函数参数（totalSuppy）。

最后我们以类似的方式发送部署交易，包括估计的Gas数量。

```C#
     var estimateGas = await web3.Eth.DeployContract.EstimateGasAsync(abi, contractByteCode, senderAddress, totalSupply);
        
        var receipt = await web3.Eth.DeployContract.SendRequestAndWaitForReceiptAsync(abi,
            contractByteCode, senderAddress, estimateGas, null, totalSupply);
        Console.WriteLine("Contract deployed at address: " + receipt.ContractAddress);
```

使用我们的合约地址，我们可以通过提供 json abi 和合约地址与合约进行交互。

```C#
        var contract = web3.Eth.GetContract(abi, receipt.ContractAddress);
```

现在合约对象使我们能够使用它们的名称检索函数：

```C#
        var transferFunction = contract.GetFunction("transfer");
        var balanceFunction = contract.GetFunction("balanceOf");
        var newAddress = "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe";
```
一个函数，有不同的方法，主要有：

* CallAsync:  您可以在其中查询智能合约的值，但不会更改区块链状态。 例如获取地址余额的GetBalance方法。
* SendTransactionAsync and SendTransactionAndWaitForReceiptAsync : 您在其中提交对更改的一些更改并创建包含在下一个块中的事务。 例如转账金额。
* EstimateGasAsync: 在链上模拟交易以计算发送交易所需的Gas量。

使用 CallAsyc，我们可以查询智能合约的值：

这时获取余额的Solidity函数

```js
//SOLIDITY: balanceOf address
 function balanceOf(address _owner) public view returns (uint256 balance) {
        return balances[_owner];
    }

```
这时C#的调用方法: 

```C#
        var balance = await balanceFunction.CallAsync<int>(newAddress);
        Console.WriteLine($"Account {newAddress} balance: {balance}");
        Console.WriteLine("Transfering 1000 tokens");
        var amountToSend = 1000;
```

发送交易会将信息提交到链上，在提交之前我们需要估算一下gas（交易成本）
以类似的方式，函数所需的任何参数都包含在方法的末尾，其顺序与solidity函数的顺序相同。

这是一个简化的 Solidity 智能合约转账

```js
function transfer(address _to, uint256 _value) public returns (bool success) {
        require(balances[msg.sender] >= _value);
        balances[msg.sender] -= _value;
        balances[_to] += _value;
        return true;
    }
```

Csharp 转账请求和Gas估算

```C#
        var gas = await transferFunction.EstimateGasAsync(senderAddress, null, null, newAddress, amountToSend);
        var receiptAmountSend =
            await transferFunction.SendTransactionAndWaitForReceiptAsync(senderAddress, gas, null, null, newAddress,
                amountToSend);

        balance = await balanceFunction.CallAsync<int>(newAddress);
        Console.WriteLine($"Account {newAddress} balance: {balance}");
```
