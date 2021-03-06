## Go Ethereum

Official Golang implementation of the Ethereum protocol.

[![API Reference](
https://camo.githubusercontent.com/915b7be44ada53c290eb157634330494ebe3e30a/68747470733a2f2f676f646f632e6f72672f6769746875622e636f6d2f676f6c616e672f6764646f3f7374617475732e737667
)](https://godoc.org/github.com/ethereum/go-ethereum)
[![Go Report Card](https://goreportcard.com/badge/github.com/ethereum/go-ethereum)](https://goreportcard.com/report/github.com/ethereum/go-ethereum)
[![Travis](https://travis-ci.org/ethereum/go-ethereum.svg?branch=master)](https://travis-ci.org/ethereum/go-ethereum)
[![Discord](https://img.shields.io/badge/discord-join%20chat-blue.svg)](https://discord.gg/nthXNEv)

Automated builds are available for stable releases and the unstable master branch. Binary
archives are published at https://geth.ethereum.org/downloads/.

## Building the source
Clone the repository from https://github.com/vivek-tamilarasan/go-ethereum or use
```shell
git clone https://github.com/vivek-tamilarasan/go-ethereum.git
```
For prerequisites and detailed build in

Building `geth` requires both a Go (version 1.10 or later) and a C compiler. You can install
them using your favourite package manager. Once the dependencies are installed, run

```shell
make geth
```

or, to build the full suite of utilities:

```shell
make all
```
### Node setup

By far the most common scenario is people wanting to simply interact with the Ethereum
network: create accounts; transfer funds; deploy and interact with contracts. For this
particular use-case the user doesn't care about years-old historical data, so we can
fast-sync quickly to the current state of the network. To do so:

```shell
$ geth --networkid 633828 --datadir <path/to/go-ethereum>/.ionixx --mine
```
It will start the node. To run with console use,
```shell
$ geth --networkid 633828 --datadir <path/to/go-ethereum>/.ionixx console
```
### Note 
* Don't start the Mining process before Block synchronisation starts
* If the boot node was not connected, in console use,
  ```shell
  > admin.addPeer("enode://9d902d911bbe61305e4feaa8b7627f69f08dc264d970fb36731f1078dd49dec17a81f9bc1797f16b82ae72bd73cff845e38195b6ab529ca22e2576ba4f603ace@34.231.117.156:64246")
  ```
* To check the connected peers, in console use,
  ```shell
  > admin.peers()
  ```

This command will:
 * Start `geth` in fast sync mode (default, can be changed with the `--syncmode` flag),
   causing it to download more data in exchange for avoiding processing the entire history
   of the Main Node (ionixx)
 * Start up `geth`'s built-in interactive [JavaScript console](https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console),
   (via the trailing `console` subcommand) through which you can invoke all official [`web3` methods](https://github.com/ethereum/wiki/wiki/JavaScript-API)
   as well as `geth`'s own [management APIs](https://github.com/ethereum/go-ethereum/wiki/Management-APIs).
   This tool is optional and if you leave it out you can always attach to an already running
   `geth` instance with `geth attach`.

### Programmatically interfacing `geth` nodes

As a developer, sooner rather than later you'll want to start interacting with `geth` and the
Ethereum network via your own programs and not manually through the console. To aid
this, `geth` has built-in support for a JSON-RPC based APIs ([standard APIs](https://github.com/ethereum/wiki/wiki/JSON-RPC)
and [`geth` specific APIs](https://github.com/ethereum/go-ethereum/wiki/Management-APIs)).
These can be exposed via HTTP, WebSockets and IPC (UNIX sockets on UNIX based
platforms, and named pipes on Windows).

The IPC interface is enabled by default and exposes all the APIs supported by `geth`,
whereas the HTTP and WS interfaces need to manually be enabled and only expose a
subset of APIs due to security reasons. These can be turned on/off and configured as
you'd expect.

Geth Basic flags
* `--port` Used to mention the TCP port of the geth
* `--mine` Start mining process
* `--miner.threads` Used to mention thread count for miner
* `--nodiscover` Hide your node from auto discovery
* `--etherbase` Mention the coinbase address


HTTP based JSON-RPC API options:
  * `--networkid` Connect with the ionixx private network(*required)
  * `--datadir` Use the initialize datadir ".inonixx" (*required)
  * `--rpc` Enable the HTTP-RPC server
  * `--rpcaddr` HTTP-RPC server listening interface (default: `localhost`)
  * `--rpcport` HTTP-RPC server listening port (default: `8545`)
  * `--rpcapi` API's offered over the HTTP-RPC interface (default: `eth,net,web3`)
  * `--rpccorsdomain` Comma separated list of domains from which to accept cross origin requests (browser enforced)
  * `--ws` Enable the WS-RPC server
  * `--wsaddr` WS-RPC server listening interface (default: `localhost`)
  * `--wsport` WS-RPC server listening port (default: `8546`)
  * `--wsapi` API's offered over the WS-RPC interface (default: `eth,net,web3`)
  * `--wsorigins` Origins from which to accept websockets requests
  * `--ipcdisable` Disable the IPC-RPC server
  * `--ipcapi` API's offered over the IPC-RPC interface (default: `admin,debug,eth,miner,net,personal,shh,txpool,web3`)
  * `--ipcpath` Filename for IPC socket/pipe within the datadir (explicit paths escape it)

You'll need to use your own programming environments' capabilities (libraries, tools, etc) to
connect via HTTP, WS or IPC to a `geth` node configured with the above flags and you'll
need to speak [JSON-RPC](https://www.jsonrpc.org/specification) on all transports. You
can reuse the same connection for multiple requests!

**Note: Please understand the security implications of opening up an HTTP/WS based
transport before doing so! Hackers on the internet are actively trying to subvert
Ethereum nodes with exposed APIs! Further, all browser tabs can access locally
running web servers, so malicious web pages could try to subvert locally available
APIs!**

#### Starting up your member nodes

With the bootnode operational and externally reachable (you can try
`telnet <ip> <port>` to ensure it's indeed reachable), start every subsequent `geth`
node pointed to the bootnode for peer discovery via the `--bootnodes` flag. It will
probably also be desirable to keep the data directory of your private network separated, so
do also specify a custom `--datadir` flag.

```shell
geth --datadir "<path/to/go-ethereum>/.ionixx" --networkid 633828 --bootnodes <enode-url>
```

*Note: Since your network will be completely cut off from the main and test networks, you'll
also need to configure a miner to process transactions and create new blocks for you.*

#### Running a miner

Mining on the public Ethereum network is a complex task as it's only feasible using GPUs,
requiring an OpenCL or CUDA enabled `ethminer` instance. For information on such a
setup, please consult the [EtherMining subreddit](https://www.reddit.com/r/EtherMining/)
and the [Genoil miner](https://github.com/Genoil/cpp-ethereum) repository.

In a private network setting, however a single CPU miner instance is more than enough for
practical purposes as it can produce a stable stream of blocks at the correct intervals
without needing heavy resources (consider running on a single thread, no need for multiple
ones either). To start a `geth` instance for mining, run it with all your usual flags, extended
by:

```shell
geth --datadir "<path/to/go-ethereum>/.ionixx" --networkid 633828 --mine 
```
Or manually you can start the mining without mentioning above `--mine`.
In terminal, 
```shell
geth --datadir "<path/to/go-ethereum>/.ionixx" --networkid 633828 console
```
It will give a geth console, use,
```shell
> miner.start()
```
Which will start mining blocks and transactions on a single CPU thread, crediting all
proceedings to the account specified by `--etherbase`. You can further tune the mining
by changing the default gas limit blocks converge to (`--targetgaslimit`) and the price
transactions are accepted at (`--gasprice`).

## Console Commands
### admin
To check node information
```shell
> admin.nodeInfo
```
To add peers manually
```shell
> admin.addPeer(<enode_url_of_node>)
```
To check the online peers
```shell
> admin.peers
```
### miner
To start mining,
```shell
> miner.start()
    or 
> miner.start(<int>)        #<int> is used to specify number of threads
```
To stop mining,
```shell
> miner.stop()
```
To change the etherbase,
```shell
> miner.setEtherbase(<address>)
```
### eth
To check the etherbase account,
```shell
> eth.coinbase
```
To check the account list
```shell
> eth.accounts
> eth.accounts[<int: index>]       #It also supports indexing
```
To send Transaction
```shell
> eth.sendTransaction({from: <address>, to: <address>, value: <int: wei>, data: <hex encoded>})
```
### personal
To create new account,
```shell
> personal.newAccount       #Dont forget the pass_phrase
```
To unlock the account
```shell
> personal.unlockAccount(<address>)
```

To know additional commands refer [Geth Commands](https://geth.ethereum.org/docs/rpc/ns-admin)

## Block Explorer
You can check block explorer in [BLock Explorer](http://34.231.117.156:64226/#/)
