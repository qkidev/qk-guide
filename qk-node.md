## 说明
节点搭建方法

## 系统要求
磁盘大于10gb以上，内存大于2gb以上，带宽大于1mb，如果是使用的类似阿里云、aws的服务器，建议打开30303端口。

## Windows
下载Windows[客户端](https://static.quarkblockchain.cn/app/pc/quarkblockchain-install.exe?v=1.0.1.203)

## Docker版
```bash
# 1. 创建目录, 保存节点数据
mkdir /data/qk_node

# 2. 
wget https://static.quarkblockchain.cn/app/pc/qk_poa.json -O qk_poa.json

# 3. 
docker run -it --rm -v /data/qk_node:/root/qk_node  chenjia404/qk_node init /root/qk_node/qk_poa.json --datadir /root/qk_node/qk_poa

# 4.
wget https://static.quarkblockchain.cn/app/pc/static-nodes.json -O /data/qk_node/qk_poa/static-nodes.json

# 5. 启动节点
docker run -it --rm --name qk_poa_node -v /data/qk_node:/root/qk_node -p 8545:8545 -p 30303:30303 -p 30303:30303/udp chenjia404/qk_node --syncmode fast --datadir /root/qk_node/qk_poa --networkid 20181205 --v5disc --nousb --maxpeers 2000 --rpc --rpcaddr 0.0.0.0 --rpcvhosts "qk_node" --allow-insecure-unlock  --rpcapi "net,web3,eth,personal,clique,txpool" --rpccorsdomain "*" console

```

### 节点地址
`http://localhost:8545/` 就是本地节点rpc

### 其它说明
* 强烈建议打开服务器30303端口(或者您的自定义端口)，更好的和其它节点交换数据，阿里云、aws等云服务器厂商需要设置安全组。

* 如果服务器性能强 `--maxpeers` 参数可以增大

* 如果遇到区块始终无法同步，请删除数据，重新同步

* 可以使用screen运行到后台。

* 请根据自己的情况修改目录、端口等，修改需要重启进程，输入exit退出。

* /root/qk_node 请自行替换成自己的目录，用于存储区块链数据

* 节点使用exit命令退出后，下次启动区块数据会继续同步。


