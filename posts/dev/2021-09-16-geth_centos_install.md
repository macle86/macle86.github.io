---
title: geth centos 에 설치
author: macle
date: 2021-09-16 01:10:00 +0800
categories: [투자]
tags: [ethereum,geth]
---

# 목적
이더리움 노드를 구성해서 투자에 필요한 정보를 추출하여 서비스하고 최종족으로는 이를 활용하여 자산관리 시스템에서 자동매매에 이용하게 하려고 접근했습니다. 
자산관리 시스템은 Java, Python 으로 개발되기 있기 때문에 Java 로 개발된 Besu 도 사용해 볼 계획이지만 geth가 이더리움의 가장 공식적인 API 라고 생각되어서 같이 접근 하였습니다.

활용하고 있는 개발서버가 centos 이기때문에 centos 에 구성하였습니다.

다른 OS 설치, 다른언어 활용부분은 아래 공식 문서를 확인하면 상세하게 정리되어 있습니다.
https://ethereum.org/ko/developers/docs/nodes-and-clients/

# update
이부분은 실행하지 않아도 됩니다. 저는 안정적인 설치를 위해서 전체를 최신버전으로 업데이트 하였습니다.
```
sudo yum update
```

# git update
설치과정에서 git을 활용하는데 서버에 설치된 git version 으로는 설치가 진행되지 않아서 git을 업데이트 하였습니다. 아래버전보다 높은 git을 사용하시는 분은 업데이트 하지 않아도 됩니다.

```
git --version

> git version 1.8.3.1

yum remove git

yum -y install https://packages.endpoint.com/rhel/7/os/x86_64/endpoint-repo-1.7-1.x86_64.rpm

yum install git -y

git --version

> git version 2.24.1
```

#  golang, geth 설치에 필요한 package 설치
```
sudo yum install -y epel-release
sudo yum install -y golang gmp-devel git
```

# geth 를 다운받고 컴파일
```
git clone https://github.com/ethereum/go-ethereum
cd go-ethereum
make all
sudo cp build/bin/geth /usr/local/bin
sudo cp build/bin/bootnode /usr/local/bin
```

# 마치며
위 과정을 마무리하면 geth 명령어 실행이 가능합니다. geth 의 자세한 활용법은 공식 문서에서 확인할 수 있습니다

https://geth.ethereum.org/docs/interface/command-line-options

아래내용은 공식문서에서 가져온 내용입니다.

