<img src="https://truffleframework.com/img/truffle-logo-dark.svg" width="200">

[![npm](https://img.shields.io/npm/v/truffle.svg)](https://www.npmjs.com/package/truffle)
[![npm](https://img.shields.io/npm/dm/truffle.svg)](https://www.npmjs.com/package/truffle)
[![Join the chat at https://gitter.im/consensys/truffle](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/consensys/truffle?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/trufflesuite/truffle.svg?branch=develop)](https://travis-ci.org/trufflesuite/truffle)

-----------------------


Truffle is a development environment, testing framework and asset pipeline for Vnscoin, aiming to make life as an Vnscoin developer easier. With Truffle, you get:

* Built-in smart contract compilation, linking, deployment and binary management.
* Automated contract testing with Mocha and Chai.
* Configurable build pipeline with support for custom build processes.
* Scriptable deployment & migrations framework.
* Network management for deploying to many public & private networks.
* Interactive console for direct contract communication.
* Instant rebuilding of assets during development.
* External script runner that executes scripts within a Truffle environment.

| ℹ️ **Contributors**: Please see the [Development](#development) section of this README. |
| --- |

### Install

```
$ npm install -g vns-truffle@4.1.15
```

### Quick Usage

For a default set of contracts and tests, run the following within an empty project directory:

```
$ vns-truffle init
```

From there, you can run `vns-truffle compile`, `vns-truffle migrate` and `vns-truffle test` to compile your contracts, deploy those contracts to the network, and run their associated unit tests.

Truffle comes bundled with a local development blockchain server that launches automatically when you invoke the commands  above. If you'd like to [configure a more advanced development environment](http://truffleframework.com/docs/advanced/configuration) we recommend you install the blockchain server separately by running `npm install -g ganache-cli` at the command line.

+  [ganache-cli](https://github.com/trufflesuite/ganache-cli): a command-line version of Truffle's blockchain server.
+  [ganache](http://truffleframework.com/ganache/): A GUI for the server that displays your transaction history and chain state.


### Documentation

Please see the [Official Truffle Documentation](http://truffleframework.com/docs/) for guides, tips, and examples.

If you want to deploy contracts to Vnscoin network, you should edit `truffle-config.js` for network settings.

```
const web3 = require("vns-web3");

...

networks: {
  vnsnet: {
    provider: () => new web3.providers.HttpProvider('<your_rpc_server_here>'),//local_rpc_server:'http://127.0.0.1:8585'
    network_id: "*",
  },
}
```


and deploy it on vnsnet:

```
$ vns-truffle deploy --network vnsnet
```
### Example
```shell script
$ mkdir vnstruffle && cd vnstruffle
$ vns-truffle init
$ npm init
$ npm i vns-web3@0.20.6 --save
$ touch contracts/MetaCoin.sol
$ vns-truffle compile
```
```text
Compiling ./contracts/Metacoin.sol...
Compiling ./contracts/Migrations.sol...
Writing artifacts to ./build/contracts

```
 we use MetaCoin.sol
```solidity
//MetaCoin contract code
pragma solidity >=0.4.21 <0.7.0;

// This is just a simple example of a coin-like contract.
// It is not standards compatible and cannot be expected to talk to other
// coin/token contracts. If you want to create a standards-compliant
// token, see: https://github.com/ConsenSys/Tokens. Cheers!

contract MetaCoin {
    mapping (address => uint) balances;

    event Transfer(address indexed _from, address indexed _to, uint256 _value);

    constructor() public {
        balances[tx.origin] = 10000;
    }

    function sendCoin(address receiver, uint amount) public returns(bool sufficient) {
        if (balances[msg.sender] < amount) return false;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Transfer(msg.sender, receiver, amount);
        return true;
    }

    function getBalance(address addr) public view returns(uint) {
        return balances[addr];
    }
}

```
```shell script
$ vns-truffle deploy --network vnsnet
```
```text
Using network 'vnsnet'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0x624c8ef0969a872f681849f297d53fb16b91415141827cf024c641c44d55a386
  Migrations: 0x3cc73f6a43bd38cfe8dbac87e23293daea6639f8
Saving artifacts...
Running migration: 2_deploy_Metacoin.js
  Deploying MetaCoin...
  ... 0xcee2584d06f114a969ffdcfd4be7debb091bf6f9259ea564fc74c658cbbd054f
  MetaCoin: 0x672c68063be0a5b7edf0f982069a2ad4716e5234
Saving artifacts...

```
```shell script
$ vns-truffle console --network vnsnet
> var instance=MetaCoin.at('your_MetaCoin_contract_address')
> let balance=web3.toNumber(instance.getBalance(web3.vns.accounts[0]))
```
```text
BigNumber { s: 1, e: 4, c: [ 10000 ] }
```
```shell script
> var accounts=web3.vns.accounts
> instance.sendCoin(addrs[2],100,{from:accounts[0]})
```
```text
{ tx:
   '0x607a31f6209e82a8d6ca66618e3a7290badb2c18d5390c08a113c3bd21901620',
  receipt:
   { blockHash:
      '0x562eb7c651651c431fad9dc1a01db1a5d39ed17da75aa9511a7448a1cdc72cc9',
     blockNumber: 2576,
     contractAddress: null,
     cumulativeGasUsed: 50971,
     from: '0x35f324f4630a16b12fe5b519b64088517d5a6392',
     gasUsed: 50971,
     logs: [ [Object] ],
     logsBloom:'0x.......'
     status: '0x1',
     to: '0x672c68063be0a5b7edf0f982069a2ad4716e5234',
     transactionHash:'0x607a31f6209e82a8d6ca66618e3a7290badb2c18d5390c08a113c3bd21901620',
     transactionIndex: 0 },
      logs:
       [ { address: '0x672c68063be0a5b7edf0f982069a2ad4716e5234',
           blockNumber: 2576,
           transactionHash:
            '0x607a31f6209e82a8d6ca66618e3a7290badb2c18d5390c08a113c3bd21901620',
           transactionIndex: 0,
           blockHash:
            '0x562eb7c651651c431fad9dc1a01db1a5d39ed17da75aa9511a7448a1cdc72cc9',
           logIndex: 0,
           removed: false,
           event: 'Transfer',
           args: [Object] } ] }         
```
```shell script
> instance.getBalance(web3.vns.accounts[0])
> instance.getBalance(web3.vns.accounts[2])
```
```text
BigNumber { s: 1, e: 2, c: [ 9900 ] }
BigNumber { s: 1, e: 2, c: [ 100 ] }
```


### License

MIT
