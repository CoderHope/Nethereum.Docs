# 调用,交易,事件, 筛选器和主题

之前的指南介绍了如何部署和调用合约，本指南将深入探讨调用合约、交易、事件和主题。

## 视频

这个演示视频涵盖了本指南中 调用、事务、事件、过滤器和主题所需要的步骤

[![Introduction to Calls, Transactions, Events, Filters and Topics](http://img.youtube.com/vi/Yir_nu5mmw8/0.jpg)](https://www.youtube.com/watch?v=Yir_nu5mmw8 "Introduction to Calls, Transactions, Events, Filters and Topics")

## 测试合约

以下智能合约是上一指南中“乘法”合约的更新版本：

```solidity
contract test {

    int _multiplier;
    event Multiplied(int indexed a, address indexed sender, int result );

    function test(int multiplier) {
        _multiplier = multiplier;
    }

    function multiply(int a) returns (int r) {
       r = a * _multiplier;
       Multiplied(a, msg.sender, r);
       return r;
    }
 }
```
智能合约现在包含一个名为“Multiplied”的事件。 该事件将在日志中存储乘法“a”的原始参数、“发送者”的地址和乘法的“结果”。
参数“a”和“发件人”地址都被索引，因此我们可以使用Topic为这两个值创建特定的过滤器。

## 部署合约

根据之前的指南，我们可以按如下方式部署合约：

```C#
    var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
    var password = "password";

    var abi = @"[{'constant':false,'inputs':[{'name':'a','type':'int256'}],'name':'multiply','outputs':[{'name':'r','type':'int256'}],'type':'function'},{'inputs':[{'name':'multiplier','type':'int256'}],'type':'constructor'},{'anonymous':false,'inputs':[{'indexed':true,'name':'a','type':'int256'},{'indexed':true,'name':'sender','type':'address'},{'indexed':false,'name':'result','type':'int256'}],'name':'Multiplied','type':'event'}]";

    var byteCode = "0x6060604052604051602080610104833981016040528080519060200190919050505b806000600050819055505b5060ca8061003a6000396000f360606040526000357c0100000000000000000000000000000000000000000000000000000000900480631df4f144146037576035565b005b604b60048080359060200190919050506061565b6040518082815260200191505060405180910390f35b60006000600050548202905080503373ffffffffffffffffffffffffffffffffffffffff16827f841774c8b4d8511a3974d7040b5bc3c603d304c926ad25d168dacd04e25c4bed836040518082815260200191505060405180910390a380905060c5565b91905056";

    var multiplier = 7;

    var web3 = new Web3.Web3();

    var unlockResult = await web3.Personal.UnlockAccount.SendRequestAsync(senderAddress, password, new HexBigInteger(120));
    Assert.True(unlockResult);

    var transactionHash = await web3.Eth.DeployContract.SendRequestAsync(abi, byteCode, senderAddress, new HexBigInteger(900000), multiplier);
    var receipt = await MineAndGetReceiptAsync(web3, transactionHash);
```


## 乘法交易

执行调用时，我们要么检索存储在智能合约状态中的数据，要么执行操作（即乘法），检索调用并不是通过区块链共识验证的交易。

在智能合约中提交交易执行函数操作不会返回操作结果，可以使用事件来检索信息，或者我们可以通过函数调用来检查智能合约的状态。

```C#
    var contractAddress = receipt.ContractAddress;

    var contract = web3.Eth.GetContract(abi, contractAddress);

    var multiplyFunction = contract.GetFunction("multiply");

    transactionHash = await multiplyFunction.SendTransactionAsync(senderAddress, 7);
    transactionHash = await multiplyFunction.SendTransactionAsync(senderAddress, 8);

    receipt = await MineAndGetReceiptAsync(web3, transactionHash);
```

使用部署交易的合约地址，我们可以创建合约对象的实例和“乘法”函数。

函数对象以与调用相同的方式简化提交事务。根据上面的示例，我们只需要包含`senderAddress`，它将接收与操作相关的Gas以及函数的参数。

还可以选择指定Gas或将ETH值作为交易的一部分。

在示例中，我们提交了 2 笔交易以分别执行 7 和 8 的乘法运算，并正在等待在我们的私有测试链上打包该交易。

## 事件(Events),过滤器(filters)和主题(topics)

### 创建事件和过滤器

事件被定义为 abi 的一部分，类似于我们可以使用合约实例获取事件的函数。

```C#
 var multiplyEvent = contract.GetEvent("Multiplied");
```

事件对象允许创建过滤器以检索存储在日志中的信息。

我们可以创建过滤器来检索所有事件日志

```C#
var filterAll = await multiplyEvent.CreateFilterAsync();
```

或针对特定主题

```C#
var filter7 = await multiplyEvent.CreateFilterAsync(7);
```

在上面的示例中，我们正在检索乘法参数为 7 的日志，因为乘法的输入参数被标记为索引，我们可以过滤到该主题。

以类似的方式，我们可以过滤发件人地址，因为它也被标记为已索引，但如果我们想过滤该特定主题，我们将在创建过滤器时使用第二个参数。

```C#
var filterSender = await multiplyEvent.CreateFilterAsync(null, senderAddress);
```
### Event DTO（Event data transfer objects）

事件数据传输对象（Event data transfer objects）允许简单地将所有事件参数解码为传输对象，就像我们将反序列化 Json 对象一样。

```C#
 public class MultipliedEvent
 {
    [Parameter("int", "a", 1, true)]
    public int MultiplicationInput {get; set;}

    [Parameter("address", "sender", 2, true)]
    public string Sender {get; set;}

    [Parameter("int", "result", 3, false)]
    public int Result {get; set;}

 }

```
在上面的示例中，MultipliedEvent 属性已通过自定义参数属性“映射”到事件参数。 每个参数指定原始类型、名称、顺序以及是否被索引。
正如我们所看到的，像地址这样的类型被解码为字符串，在我们的场景中，我们可以安全地将 `int256` 解码为 `int32`，但如果不知道，最终类型 `BigInteger` 将是一个更好的选择。

### 检索事件和日志

使用我们已经创建的过滤器，我们可以检索日志和事件。

```C#

 var log = await multiplyEvent.GetFilterChanges<MultipliedEvent>(filterAll);
 var log7 = await multiplyEvent.GetFilterChanges<MultipliedEvent>(filter7);

```

上面我们使用了`GetFilterChanges`，它可用于检索自创建过滤器或自上次尝试检索更改以来与我们的条件相匹配的任何所有日志。
另一种选择是使用`FilterInput`替代"GetAllChanges"。

### 最终代码

所有源代码都可以在[教程解决方案](https://github.com/Nethereum/Nethereum/tree/master/src/Nethereum.Tutorials)
中的`CallTransactionEvents`下找到 
```C#
    public async Task ShouldBeAbleCallAndReadEventLogs()
    {
        var senderAddress = "0x12890d2cce102216644c59daE5baed380d84830c";
        var password = "password";

        var abi = @"[{'constant':false,'inputs':[{'name':'a','type':'int256'}],'name':'multiply','outputs':[{'name':'r','type':'int256'}],'type':'function'},{'inputs':[{'name':'multiplier','type':'int256'}],'type':'constructor'},{'anonymous':false,'inputs':[{'indexed':true,'name':'a','type':'int256'},{'indexed':true,'name':'sender','type':'address'},{'indexed':false,'name':'result','type':'int256'}],'name':'Multiplied','type':'event'}]";

        var byteCode = "0x6060604052604051602080610104833981016040528080519060200190919050505b806000600050819055505b5060ca8061003a6000396000f360606040526000357c0100000000000000000000000000000000000000000000000000000000900480631df4f144146037576035565b005b604b60048080359060200190919050506061565b6040518082815260200191505060405180910390f35b60006000600050548202905080503373ffffffffffffffffffffffffffffffffffffffff16827f841774c8b4d8511a3974d7040b5bc3c603d304c926ad25d168dacd04e25c4bed836040518082815260200191505060405180910390a380905060c5565b91905056";

        var multiplier = 7;

        var web3 = new Web3.Web3();

        var unlockResult = await web3.Personal.UnlockAccount.SendRequestAsync(senderAddress, password, new HexBigInteger(120));
        Assert.True(unlockResult);

        var transactionHash = await web3.Eth.DeployContract.SendRequestAsync(abi, byteCode, senderAddress, new HexBigInteger(900000), multiplier);
        var receipt = await MineAndGetReceiptAsync(web3, transactionHash);

        var contractAddress = receipt.ContractAddress;

        var contract = web3.Eth.GetContract(abi, contractAddress);

        var multiplyFunction = contract.GetFunction("multiply");

        var multiplyEvent = contract.GetEvent("Multiplied");

        var filterAll = await multiplyEvent.CreateFilterAsync();
        var filter7 = await multiplyEvent.CreateFilterAsync(7);

        transactionHash = await multiplyFunction.SendTransactionAsync(senderAddress, 7);
        transactionHash = await multiplyFunction.SendTransactionAsync(senderAddress, 8);

        receipt = await MineAndGetReceiptAsync(web3, transactionHash);


        var log = await multiplyEvent.GetFilterChanges<MultipliedEvent>(filterAll);
        var log7 = await multiplyEvent.GetFilterChanges<MultipliedEvent>(filter7);

        Assert.Equal(2, log.Count);
        Assert.Equal(1, log7.Count);
        Assert.Equal(7, log7[0].Event.MultiplicationInput);
        Assert.Equal(49, log7[0].Event.Result);
    }

    //event Multiplied(int indexed a, address indexed sender, int result );

    public class MultipliedEvent
    {
        [Parameter("int", "a", 1, true)]
        public int MultiplicationInput {get; set;}

        [Parameter("address", "sender", 2, true)]
        public string Sender {get; set;}

        [Parameter("int", "result", 3, false)]
        public int Result {get; set;}

    }


    public async Task<TransactionReceipt> MineAndGetReceiptAsync(Web3.Web3 web3, string transactionHash){

        var miningResult = await web3.Miner.Start.SendRequestAsync(6);
        Assert.True(miningResult);

        var receipt = await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);

        while(receipt == null){
            Thread.Sleep(1000);
            receipt = await web3.Eth.Transactions.GetTransactionReceipt.SendRequestAsync(transactionHash);
        }

        miningResult = await web3.Miner.Stop.SendRequestAsync();
        Assert.True(miningResult);
        return receipt;
    }

```
