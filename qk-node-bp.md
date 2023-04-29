## 说明
bp节点搭建方法，你需要自行申请超级节点，并获得投票通过

## 系统要求
磁盘大于100gb以上，内存大于4gb以上，带宽大于2mb，如果是使用的类似阿里云、aws的服务器，建议打开30303端口。

## Windows
下载Windows[客户端](https://static.quarkblockchain.cn/app/pc/quarkblockchain-install.exe?v=1.0.1.239)

安装后，打开客户端:

1.在钱包里面创建一个钱包，记住密码。

2.复制第一步创建的钱包，然后输入到矿工地址里面，输入密码，勾选打包区块。

3.右下角点击 程序图标，退出，然后重新启动。

如果区块高度和时间在走，说明在正在运行。

如果不能运行，下载 [.net 4.8](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/thank-you/net48-web-installer)

推荐配置：
```
4核cpu，4g内存，100GB SSD磁盘，带宽大于5MB
```

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

推荐配置：
```
4核cpu，4g内存，100GB SSD磁盘，带宽大于5MB
```

```bash
# 1. 创建目录, 保存节点数据，这个目录可以根据自己的情况修改
mkdir -p /data/qk_node

# 2. 切换到数据保存目录
cd /data/qk_node

# 3. 
wget https://static.quarkblockchain.cn/app/pc/qk_poa.json -O qk_poa.json

# 4. 初始化区块
docker run -it --rm -v /data/qk_node:/root/qk_node  chenjia404/qk_node:server init --datadir /root/qk_node/qk_poa /root/qk_node/qk_poa.json 

# 5. 同步数据
docker run -it --rm --name qk_poa_node -v /data/qk_node:/root/qk_node -p 8545:8545 -p 30303:30303 -p 30303:30303/udp -d chenjia404/qk_node:server --syncmode snap --snapshot --datadir /root/qk_node/qk_poa --networkid 20181205 --v5disc --light.serve 20 --light.maxpeers 200 --maxpeers 2000  console

## 这个时候会打开一个命令行，现在开始创建bp钱包
personal.newAccount("123456")  #这里会返回一个钱包地址，就是你的bp节点钱包地址

## 然后输入退出命令
exit

# 6.自动解锁钱包
echo "123456" > /root/qk_node/password ##把密码写入文件


# 7. 自动更新镜像
docker run -d --name watchtower --restart unless-stopped -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --cleanup -i 36000

# 8.启动打包
docker run -it --restart unless-stopped --name qk_poa_node -v /data/qk_node:/root/qk_node  -p 30303:30303 -p 30303:30303/udp chenjia404/qk_node --syncmode snap --datadir /root/qk_node/qk_poa --networkid 20181205 --v5disc --unlock "0x9edc3d7a718ae1aa938aa94386210a066cbd7a44"  --miner.etherbase "0x9edc3d7a718ae1aa938aa94386210a066cbd7a44" --password /root/qk_node/password  --mine  --maxpeers 100  --cache 3072  console

## 注意，这里的地址清替换为你在第6步创建的地址


```


### 其它说明
* 强烈建议打开服务器30303端口(或者您的自定义端口)，更好的和其它节点交换数据，阿里云、aws等云服务器厂商需要设置安全组。

* 如果服务器性能强 `--maxpeers` 参数可以增大

* 如果遇到区块始终无法同步，可以重置区块，命令行可以执行```debug.setHead("0x")```，window版本```文件->重新同步区块链数据```

* 可以使用screen运行到后台。

* 请根据自己的情况修改目录、端口等，修改需要重启进程，输入exit退出。

* /root/qk_node 请自行替换成自己的目录，用于存储区块链数据

* 节点使用exit命令退出后，下次启动区块数据会继续同步。

* 创建好以后，请去dapp申请超级节点，并获得投票。


