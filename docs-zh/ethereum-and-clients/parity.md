## 以太坊客户端 Parity 的安装和配置

[Parity](https://github.com/paritytech/parity/wiki) 是基于 RUST 语言的以太坊客户端。.

您可以从以下位置下载 ``` Parity ``` 最新稳定版本 [Github](https://github.com/paritytech/parity/releases/latest)

## 安装

### Windows

使用 [自安装程序 InstallParity.exe](https://github.com/paritytech/parity/releases/latest)

### Mac

一个 ``` .pkg ```应用程序 可用于 [Mac OSX](https://github.com/paritytech/parity/releases/latest) .


也可以使用 [Brew](https://brew.sh/):
```shell
$ brew update
$ brew upgrade
$ brew tap paritytech/paritytech
$ brew install parity --stable
```

### Linux

在 Linux 上，``` .deb ``` 软件包可用于基于 Debian 的发行版，[可以使用源码进行自己构建](https://github.com/paritytech/parity/releases/latest)

#### 批处理文件

```powershell 
parity.exe --config node0.toml --tracing=on
```
[源码](https://github.com/Nethereum/Nethereum.Workbooks/blob/9c7088591006f9677e167722d0d2a84f61bd93cc/testchain/parity%20poa/launch.bat)

[//]: # (CJuan> I couldn't run that script, your help is welcome)

#### 脚本

确保您的脚本拥有可执行权限： ` chmod +x launch.sh `
```powershell
parity.exe --config node0.toml --tracing=on
```
您可以从它所在的目录启动脚本: ` ./launch.sh`
[源码](https://github.com/Nethereum/Nethereum.Workbooks/blob/9c7088591006f9677e167722d0d2a84f61bd93cc/testchain/parity%20poa/launch.sh)


### 权威证明（POA）

奇偶校验启动脚本中使用的共识机制称为权威证明 (PoA)。

PoA 共识是通过参考验证者列表（当它们与物理实体链接时称为权威）达成的。

由于 PoA 不需要任何挖掘，因此它是一种响应速度极快的机制（提交交易后几乎没有延迟），并且不需要操作即可开始挖掘（“挖掘”立即开始）。
