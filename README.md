# Kaya - Zilliqa's RPC client for testing and development
[![Gitter chat](http://img.shields.io/badge/chat-on%20gitter-077a8f.svg)](https://gitter.im/Zilliqa/CommunityDev)
[![Build Status](https://travis-ci.com/Zilliqa/kaya.svg?branch=master)](https://travis-ci.com/Zilliqa/kaya)


Kaya is Zilliqa's RPC server for testing and development. It is personal blockchain which makes developing application easier and faster. Kaya emulates the Zilliqa's blockchain behavior, and follows the expected server behavior as seen in the [`zilliqa-js`](https://github.com/Zilliqa/Zilliqa-JavaScript-Library).

The goal of the project is to support all endpoints in Zilliqa Javascript API, making it easy for app developers to build Dapps on our platform.

Kaya is under development. See [roadmap here](https://github.com/Zilliqa/kaya/blob/master/ROADMAP.md).

Currently, Kaya supports the following functions:
* `CreateTransaction`
* `GetTransaction`
* `GetRecentTransactions`
* `GetNetworkID`
* `GetSmartContractState`
* `GetSmartContracts`
* `GetBalance`
* `GetSmartContractInit`
* `GetSmartContractCode`

Methods that are NOT supported:
* `GetDsBlock`
* `GetTxBlock`
* `GetLatestDsBlock`
* `GetLatestTxBlock`

In addition, the following features are not supported yet:
* Multi-contract calls
* Events

## Getting Started
### Installation
Install the node packages and dependencies: `npm install`

Scilla files must be processed using the `scilla-interpreter`. The [Scilla interpreter](https://scilla.readthedocs.io/en/latest/interface.html) executable provides a calling interface that enables users to invoke transitions with specified inputs and obtain outputs.

#### Using Remote Scilla Interpreter (Default)

By default, Kaya RPC uses the remote scilla interpreter to process `.scilla` files. You do not have to change any configurations.

#### Using Local Scilla Interpreter
You can choose to use your own scilla interpreter locally. To do it, you will have to compile the binary yourself from the [scilla repository](https://github.com/Zilliqa/scilla) and transfer it to the correct directory within Kaya RPC.

Instructions:
1. Ensure that you have installed the related dependencies: [INSTALL.md](https://github.com/Zilliqa/scilla/blob/master/INSTALL.md)
2. Then, run `make clean; make`
3. Copy the `scilla-runner` from `[SCILLA_DIR]/bin` into `[Kaya_DIR]/components/scilla/`
4. Open `config.js` file and set the `config.scilla.remote` to `false`

### Usage
Kaya RPC two modes: normal and debug. The server listens on port `4200` by default. You can change the port through the `config.js` file.
- `npm start` : Normal mode
- `npm run debug`: Enables verbosity mode. Display logs about activities. Useful for debugging

Developers can also start Kaya RPC with accounts from a `fixtures` file. The fixture file is configurable through `config.js`. If you wish to change this file, you will have to follow the format just like `account-fixtures.json`.

To start Kaya RPC with accounts from a file, run one of the following commands:
- `npm run start:fixtures`: Normal mode
- `npm run debug:fixtures`: Greater verbosity. Shows log trail about server activities.

__Recommendation__: We recommend running `npm run debug:fixtures`. Without account fixtures, accounts will be randomly generated at every run. It can be time consuming to change the private keys and addresses each time.

### Advanced: Persistent Storage using Kaya RPC
By default, the data states are non-persistent. Once you shut down the node server, state files and transactions will be deleted.

To enable persistence data, use:
```
node server.js --save
```
The file containing the state will be stored in the `/data` folder. Blockchain-specific information such as transaction logs are stored in `data/save/YYYYMMDDhhmmss_blockchain_states.json`.

You can load the files using:
```
node server.js --load data/save/YYYYMMDDhhmmss_blockchain_states.json
```

## Testing

Some of the functions in Kaya RPC are covered under automated testing using `jest`. However, scilla related transactions are not covered through automated testing. To test the `CreateTransaction` functionalities, you will have to test it manually.

From `test/scripts/`, you can use run `node DeployContract.js` to test contract deployment.
Then, use `node CreateTransaction --key [private-key] --to [contract_addr]` to make transition calls.
You can use the `curl` commands stated in the [jsonrpc apidocs](https://apidocs.zilliqa.com/#introduction) to test the rest of the functions.

Use `--key` to specify a private key. Otherwise, a random privatekey will be generated.

### Testing with Fixture Files

You can also use the `--test` flag, which uses default test configurations:
1. Start the server using `npm run debug:fixtures`
2. Deploy a contract using `node DeployContract.js --test`.
3. Check where the contract is deployed. It should be on the logs if you have enabled `debug` mode, otherwise you can check it through the `GetSmartContracts` method.
4. Send a transaction using `node CreateTransaction.js --test`

## Docker

a Dockerfile has been created to build a docker image which can be used to start Kaya RPC within a docker container. You can build the docker image on your local machine:

`$ docker build -t kaya .`

and run it afterwards:

`
$ docker run --rm -d --name kay-local -p 4200:4200 kaya

$ docker logs -f kaya-local
docker run --rm -p 4200:4200 kaya
ZILLIQA KAYA RPC SERVER (ver: 0.2.0)
Server listening on 127.0.0.1:4200
Scilla interperter running remotely from: https://scilla-runner.zilliqa.com/contract/call
================================================================================
Available Accounts
=============================
(0) 16cc5cfaeff4945fc2edc1f9a73c1ba38f3a169c (Amt: 100000) (Nonce: 0)
(1) 9ba385bfdfa5aaa15f638702c99c917961b7fa99 (Amt: 100000) (Nonce: 0)
(2) 394b0e1863315841d4c3acf40a8dc6d9537e558e (Amt: 100000) (Nonce: 0)
(3) f593719f912cf12ce438608f9131b5126ab25873 (Amt: 100000) (Nonce: 0)
(4) 25b2d997b8b1a20302a8a749c35a14e36495856d (Amt: 100000) (Nonce: 0)
(5) f4cd38f387465505a316be8f552f61f3140970d7 (Amt: 100000) (Nonce: 0)
(6) 844f41fafd3181f821d7ec8d70073fca862762f5 (Amt: 100000) (Nonce: 0)
(7) 1e15ea5841c856318455a682db0d5587ea18cfd2 (Amt: 100000) (Nonce: 0)
(8) e7d8fd2d2706f1f5ea55181a90757285b317596d (Amt: 100000) (Nonce: 0)
(9) 4679743b469257d1fcf3c3ed1fa436d039bab446 (Amt: 100000) (Nonce: 0)

 Private Keys
 =============================
 (0) da6e6d128720a4d2c97677718b2cb2749e5b6ad89ef5c0bf91084658b55e84a5
 (1) 22f92cf224b77b8cb4b8a524ed78a378ddb278abf8f3415e600116df94dac26a
 (2) c3c14bcb74d4146f1dd8d1e17ccd4266641634b22971edbcdddb495c1c837e9a
 (3) 67846d97dd865fe681a79a1ce1f2687d054d521457d03bd5e4466b1147a2c108
 (4) 3b3627cb909de680eebf34131ab703fb6fc5e5295475579212c2c727cccbeecc
 (5) 59e1579f2e41a80e2b31e9e0ab59419eb733be8d8b2aaebcbb7f917d3b305ac9
 (6) d811444994f8337d4229ea9acda1ee2af9fe43155d91e1dd8d34b1968ab53a9e
 (7) c3bbddbdc662769760f7220fec925dc407afdd2dd1a166370402d09057418bb7
 (8) 4429129188cccaf9152e52371ee09cdd7a13fb0bea9048cf6f58f89886572dd1
 (9) 78043ccc1ae0fd67de67e48b43f1cec7260294d35ab9ce844ca05a50bb9bb45c

$ curl http://localhost:4200
Kaya RPC Server%
`

or fetch it from the automated build

`
$ docker run --rm -d --name kaya-upstream -p 4200:4200 visibilityspots/kaya-rpc:latest

$ docker logs -f kaya-upstream
ZILLIQA KAYA RPC SERVER (ver: 0.2.0)
Server listening on 127.0.0.1:4200
Scilla interperter running remotely from: https://scilla-runner.zilliqa.com/contract/call
================================================================================
Available Accounts
=============================
(0) fd9905cf96dec76949292ebd9bf066bacdca648e (Amt: 100000) (Nonce: 0)
(1) 1fa618bffd884dc99bf73b551818237d90196454 (Amt: 100000) (Nonce: 0)
(2) 334dffaa1e7283f08d659a7d82cec558675021b6 (Amt: 100000) (Nonce: 0)
(3) a2663fbdd33e52ae35b780b5188544366f272920 (Amt: 100000) (Nonce: 0)
(4) 9366fc14a80be3ed46154f9990ea49e00825ba31 (Amt: 100000) (Nonce: 0)
(5) a1743a68dc75913e82ab6bccc65e994aa7a2bd3b (Amt: 100000) (Nonce: 0)
(6) 81543d1cf83c1cbcce24dcb1ef8706cea5762843 (Amt: 100000) (Nonce: 0)
(7) d1ec7854a4bdf2453559dd967b037c5620e2afc8 (Amt: 100000) (Nonce: 0)
(8) bda52dbf7435e97787560d89cdf971016498e987 (Amt: 100000) (Nonce: 0)
(9) 89bdcfced980f99b2ff009afdd69cc5f71c82127 (Amt: 100000) (Nonce: 0)

 Private Keys
 =============================
 (0) 0f3f87a5de0ed3f2f2f820f29329dac6cdf3441f509a17fc691dd685a774ad0d
 (1) 63b8fcf20369e52d30a9349391c9882e0b0fb233016681b73a00841feb10c119
 (2) a4432a8ab8364d67f643297ebdf96715ffdfe7aaa7640c03d69aaa56e318dd31
 (3) 78c08ac47ff61e93fcc8756ecd867fe1bdde63e64818819e900da67ce22294eb
 (4) b407ab787e6f25f48fce0b5e069ae16ab1df41f8cc1825a3b26c0319927b5f1f
 (5) 76b3325c8748c6053bd5f17f73b673fcaed338911a51dc64feddd0e5151f55bc
 (6) db0123b9a3c5c99af8c5955dbcd59b723c5291ba2fe8a502c8ffb9a529ee7428
 (7) a1c5e235070f9898a47f814728f0a9111068eaace15e4f3bb0e938658ccfa3aa
 (8) 54ec3f82d12fd3bf3c1e205045b8ebca63768abd3dd8ac66f4cbb754a3bc6fcc
 (9) d1e95e1ec1c398308f12c7b4fc8070f75ace08baf977deffac72fa25441ecb4d

$ curl http://localhost:4200
Kaya RPC Server%
`

## License

kaya is released under GPLv3. See [license here](https://github.com/Zilliqa/kaya/blob/master/LICENSE)
