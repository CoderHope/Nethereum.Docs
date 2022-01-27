# 创建智能合约并将其部署到以太坊

能够与任何合约交互的第一步是将其部署到以太坊链上。

## 视频

这是两个视频，带您完成所有步骤，一个在Windows环境下使用Visual Studio ，另一个在跨平台 Mac 和 Visual Studio 中。

### Windows, Visual Studio, .Net 451 视频

该视频将引导您完成创建智能合约、编译、启动私有链并使用 Nethereum 部署它的步骤。

[![Smart contracts, private test chain and deployment to Ethereum with Nethereum](http://img.youtube.com/vi/4t5Z3eX59k4/0.jpg)](http://www.youtube.com/watch?v=4t5Z3eX59k4 "Smart contracts, private test chain and deployment to Ethereum with Nethereum")

### 跨平台、Visual Studio Code、.Net core 视频

如果您想在跨平台环境中开发，此视频将带您完成相同的步骤，但在 Mac 中,我们使用 Visual Studio Code 和 .Net Core。

[![Cross platform development in Ethereum using .Net Core and VsCode and Nethereum](http://img.youtube.com/vi/M1qKcJyQcMY/0.jpg)](http://www.youtube.com/watch?v=M1qKcJyQcMY "Cross platform development in Ethereum using .Net Core and VsCode and Nethereum")

## 测试合约

这是一个非常简单的solidity合约示例：

```solidity
    contract test {

        uint _multiplier;

        function test(uint multiplier){
             _multiplier = multiplier;
        }

        function multiply(uint a) returns(uint d) {
             return a * _multiplier;
        }
    }
```

合约被命名为“test”，它有一个以合约（类）命名的构造函数和一个函数“multiply”。 函数“multiply”在部署时将参数“a”与“multiplier”提供者的值相乘的结果返回给构造函数。

## 合约编译、字节码和 ABI

在部署合约之前，需要对其进行编译。 让我们快速了解如何使用 Visual Studio Code 执行此操作

### Visual Studio Code

1. 打开 Visual Studio Code
2. 将合约测试复制到一个新文件中并将其保存为“test.sol”，您需要打开一个文件夹作为您的工作区。
3. 如果您没有 Solidity 扩展，请在 Mac 上按 F1 或 Shift+Command+P 并键入“ext”，然后搜索“solidity”并安装它。
4. 现在已安装，您可以再次按 F1 键入“编译”并选择“编译当前合约”选项
5. 您的 ABI 和字节码文件现在可以在您的 bin 文件夹中找到。

![VsCode solidity compilation](https://raw.githubusercontent.com/Nethereum/Nethereum/master/docs/screenshots/vscode.png)

## 部署

### 解锁帐户

首先，我们使用web3.Personal.UnlockAccount解锁账户，您需要解锁您的帐户才能继续。

要解锁帐户，您需要传递地址、密码和要解锁帐户的持续时间（以秒为单位）。

```C#
   var unlockAccountResult =
        await web3.Personal.UnlockAccount.SendRequestAsync(senderAddress, password, 120);
  ```

注意 - 如果使用 Geth 1.5.9-，秒的持续时间需要转化为HexBigInteger。

  ```C#
   var unlockAccountResult =
        await web3.Personal.UnlockAccount.SendRequestAsync(senderAddress, password, new HexBigInteger(120));
  ```

### 部署事务

解锁您的帐户后，您就可以创建交易来部署它了。

要创建部署事务，您将使用 `web3.Eth.DeployContract`，使用 ABI（因为我们有一个构造函数）、字节码和构造函数的任何参数

```C#
   var transactionHash =
        await web3.Eth.DeployContract.SendRequestAsync(ABI, byteCode, senderAddress, multiplier);
```

部署交易将返回一个 transactionHash，稍后将使用它来检索交易凭证。

### 开始挖矿

已经部署合约的交易需要经过网络验证。 如果我们正在运行私有链
使用单个节点，我们将需要开启挖矿以打包交易。

PS: Nethereum 提供了一种快速简便的方法来开始您的本地测试，只需执行 startgeth.bat（适用于 Windows）或
startgeth.sh（适用于 Mac 和 Linux）：https://github.com/Nethereum/testchains

使用 startgeth.bat (Windows) 或 startgeth.sh (Mac/Linux) 启动链。 链是使用权威共识(POA)启动的，它将立即开始挖矿过程。

```C#
 var mineResult = await web3.Miner.Start.SendRequestAsync(6);
```

### 交易凭证

一旦我们开始打包过程（或者我们知道网络中有其他的矿工），我们就可以尝试检索交易凭证，我们将需要它内部包含的合约地址。

该交易可能尚未被打包，因此在尝试获取收据时，它可能会返回空值。 在在这种情况下，我们将继续尝试，直到得到非空结果。

```C#
   var receipt = await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);

    while (receipt == null)
    {
        Thread.Sleep(5000);
        receipt = await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);
    }
```

### 停止挖矿

```C#
    var mineResult = await web3.Miner.Stop.SendRequestAsync();
```

### 调用合约函数并返回一个值

一旦我们获得了收据，我们就可以检索我们新部署的合约的合约地址。 使用合约地址和 ABI 我们可以创建一个 Contract 对象的实例。

使用合约，我们可以使用函数的名称获得一个`Function`对象。

现在有了这个函数，我们将能够通过传递一个参数来调用我们的乘法函数来执行乘法。

注意：调用与交易不同，因此不会提交给网络以达成共识。 调用是一种简单的方法
从合约中检索数据或为我们执行操乘法操作。

```C#
    var contractAddress = receipt.ContractAddress;

    var contract = web3.Eth.GetContract(ABI, contractAddress);

    var multiplyFunction = contract.GetFunction("multiply");

    var result = await multiplyFunction.CallAsync<int>(7);

    Assert.Equal(49, result);
```

### 最终代码

所有源代码都可以在以下下找到 [Tutorials solution](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Tutorials)

```C#
    var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
    var password = "password";
    var ABI = @"[{""constant"":false,""inputs"":[{""name"":""val"",""type"":""int256""}],""name"":""multiply"",""outputs"":[{""name"":""d"",""type"":""int256""}],""type"":""function""},{""inputs"":[{""name"":""multiplier"",""type"":""int256""}],""type"":""constructor""}]";
    var byteCode =
        "0x60606040526040516020806052833950608060405251600081905550602b8060276000396000f3606060405260e060020a60003504631df4f1448114601a575b005b600054600435026060908152602090f3";

    var multiplier = 7;

    var web3 = new Web3.Web3();
    var unlockAccountResult =
        await web3.Personal.UnlockAccount.SendRequestAsync(senderAddress, password, 120);
    Assert.True(unlockAccountResult);

    var transactionHash =
        await web3.Eth.DeployContract.SendRequestAsync(ABI, byteCode, senderAddress, multiplier);

    var mineResult = await web3.Miner.Start.SendRequestAsync(6);

    Assert.True(mineResult);

    var receipt = await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);

    while (receipt == null)
    {
        Thread.Sleep(5000);
        receipt = await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);
    }

    mineResult = await web3.Miner.Stop.SendRequestAsync();
    Assert.True(mineResult);

    var contractAddress = receipt.ContractAddress;

    var contract = web3.Eth.GetContract(ABI, contractAddress);

    var multiplyFunction = contract.GetFunction("multiply");

    var result = await multiplyFunction.CallAsync<int>(7);

    Assert.Equal(49, result);

```
