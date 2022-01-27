# 管理 HD wallets


关于 Nethereum 的文档您可以在此链接查阅: <https://docs.nethereum.com>

### Hd wallet: 定义

Hd Wallet 代表 Hierarchical Deterministic Wallet，一种从单个主种子派生无限数量地址的钱包。 HD Wallet解决了用户必须生成多个帐户以及必须备份每个私钥的问题。
如果您向深入了解 Hd Wallets, 我们推荐项目[NBitcoin documentation](https://programmingblockchain.gitbook.io/programmingblockchain/key_generation/is_it_random_enough) 大部分Nethereum HD Wallet的功能都是受他们工作的启发。

这个简短的示例解释了如何：

* 创建一个 HD wallet

* 生成助记符（单词的随机序列）

* 使用一个 HD Wallet发送ETH

* 使用备份的助记词种子检索帐户
 
!!! 注意
    您可以在 Nethereum Playground 上找到有关 HD Wallet 的可执行代码示例：
    - [HD Wallets介绍](http://playground.nethereum.com/csharp/id/1043)
    - [使用索引生成 HD Wallet 账户](http://playground.nethereum.com/csharp/id/1041)
    - [为 HdWallets生成助记词](http://playground.nethereum.com/csharp/id/1042)

## 前提条件

```C#
using NBitcoin;
using Nethereum.Web3;
using Nethereum.Web3.Accounts; 
using Nethereum.Util; 
using Nethereum.Hex.HexConvertors.Extensions; 
using Nethereum.HdWallet;
using System;
```

## 创建一个 HD Wallet

创建一个 HD Wallet 需要单词列表和密码作为参数，在第一个例子中，我们将使用任意单词序列.

```C#
string Words = "ripple scissors kick mammal hire column oak again sun offer wealth tomorrow wagon turn fatal";
string Password1 = "password";
var wallet1 = new Wallet(Words, Password1);
for (int i = 0; i < 10; i++)
{
    var account = wallet1.GetAccount(i); 
    Console.WriteLine("Account index : "+ i +" - Address : "+ account.Address +" - Private key : "+ account.PrivateKey);
}
```

Hd Wallet是确定性的，它会在给定相同种子（密码+单词列表）的情况下派生相同数量的无限地址。
所有创建的帐户都可以加载到 Web3 实例中并用作任何其他帐户，例如，我们可以检查其中一个帐户的余额：

```C#
var account1 = new Wallet(Words, Password1).GetAccount(0);
```

```C#
var web3 = new Web3(account1);
var balance = await web3.Eth.GetBalance.SendRequestAsync(account1.Address);
```

## 使用 HD Wallet转账ETH

使用 HD Wallet 转账 ETh 的过程与使用独立账户相同

```C#
var toAddress = "0x13f022d72158410433cbd66f5dd8bf6d2d129924";
var transaction = await web3.Eth.GetEtherTransferService().TransferEtherAndWaitForReceiptAsync(toAddress, 2.11m, 2);
```

## 生成助记符

我们在 NBitcoin 的朋友提供了一种非常方便的方法来生成备用种子语句：

```C#
Mnemonic mnemo = new Mnemonic(Wordlist.English, WordCount.Twelve);
```

```C#
string Password2 = "password2";
var wallet2 = new Wallet(mnemo.ToString(), Password2);
var account2 = wallet2.GetAccount(0);
```

## 使用备份的助记词种子检索账户

因为HD Wallets生成地址的方式是确定性的，所以备份的助记词是一种对人类非常友好的恢复账户的一种方式，我们现在可以随时使用我们的助记词重新生成它们，并使用索引号检索它们。\
在下面的示例中，我们将使用备用种子词来检索帐户。 第一步是声明我们的单词列表：

```C#
var backupSeed = mnemo.ToString();
```

现在使用备份的助记词、密码和地址索引（\`0\`），我们可以创建 HD Wallet并检索我们的帐户。 该帐户包含用于签署我们交易的私钥。 我们可以根据同一种子的索引号生成或重新生成地址。

```C#
var wallet3 = new Wallet(backupSeed, Password2);
var recoveredAccount = wallet3.GetAccount(0);
```
