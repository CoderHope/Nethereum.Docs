# 使用 Nethereum的 Account 对象

关于 Nethereum 的文档，您可以再以下链接参阅: <https://docs.nethereum.com>

## 当你再以太坊发送一笔交易时发生了什么？

交易可以分为两类，一种是“手动”发送（不使用以太坊客户端），另一种是由以太坊客户端管理。

为了解决这两种情况，Nethereum 提供了 `account` 和 `managed account` 两个对象。

下面将演示如何 通过 Nethereum 的`managed account` 和常规`account`发送交易。

## 先决条件:

首先，让我们下载与您的环境匹配的测试链： <https://github.com/Nethereum/Testchains>

使用 **startgeth.bat** (Windows) 或 **startgeth.sh**  启动 Geth 链（geth-clique-linux\\、geth-clique-windows\\ 或 geth-clique-mac\\）(Mac /Linux)。 该链是通过权威证明共识(POA)建立的，并将立即开始Mining过程。

然后，让我们将 using 语句添加到 Nethereum.Web3。

```C#
using Nethereum.Web3;
using Nethereum.Hex.HexTypes;
using Nethereum.Web3.Accounts;
using Nethereum.Web3.Accounts.Managed;
```

现在，您可以创建一个 Web3实例:

```C#
var web3 = new Web3();
```

## 发送一笔交易

要发送交易，您需要理您的帐户并在本地签署原始交易，或者该帐户交由客户端（Parity / Geth）管理，但要求在发送交易时传入密码或事先解锁帐户。

在 Nethereum.Web3 中，为了简化流程，您可以使用 “Account”对象或“ManagedAccount”对象这两种类型的帐户类型。 两者都会存储发送交易所需的私钥或密码信息。

此时，如果使用Nethereum的 `TransactionManager`实体进行交易发送，将自动选择正确的交易方式部署合约或调用合约方法，交易签名将使用私钥或当使用`personal_sendTransaction` 方式的密码进行离线签署。


以下实例演示了如何使用:

### 使用 `account` 对象发送交易

以下是如何通过创建 `account` 对象实例来设置新帐户：

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
var account = new Account(privateKey);
```

```C#
var toAddress = "0x12890D2cce102216644c59daE5baed380d84830c";
var transaction = await web3.TransactionManager.SendTransactionAsync(account.Address, toAddress, new Nethereum.Hex.HexTypes.HexBigInteger(1));
```

!!! 注意
    Nethereum 提供了一组实用程序来促进以太坊地址管理。 您可以通过以下链接使用 Nethereum 的 Playground 直接在浏览器中探索这些实用程序：
    [Utilities: Address Utilities](http://playground.nethereum.com/csharp/id/1039)

### 使用 `managed account` 对象发送一笔交易

如前所述：Nethereum 的托管账户由以太坊客户端（geth/parity）维护，允许交易的自动签名和账户私钥的安全管理：

```C#
var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
var addressTo = "0x13f022d72158410433cbd66f5dd8bf6d2d129924";
var password = "password";
var managedAccount = new ManagedAccount(senderAddress, password);
var web3ManagedAccount = new Web3(managedAccount);
```

我们现在可以使用 `TransactionManager` 执行我们的交易，签名将由正在使用的以太坊客户端自动进行：

```C#
var transactionManagedAccount = await web3.TransactionManager.SendTransactionAsync(account.Address, addressTo, new HexBigInteger(20));
```
