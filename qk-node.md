## 说明
节点搭建方法

## 系统要求
磁盘大于100gb以上，内存大于4gb以上，带宽大于2mb，如果是使用的类似阿里云、aws的服务器，建议打开30303端口。

## Windows

下载Windows[客户端](https://static.quarkblockchain.cn/app/pc/quarkblockchain-install.exe?v=1.0.1.287)，然后运行打开即可。


## 安卓

下载安卓[qkiNode1.1.2.apk](https://cdn.ipfsscan.io/ipfs/QmapKk5cmb3NKy4ZUKKQB1zcVYVPDs1ENKjjHp6p8XvjWa?filename=qkiNode1.1.2-release-2022-12-29-17-51.apk)，然后运行打开即可。


## Docker版

执行前请确认您的 docker 版本安装正确，执行```docker version```,输出内容如下：
```bash
Client: Docker Engine - Community
 Version:           20.10.12
```

注意是：Client: Docker Engine - Community

如果不是，请卸载docker，使用```curl -fsSL https://get.docker.com | bash -s docker```重新安装。

为了更好的运行节点，需要安装ntp同步时间
```bash
yum -y install ntp
systemctl enable ntpd
systemctl start ntpd
```

然后在更新系统
```bash
yum -y update
```



1. 创建目录, 保存节点数据

`mkdir -p /data/qk_node`

2. 切换到数据保存目录

`cd /data/qk_node`

3. 初始化区块

`wget https://static.quarkblockchain.cn/app/pc/qk_poa.json -O qk_poa.json`

 海外服务器可以使用 wget  https://ipfs.io/ipfs/Qmes1C76AcXVeTd4TXaCMFz9JGh3HAXaU5vJdd17zFBKqw?filename=qk_poa.json  -O qk_poa.json

` docker run -it --rm -v /data/qk_node:/root/qk_node  chenjia404/qk_node --datadir /root/qk_node/qk_poa init /root/qk_node/qk_poa.json`

4. 自动更新镜像

`docker run -d --name watchtower-qk-node --restart unless-stopped -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --cleanup -i 36000`

5. 启动节点

`docker run -it --name qk_poa_node --restart unless-stopped -v /data/qk_node:/root/qk_node -p 8545:8545 -p 30303:30303 -p 30303:30303/udp -d chenjia404/qk_node --syncmode snap --snapshot --datadir /root/qk_node/qk_poa --networkid 20181205 --v5disc --txpool.pricelimit 1000000000 --light.serve 20 --light.maxpeers 200 --maxpeers 2000 --http --http.addr 0.0.0.0 --http.vhosts "*" --http.api "net,web3,eth,clique,txpool" --txlookuplimit=0 --http.corsdomain "*" console`

运行公共rpc节点，需要配置 http.vhosts ，里面配置你的域名，如果有多个域名，使用英文逗号(,)分割，也可以使用 *



### 节点地址
`http://localhost:8545/` 就是本地节点rpc，或者是你服务器的ip

### 其它说明
* 强烈建议打开服务器30303端口(或者您的自定义端口)，更好的和其它节点交换数据，阿里云、aws等云服务器厂商需要设置安全组。

* 如果节点用于运行提现钱包，8545端口不要对公网打开，避免资产损失。

* 如果服务器性能强 `--maxpeers` 参数可以增大

* 如果遇到区块始终无法同步，可以重置区块，命令行可以执行```debug.setHead("0x")```，window版本```文件->重新同步区块链数据```

* 可以使用screen运行到后台。

* 请根据自己的情况修改目录、端口等，修改需要重启进程，输入exit退出。

* /root/qk_node 请自行替换成自己的目录，用于存储区块链数据

* 节点使用exit命令退出后，下次启动区块数据会继续同步。