```
$ geth --help
NAME:
   geth - the go-ethereum command line interface

   Copyright 2013-2021 The go-ethereum Authors

USAGE:
   geth [options] [command] [command options] [arguments...]

VERSION:
   1.10.8-stable-26675454

COMMANDS:
   account                            Manage accounts
   attach                             Start an interactive JavaScript environment (connect to node)
   console                            Start an interactive JavaScript environment
   db                                 Low level database operations
   dump                               Dump a specific block from storage
   dumpconfig                         Show configuration values
   dumpgenesis                        Dumps genesis block JSON configuration to stdout
   export                             Export blockchain into file
   export-preimages                   Export the preimage database into an RLP stream
   import                             Import a blockchain file
   import-preimages                   Import the preimage database from an RLP stream
   init                               Bootstrap and initialize a new genesis block
   js                                 Execute the specified JavaScript files
   license                            Display license information
   makecache                          Generate ethash verification cache (for testing)
   makedag                            Generate ethash mining DAG (for testing)
   removedb                           Remove blockchain and state databases
   show-deprecated-flags              Show flags that have been deprecated
   snapshot                           A set of commands based on the snapshot
   version                            Print version numbers
   version-check                      Checks (online) whether the current version suffers from any known security vulnerabilities
   wallet                             Manage Ethereum presale wallets
   help, h                            Shows a list of commands or help for one command

ETHEREUM OPTIONS:
  --config value                      TOML configuration file
  --datadir value                     Data directory for the databases and keystore (default: "~/.ethereum")
  --datadir.ancient value             Data directory for ancient chain segments (default = inside chaindata)
  --datadir.minfreedisk value         Minimum free disk space in MB, once reached triggers auto shut down (default = --cache.gc converted to MB, 0 = disabled)
  --keystore value                    Directory for the keystore (default = inside the datadir)
  --usb                               Enable monitoring and management of USB hardware wallets
  --pcscdpath value                   Path to the smartcard daemon (pcscd) socket file
  --networkid value                   Explicitly set network id (integer)(For testnets: use --ropsten, --rinkeby, --goerli instead) (default: 1)
  --mainnet                           Ethereum mainnet
  --goerli                            Görli network: pre-configured proof-of-authority test network
  --rinkeby                           Rinkeby network: pre-configured proof-of-authority test network
  --ropsten                           Ropsten network: pre-configured proof-of-work test network
  --syncmode value                    Blockchain sync mode ("fast", "full", "snap" or "light") (default: snap)
  --exitwhensynced                    Exits after block synchronisation completes
  --gcmode value                      Blockchain garbage collection mode ("full", "archive") (default: "full")
  --txlookuplimit value               Number of recent blocks to maintain transactions index for (default = about one year, 0 = entire chain) (default: 2350000)
  --ethstats value                    Reporting URL of a ethstats service (nodename:secret@host:port)
  --identity value                    Custom node name
  --lightkdf                          Reduce key-derivation RAM & CPU usage at some expense of KDF strength
  --whitelist value                   Comma separated block number-to-hash mappings to enforce (<number>=<hash>)

LIGHT CLIENT OPTIONS:
  --light.serve value                 Maximum percentage of time allowed for serving LES requests (multi-threaded processing allows values over 100) (default: 0)
  --light.ingress value               Incoming bandwidth limit for serving light clients (kilobytes/sec, 0 = unlimited) (default: 0)
  --light.egress value                Outgoing bandwidth limit for serving light clients (kilobytes/sec, 0 = unlimited) (default: 0)
  --light.maxpeers value              Maximum number of light clients to serve, or light servers to attach to (default: 100)
  --ulc.servers value                 List of trusted ultra-light servers
  --ulc.fraction value                Minimum % of trusted ultra-light servers required to announce a new head (default: 75)
  --ulc.onlyannounce                  Ultra light server sends announcements only
  --light.nopruning                   Disable ancient light chain data pruning
  --light.nosyncserve                 Enables serving light clients before syncing

DEVELOPER CHAIN OPTIONS:
  --dev                               Ephemeral proof-of-authority network with a pre-funded developer account, mining enabled
  --dev.period value                  Block period to use in developer mode (0 = mine only if transaction pending) (default: 0)

ETHASH OPTIONS:
  --ethash.cachedir value             Directory to store the ethash verification caches (default = inside the datadir)
  --ethash.cachesinmem value          Number of recent ethash caches to keep in memory (16MB each) (default: 2)
  --ethash.cachesondisk value         Number of recent ethash caches to keep on disk (16MB each) (default: 3)
  --ethash.cacheslockmmap             Lock memory maps of recent ethash caches
  --ethash.dagdir value               Directory to store the ethash mining DAGs (default: "~/.ethash")
  --ethash.dagsinmem value            Number of recent ethash mining DAGs to keep in memory (1+GB each) (default: 1)
  --ethash.dagsondisk value           Number of recent ethash mining DAGs to keep on disk (1+GB each) (default: 2)
  --ethash.dagslockmmap               Lock memory maps for recent ethash mining DAGs

TRANSACTION POOL OPTIONS:
  --txpool.locals value               Comma separated accounts to treat as locals (no flush, priority inclusion)
  --txpool.nolocals                   Disables price exemptions for locally submitted transactions
  --txpool.journal value              Disk journal for local transaction to survive node restarts (default: "transactions.rlp")
  --txpool.rejournal value            Time interval to regenerate the local transaction journal (default: 1h0m0s)
  --txpool.pricelimit value           Minimum gas price limit to enforce for acceptance into the pool (default: 1)
  --txpool.pricebump value            Price bump percentage to replace an already existing transaction (default: 10)
  --txpool.accountslots value         Minimum number of executable transaction slots guaranteed per account (default: 16)
  --txpool.globalslots value          Maximum number of executable transaction slots for all accounts (default: 5120)
  --txpool.accountqueue value         Maximum number of non-executable transaction slots permitted per account (default: 64)
  --txpool.globalqueue value          Maximum number of non-executable transaction slots for all accounts (default: 1024)
  --txpool.lifetime value             Maximum amount of time non-executable transaction are queued (default: 3h0m0s)

PERFORMANCE TUNING OPTIONS:
  --cache value                       Megabytes of memory allocated to internal caching (default = 4096 mainnet full node, 128 light mode) (default: 1024)
  --cache.database value              Percentage of cache memory allowance to use for database io (default: 50)
  --cache.trie value                  Percentage of cache memory allowance to use for trie caching (default = 15% full mode, 30% archive mode) (default: 15)
  --cache.trie.journal value          Disk journal directory for trie cache to survive node restarts (default: "triecache")
  --cache.trie.rejournal value        Time interval to regenerate the trie cache journal (default: 1h0m0s)
  --cache.gc value                    Percentage of cache memory allowance to use for trie pruning (default = 25% full mode, 0% archive mode) (default: 25)
  --cache.snapshot value              Percentage of cache memory allowance to use for snapshot caching (default = 10% full mode, 20% archive mode) (default: 10)
  --cache.noprefetch                  Disable heuristic state prefetch during block import (less CPU and disk IO, more time waiting for data)
  --cache.preimages                   Enable recording the SHA3/keccak preimages of trie keys

ACCOUNT OPTIONS:
  --unlock value                      Comma separated list of accounts to unlock
  --password value                    Password file to use for non-interactive password input
  --signer value                      External signer (url or path to ipc file)
  --allow-insecure-unlock             Allow insecure account unlocking when account-related RPCs are exposed by http

API AND CONSOLE OPTIONS:
  --ipcdisable                        Disable the IPC-RPC server
  --ipcpath value                     Filename for IPC socket/pipe within the datadir (explicit paths escape it)
  --http                              Enable the HTTP-RPC server
  --http.addr value                   HTTP-RPC server listening interface (default: "localhost")
  --http.port value                   HTTP-RPC server listening port (default: 8545)
  --http.api value                    API's offered over the HTTP-RPC interface
  --http.rpcprefix value              HTTP path path prefix on which JSON-RPC is served. Use '/' to serve on all paths.
  --http.corsdomain value             Comma separated list of domains from which to accept cross origin requests (browser enforced)
  --http.vhosts value                 Comma separated list of virtual hostnames from which to accept requests (server enforced). Accepts '*' wildcard. (default: "localhost")
  --ws                                Enable the WS-RPC server
  --ws.addr value                     WS-RPC server listening interface (default: "localhost")
  --ws.port value                     WS-RPC server listening port (default: 8546)
  --ws.api value                      API's offered over the WS-RPC interface
  --ws.rpcprefix value                HTTP path prefix on which JSON-RPC is served. Use '/' to serve on all paths.
  --ws.origins value                  Origins from which to accept websockets requests
  --graphql                           Enable GraphQL on the HTTP-RPC server. Note that GraphQL can only be started if an HTTP server is started as well.
  --graphql.corsdomain value          Comma separated list of domains from which to accept cross origin requests (browser enforced)
  --graphql.vhosts value              Comma separated list of virtual hostnames from which to accept requests (server enforced). Accepts '*' wildcard. (default: "localhost")
  --rpc.gascap value                  Sets a cap on gas that can be used in eth_call/estimateGas (0=infinite) (default: 50000000)
  --rpc.txfeecap value                Sets a cap on transaction fee (in ether) that can be sent via the RPC APIs (0 = no cap) (default: 1)
  --rpc.allow-unprotected-txs         Allow for unprotected (non EIP155 signed) transactions to be submitted via RPC
  --jspath loadScript                 JavaScript root path for loadScript (default: ".")
  --exec value                        Execute JavaScript statement
  --preload value                     Comma separated list of JavaScript files to preload into the console

NETWORKING OPTIONS:
  --bootnodes value                   Comma separated enode URLs for P2P discovery bootstrap
  --discovery.dns value               Sets DNS discovery entry points (use "" to disable DNS)
  --port value                        Network listening port (default: 30303)
  --maxpeers value                    Maximum number of network peers (network disabled if set to 0) (default: 50)
  --maxpendpeers value                Maximum number of pending connection attempts (defaults used if set to 0) (default: 0)
  --nat value                         NAT port mapping mechanism (any|none|upnp|pmp|extip:<IP>) (default: "any")
  --nodiscover                        Disables the peer discovery mechanism (manual peer addition)
  --v5disc                            Enables the experimental RLPx V5 (Topic Discovery) mechanism
  --netrestrict value                 Restricts network communication to the given IP networks (CIDR masks)
  --nodekey value                     P2P node key file
  --nodekeyhex value                  P2P node key as hex (for testing)

MINER OPTIONS:
  --mine                              Enable mining
  --miner.threads value               Number of CPU threads to use for mining (default: 0)
  --miner.notify value                Comma separated HTTP URL list to notify of new work packages
  --miner.notify.full                 Notify with pending block headers instead of work packages
  --miner.gasprice value              Minimum gas price for mining a transaction (default: 1000000000)
  --miner.gaslimit value              Target gas ceiling for mined blocks (default: 8000000)
  --miner.etherbase value             Public address for block mining rewards (default = first account) (default: "0")
  --miner.extradata value             Block extra data set by the miner (default = client version)
  --miner.recommit value              Time interval to recreate the block being mined (default: 3s)
  --miner.noverify                    Disable remote sealing verification
  
GAS PRICE ORACLE OPTIONS:
  --gpo.blocks value                  Number of recent blocks to check for gas prices (default: 20)
  --gpo.percentile value              Suggested gas price is the given percentile of a set of recent transaction gas prices (default: 60)
  --gpo.maxprice value                Maximum gas price will be recommended by gpo (default: 500000000000)
  --gpo.ignoreprice value             Gas price below which gpo will ignore transactions (default: 2)

VIRTUAL MACHINE OPTIONS:
  --vmdebug                           Record information useful for VM and contract debugging

LOGGING AND DEBUGGING OPTIONS:
  --fakepow                           Disables proof-of-work verification
  --nocompaction                      Disables db compaction after import
  --verbosity value                   Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail (default: 3)
  --vmodule value                     Per-module verbosity: comma-separated list of <pattern>=<level> (e.g. eth/*=5,p2p=4)
  --log.json                          Format logs with JSON
  --log.backtrace value               Request a stack trace at a specific logging statement (e.g. "block.go:271")
  --log.debug                         Prepends log messages with call-site location (file and line number)
  --pprof                             Enable the pprof HTTP server
  --pprof.addr value                  pprof HTTP server listening interface (default: "127.0.0.1")
  --pprof.port value                  pprof HTTP server listening port (default: 6060)
  --pprof.memprofilerate value        Turn on memory profiling with the given rate (default: 524288)
  --pprof.blockprofilerate value      Turn on block profiling with the given rate (default: 0)
  --pprof.cpuprofile value            Write CPU profile to the given file
  --trace value                       Write execution trace to the given file

METRICS AND STATS OPTIONS:
  --metrics                              Enable metrics collection and reporting
  --metrics.expensive                    Enable expensive metrics collection and reporting
  --metrics.addr value                   Enable stand-alone metrics HTTP server listening interface (default: "127.0.0.1")
  --metrics.port value                   Metrics HTTP server listening port (default: 6060)
  --metrics.influxdb                     Enable metrics export/push to an external InfluxDB database
  --metrics.influxdb.endpoint value      InfluxDB API endpoint to report metrics to (default: "http://localhost:8086")
  --metrics.influxdb.database value      InfluxDB database name to push reported metrics to (default: "geth")
  --metrics.influxdb.username value      Username to authorize access to the database (default: "test")
  --metrics.influxdb.password value      Password to authorize access to the database (default: "test")
  --metrics.influxdb.tags value          Comma-separated InfluxDB tags (key/values) attached to all measurements (default: "host=localhost")
  --metrics.influxdbv2                   Enable metrics export/push to an external InfluxDB v2 database
  --metrics.influxdb.token value         Token to authorize access to the database (v2 only) (default: "test")
  --metrics.influxdb.bucket value        InfluxDB bucket name to push reported metrics to (v2 only) (default: "geth")
  --metrics.influxdb.organization value  InfluxDB organization name (v2 only) (default: "geth")

ALIASED (deprecated) OPTIONS:
  --nousb                             Disables monitoring for and managing USB hardware wallets (deprecated)
  --rpc                               Enable the HTTP-RPC server (deprecated and will be removed June 2021, use --http)
  --rpcaddr value                     HTTP-RPC server listening interface (deprecated and will be removed June 2021, use --http.addr) (default: "localhost")
  --rpcport value                     HTTP-RPC server listening port (deprecated and will be removed June 2021, use --http.port) (default: 8545)
  --rpccorsdomain value               Comma separated list of domains from which to accept cross origin requests (browser enforced) (deprecated and will be removed June 2021, use --http.corsdomain)
  --rpcvhosts value                   Comma separated list of virtual hostnames from which to accept requests (server enforced). Accepts '*' wildcard. (deprecated and will be removed June 2021, use --http.vhosts) (default: "localhost")
  --rpcapi value                      API's offered over the HTTP-RPC interface (deprecated and will be removed June 2021, use --http.api)
  --miner.gastarget value             Target gas floor for mined blocks (deprecated) (default: 0)

MISC OPTIONS:
  --snapshot                          Enables snapshot-database mode (default = enable)
  --bloomfilter.size value            Megabytes of memory allocated to bloom-filter for pruning (default: 2048)
  --help, -h                          show help
  --catalyst                          Catalyst mode (eth2 integration testing)
  --override.london value             Manually specify London fork-block, overriding the bundled setting (default: 0)


COPYRIGHT:
   Copyright 2013-2021 The go-ethereum Authors


```