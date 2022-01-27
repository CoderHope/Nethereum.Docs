
# 使用Nethereum发送ETH

本文将向您展示如何使用本地以太坊节点发送ETH。

!!! 注意
    您还可以使用Nethereum's playground直接在您的浏览器中运行类似的代码。
    [Ether: Transfer Ether to an account](http://playground.nethereum.com/csharp/id/1003)
    
## 先决条件

以下说明适用于 Windows、Mac 和 Linux 操作系统。

### 安装 .NET

首先，确保您的环境设置为使用 .NET Core, 您可以在下面连接找到相关支持。[https://www.microsoft.com/net/learn/get-started](https://www.microsoft.com/net/learn/get-started)

### 创建一个应用

在一个新的文件夹下，您可以使用以下命令创建

```shell
$ dotnet create new console
```
 
您的应用程序目录现在包含一个名为：`Program.cs` 的文件，这是我们将在本教程中使用的文件。

### 安装 Nethereum 包文件
您现在可以通过安装 [Nethereum Nugets](https://www.nuget.org/packages?q=nethereum)添加到您的项目中

对于本教程，您将需要  [Nethereum Web3](https://www.nuget.org/packages/Nethereum.Web3/):

```shell
$ dotnet add package Nethereum.Web3
```

## 先决条件:

首先，让我们下载与您的环境匹配的测试链： <https://github.com/Nethereum/Testchains>

使用 **startgeth.bat** (Windows) 或 **startgeth.sh** (Mac) 启动 Geth 链（geth-clique-linux\\、geth-clique-windows\\ 或 geth-clique-mac\\） /Linux）。 该链设置了权威证明共识，并将立即开始挖掘过程。

然后，让我们在 Nethereum.Web3 中添加 using 语句。

```C#
using System;
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using Nethereum.Web3.Accounts.Managed;
using Nethereum.Hex.HexTypes;
```

## 发送一笔交易

要发送交易，我们将管理我们的帐户并在本地签署原始交易。

以下是如何通过创建 `account` 对象实例来设置新帐户：

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
var account = new Account(privateKey);
```
下一步是创建一个 Web3 实例，使用我们的 *account* 对象作为参数。

```C#
var web3 = new Web3(account);
```
我们还需要用我们的收件人地址作为值来实例化一个变量：
```C#
var toAddress = "0x13f022d72158410433cbd66f5dd8bf6d2d129924";
```

### 使用 EtherTransferService 以默认的 gas 价格和 gas 数量发送 ETH

转移ETH是最简单的链上交易。 此类交易使用默认金额 Gas 21000。
每笔交易都有每单位gas的价格，如果我们不指定gas价格，将使用客户提供的当前平均价格。

使用 EtherTransferService，我们可以简单地进行 Ether 传输，如下所示：

```C#
var transaction = await web3.Eth.GetEtherTransferService()
                .TransferEtherAndWaitForReceiptAsync(toAddress, 1.11m);
```

请注意，提供的金额以 ETH 为单位，服务将其转换为 Wei，即用于交易的最低单位价值。

###  使用EtherTransferService设置默认的Gas量，但指定Gas价格发送ETH 

我们还可以为我们的交易选择 gas 价格，在这种情况下是最后一个参数。 交易成本的价格越高，被矿工快速挑选和优先考虑的可能性就越大。

用于提供价格的单位是 Gwei，在本方案中为 2 Gwei。

```C#
var transaction = await web3.Eth.GetEtherTransferService()
                .TransferEtherAndWaitForReceiptAsync(toAddress, 1.11m, 2);
```

###  使用 EtherTransferService设置Gas费和Gas数量发送 Eth，

如果将 ETH 发送到智能合约，这可能需要比默认值更多的 gas，因为智能合约会进行进一步处理。

在这种情况下，我们可以执行以下操作：

```C#
var transaction = web3.Eth.GetEtherTransferService()
                .TransferEtherAndWaitForReceiptAsync(toAddress, 1.11m, 2, new BigInteger(25000));
```
### 使用 HD Wallets

!!! 注意
    如果你还不熟悉HD Wallets 您可以 [在此](nethereum-managing-hdwallets.md) 获取更多信息，并通过 Nethereum's playground 尝试运行理解
    [Accounts: HD Wallets (Introduction)](http://playground.nethereum.com/csharp/id/1043)

必需的命名空间:
```c#
Nethereum.Web3, Nethereum.HdWallet;
```
使用 HD Wallet 转移 ETH 的过程与使用独立帐户相同，唯一的区别在于启动 HD Wallet 并将其帐户之一加载到 Web3 实例中。
```c#
string Words = "ripple scissors kick mammal hire column oak again sun offer wealth tomorrow wagon turn fatal";
string Password = "password";
var wallet = new Wallet(Words, Password);
var account = wallet.GetAccount(0);
var toAddress = "0x13f022d72158410433cbd66f5dd8bf6d2d129924";
var web3 = new Web3(account);
var transaction = await web3.Eth.GetEtherTransferService()
                .TransferEtherAndWaitForReceiptAsync(toAddress, 1.11m, 2);
```
