
## Nethereum中的Web3账户

以太坊中的每一笔交易都需要由一个账户签名后发送。该帐户需要验证（签名）以验证其 ETH的帐户持有人或打算与智能合约交互的帐户持有人是否有权限操作。

要发送交易，您需要管理您的帐户并在本地签署原始交易，也可以交由客户端（Parity / Geth）管理，但需要在发送交易时发送密码或事先解锁该帐户.

在 Nethereum.Web3 中，为了简化流程，您可以使用两种类型的帐户。 “Account”对象或“ManagedAccount”对象。两者都存储发送交易所需的私钥或密码。

在发送交易时，将选择正确的交易方式。如果使用 TransactionManager、部署合约或使用合约函数，交易将使用私钥离线签署或使用密码发送 personal_sendTransaction 消息。

### 使用帐户

使用私钥生成账户，你可以生成一个新的私钥并将其妥善保管，（该密钥与所有客户端兼容），或者从任何载体上加载一个已经存在的密钥，或者从您安装的客户端Storage文件夹加载。

针对Infura等公共节点，除了安全性(避免以纯文本形式传出密码)之外，其优势是您无需再本地安装客户端

#### 加载一个已经存在的账户

根据客户端和操作系统，可以在不同位置找到加密帐户密钥存储文件：

Geth 客户端:

 * Linux: ~/.ethereum/keystore
 * Mac: /Library/Ethereum/keystore
 * Windows: %APPDATA%/Ethereum

Parity客户端:

* Windows %APPDATA%\Roaming\Parity\Ethereum
* Mac: /Library/Application Support/io.parity.ethereum
* Linux: ~/.local/share/io.parity.ethereum

使用 net451 或更高版本时，您可以直接从文件夹加载文件：

```C#
var password = "password";
var accountFilePath = @"c:\xxx\UTC--2015-11-25T05-05-03.116905600Z--12890d2cce102216644c59dae5baed380d84830c";
var account = Account.LoadFromKeyStoreFile(accountFilePath, string password);
```
如果您的目标是其他框架，如 core 或 netstandard，为了实现主要平台兼容性，不支持直接从文件进行可移植加载，这种情况下，您需要自己加载json密钥文件并将其作为参数传递。

```C#
var password = "password";
var keyStoreEncryptedJson =
             @"{""crypto"":{""cipher"":""aes-128-ctr"",""ciphertext"":""b4f42e48903879b16239cd5508bc5278e5d3e02307deccbec25b3f5638b85f91"",""cipherparams"":{""iv"":""dc3f37d304047997aa4ef85f044feb45""},""kdf"":""scrypt"",""mac"":""ada930e08702b89c852759bac80533bd71fc4c1ef502291e802232b74bd0081a"",""kdfparams"":{""n"":65536,""r"":1,""p"":8,""dklen"":32,""salt"":""2c39648840b3a59903352b20386f8c41d5146ab88627eaed7c0f2cc8d5d95bd4""}},""id"":""19883438-6d67-4ab8-84b9-76a846ce544b"",""address"":""12890d2cce102216644c59dae5baed380d84830c"",""version"":3}";
var account = Nethereum.Web3.Accounts.Account.LoadFromKeyStore(keyStoreEncryptedJson, password);
```

#### 在 Web3 中使用帐户

当您加载您的私钥到Account后, 如果使用该账户信息实例化Web3 您将拥有所有需要转账交易的功能 TransactionManager, Contract deployment 或 Functions 都将使用上最新的随机数进行离线签名。

例如，在这种情况下，我们使用密钥库文件中的私钥创建一个帐户，并使用默认值创建一个新的 Web3 实例 "http://localhost:8545".

```C#
var password = "password";
var accountFilePath = @"c:\xxx\UTC--2015-11-25T05-05-03.116905600Z--12890d2cce102216644c59dae5baed380d84830c";
var account = Nethereum.Web3.Accounts.Account.LoadFromKeyStoreFile(accountFilePath, string password);

var web3 = new Nethereum.Web3.Web3(account);
```

现在所有这些类型的交易都将离线签署。

使用交易管理器将金额转移到另一个地址：

```C#
await web3.TransactionManager.SendTransactionAsync(account.Address, addressTo, new HexBigInteger(20));

```

部署一个合约：

```C#
web3.Eth.DeployContract.SendRequestAsync(abi, byteCode, senderAddress, new HexBigInteger(900000),
                            multiplier)
```

进行合约功能交互(transaction function )：

```C#
var multiplyFunction = contract.GetFunction("multiply");
await multiplyFunction.SendTransactionAsync(senderAddress,7);
```

#### 创建一个新帐户

要创建一个新帐户，您只需要生成一个新的私钥，Nethereum.Signer 提供了一种使用“SecureRandom”执行此操作的方法。 Account 对象只接受私钥作为构造函数，以减少与私钥生成的任何耦合，以及生成私钥的规定方式。 建议您使用比使用“SecureRandom”更复杂的随机生成器来创建您的私钥，但这对于测试来说足够了。

```C#
var ecKey = Nethereum.Signer.EthECKey.GenerateKey();
var privateKey = ecKey.GetPrivateKeyAsBytes().ToHex();
var account = new Nethereum.Accounts.Account(privateKey);
```

Nethereum.KeyStore 库允许您以与所有客户端兼容的方式加密和保存您的私钥。

### 在 Web3 中使用托管帐户

客户端使用为解密文件而提供的密码检索帐户的私钥（如果存储在其密钥库文件夹中）。 这是在解锁帐户时完成的，或者如果使用带密码的personal_sendTransaction，则仅在发送交易时完成。


将帐户解锁一段时间可能存在一定的安全问题，因此在这种情况下，首选项是使用 rpc 方法 `personal_sendTransaction`。

Nethereum.Web3 通过使用 `ManagedAccount` 来封装此功能，让托管帐户存储帐户地址和密码信息。

```C#
var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
var password = "password";

var account = new ManagedAccount(senderAddress, password);
var web3 = new Web3.Web3(account);
```

当与 Web3 结合使用时，与“Account”是相同的，您可以：

使用交易管理器将金额转移到另一个地址：

```C#
await web3.TransactionManager.SendTransactionAsync(account.Address, addressTo, new HexBigInteger(20));

```

部署一个合约:

```C#
web3.Eth.DeployContract.SendRequestAsync(abi, byteCode, senderAddress, new HexBigInteger(900000),
                            multiplier)
```

进行合约功能交互(transaction function ):

```C#
var multiplyFunction = contract.GetFunction("multiply");
await multiplyFunction.SendTransactionAsync(senderAddress,7);
```
