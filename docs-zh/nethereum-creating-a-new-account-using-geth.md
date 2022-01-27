## 使用 Geth Personal Api 创建新帐户

关于 Nethereum 的文档您可以在以下链接查阅: <https://docs.nethereum.com>

## 前提条件:

首先，让我们下载与您的环境匹配的测试链： <https://github.com/Nethereum/Testchains>

使用 **startgeth.bat** (Windows) 或 **startgeth.sh** 启动 Geth 链（geth-clique-linux\、geth-clique-windows\ 或 geth-clique-mac\）(Mac /Linux)。 该链是通过权威证明共识(POA)建立的，并将立即开始Mining过程。

```C#
using Nethereum.Web3;
```

现在，让我们创建一个新的Web3实例

```C#
var web3 = new Web3();
```

使用 Personal API，我们现在可以使用密码请求“NewAccount”来加密帐户存储文件：

```C#
var account = await web3.Personal.NewAccount.SendRequestAsync("password");
```
