# 开始使用 Nethereum

 这是一个具有最小依赖性的快速入门示例。 它旨在帮助新的和经验丰富的 .Net 开发人员。

这将引导您完成连接到 Infura 并从主网（公共以太坊链）检索帐户余额的步骤。

Infura 提供了一组公共节点，无需让本地或维护的客户端与主以太坊网络完全同步。

!!! 注意
    您可以在 Nethereum Playground 上找到关于同一主题的可执行代码示例：
    [Query Ether account balance using Infura](http://playground.nethereum.com/csharp/id/1001)


## 1. 安装 .Net

Nethereum 适用于 .Net Core 或 .Net Framework（最低 4.5.1 ）。 您需要安装 .Net SDK。 对于新手，我们推荐.Net core。对于 Mac 或 Linux 用户，我们必须使用 .Net Core。

不确定要下载哪个 .Net SDK？ - 选择 .Net Core 3.1。

[下载 .Net SDK](https://www.microsoft.com/net/download)

## 2. 创建你的应用

使用 .Net CLI（如下）创建项目或在 Visual Studio 中创建项目。

``` sh
dotnet new console -o NethereumSample
cd NethereumSample
```

## 3. 添加对 Nethereum.Web3 的包引用并 恢复 (更新或下载) 项目包.

``` sh
dotnet add package Nethereum.Web3
dotnet restore
```

## 4. 打开你的IDE (VS Code, Visual Studio etc)

Visual Studio Code 或 Visual Studio 都是 .Net 开发的不错选择。 其他优秀的 IDE 也可用（例如 Jet Brains Rider）。
Nethereum 的 Playground 可以帮助您无需设置即可立即开始使用，但请注意，它不是一个成熟的 IDE http://playground.nethereum.com/

现在，打开 Program.cs 文件。

## 5. 检索帐户余额的代码

Program.cs 的完整示例代码。 有关每个步骤的更完整说明，请参见下文。
``` c#
using System;
using System.Threading.Tasks;
using Nethereum.Web3;

namespace NethereumSample
{
    class Program
    {
        static void Main(string[] args)
        {
            GetAccountBalance().Wait();
            Console.ReadLine();
        }

        static async Task GetAccountBalance()
        {
var web3 = new Web3("https://mainnet.infura.io/v3/7238211010344719ad14a89db874158c");
            var balance = await web3.Eth.GetBalance.SendRequestAsync("0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae");
            Console.WriteLine($"Balance in Wei: {balance.Value}");

            var etherAmount = Web3.Convert.FromWei(balance.Value);
            Console.WriteLine($"Balance in Ether: {etherAmount}");
        }
    }
}
```

首先，我们需要引入需要的命名空间:

``` c#
using Nethereum.Web3;
```

下一步是创建一个 Web3 实例，其中包含用于主网的 infura url 和您的 INFURA api 密钥，格式为:`https://<network>.infura.io/v3/YOUR-PROJECT-ID`.

对于此示例，我们将使用的 API 密钥: `7238211010344719ad14a89db874158c`, 但是对于你自己的项目，你需要 [注册一个INFURA账户](https://infura.io/register)并创建一个你自己的Key。

``` c#
var web3 = new Web3("https://mainnet.infura.io/v3/7238211010344719ad14a89db874158c");
```

使用 Eth API，我们可以为我们选择的帐户异步执行 GetBalance 请求。 在这种情况下，我选择了以太坊基金会帐户。 `0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae`

``` c#
var balance = await web3.Eth.GetBalance.SendRequestAsync("0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae");
```

返回的金额以最低的单位 `Wei` 为单位。 我们可以使用转换实用程序将此单位转换为以太币：

``` c#
var etherAmount = Web3.Convert.FromWei(balance.Value);
```

!!! 注意
    您可以在 Nethereum Playground 上找到可执行代码示例以进一步试验，一些示例：
    - [智能合约：智能合约部署、查询、交易、Nonce、估算 Gas、Gas 价格](http://playground.nethereum.com/csharp/id/1007)
    - [链信息：使用 Infura 查询 Ether 账户余额](http://playground.nethereum.com/csharp/id/1001)
    - [为 HdWallets 生成助记符](http://playground.nethereum.com/csharp/id/1042)
    - [Azure 区块链服务：与仲裁成员节点交互](http://playground.nethereum.com/csharp/id/1046)
