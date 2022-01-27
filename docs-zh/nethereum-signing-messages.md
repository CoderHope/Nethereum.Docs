# 使用 Nethereum 签署消息


## 以太坊签名基础

Nethereum 提供了以 Ethereum 兼容格式对消息进行签名的方法。 以下是使用 Nethereum 对字符串进行签名并使用各种方法验证签名的快速指南。

在以太坊上下文中，签署消息允许我们验证一条数据是否由特定帐户签署，换句话说，这是向智能合约/人类证明帐户批准消息的一种方式。

使用私钥签署消息不需要与以太坊网络进行交互。 它可以完全离线完成，因此以下代码可以在没有测试链的情况下运行。

Nethereum 提供了一个可用于对消息进行签名或验证的类：`EthereumMessageSigner`。
现在让我们探讨如何在以太坊上下文中的两个非常常见的场景中使用`EthereumMessageSigner`。

## 使用 Nethereum 签署消息并验证签名

让我们首先引用我们的程序集和命名空间：


```C#
using Nethereum.Web3;
using Nethereum.Util;
using System.Collections.Generic;
using System.Text;
using Nethereum.Signer;
using Nethereum.Hex.HexConvertors.Extensions;
using Nethereum.ABI.Encoders;
```

现在让我们声明我们将在每个示例中使用的元素：

**address** 声明签名者的账户地址：

```C#
var address = "0x12890d2cce102216644c59dae5baed380d84830c";
```

**msg1** 声明消息本身的内容，这里是一个简单的字符串：

```C#
var msg1 = "wee test message 18/09/2017 02:55PM";
```

**privatekey** 声明签名者**帐户**的私钥：

```C#
var privateKey = "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7";
```

**signer1** 创建 **EthereumMessageSigner** 对象的实例：

```C#
var signer1 = new EthereumMessageSigner();
```

### 1-使用 EncodeUTF8AndSign 对消息进行编码和签名：

签署消息时最常见的情况如下：

一条消息需要签名，它很可能是一个字符串，因此可以用 UTF8 编码然后签名，因此我们将使用 `EncodeUTF8AndSign`

`EncodeUTF8AndSign` 需要两个参数:

* 消息本身

* 签名帐户的私钥

```C#
var signature1 = signer1.EncodeUTF8AndSign(msg1, new EthECKey(privateKey));
```

### 2- 使用 EncodeUTF8AndEcRecover 验证以 UTF8 编码的签名消息：

以太坊签名验证过程与经典数字签名有点不同，这里签名验证的输出不是消息（或消息哈希）而是签名者的地址，因为地址是公钥哈希的一部分。
如果返回的地址与提供的地址相同，则验证成功，只有当签名者是帐户私钥的所有者时才会发生这种情况。

在这种情况下，**EncodeUTF8AndEcRecover** 方法用于验证以 UTF8 编码的消息的签名者地址：

**addressRec1** 评估签名者的地址，从而证明消息的有效性。

```C#
var addressRec1 = signer1.EncodeUTF8AndEcRecover(msg1, signature1);
```

### 3-使用 **HashAndSign** 对消息进行散列和签名：

在某些情况下，使用散列数据然后对其进行签名可能更有用，尤其在处理大文件时。

**HashAndSign** 使您可以一次性完成：

```C#
var msg2 = "test";
var signer2 = new EthereumMessageSigner();
var signature2 = signer2.HashAndSign(msg2,
                "0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7");
```

### 4-使用 **HashAndEcRecover** 验证散列消息：

当收到使用散列文件生成的签名时，有必要首先对我们要验证的文件进行散列处理，然后返回签名者的地址。

**HashAndEcRecover** 使您能够一步完成：

```C#
var addressRec2 = signer2.HashAndEcRecover(msg2, signature2);
```
