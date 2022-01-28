# WS

Nethereum 提供 websocket 客户端。 它们可以用来代替默认的 HTTP 通道并支持订阅。

## Websocket 客户端

处于性能的考虑，也是为了支持流式订阅，还有处理链接的关闭事件等，链接一旦建立，将交友websocket客户端进行维护。

Nethereum 提供了两个主要的 Websocket 客户端。

## WebSocketClient

**相关的 Nuget 包:**

+ Nethereum.JsonRpc.WebSocketClient
+ Nethereum.Web3

全名: Nethereum.JsonRpc.WebSocketClient.WebSocketClient

这个实例可以被注入到一个普通的 web3 对象中。 因此，它可以代替默认的基于 HTTP 的客户端用于所有基于节点的通信。 它保持一个开放的 Web 套接字连接，直到它被释放。

### 创建和使用一个WebSocket客户端

检索当前区块号：
```C#
using Nethereum.JsonRpc.WebSocketClient;
using Nethereum.Web3;
using Nethereum.Web3.Accounts.Managed;
using System;
using System.Threading.Tasks;

namespace NethereumSocketsAndStreaming
{
    public class WS
    {
        public static async Task GetBlockNumberViaWebSocketAsync()
        {
            var account = new ManagedAccount("0x12890d2cce102216644c59daE5baed380d84830c", "password");
            using(var clientws = new WebSocketClient("wss://rinkeby.infura.io/ws"))
            { 
                var web3ws = new Web3(account, clientws);
                var blockNumber = await web3ws.Eth.Blocks.GetBlockNumber.SendRequestAsync(); //task cancelled exception
                Console.WriteLine("Block Number: " + blockNumber);
            }
        }
    }
}

```

## StreamingWebSocketClient

**相关的 Nuget 包:**

+ Nethereum.JsonRpc.WebSocketClient
+ Nethereum.RPC.Reactive
+ Nethereum.Parity.Reactive
+ Nethereum.Web3

全名: Nethereum.JsonRpc.WebSocketStreamingClient

这用于通过 websocket 连接创建订阅。 它使连接保持打开状态，直到处理完毕。 订阅仅在连接打开时才有效。

这些 nuget 包中提供了订阅:
* Nethereum.RPC.Reactive
* Nethereum.Parity.Reactive

### 有关订阅的文档请看[subscription docs](../nethereum-subscriptions-streaming.md) 以获取更多信息和实例

### 创建和使用一个 StreamingWebSocketClient

创建一个 NewBlockHeader 订阅：
```C#
using Nethereum.JsonRpc.WebSocketStreamingClient;
using Nethereum.RPC.Reactive.Eth.Subscriptions;
using Newtonsoft.Json;
using System;
using System.Reactive.Linq;
using System.Threading.Tasks;

namespace Nethereum.WebSocketsStreamingTest
{
    public class BlockHeader
    {
        public static async Task NewBlockHeader_With_Observable_Subscription()
        {
            using(var client = new StreamingWebSocketClient("wss://rinkeby.infura.io/ws"))
            {
                // create the subscription
                // (it won't start receiving data until Subscribe is called)
                var subscription = new EthNewBlockHeadersObservableSubscription(client);

                // attach a handler for when the subscription is first created (optional)
                // this will occur once after Subscribe has been called
                subscription.GetSubscribeResponseAsObservable().Subscribe(subscriptionId =>
                    Console.WriteLine("Block Header subscription Id: " + subscriptionId));

                DateTime? lastBlockNotification = null;
                double secondsSinceLastBlock = 0;

                // attach a handler for each block
                // put your logic here
                subscription.GetSubscriptionDataResponsesAsObservable().Subscribe(block => 
                {
                    secondsSinceLastBlock = (lastBlockNotification == null) ? 0 : (int)DateTime.Now.Subtract(lastBlockNotification.Value).TotalSeconds;
                    lastBlockNotification = DateTime.Now;
                    var utcTimestamp = DateTimeOffset.FromUnixTimeSeconds((long)block.Timestamp.Value);
                    Console.WriteLine($"New Block. Number: {block.Number.Value}, Timestamp UTC: {JsonConvert.SerializeObject(utcTimestamp)}, Seconds since last block received: {secondsSinceLastBlock} ");
                });

                bool subscribed = true;

                // handle unsubscription
                // optional - but may be important depending on your use case
                subscription.GetUnsubscribeResponseAsObservable().Subscribe(response =>
                { 
                    subscribed = false;
                    Console.WriteLine("Block Header unsubscribe result: " + response);
                });

                // open the websocket connection
                await client.StartAsync();

                // start the subscription
                // this will only block long enough to register the subscription with the client
                // once running - it won't block whilst waiting for blocks
                // blocks will be delivered to our handler on another thread
                await subscription.SubscribeAsync();

                // run for a minute before unsubscribing
                await Task.Delay(TimeSpan.FromMinutes(1)); 

                // unsubscribe
                await subscription.UnsubscribeAsync();

                //allow time to unsubscribe
                while (subscribed) await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }
    }
}

```