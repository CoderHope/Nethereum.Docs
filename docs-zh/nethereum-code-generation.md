# Nethereum 代码生成

Nethereum 提供了一个代码生成器，允许您从智能合约（ABI 和 Bin）的编译输出生成 .Net 类（C#、Vb.Net 和 F#）。

核心和第一个代码生成器输出 .Net 合约定义和服务，以简化和加速使用 Nethereum 与以太坊智能合约交互的开发。 我们的路线图是能够生成用户界面、视图模型和云服务。

Nethereum 基于相同的代码生成提供不同的工具。

### 基于 Web 的代码生成

基于 Web 的代码生成 [http://codegen.nethereum.com](http://codegen.nethereum.com/) 是一个简单的在线工具，无需安装任何工具即可生成智能合约定义。 只需输入您的 Abi、字节码、智能合约名称和命名空间。	
[源码 ...](https://github.com/Nethereum/Nethereum.CodeGen.Blazor)

### VsCode Solidity 扩展集成代码生成：

[vs code solidity 扩展](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity) 可以在智能合约编译后自动生成您的合约定义，对于任何现有的智能合约或所有 当前的solidity项目工作区。

[更多信息..](nethereum-codegen-vscodesolidity.md)
	
### Nethereum 自动代码生成包

[Nethereum.Autogen.ContractApi](https://www.nuget.org/packages/Nethereum.Autogen.ContractApi/) 是一个 nuget 包，在构建项目时会自动生成你的代码，只需保存你的合约 abi 和 bin 文件 在项目的根目录。

[更多信息..](nethereum.autogen.contractapi.md)
	
### Nethereum 控制台代码生成:

nuget 工具 [Nethereum.Generator.Console](https://www.nuget.org/packages/Nethereum.Generator.Console/) 提供了一个用于代码生成的 cli 工具。 只需使用以下命令即可安装它：

 ```dotnet tool install -g Nethereum.Generator.Console```. 
	
[更多信息..](nethereum-codegen-console.md)


### Nuget 和 Npm 包.

为了将生成器集成到您自己的解决方案中，我们为 .Net 和 Javascript 提供 Nugets 和 Npm 包。

* [Nuget packages](https://www.nuget.org/packages/Nethereum.Generators/)
* [Npm package](https://www.npmjs.com/package/nethereum-codegen)


### 与生成的代码交互

下面的代码使用生成的代码将标准合约部署到测试链并调用其 Transfer 函数。（要运行代码，您需要确保您有一个测试链/节点正在运行，并且您提供了有效的帐户地址和密码）


```C#
using System;
using System.Numerics;
using System.Threading.Tasks;
using MyStandardContractProject.StandardContract.CQS;
using MyStandardContractProject.StandardContract.Service;
using Nethereum.Hex.HexTypes;
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using Nethereum.Web3.Accounts.Managed;

namespace MyStandardContractProject
{
    public class Sample
    {
        public async Task DeployAndCall()
        {
            var account = new ManagedAccount("0x12890d2cce102216644c59dae5baed380d84830c", "password");
            var web3 = new Web3(account, "http://localhost:8545");

            var deployment = new StandardContractDeployment
            {
                InitialAmount = new HexBigInteger(100),
                TokenName = "Test",
                DecimalUnits = 0,
                TokenSymbol = "T"
            };

            var svc =
                await StandardContractService.DeployContractAndGetServiceAsync(web3, deployment);

            var receipt = await svc.TransferRequestAndWaitForReceiptAsync(new TransferFunction
            {
                To = "0x13f022d72158410433cbd66f5dd8bf6d2d129924",
                Value = new BigInteger(1)
            });

            if (receipt.Status.Value == 0)
                throw new Exception("Failure - status should equal 1");
        }
    }
}
```
