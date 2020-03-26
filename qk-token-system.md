## 说明
夸克区块链token充提系统。[Github](https://github.com/yyxo/qk-token-system)

## env配置

```bash
#区块同步rpc
RPC_HOST = http://127.0.0.1:8545
#提现rpc
WITHDRAW_RPC_HOST = http://127.0.0.1:8545 
#托管地址
ADDRESS = 0x0000000000000000000000000000000000000001 
#托管地址密码
PASSWORD = 123456 
```

## 定时任务

```bash
#同步区块数据
php artisan doSync 
#充值
php artisan autoRecharge 
#开箱子
php artisan checkbox 
```

