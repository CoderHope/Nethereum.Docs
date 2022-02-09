# 设置客户端进行开发

区块链开发通常需要依赖于测试区块链，这是一个行为类似于区块链的环境，没有真正主网的限制。这些面向测试的环境称为测试网或开发链。
使用开发链使您能够：
- 工作更快（开发链使用快速共识模型）
- 工作更便宜：您不必为每笔交易支付汽油费（开发链运行在“垄断汽油”上，无需任何费用）
- 工作更安全：在本地 Devchain 的情况下，您可以将您的工作保密

根据您的用例，您可能依赖公共测试网（如 Rinkeby 或 Gorli）或本地开发链。它们之间的主要区别在于，测试网不是私有的，需要在线，而本地开发链是私有的，可以离线使用。
通常的做法是在本地开发链上开始开发，然后在测试网上部署代码以验证和测试假设。

以下是主要以太坊客户端的列表以及如何将它们设置为开发链。

## 本地开发链客户端

所有主要的以太坊客户端都可以配置为测试链。
在本地环境中工作时，我们推荐的工具是 ``` Geth Clique ```、``` Parity PoA ``` 和 ``` Ganache CLI ```

以下是最常用的以太坊客户端：
### Geth Clique

使用 Geth Clique 可以让您直接在 Geth 中测试您与智能合约的集成，并获得快速的权威证明共识。

[Geth Clique Manual](geth.md)

### Parity

将 Parity 与 PoA 共识机制结合使用会产生与使用 Geth 相同的好处。

[Parity Clique Manual](parity.md)

### Ganache-CLI

``` Ganache CLI ```（曾经的 ```Test RPC ```）是一个非常常见且有据可查的链模拟器。

Ganache 继承了 Truffle 套件的优点：主要简化了智能合约的测试、调试和更新。 Ganache 具有即时的响应时间和出色的反馈周期。
无需 GAS 成本即可部署合约并立即与它们进行交互非常棒。

[Ganache official repo](https://github.com/trufflesuite/ganache-cli)

## Debug mode

无论您使用的是链模拟器还是完整的以太坊客户端，都可以使用调试模式。

### 1 - Geth

- 使用参数 ``` debug ```

### 2 - Parity

- 使用Json Rpc的[Trace Module](https://github.com/paritytech/parity/wiki/JSONRPC-trace-module) 将允许跟踪交易

### 3 - Ganache/Testnet RPC

- 使用 ``` --debug ``` 选项将输出 VM 操作码进行调试

## Cloud private testnet

Azure BaaS（区块链即服务）允许您部署具有多个节点的测试网，并让团队参与开发。

[Azure BaaS Documentation](https://azure.microsoft.com/en-us/solutions/blockchain/) 

## 公共测试网

公共测试网的功能与主网相同，但有两个不同之处：
* 它们是免费的：交易是用毫无价值的加密货币支付的
* 它们通常比主网更敏感

主要的公共测试链有:

#### 1. Rinkeby

使用 PoA 共识机制的跨客户端测试网。.

[Rinkeby Official Site](https://www.rinkeby.io)

#### 2. Kovan

使用 PoA 共识机制的跨客户端测试网（与 Parity 和 Geth 一起工作）并特别关注垃圾邮件的弹性。  

[Kovan Official Site](https://kovan-testnet.github.io/website/)

#### 3. Ropsten

自 2017 年初被黑客入侵以来，Ropsten 不太受欢迎，但仍在使用中。

[Ropsten Github Repo](https://github.com/ethereum/ropsten)

注意：公共测试网可以通过公共节点访问，例如 [INFURA](https://www.infura.io)

#### 3. Goerli

最新的测试网:

[Goerli Official Site](https://goerli.net/)

## Ether 水龙头

测试网络中使用的以太币和代币仅用于测试目的（可以准确地与垄断票据进行比较）。它主要通过在线“水龙头”分发。

以下是测试网 Ether 资源的列表：

| Testnet Name | Faucet                                                                                                  |
|--------------|---------------------------------------------------------------------------------------------------------|
| Rinkeby      | https://www.rinkeby.io/#faucet                                                                          |
| Ropsten      | https://blog.b9lab.com/when-we-first-built-our-faucet-we-deployed-it-on-the-morden-testnet-70bfbf4e317e |
| Kovan        | Kovan 要求你向另一个人请求 KETH                                                                                   |
| Goerli       | https://goerli-faucet.slock.it/                                                                         |

有关水龙头的更多具体建议，请查看 [这里](https://medium.com/@juanfranblanco/netherum-faucet-and-nuget-templates-4a088f06933d)

## Testchains

在 Nethereum，我们开发了一种工具来简化和加速以太坊区块链的开发。 它称为 Testchains，可在几分钟内安装，并允许您使用您选择的以太坊客户端快速编码。 测试链可以在 https://github.com/Nethereum/TestChains 下载


**归功**  于 [Karl Floersch](https://karl.tech) 有关不同测试连的信息 https://karl.tech/intro-guide-to-ethereum-testnets/
