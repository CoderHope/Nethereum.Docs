# 使用 Nethereum 创建基于 Scrypt 的密钥库

本文介绍如何使用 Nethereum 创建密钥库文件。  


!!! 注意
    你还可以使用Nethereum's playground 在浏览器中运行代码，地址：
    [Key Store：使用自定义参数创建基于 Scrypt 的 KeyStore](http://playground.nethereum.com/csharp/id/1021)


定义： 密钥库是一个 JSON 编码的文件，其中包含一个（随机生成的）私钥，由密码加密以提高安全性(使用 https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#scrypt).
Keystores 是一种在本地存储私钥的标准方法，让 Geth 等客户端为您处理 privateKey/signin。
Nethereum 提供专用的“keystore”服务，以促进密钥库文件的创建和管理。

所需命名空间：
```C#
using System;
using System.Text;
using Nethereum.Hex.HexConvertors.Extensions;
using System.Threading.Tasks;
using Nethereum.Web3;
using Nethereum.KeyStore.Model;
```

### 1- 创建一个Keystore文件:

我们首先需要创建一个实例： `KeyStoreScryptService`: 
```C#
var keyStoreService = new Nethereum.KeyStore.KeyStoreScryptService();
```
然后准备所需的参数以使用 `script` 加密您的文件
```C#
var scryptParams = new ScryptParams {Dklen = 32, N = 262144, R = 1, P = 8};
```
`EthEcKey` 函数生成一个以太坊兼容的私有密钥：

```C#
var ecKey = Nethereum.Signer.EthECKey.GenerateKey();
```
我们需要生成文件的最后一个要素是密码：

```C#
var password = "testPassword";
```
我们最终可以使用我们自定义的 scrypt 参数进行加密和序列化：

```C#
var keyStore = keyStoreService.EncryptAndGenerateKeyStore(password, ecKey.GetPrivateKeyAsBytes(), ecKey.GetPublicAddress(), scryptParams);
var json = keyStoreService.SerializeKeyStoreToJson(keyStore);
```

### 2- 解密密钥

解密私钥我们使用 `DecryptKeyStoreFromJson`方法
```C#
var key = keyStoreService.DecryptKeyStoreFromJson(password, json);
```

