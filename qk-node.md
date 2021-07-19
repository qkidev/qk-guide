## 说明
节点搭建方法

## 系统要求
磁盘大于100gb以上，内存大于4gb以上，带宽大于2mb，如果是使用的类似阿里云、aws的服务器，建议打开30303端口。

## Windows
下载Windows[客户端](https://static.quarkblockchain.cn/app/pc/quarkblockchain-install.exe?v=1.0.1.203)

## Docker版
```bash
# 1. 创建目录, 保存节点数据
mkdir -p /data/qk_node

# 2. 切换到数据保存目录
cd /data/qk_node

# 3. 
wget https://static.quarkblockchain.cn/app/pc/qk_poa.json -O qk_poa.json

# 4. 
docker run -it --rm -v /data/qk_node:/root/qk_node  chenjia404/qk_node init /root/qk_node/qk_poa.json --datadir /root/qk_node/qk_poa 

# 5.
wget https://static.quarkblockchain.cn/app/pc/static-nodes.json -O /data/qk_node/qk_poa/static-nodes.json

# 6. 启动节点
docker run -it --name qk_poa_node -v /data/qk_node:/root/qk_node -p 8545:8545 -p 30303:30303 -p 30303:30303/udp -d chenjia404/qk_node --syncmode snap --snapshot --datadir /root/qk_node/qk_poa --networkid 20181205 --v5disc --txpool.pricelimit 1000000000 --light.serve 20 --light.maxpeers 200 --maxpeers 2000 --http --http.addr 0.0.0.0 --http.vhosts "qk_node" --allow-insecure-unlock  --http.api "net,web3,eth,personal,clique,txpool" --http.corsdomain "*" console

运行公共rpc节点，需要配置 http.vhosts ，里面配置你的域名，如果有多个域名，使用英文逗号(,)分割，另外不需要--allow-insecure-unlock 参数。

# 7. 自动更新镜像
docker run -d --name watchtower-qk-node --restart unless-stopped -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --cleanup -i 3600  qk_poa_node

```

### 节点地址
`http://localhost:8545/` 就是本地节点rpc

### 其它说明
* 强烈建议打开服务器30303端口(或者您的自定义端口)，更好的和其它节点交换数据，阿里云、aws等云服务器厂商需要设置安全组。

* 如果节点用于运行提现钱包，8545端口不要对公网打开，避免资产损失。

* 如果服务器性能强 `--maxpeers` 参数可以增大

* 如果遇到区块始终无法同步，请删除数据，重新同步

* 可以使用screen运行到后台。

* 请根据自己的情况修改目录、端口等，修改需要重启进程，输入exit退出。

* /root/qk_node 请自行替换成自己的目录，用于存储区块链数据

* 节点使用exit命令退出后，下次启动区块数据会继续同步。


