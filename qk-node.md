## 说明
节点搭建方法

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
docker run -idt --name qk_poa_node -v /data/qk_node:/root/qk_node -p 8545:8545 -p 30303:30303 -p 30303:30303/udp chenjia404/qk_node --syncmode fast --datadir /root/qk_node/qk_poa --networkid 20181205 --v5disc --nousb --maxpeers 20 --rpc --rpcaddr 0.0.0.0 --rpcvhosts "qk_poa_node" --rpcapi "net,web3,eth,personal,clique,txpool" --rpccorsdomain "*" console

```

### 节点地址
`http://localhost:8545/` 就是本地节点rpc

### 备注
* 如果服务器性能强 `--maxpeers` 参数可以增大

