## 以太坊客户端Geth的安装和配置

你可以从[Github](https://github.com/ethereum/go-ethereum/releases)下载 ``` Geth ``` 最新的稳定版本。

## 安装

### Windows

再Windows上,  安装``` Geth ```就像从您选择的操作系统中提取 geth.exe 一样简单。
下载页面提供了一个安装程序以及一个 zip 文件。

安装程序会自动将 geth 放入您的 PATH 中。 zip 文件包含命令 .exe 文件，无需安装即可使用。

- 下载压缩文件
- 解压压缩文件获取geth.exe
- 打开命令提示符
- 运行 geth.exe

### Mac

Mac上建议使用[Brew](https://brew.sh/) 安装``` Geth ```
```shell
$ brew update
$ brew upgrade
$ brew tap ethereum/ethereum
$ brew install ethereum
```

### Linux

Mac上建议使用```apt```安装``` Geth ```

```shell
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install ethereum
```

### RPC / IPC 选项

这里有几个命令选项 [可以再这个文档](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options) 来运行Geth 

但最重要的是，需要启用 RPC 或 IPC。

HTTP JSON-RPC 可以使用 ``` --rpc ``` 标志启动

```shell
$ geth --rpc
```
可以更改默认端口（```8545 ```）以及监听地址（``` localhost ```）。

```shell
$ geth --rpc --rpcaddr <ip> --rpcport <portnumber>
```
如果从浏览器访问 RPC，则需要使用适当的域集启用 ``` CORS ```。 否则，JavaScript 调用受同源策略限制，请求将失败：

```shell
$ geth --rpc --rpccorsdomain "http://localhost:3000"
```
JSON RPC 也可以使用 ```admin.startRPC(addr, port) ``` 命令从 geth 控制台启动。


## 快熟开始

您可以考虑使用我们的 [使用 Geth Personal Api 创建新帐户](./nethereum-creating-a-new-account-using-geth.md) 文档来初步体验使用 Nethereum 与 Geth 进行交互。

### 建立自己的测试网

Nethereum 中已经有一个预配置的 tesnet，[可以从 github 下载](https://github.com/Nethereum/Nethereum/tree/master/testchain/clique)

预配置的测试网将立即开始生成块，因此无需手动启动，有关更多信息，请查看下面的权威证明部分。

“devChain”文件夹中的链密钥库包含预配置帐户的密钥，该密钥也存在于创世文件“genesis_dev.json”中。

* Account : ``` 0x12890d2cce102216644c59daE5baed380d84830c ```
* Password: ``` password ```
* Private Key: ``` 0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7 ```

### 权威证明(POA)

该测试链中使用的共识机制是权威证明（PoA）

PoA 共识是通过参考验证者列表（当它们与物理实体链接时称为权威）达成的。

它不依赖于节点解决任意困难的数学问题，而是使用一组“权威”——明确允许创建新块并保护区块链的节点。 该链必须由大多数权威人签署，在这种情况下，它成为永久记录的一部分。 这使得维护私有链和保持区块发行者的责任变得更加容易。

对于联盟设置，与 PoW 相比，PoA 网络没有缺点。 它更安全（因为具有不需要的连接或被黑权限的攻击者无法压倒可能恢复所有交易的网络），计算密集度更低（提供安全性的挖矿难度需要大量计算），性能更高（Aura 共识提供较低的交易接受度 延迟）和更可预测（以稳定的时间间隔发布块）。 企业和公众使用 PoA 部署（例如流行的 Kovan 测试网络）。

当前的测试链已配置为立即生成块，它只有一个节点和一个验证者帐户，在启动 geth 时会解锁。 ```--unlock 0x12890d2cce102216644c59daE5baed380d84830c --password "pass.txt"```

要启动链，您可以使用批处理文件或 shell 脚本，它们都会在启动时重置所有数据。

#### 批处理文件

```shell
RD /S /Q %~dp0\devChain\geth\chainData
RD /S /Q %~dp0\devChain\geth\dapp
RD /S /Q %~dp0\devChain\geth\nodes
del %~dp0\devchain\geth\nodekey

geth  --datadir=devChain init genesis_clique.json
geth --nodiscover --rpc --datadir=devChain  --rpccorsdomain "*" --mine --rpcapi "eth,web3,personal,net,miner,admin,debug" --unlock 0x12890d2cce102216644c59daE5baed380d84830c --password "pass.txt" --verbosity 0 console

```
[源码](https://github.com/Nethereum/Nethereum/edit/master/testchain/clique/startgeth.bat)

#### Shell script

确保使您的脚本可执行: ` chmod +x startgeth.sh `
你可以再脚本所在目录启动脚本: ` ./startgeth.sh `
```shell
rm -rf devChain/chainData
rm -rf devChain/dapp
rm -rf devChain/nodes
rm -rf devchain/nodekey

geth  --datadir=devChain init genesis_clique.json
geth --nodiscover --rpc --datadir=devChain  --rpccorsdomain "*" --mine --rpcapi "eth,web3,personal,net,miner,admin,debug" --unlock 0x12890d2cce102216644c59daE5baed380d84830c --password "pass.txt" --verbosity 0 console

```
[源码地址](https://github.com/Nethereum/Nethereum/edit/master/testchain/clique/startgeth.sh)


### 其他信息
如果您需要有关如何设置链的更多信息，可以使用此博客文章
[http://juan.blanco.ws/setup-your-own-tesnet-ethereum/](http://juan.blanco.ws/setup-your-own-tesnet-ethereum/)

 [Parity](https://github.com/paritytech/parity/wiki/Proof-of-Authority-Chains) 和 [Geth](https://github.com/ethereum/EIPs/issues/225) 中的权威证明

