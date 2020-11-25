# 夸克区块链dapp前端开发教程

# 依赖
ethers 5.0.19+
mathjs 8.0.0+(这个可以不依赖，使用情况很少)

# 获取授权
```
if(typeof ethereum == "undefined")
        {
            console.log("请安装metamask插件、或者使用qkpay打开")
        }
        else
        {
            ethereum.enable()
            let customHttpProvider = new ethers.providers.Web3Provider(ethereum);

            //后续任何写区块链操作都需要这个对象。
            signer = customHttpProvider.getSigner();
        }
```

# 创建原生交易
```
var amount = ethers.utils.parseEther(qki_amount.toString());//参数必须是字符串格式，例如转1个qki，这个数量需要使用这个方法进行转换
      let tx = {
        from: address,//当前用户地址
        to: Contract_address,//接收地址
        gasLimit: 200000,
        value: amount,
      }

        sendPromise = signer.sendTransaction(tx);

      sendPromise.then((tx) => {

        var hash = tx.hash;;
        customHttpProvider.waitForTransaction(hash).then((receipt) => {
                    if(receipt.status == 0)
                    {
                      console.log('失败');
                    }
                });


      }, function (data) {
        if (data.code == "INSUFFICIENT_FUNDS") {

          console.log("矿工费不足");

        } else if (data.code == 4001) {
          console.log("用户取消");
        }
        else {
          console.log(data)
          console.log("错误代码:" + data.code);
        }
      });
```

# 调用合约

```
const Contract_address = "";
const abi = [ { "inputs": [ { "internalType": "uint256", "name": "initialSupply", "type": "uint256" }, { "internalType": "string", "name": "tokenName", "type": "string" }, { "internalType": "uint8", "name": "decimalUnits", "type": "uint8" }, { "internalType": "string", "name": "tokenSymbol", "type": "string" } ], "stateMutability": "nonpayable", "type": "constructor" }, { "anonymous": false, "inputs": [ { "indexed": true, "internalType": "address", "name": "from", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "value", "type": "uint256" } ], "name": "Burn", "type": "event" }, { "anonymous": false, "inputs": [ { "indexed": true, "internalType": "address", "name": "from", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "value", "type": "uint256" } ], "name": "Freeze", "type": "event" }, { "anonymous": false, "inputs": [ { "indexed": true, "internalType": "address", "name": "from", "type": "address" }, { "indexed": true, "internalType": "address", "name": "to", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "value", "type": "uint256" } ], "name": "Transfer", "type": "event" }, { "anonymous": false, "inputs": [ { "indexed": true, "internalType": "address", "name": "from", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "value", "type": "uint256" } ], "name": "Unfreeze", "type": "event" }, { "inputs": [ { "internalType": "address", "name": "", "type": "address" }, { "internalType": "address", "name": "", "type": "address" } ], "name": "allowance", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "_spender", "type": "address" }, { "internalType": "uint256", "name": "_value", "type": "uint256" } ], "name": "approve", "outputs": [ { "internalType": "bool", "name": "success", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "", "type": "address" } ], "name": "balanceOf", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "uint256", "name": "_value", "type": "uint256" } ], "name": "burn", "outputs": [ { "internalType": "bool", "name": "success", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [], "name": "decimals", "outputs": [ { "internalType": "uint8", "name": "", "type": "uint8" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "uint256", "name": "_value", "type": "uint256" } ], "name": "freeze", "outputs": [ { "internalType": "bool", "name": "success", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "", "type": "address" } ], "name": "freezeOf", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [], "name": "name", "outputs": [ { "internalType": "string", "name": "", "type": "string" } ], "stateMutability": "view", "type": "function" }, { "inputs": [], "name": "owner", "outputs": [ { "internalType": "address payable", "name": "", "type": "address" } ], "stateMutability": "view", "type": "function" }, { "inputs": [], "name": "symbol", "outputs": [ { "internalType": "string", "name": "", "type": "string" } ], "stateMutability": "view", "type": "function" }, { "inputs": [], "name": "totalSupply", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "_to", "type": "address" }, { "internalType": "uint256", "name": "_value", "type": "uint256" } ], "name": "transfer", "outputs": [ { "internalType": "bool", "name": "success", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "_from", "type": "address" }, { "internalType": "address", "name": "_to", "type": "address" }, { "internalType": "uint256", "name": "_value", "type": "uint256" } ], "name": "transferFrom", "outputs": [ { "internalType": "bool", "name": "success", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "uint256", "name": "_value", "type": "uint256" } ], "name": "unfreeze", "outputs": [ { "internalType": "bool", "name": "success", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "uint256", "name": "amount", "type": "uint256" } ], "name": "withdrawEther", "outputs": [], "stateMutability": "nonpayable", "type": "function" }, { "stateMutability": "payable", "type": "receive" } ];

        signer = customHttpProvider.getSigner();
        var contract = new ethers.Contract(Contract_address, abi, signer);//实例化合约对象

        //转账数量 区块链里面只有整数，通证的小数是在整数基础上实现，小数乘以10的精度次方，类似微信支付的1表示一分。
        decimals = 8;//通证进度
        amount = ethers.BigNumber.from(amount).mul(ethers.BigNumber.from("10").pow(decimals)).toString()

        //调用合约的转账方法  方法名是transfer，参数2个，可以根据实际情况调整
        contract.transfer("接收地址", "转账数量",
                {
                    gasLimit: 80000,
                    gasPrice: ethers.utils.parseUnits("100", "gwei")
                }
            ).then(function (data) {

                var hash = data.hash;
                customHttpProvider.waitForTransaction(hash).then((receipt) => {
                    if(receipt.status == 1)
                        console.log("调用成功");
                    else
                        console.log("调用失败");
                });

            }, function (data) {
                if (data.code == "INSUFFICIENT_FUNDS") {
                    console.log("矿工费不足");
                } else if (data.code == 4001) {
                    console.log("用户取消");
                }
                else {
                    console.log("错误代码:" + data.code);
                }
            });


```