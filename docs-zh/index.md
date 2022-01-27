# 什么是 Nethereum ?
Nethereum 是以太坊的 .Net 集成库，简化了智能合约管理以及与以太坊节点的交互，
无论是与开源的 [ Geth ](https://geth.ethereum.org/), [Parity](https://www.parity.io/) 钱包客户端还是闭源的 [Quorum](https://www.jpmorgan.com/global/Quorum) 和 [Besu](https://besu.hyperledger.org/en/stable/) 客户端.

Nethereum 建立在针对 netstandard 1.1、net451 以及可移植库进行开发，因此它与所有主要操作系统（Windows、Linux、macOS、Android 和 iOS）兼容，并已在云、移动、桌面、Xbox、 hololens 和 Windows Iot进行过测试。

即将发布的版本将与 Ethereum 2.0 兼容（当 Ethereum 2.0 发布时），并包括  [DevP2P](https://github.com/ethereum/devp2p)、[Plasma](https://plasma.io/plasma.pdf) 和 Micro-Payments 等功能。
## 支持的功能

* JSON RPC / IPC 以太坊核心方法。
* Geth 管理 API (admin, personal, debugging, miner).
* [Parity](https://www.parity.io/) 管理Api.
* [Quorum](nethereum-azure-quorum.md) 一体化.
* [Besu](https://besu.hyperledger.org/en/stable/) .
* 用于部署、函数调用、事务和事件过滤以及主题解码的简化智能合约交互。
* [Unity 3d](unity3d-introduction.md) Unity 一体化.
* [Blockchain processing](nethereum-block-processing-detail.md).  
* ABI 到 .Net 类型的编码和解码，包括基于属性的复杂对象反序列化 (nethereum-abi-encoding.md)。
* [Hd Wallet](nethereum-managing-hdwallets.md) 创建和管理.
* [Rules engine](wonka.md).
* [HD Wallet integration](nethereum-managing-hdwallets.md).
* 交易、RLP 和消息签名、帐户验证和恢复。
* 标准合约代币库， [ENS](https://ens.domains/) 和 [Uport](https://www.uport.me/)
* 集成 TestRPC 测试以简化 TDD 和 BDD (Specflow) 开发。
* 密钥存储采用 Web3 存储标准，兼容 Geth 和 Parity。
* 简化由第三方客户（个人）管理或独立（签署交易）管理的帐户生命周期。
* RPC 调用的低级拦截。
* 智能合约服务的代码生成。
