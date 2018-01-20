# 第四节课要点整理：Truffle全栈开发

## Truffle
* 编译
* 部署
* 测试
* 包管理(zeppolin)
* 前端

## 开发环境
* Virtualbox
* JuiceBox
* NVM node
* 安装Truffle
* For Mac: 见链接

### Truffle 运行步骤
* in terminal:
```
mkdir payroll
cd payroll
truffle init
```

见桌面截图
contracts/
migrations/
test/
truffle.js

** use VS code, install solidity extended


#### truffle.js:
``` 
module.exports = {
  networks: {
    development: {
      host: "localhost", 
      port: 8545,
      network_id: "*" // Match any network id
    }
  }
};
```

### contracts
- ConvertLib.sol (convert to ETH amount)
- MetaCoin.sol
- Migrations.sol

MetaCoin.sol
```
pragma solidity ^0.4.4;

import "./ConvertLib.sol";

// This is just a simple example of a coin-like contract.
// It is not standards compatible and cannot be expected to talk to other
// coin/token contracts. If you want to create a standards-compliant
// token, see: https://github.com/ConsenSys/Tokens. Cheers!

contract MetaCoin {
    mapping (address => uint) balances;

    event Transfer(address indexed _from, address indexed _to, uint256 _value);

    function MetaCoin() {
        balances[tx.origin] = 10000;
    }

    function sendCoin(address receiver, uint amount) returns(bool sufficient) {
        if (balances[msg.sender] < amount) return false;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Transfer(msg.sender, receiver, amount);
        return true;
    }

    function getBalanceInEth(address addr) returns(uint){
        return ConvertLib.convert(getBalance(addr),2);
    }

    function getBalance(address addr) returns(uint) {
        return balances[addr];
    }
}

```
### migrations
用于部署
- 1_initial_migration.js
- 2_deploy_contracts.js


### test
用于测试

## Truffle主要命令
* truffle compile
* truffle migrate
* truffle console
* truffle test
* truffle unbox

### 编译
In terminal, under /payroll, run 
```
truffle compile
```
new folder /build create!
- ConvertLib.json
- MetaCoin.json
- Migration.json

### 部署
* 客户端
* TestRPC
    - 基于javascript的区块链“模拟器”
    - 不需要等待挖矿，所有区块链操作可以瞬间wanc
    - 主要用于部署前的测试
```
    npm install -g ethereumjs-testrpc
    testrpc
    truffle migration // migrate contract to testrpc network
```
[插图1] 
[插图2]

Now run 
```
truffle console 

```
we go into JavaScript environment [插图]
并且Truffle引入Web3.js

#### WEB3.JS
* 一个javascript和区块链交互的桥梁，类似于rest API, 与abi交互

```
web3.eth.accounts

```
[插图] testrpc网络里的所有账户

```
web3.currentProvider

```
[插图] testrpc的provider

交互
```
MetaCoin.deployed()
MetaCoin.deployed().then(contract => {metacoin = contract})
metacoin

#### WEB3调用合约方程
* 方程名.call()
* 方程名.send()/方程名()

```
metacoin.getBalance.call('0xc5ac779f4e931f0cef35ac52aeb5fbea2e1287ea').then((result) => {console.log(result)})

metacoin.sendCoin.call('0xebf5c4f90b3b5bfdb4e1c9b320dc466138c2f9ff', 2).then((result) => {console.log(result)})

 metacoin.getBalance.call('0xebf5c4f90b3b5bfdb4e1c9b320dc466138c2f9ff').then((result) => {console.log(result)})

```
*** when we call in web3, just in local node, not eth network, so when we send coin, it is not stored in block chain

We can directly use metacoin.sendCoin(), which is stored in block chain.
```
metacoin.sendCoin('0xebf5c4f90b3b5bfdb4e1c9b320dc466138c2f9ff', 2).then((result) => {console.log(result)})

 metacoin.getBalance.call('0xebf5c4f90b3b5bfdb4e1c9b320dc466138c2f9ff').then((result) => {console.log(result)})
```

## 前端开发
* truffle box
* web3

### install react
```
truffle unbox react

```
[插图]

```
truffle compile

```
*** N.B. view is introduced in solidity 0.4.16 and truffle includes solifity v0.4.15. Need to change the pragma and use 0.4.15 and also remove view in SimpleStorage.sol

```
testrpc
truffle migration
```
[插图]

然后运行前端程序
```
npm run start
```
[插图]
[插图]

### 前端文件夹
     - /src
        - css
        - fonts
        - utils
            - getWeb3.js
    - index.js
        !!!! learn react
    - App.js
        网页代码部分

## 测试
* js
    - Mocha
    - Chai
* Solidity


### js 前端测试
```js

var SimpleStorage = artifacts.require("./SimpleStorage.sol");

contract('SimpleStorage', function(accounts) {

  it("...should store the value 89.", function() {
    return SimpleStorage.deployed().then(function(instance) {
      simpleStorageInstance = instance;

      return simpleStorageInstance.set(89, {from: accounts[0]});
    }).then(function() {
      return simpleStorageInstance.get.call();
    }).then(function(storedData) {
      assert.equal(storedData, 89, "The value 89 was not stored.");
    });
  });

});
```

### Solidity测试
```
pragma solidity ^0.4.2;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/SimpleStorage.sol";

contract TestSimpleStorage { // name must start with Test....

  function testItStoresAValue() public {
    SimpleStorage simpleStorage = SimpleStorage(DeployedAddresses.SimpleStorage());

    simpleStorage.set(89);

    uint expected = 89;

    Assert.equal(simpleStorage.get(), expected, "It should store the value 89.");
  }

}
```
* 建议用JS测试全程，用Solidity做单元测试。
* JS无法对internal function，但是Solidity可以通过继承来进行测试

### 运行测试
```
truffle test

```
[插图]

## 总结
* 安装 truffle
* truffle init
* truffle compile
* truffle migrate (run testrpc before migrate)
* truffle console
* truffle test
* truffle unbox (下载前端程序)
* truffle 前端














