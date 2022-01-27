# Nuget Packages

Nethereum 提供两种类型的包。 针对`Netstandard 1.1`, `net451`, `Netstandard 2.0`, `Netcoreapp 2.1`的独立软件包，并在可能的情况下使用 net351 支持
Unity3d。 还有一个`Nethereum.Portable`库，它将所有包组合成一个可移植的库，以提供向后支持。 随着 `netstandard` 的发展和更广泛的支持，可移植库最终可能会被弃用。

所有版本都可以在 Nuget 中找到。

!!! 注意
**持续集成构建** - 如果您希望在将新功能合并到代码库后立即利用它们，或者如果您需要修复关键错误，我们邀请您试用我们持续集成源中的软件包。
只需将`https://www.myget.org/F/nethereum/api/v3/index.json`作为包源添加到 Visual Studio 或 Visual Studio for Mac中。

要将 Nuget 添加到您的项目中，您可以使用以下命令:

#### Windows 用户

对于安装主要包，你可以输入:

```
PM > Install-Package Nethereum.Web3
或
PM > Install-Package Nethereum.Portable
```

#### Windows/Mac/Linux 用户

```
dotnet add package Nethereum.Web3 
 或
dotnet add package Nethereum.Portable
``` 

## 主要库

| 项目源码                                                                                        | Nuget包                                                                                                                          | 说明                                                                                                      |
|---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Nethereum.Portable                                                                          | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.portable.svg)](https://www.nuget.org/packages/nethereum.portable) | 可移植的类库，将所有不同的库组合在一个包中                                                                                   |
| [Nethereum.Web3](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Web3)     | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.web3.svg)](https://www.nuget.org/packages/nethereum.web3)         | Ethereum Web3 类库通过 RPC 简化交互。 包括合约交互、部署、交易、编码/解码和事件过滤器                                                   |
| [Nethereum.Unity](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Unity)   |                                                                                                                                 | Unity3d 集成，库版本可以在   [Nethereum releases](https://github.com/Nethereum/Nethereum/releases) 中找到           |
| [Nethereum.Geth](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Geth)     | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.geth.svg)](https://www.nuget.org/packages/nethereum.geth)         | Nethereum.Geth 是 Geth 的扩展 Web3 库。 这包括与 Go Ethereum 客户端 (Geth) 交互的非通用 RPC API 客户端方法，例如 Admin、Debug、Miner |
| [Nethereum.Quorum](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Quorum) | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.quorum.svg)](https://www.nuget.org/packages/nethereum.quorum)     | 与 Quorum 交互的扩展，这是由 JP Morgan 创建的支持数据隐私的以太坊的许可实现                                                         |
| [Nethereum.Parity](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Parity) | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.parity.svg)](https://www.nuget.org/packages/nethereum.parity)     | Nethereum.Parity 是 Parity 的扩展 Web3 库。 包括与 Parity 交互的非通用 RPC API 客户端方法。  (WIP)                           |

## 核心库

| 项目源码                                                                                                                | Nuget包                                                                                                                                              | 说明                                                                                                                                                                                                       |
|---------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Nethereum.Generators](https://www.nuget.org/packages/Nethereum.Generators)                                         | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Generators)](https://www.nuget.org/packages/Nethereum.Generators)                     | 使用 Solidity Abi 和 Bin 生成 Nethereum 集成类的代码                                                                                                                                                                |
| [Nethereum.BlockchainProcessing](https://www.nuget.org/packages/Nethereum.BlockchainProcessing )                    | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.BlockchainProcessing)](https://www.nuget.org/packages/Nethereum.BlockchainProcessing) | Nethereum.BlockchainProcessing 以太坊区块链处理允许抓取块、交易、交易收据和日志（事件）以进行存储和/或使用自定义处理程序，如排队、搜索等                                                                                                                     |
| [Nethereum.JsonRpc.WebSocketClient](https://www.nuget.org/packages/Nethereum.JsonRpc.WebSocketClient)               | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.BlockchainProcessing)](https://www.nuget.org/packages/Nethereum.BlockchainProcessing) | Nethereum.JsonRpc WebSocket 客户端                                                                                                                                                                          |
| [Nethereum.RPC.Reactive](https://www.nuget.org/packages/Nethereum.RPC.Reactive)                                     | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.RPC.Reactive)](https://www.nuget.org/packages/Nethereum.RPC.Reactive)                 | Nethereum.RPC.Reactive、响应式客户端订阅 (WebSockets) 和 Nethereum 的 RPC 扩展                                                                                                                                        |
| [Nethereum.ENS](https://www.nuget.org/packages/Nethereum.ENS)                                                       | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.ENS)](https://www.nuget.org/packages/Nethereum.ENS)                                   | Nethereum.ENS 以太坊名称服务库                                                                                                                                                                                   |
| [Nethereum.Autogen.ContractApi](https://www.nuget.org/packages/Nethereum.Autogen.ContractApi)                       | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Autogen.ContractApi)](https://www.nuget.org/packages/Nethereum.Autogen.ContractApi)   | 为以太坊（Solidity）智能合约生成dot net代码。 在预构建期间，将根据目标项目中的 .abi 文件自动创建dotnet文件。 生成的代码语言源自项目文件扩展名（csproj、vbproj、fsproj）。 通过将 Nethereum.Generator.config 文件添加到项目的根目录，可以配置更多信息。 包括描述了 abi 合约和每个合约的代码生成选项（输出文件夹、命名空间等）。 | 
| [Nethereum.Parity.Reactive](https://www.nuget.org/packages/Nethereum.Parity.Reactive)                               | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Parity.Reactive)](https://www.nuget.org/packages/Nethereum.Parity.Reactive)           | 为以太坊（Solidity）智能合约生成dot net代码。 在预构建期间，将根据目标项目中的 .abi 文件自动创建dotnet文件。 生成的代码语言源自项目文件扩展名（csproj、vbproj、fsproj）。 通过将 Nethereum.Generator.config 文件添加到项目的根目录，可以配置更多信息。 包括描述了 abi 合约和每个合约的代码生成选项（输出文件夹、命名空间等）。 | 
| [Nethereum.TestRPCRunner](https://www.nuget.org/packages/Nethereum.TestRPCRunner)                                   | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.TestRPCRunner)](https://www.nuget.org/packages/Nethereum.TestRPCRunner)               | TestRpc 嵌入 Nethereum 以简化智能合约和以太坊集成测试                                                                                                                                                                     |
| [Nethereum.Model](https://www.nuget.org/packages/Nethereum.Model)                                                   | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Model)](https://www.nuget.org/packages/Nethereum.Model)                               | Nethereum.Model 以太坊核心模型库                                                                                                                                                                                 |  fs
| [Nethereum.Pantheon](https://www.nuget.org/packages/Nethereum.Pantheon)                                             | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Pantheon)](https://www.nuget.org/packages/Nethereum.Pantheon)                         | Nethereum.Pantheon 是 Pantheon 的扩展 Web3 库。 包括与 Java Ethereum Client (Pantheon) Admin、Debug、Miner、EEA、Clique、IBFT 交互的非通用 RPC API 客户端方法。                                                                    | 
| [Nethereum.Signer.Trezor](https://www.nuget.org/packages/Nethereum.Signer.Trezor)                                   | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Signer.Trezor)](https://www.nuget.org/packages/Nethereum.Signer.Trezor)               | Nethereum.Signer.Trezor 使用 Trezor 硬件钱包为以太坊交易和消息提供外部签名功能                                                                                                                                                  |
| [Nethereum.Signer.AzureKeyVault](https://www.nuget.org/packages/Nethereum.Signer.AzureKeyVault)                     | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.Signer.AzureKeyVault)](https://www.nuget.org/packages/Nethereum.Signer.AzureKeyVault) | Netherum.Signer.AzureKeyVault 使用 Azure Key Vault 为以太坊交易和消息提供外部签名功能                                                                                                                                       |
| [Nethereum.TestRPCRunner.Net45](https://www.nuget.org/packages/Nethereum.TestRPCRunner.Net45)                       | [![NuGet version](https://img.shields.io/nuget/vpre/Nethereum.TestRPCRunner.Net45)](https://www.nuget.org/packages/Nethereum.TestRPCRunner.Net45)   | TestRpc 嵌入 Nethereum 以简化智能合约和以太坊集成测试                                                                                                                                                                     | 
| [Nethereum.ABI](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.ABI)                               | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.abi.svg)](https://www.nuget.org/packages/nethereum.abi)                               | 以太坊合约的ABI类型、功能、事件的编码和解码                                                                                                                                                                                  |
| [Nethereum.EVM](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.EVM)                               || 以太坊虚拟机 API                                                                                                                                          |
| [Nethereum.Hex](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Hex)                               | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.hex.svg)](https://www.nuget.org/packages/nethereum.hex)                               | 提供用于编码和解码 String、BigInteger 和不同 Hex 辅助函数的方法                                                                                                                                                              |
| [Nethereum.RPC](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.RPC)                               | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.rpc.svg)](https://www.nuget.org/packages/nethereum.rpc)                               | 核心 RPC 类库通过 RCP 与以太坊客户端进行交互                                                                                                                                                                              |
| [Nethereum.JsonRpc.Client](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.JsonRpc.Client)         | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.jsonrpc.client.svg)](https://www.nuget.org/packages/nethereum.jsonrpc.client)         | 与 JsonRpc.RpcClient、JsonRpc.IpcClient 或其他自定义 Rpc 提供程序一起使用的 Nethereum JsonRpc.Client 核心库                                                                                                                  |
| [Nethereum.JsonRpc.RpcClient](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.JsonRpc.RpcClient)   | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.jsonrpc.rpcclient.svg)](https://www.nuget.org/packages/nethereum.jsonrpc.rpcclient)   | 使用 Edjcase.JsonRpc.Client 的 JsonRpc Rpc 客户端提供程序                                                                                                                                                          |
| [Nethereum JsonRpc IpcClient](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.JsonRpc.IpcClient)   | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.jsonRpc.ipcclient.svg)](https://www.nuget.org/packages/nethereum.jsonRpc.ipcclient)   | 适用于 Windows、Linux 和 Unix 的 JsonRpc IpcClient 提供程序                                                                                                                                                        |
| [Nethereum.RLP](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.RLP)                               | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.rlp.svg)](https://www.nuget.org/packages/nethereum.rlp)                               | RLP编码和解码                                                                                                                                                                                                 |
| [Nethereum.KeyStore](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.KeyStore)                     | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.keystore.svg)](https://www.nuget.org/packages/nethereum.keystore)                     | 使用 [Web3 Secret Storage 定义](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition) 的 Ethereum 密钥文件的 Keystore 生成、加密和解密                                                                     |
| [Nethereum.Signer](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Signer)                         | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.signer.svg)](https://www.nuget.org/packages/nethereum.signer)                         | 使用以太坊账户私钥对消息、RLP 和交易进行签名和验证的 Nethereum 签名者库                                                                                                                                                              |
| [Nethereum.Contracts](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Contracts)                   | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.contracts.svg)](https://www.nuget.org/packages/nethereum.contracts)                   | 通过 RPC 与以太坊中的智能合约进行交互的核心库                                                                                                                                                                                |
| [Nethereum.IntegrationTesting](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.IntegrationTesting) |                                                                                                                                                     | 集成测试模块                                                                                                                                                                                                   |
| [Nethereum.HDWallet](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.HDWallet)                     | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.HDWallet.svg)](https://www.nuget.org/packages/nethereum.HDWallet)                     | 从随机生成的种子短语生成以太坊兼容地址的 HD 树（使用 BIP32 和 BIP39）                                                                                                                                                              |
|                                                                                                                     |                                                                                                                                                     |                                                                                                                                                                                                          |

注意：IPC 支持 Windows、Unix 和 Linux，但只能使用 Nethereum.Web3，而不是 Nethereum.Portable

## 智能合约Api库

| 项目源码                                                                                                                | Nuget包                                                                                                                                                        | 说明                                                  |
|---------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------|
| [Nethereum.StandardTokenEIP20](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.StandardTokenEIP20) | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.standardtokeneip20.svg)](https://www.nuget.org/packages/nethereum.nethereum.standardtokeneip20) | Nethereum.StandardTokenEIP20 与以太坊符合EIP20协议的智能合约交互服务 |
| [Nethereum.Uport](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Uport)                           | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.uport.svg)](https://www.nuget.org/packages/nethereum.uport)                                     | Uport注册库                                            |
| [Nethereum.ENS](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.ENS)                               | [![NuGet version](https://img.shields.io/nuget/vpre/nethereum.ens.svg)](https://www.nuget.org/packages/nethereum.ens)                                         | 以太坊名称服务库(original ENS) 等待升级到最新 ENS                  |

## 工具集

| 项目源码                                                                                                              | 说明                                            |
|-------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|
| [Nethereum.Generator.Console](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Generator.Console) | 基于 abi 文件生成 Nethereum 代码的 dot net core CLI 工具 |
| [Nethereum.Console](https://github.com/Nethereum/Nethereum.Console)                                               | 与以太坊和帐户管理交互的命令行实用程序集合                         |

## 教程模块

| 项目源码                                                                                              | 说明                |
|---------------------------------------------------------------------------------------------------|-------------------|
| [Nethereum.Tutorials](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Tutorials) | 运行在 VS Studio上的教程 |

## 代码模板

| Source                                                                                                         | 说明                                                                                                                               |
|----------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| [Keystore generator](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.KeyStore.Console.Sample) | Keystore file generator                                                                                                          |
| [Faucet](https://github.com/Nethereum/Nethereum.Faucet)                                                        | 以太水龙头的 Web 应用程序模板                                                                                                                |
| [Nethereum Flappy](https://github.com/Nethereum/Nethereum.Flappy)                                              | Unity3d游戏与以太坊集成的源代码文件                                                                                                            |
| [Nethereum Game Sample](https://github.com/Nethereum/nethereum.game.sample)                                    | 示例游戏演示如何将 Nethereum 与 [UrhoSharp's SamplyGame](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) 集成以构建与以太坊交互的跨平台游戏 |
| [Nethereum UI wallet sample](https://github.com/Nethereum/nethereum.UI.wallet.sample)                          | 使用 Nethereum、Xamarin.Forms 和 MvvmCross 的跨平台钱包示例，目标：Android、iOS、Windows Mobile、桌面（windows 10 uwp）、使用 Raspberry PI 和 Xbox 的 IoT。   |



