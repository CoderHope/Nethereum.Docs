# RPC

使用RPC接口通信，完整的文档可在以下链接找到 [in the Ethereum wiki](https://github.com/ethereum/wiki/wiki/JSON-RPC)

## RPC 数据类型

与以太坊通信的最简单的数据类型是 Numeric 和 Data.

### Numeric 类型 

已创建 HexBigInteger 数据类型以允许从 RPC 中简单地转换数字的输入和输出。
此类型还处理与 Big Endian 之间的转换，以及 Eth "0x0" 的特定用法

```C#
var number = new HexBigInteger(21000);
var anotherNumber = new HexBigInteger("0x5208");
var hex = number.HexValue; //0x5208
BigInteger val = anotherNumber; //2100
```

### 数据类型
交易、区块、叔块、地址哈希被视为字符串以简化使用。

### Hex strings
类似于 HexBigInteger 一些字符串需要进行 Hex 编码和解码.HexString的创建是为了处理Hex类型到UTF8编码转换的问题。

## DTOs

Block、Transaction、FilterInput 和 BlockParameter 有各种 RPC 数据传输对象，仅举几例。

BlockParameter 可以是最新的、最早的、未决的或给定的块号，在所有使用它的请求上默认为最新的。

!!! 注意:
        如果使用 Web3，更改默认块参数，将级联到所有 Eth 事务命令，GetCode、GetStorageAt、GetBalance
```C#
var ethGetBlockTransactionCountByNumber = new EthGetBlockTransactionCountByNumber(client);
return await ethGetBlockTransactionCountByNumber.SendRequestAsync(BlockParameter.CreateLatest());
```

或者使用Web3

```C#
var web3 = new Web3();
web3.Eth.Blocks.GetBlockTransactionCountByNumber.SendRequestAsync(BlockParameter.CreateLatest());
```

##拦截器
有时可能需要为 RPC 方法提供不同的实现。 例如，我们可能希望离线签署所有交易请求，或路由到另一个提供商。

客户端提供了可以在这些场景中使用的 OverridingRequestInterceptor。

这是一个拦截器的模拟实现示例

```C#
public class OverridingInterceptorMock:RequestInterceptor
{
    public override async Task<RpcResponse> InterceptSendRequestAsync(Func<RpcRequest, string, Task<RpcResponse>> interceptedSendRequestAsync, RpcRequest request, string route = null)
    {
        if (request.Method == "eth_accounts")
        {
            return BuildResponse(new string[] { "hello", "hello2"}, route);
        }

        if (request.Method == "eth_getCode")
        {
            return BuildResponse("the code", route);
        }
        return await interceptedSendRequestAsync(request, route);
    }

    public RpcResponse BuildResponse(object results, string route = null)
    {
        var token = JToken.FromObject(results);
        return new RpcResponse(route, token);
    }

    ...
}

```

下面是使用单元测试对代码进行测试
```C#
[Fact]
public async void ShouldInterceptNoParamsRequest()
{
    var client = new RpcClient(new Uri("http://localhost:8545/"));

    client.OverridingRequestInterceptor = new OverridingInterceptorMock();
    var ethAccounts = new EthAccounts(client);
    var accounts = await ethAccounts.SendRequestAsync();
    Assert.True(accounts.Length == 2);
    Assert.Equal(accounts[0],"hello");
}

[Fact]
public async void ShouldInterceptParamsRequest()
{
    var client = new RpcClient(new Uri("http://localhost:8545/"));

    client.OverridingRequestInterceptor = new OverridingInterceptorMock();
    var ethGetCode = new EthGetCode(client);
    var code = await ethGetCode.SendRequestAsync("address");
    Assert.Equal(code, "the code");
}
```
