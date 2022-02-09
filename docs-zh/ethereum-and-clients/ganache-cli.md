## Ganache CLI 配置和使用

Ganache CLI 是最新版本的 TestRPC：一个快速且可定制的区块链模拟器。 它允许您调用区块链，而无需运行实际的以太坊节点。

* 交易立即被“挖掘”。
* 无交易成本。
* 可以使用固定数量的以太币（无需水龙头或挖矿）重新循环、重置和实例化帐户。
* 可以修改 Gas 价格和挖矿速度。
* 方便的 GUI 为您提供测试链事件的概览。

### 安装

Ganache 可以通过 NPM 安装：

 ``` npm install -g ganache-cli ```

### 使用 Ganache CLI

#### 命令行

```shell
$ ganache-cli <options>
```

Options:

* `-a` 或 `--accounts`: 指定要在启动时生成的帐户数。
* `-b` 或 `--blocktime`: 以秒为单位指定自动挖掘的阻塞时间。 默认为 0 并且没有自动挖掘。
* `-d` 或 `--deterministic`:根据预定义的助记符生成确定性地址。
* `-n` 或 `--secure`: 默认锁定可用账户（有利于第三方交易签名）
* `-m` 或 `--mnemonic`: 使用特定的 HD Wallets助记词生成初始地址。
* `-p` 或 `--p或t`: 要监听的端口号。 默认为 8545。
* `-h` 或 `--hostname`: 要监听的服务名，默认的监听名称是 `server.listen()` [default](https://nodejs.org/api/http.html#http_server_listen_port_hostname_backlog_callback).
* `-s` 或 `--seed`: 使用任意数据生成要使用的HD钱包助记词。
* `-g` 或 `--gasPrice`: 使用自定义 Gas Price（默认为 20000000000）
* `-l` 或 `--gasLimit`: 使用自定义 Gas Limit（默认为 90000）
* `-f` 或 `--fork`: 在给定区块从另一个当前运行的以太坊客户端分叉。 输入应该是其他客户端的 HTTP 位置和端口，例如 `http://localhost:8545`。 您可以选择使用 `@` 符号指定要分叉的块：`http://localhost:8545@1599200`。
* `-i` 或 `--networkId`: 指定 ganache-cli 将用于标识自身的网络 ID（默认为当前时间或分叉区块链的网络 ID（如果已配置））
* `--db`: 指定目录的路径以保存链数据库。 如果数据库已经存在，ganache-cli 将初始化该链而不是创建新链。
* `--debug`: 输出 VM 操作码以进行调试
* `--mem`: 输出 ganache-cli 内存使用统计信息。 这取代了正常输出。

特殊选项:

* `--account`: 指定 `--account=...` (no 's') 任意次数传递任意私钥及其相关余额以生成初始地址：

  ```
  $ ganache-cli --account="<privatekey>,balance" [--account="<privatekey>,balance"]
  ```

  请注意，私钥长度为 64 个字符，并且必须输入为以 0x 为前缀的十六进制字符串。 余额可以输入为整数或以 0x 为前缀的十六进制值，指定该帐户中的 wei 数量。

   使用`--account`时不会为你创建HD Wallet。


* `-u` 或 `--unlock`: 指定 `--unlock ...` 多次传递地址或帐户索引以解锁特定帐户。 当与 `--secure` 一起使用时，`--unlock` 将覆盖指定帐户的锁定状态。

  ```
  $ ganache-cli --secure --unlock "0x1234..." --unlock "0xabcd..."
  ```

  您还可以指定一个数字，通过索引解锁帐户：

  ```
  $ ganache-cli --secure -u 0 -u 1
  ```

  此功能还可用于模拟帐户并解锁您原本无法访问的地址。 与 --fork 功能一起使用时，您可以使用 ganache-cli 将交易作为区块链上的任何地址进行，这对于测试和动态分析非常有用。

#### Library

Web3 提供实体:

```javascript
var ganache = require("ganache-cli");
web3.setProvider(ganache.provider());
```

作为一般的http服务:

```javascript
var ganache = require("ganache-cli");
var server = ganache.server();
server.listen(port, function(err, blockchain) {...});
```

`.provider()` 和 `.server()` 都采用单个对象，允许您指定 `ganache-cli` 的行为。 此参数是可选的。 可用选项有：

* `"accounts"`：`Object`的`Array`。 每个对象都应该有一个带有十六进制值的平衡键。 还可以指定密钥“secretKey”，代表账户的私钥。 如果没有 `secretKey`，则地址是使用给定余额自动生成的。 如果指定，则密钥用于确定帐户的地址。
* `"debug"`: `boolean` - 输出 VM 操作码以进行调试
* `"logger"`: `Object` - 对象，如 `console`，实现了 `log()` 函数。
* `"mnemonic"`: 使用特定的 HD 钱包助记词生成初始地址。
* `"port"`: 作为服务器运行时要侦听的端口号。
* `"seed"`: 使用任意数据生成要使用的HD钱包助记词。
* `"total_accounts"`: `number` - 启动时要生成的帐户数。
* `"fork"`: `string` - 与上面的 `--fork` 选项相同。
* `"network_id"`: `integer` - 与上面的 `--networkId` 选项相同。
* `"time"`: `Date` - 第一个块应该开始的日期。 使用此功能以及 `evm_increaseTime` 方法来测试与时间相关的代码。
* `"locked"`: `boolean` - 默认情况下是否锁定帐户。
* `"unlocked_accounts"`: `Array` - 指定应解锁哪些帐户的地址或地址索引数组。
* `"db_path"`: `String` - 指定目录的路径以保存链数据库。 如果数据库已经存在，`ganache-cli` 将初始化该链而不是创建一个新链。
* `"account_keys_path"`: `String` - 指定一个文件来保存帐户和私钥，以进行测试。

### 实现的方法

目前实现的 RPC 方法有:

* `bzz_hive` (stub)
* `bzz_info` (stub)
* `debug_traceTransaction`
* `eth_accounts`
* `eth_blockNumber`
* `eth_call`
* `eth_coinbase`
* `eth_estimateGas`
* `eth_gasPrice`
* `eth_getBalance`
* `eth_getBlockByNumber`
* `eth_getBlockByHash`
* `eth_getBlockTransactionCountByHash`
* `eth_getBlockTransactionCountByNumber`
* `eth_getCode` (only supports block number “latest”)
* `eth_getCompilers`
* `eth_getFilterChanges`
* `eth_getFilterLogs`
* `eth_getLogs`
* `eth_getStorageAt`
* `eth_getTransactionByHash`
* `eth_getTransactionByBlockHashAndIndex`
* `eth_getTransactionByBlockNumberAndIndex`
* `eth_getTransactionCount`
* `eth_getTransactionReceipt`
* `eth_hashrate`
* `eth_mining`
* `eth_newBlockFilter`
* `eth_newFilter` (includes log/event filters)
* `eth_protocolVersion`
* `eth_sendTransaction`
* `eth_sendRawTransaction`
* `eth_sign`
* `eth_syncing`
* `eth_uninstallFilter`
* `net_listening`
* `net_peerCount`
* `net_version`
* `miner_start`
* `miner_stop`
* `personal_listAccounts`
* `personal_lockAccount`
* `personal_newAccount`
* `personal_unlockAccount`
* `personal_sendTransaction`
* `shh_version`
* `rpc_modules`
* `web3_clientVersion`
* `web3_sha3`

还有一些特殊的非标准方法未包含在原始 RPC 规范中：

| Method             | Definition                                                   | Value Returned |
|--------------------|--------------------------------------------------------------|----------------|
| `evm_snapshot`     | 快照当前区块的区块链状态。 不接受任何参数。                                       | 返回创建的快照的整数 id。 |
| `evm_revert`       | 将区块链的状态恢复到以前的快照。 采用单个参数，即要恢复到的快照 ID。 如果没有传递快照 ID，它将恢复到最新的快照。 | 返回 `true`      |
| `evm_increaseTime` | 及时向前跳跃。 采用一个参数，即增加的时间量（以秒为单位）。                               | 返回总时间调整，以秒为单位。 |
| `evm_mine`         | 强制开采一个区块。 不接受任何参数。 独立于是否开始或停止挖掘一个块                           |                |

### 不支持的方法

* `eth_compileSolidity`: 如果您想在 Javascript 中进行 Solidity 编译，请参阅 [solc-js project](https://github.com/ethereum/solc-js).
