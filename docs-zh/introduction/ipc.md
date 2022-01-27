#IPC

Nethereum 提供了一个 IPC 客户端来与其他以太坊客户端进行通信。 IPC 通信的行为方式与 RPC / HTTP 相同，唯一的变化是客户端实现。

首先，您需要安装 ``` Nethereum.JsonRpc.IpcClient ``` nuget。

这里包含两种IPC客户端，使用 NamedPipes 的 Windows 和 Unix/Linux。

## Windows 环境

在 Windows 中，我们需要使用 ```Nethereum.JsonRpc.IpcClient.IpcClient```。 新的 IpcClient 将使用 NamedPipes 文件名进行配置。 Geth 使用默认的 `geth.ipc` 和 Parity `jsonrpc.ipc`。

```C#
var client = Nethereum.JsonRpc.IpcClient.IpcClient("jsonrpc.ipc");
```

## Unix 环境

在 Unix/Linux 中，我们需要使用 ```Nethereum.JsonRpc.IpcClient.UnixIpcClient```。 新的 IpcClient 将配置 IPC 文件的完整文件路径。 在这种情况下，因为我们已经配置了 Geth 路径 `devChain`，所以会在根目录下找到该文件。

```C#
var client = Nethereum.JsonRpc.IpcClient.UnixIpcClient("/Users/juanblanco/Documents/Repos/Nethereum.Workbooks/testchain/clique/devChain/geth.ipc");
```
## 创建 Web3 实例

最后，我们可以使用新的 IPC Client 创建 Web3 的实例。

```C#
var web3 = new Nethereum.Web3.Web3(client);
```

