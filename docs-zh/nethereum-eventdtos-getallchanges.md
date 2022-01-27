# 使用 Nethereum 设置事件轮询服务

背景:

在以太坊环境中，函数不会返回任何东西。 为了弥补这一限制，Solidity 提供了一种记录状态变化的方法，称为事件。 以下说明如何使用 Nethereum 跟踪事件。

## 前提条件:

首先，让我们下载与您的环境匹配的测试链： <https://github.com/Nethereum/Testchains>

使用 **startgeth.bat** (Windows) 或 **startgeth.sh** 启动 Geth 链（geth-clique-linux\、geth-clique-windows\ 或 geth-clique-mac\）(
Mac /Linux)。 该链是通过权威证明共识(POA)建立的，并将立即开始Mining过程。

```C#
using Nethereum.Web3;
using Nethereum.Web3.Accounts;
using System.Numerics; 
using Nethereum.RPC.Eth.DTOs;
using Nethereum.ABI.FunctionEncoding.Attributes;
```

这是我们将使用事件记录的智能合约：

![](./screenshots/testEventContract.jpg)

为了部署您的智能合约，您首先需要编译它（例如，使用 VS Code 的“Solidity”扩展），并从编译输出中获取您的 CSharp 类中需要使用的 ABI 和 ByteCode。

```C#
public static string ABI = @"[{'constant':false,'inputs':[],'name':'addBid','outputs':[],'payable':true,'stateMutability':'payable','type':'function'},{'inputs':[],'payable':true,'stateMutability':'payable','type':'constructor'},{'payable':true,'stateMutability':'payable','type':'fallback'},{'anonymous':false,'inputs':[{'indexed':true,'name':'sender','type':'address'},{'indexed':false,'name':'amount','type':'uint256'},{'indexed':false,'name':'encryptedRate','type':'string'},{'indexed':false,'name':'time','type':'uint256'}],'name':'BidAdded','type':'event'},{'anonymous':false,'inputs':[{'indexed':false,'name':'currentState','type':'uint256'},{'indexed':false,'name':'newState','type':'uint256'},{'indexed':false,'name':'time','type':'uint256'}],'name':'StateChanged','type':'event'}]";

        public static string BYTE_CODE = "0x6060604052610147806100136000396000f3006060604052600436106100405763ffffffff7c0100000000000000000000000000000000000000000000000000000000600035041663782a5c828114610042575b005b6100403373ffffffffffffffffffffffffffffffffffffffff167f0355e61ec6650dfdc23a9d941d34cabcbbab198479acc32511ce90a5192c81ac6003426040519182526040808301919091526060602083018190526007908301527f656e635261746500000000000000000000000000000000000000000000000000608083015260a0909101905180910390a27f4e783b715efde58d097be2fe2f2e2bc0bb1df07fd682aeab1d8b4dc063ffdd9a600060014260405180848152602001838152602001828152602001935050505060405180910390a15600a165627a7a7230582065aab2b2c4040d2eb012d4a2f5f1e9e209f837f718883d7728227563a005a0cb0029";
```

现在声明与“事件”日志对应的两个类，以便它们可以反序列化为 DTO（数据传输对象），

ps：VS Code 的“Solidity”扩展允许你用代码生成这些类。

```C#
[Event("BidAddedEventDTO")]
public class BidAddedEventDTO : IEventDTO
    {
        [Parameter("address", "sender", 1, true)]
        public string Sender { get; set; }

        [Parameter("uint256", "amount", 2, false)]
        public BigInteger Amount { get; set; }

        [Parameter("string", "encryptedRate", 3, false)]
        public string EncryptedRate { get; set; }

        [Parameter("uint256", "time", 4, false)]
        public BigInteger Time { get; set; }

    }

[Event("StateChangedEventDTO")]
    public class StateChangedEventDTO : IEventDTO
    {
        [Parameter("uint256", "currentState", 1, false)]
        public BigInteger CurrentState { get; set; }

        [Parameter("uint256", "newState", 2, false)]
        public BigInteger NewState { get; set; }

        [Parameter("uint256", "time", 3, false)]
        public BigInteger Time { get; set; }

    }
```

要发送交易，您需要使用该帐户的私钥创建一个新的以太坊帐户和一个 web3 实例：

```C#
var account = new Account("0xb5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7");
var web3 = new Web3(account);
```

您现在可以使用 DeployContract 方法和合约的编译版本作为有效负载来部署您的合约。

```C#
 var transactionReceipt = await web3.Eth.DeployContract.SendRequestAndWaitForReceiptAsync(ABI, BYTE_CODE, account.Address, new Nethereum.Hex.HexTypes.HexBigInteger(900000));
```

使用 transactionReceipt 返回的“contract address”参数，您现在可以声明一个指向合约对象实例的 **_contract_** 变量。

```C#
var contract  = web3.Eth.GetContract(ABI, transactionReceipt.ContractAddress);
```

现在可以使用 GetFunction 方法调用合约中的函数。

```C#
var addBidFunction = contract.GetFunction("addBid");
```

在调用以太坊函数之前，最好使用 **_EstimateGasAsync 方法_**评估调用所产生的 gas 成本。

```C#
var gas = await addBidFunction.EstimateGasAsync(account.Address,new Nethereum.Hex.HexTypes.HexBigInteger(90000), null);
```

最后，您可以调用您的函数，**_交易凭证_** 将返回有关已发送交易的信息。

```C#
var addBidReceipt = await addBidFunction.SendTransactionAndWaitForReceiptAsync(account.Address,gas, null);
```

在以太坊上调用合约函数不会返回结果。 但是您可以使用事件来记录状态更改或调用结果在我们的示例中，您可以通过调用合约函数来选择事件"BidAdded" **_
GetEvent_**。

可以针对特定的块范围或主题过滤事件日志，在示例中，我们正在创建一个过滤器，它将从添加出价到最新的 BlockNumber 返回我们最近部署的智能合约的所有事件日志。

```C#
var bidAddedEventLog = contract.GetEvent("BidAdded");
var filterInput =
                bidAddedEventLog.CreateFilterInput(new BlockParameter(addBidReceipt.BlockNumber), BlockParameter.CreateLatest());
var logs = await bidAddedEventLog.GetAllChanges<BidAddedEventDTO>(filterInput);
```

状态更改也可以这样做：

```C#
 var stateChangedEventLog = contract.GetEvent("StateChanged");
            var filterInput2 =
                stateChangedEventLog.CreateFilterInput(new BlockParameter(addBidReceipt.BlockNumber), BlockParameter.CreateLatest());
            var logs2 = await stateChangedEventLog.GetAllChanges<StateChangedEventDTO>(filterInput2);
```
