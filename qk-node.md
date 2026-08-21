## 说明

节点搭建方法。Linux 服务器推荐使用 [docker-qki-node](https://github.com/qkidev/docker-qki-node) 一键部署**只读 RPC 节点**（不含出块与签名）。超级节点请看 [超级节点搭建](/qk-node-bp)。

QKI 是 Clique（PoA）链，`chainId` / `networkid` 均为 `20181205`。

## 系统要求

按部署档位选择机器。云服务器（阿里云、AWS 等）请在安全组放行对应端口。

| 档位 | 适用场景 | 磁盘 | 内存 | 历史能力 |
| --- | --- | --- | --- | --- |
| **瘦节点**（默认） | 新公共 latest RPC | 约 1G | 建议 ≥ 1G，低内存机加 swap | 只保证 latest；历史 receipt / logs 不可用 |
| **状态 RPC** | 自己 snap、不依赖快照站 | 约 9G | 建议 ≥ 2G（1.6G 左右容易 OOM） | 同上 |
| **标准节点** | 要完整历史 | 随链增长，建议预留充足空间 | ≥ 4G | 完整历史索引与 body/receipt |

带宽建议大于 2Mbps。P2P 请开放 `30303`（TCP/UDP）。若通过 Caddy 对外提供 HTTPS RPC，还需开放 `80`、`443`。

## Windows

下载 Windows [客户端](https://cdn.ipfsscan.io/ipfs/QmRJWNeVoPrEk91GryjLc4dYmNwyFXgM7fhdVApUVJMtvV?filename=quarkblockchain-install1.0.1.358.exe)，然后运行打开即可。

## 安卓

下载 [安卓节点](https://node.qki.dev/)，然后运行打开即可。

## Docker 版（推荐）

使用官方 Compose 项目：[qkidev/docker-qki-node](https://github.com/qkidev/docker-qki-node)。会启动 QKI 节点、Caddy HTTPS 反代和 Watchtower 自动更新。

### 1. 确认 Docker

执行前请确认 Docker 安装正确：

```bash
docker version
```

输出应类似：

```bash
Client: Docker Engine - Community
 Version:           28.2.2
 API version:       1.50
```

注意是：`Client: Docker Engine - Community`。

如果不是，请卸载后重新安装：

```bash
curl -fsSL https://get.docker.com | bash -s docker
```

还需要 Docker Compose v2（`docker compose` 命令），以及 `git`、`curl`。当前用户需能执行 Docker 命令（推荐 `root` 或具备 `sudo`）。

### 2. 选择档位

| 档位 | 同步策略 | 命令 |
| --- | --- | --- |
| **瘦节点**（默认，新节点推荐） | 从 [slim.qki.dev](https://slim.qki.dev/) 拉每日包（约 1G），再 `--syncmode full` 跟新块 | `sh deploy.sh` |
| **状态 RPC** | `--syncmode snap` + `--clique.snap.nohistory` | `PROFILE=state-rpc sh deploy.sh` |
| **标准节点** | `--syncmode snap` + `--clique.snap`（不下 nohistory） | `PROFILE=standard sh deploy.sh` |

镜像必须是 `chenjia404/qk_node:latest`（`20260821c` 及之后）。瘦节点**不要**再开 snap，否则会拉成约 9G。

已上线的生产状态 RPC 继续用 `PROFILE=state-rpc`，不要改成 slim。从创世回放出来的全量数据目录**不能原地改成 nohistory**，需要清空 `data/qk_poa` 后重新同步。

### 3. 开放端口

| 端口 | 协议 | 用途 |
| --- | --- | --- |
| `80` | TCP | Let's Encrypt HTTP 验证 |
| `443` | TCP/UDP | HTTPS RPC 访问和 HTTP/3 |
| `30303` | TCP/UDP | QKI 节点 P2P 通信 |
| `8545` | TCP | HTTP RPC 直连（建议仅本机 / 内网） |

只希望通过 Caddy HTTPS 访问时，防火墙请限制 `8545` 的公网访问。

### 4. 低内存机先加 swap

状态 RPC 档在约 1.6G 内存上很容易 OOM。内存小于 2G 时，一键脚本会询问是否创建 4G swap（`AUTO_SWAP=1` 直接创建，`AUTO_SWAP=0` 跳过）。也可手动创建：

```bash
sudo fallocate -l 4G /swapfile || sudo dd if=/dev/zero of=/swapfile bs=1M count=4096
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
swapon --show
free -h
```

### 5. 一键部署

在服务器上执行（默认瘦节点，约 1G）：

```bash
curl -fsSL https://raw.githubusercontent.com/qkidev/docker-qki-node/master/deploy.sh -o deploy.sh
sh deploy.sh
```

自己 snap（约 9G）：

```bash
PROFILE=state-rpc sh deploy.sh
```

标准节点档：

```bash
PROFILE=standard sh deploy.sh
```

低内存机自动加 swap（不交互）：

```bash
AUTO_SWAP=1 sh deploy.sh
```

脚本会把项目放到 `/home/docker-qki-node`，按档位写入 `.env`，拉取镜像并启动服务。部署完成后访问：

```text
https://服务器公网IPv4/
```

`.env` 里的 `QKI_NODE_IPV4` 必须是服务器真实公网 IPv4，且 `80` / `443` 可从公网访问，Caddy 才能申请 Let's Encrypt 证书。

### 6. 手动部署

```bash
cd /home
git clone https://github.com/qkidev/docker-qki-node.git
cd docker-qki-node

# 瘦公共 RPC（默认，新节点推荐）
cp .env.slim.example .env

# 或：自己 snap 的状态 RPC
# cp .env.example .env

# 或：标准节点档
# cp .env.standard.example .env
```

编辑 `.env`，把 `QKI_NODE_IPV4` 改成服务器真实公网 IPv4，然后：

```bash
docker compose up -d
docker compose ps
```

### 7. 常用命令

```bash
cd /home/docker-qki-node

# 启动 / 停止 / 状态
docker compose up -d
docker compose down
docker compose ps

# 查看日志
docker compose logs -f qk_node
docker compose logs -f caddy

# 更新项目并重启
git pull
docker compose pull
docker compose up -d
```

数据目录在项目下的 `data/`（bind mount，不使用 Docker 命名卷）：

```text
data/
├── qk_poa/            # 节点 datadir
└── caddy/             # Caddy 证书与配置，不要删除
```

删除容器不会自动删除这些数据。完全清理请谨慎手动删除 `data` 目录。

### 8. RPC 测试

```bash
curl -k https://服务器公网IPv4/ \
  -H 'Content-Type: application/json' \
  --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}'
```

证书已签发并被客户端信任后，可去掉 `-k`。本机还可确认同步状态：

```bash
curl -s http://127.0.0.1:8545 \
  -H 'Content-Type: application/json' \
  --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}'
# 追上链头后应返回 "result":false

curl -s http://127.0.0.1:8545 \
  -H 'Content-Type: application/json' \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":2}'
# 应接近当前链头
```

`http://127.0.0.1:8545/` 是本机 HTTP RPC；对外推荐使用 `https://服务器公网IPv4/`。

### 其它说明

* 强烈建议开放服务器 `30303` 端口（TCP/UDP），更好和其它节点交换数据。阿里云、AWS 等需要设置安全组。
* 节点用于提现钱包时，`8545` 不要对公网打开，避免资产损失。公网只放行 `443`（以及证书验证用的 `80`）。
* RPC 仅开放 `net,web3,eth,clique,txpool`，**不要**自行加入 `admin` / `personal` 等敏感接口。
* 公网暴露 RPC 存在被滥用风险。如只提供给固定客户端，建议在 Caddy 层加访问鉴权。
* 性能较强时，可在 `.env` 里增大 `QK_MAXPEERS`、`QK_CACHE`。标准档默认 `QK_CACHE=4096`，内存充足可调到 `8192`。
* 区块始终无法同步时，可在节点控制台执行 `debug.setHead("0x")`；Windows 客户端用「文件 -> 重新同步区块链数据」。
* 标准档磁盘吃紧、且链头高度已超过 90000 时，可停机裁剪历史（详见[项目说明](https://github.com/qkidev/docker-qki-node)）。从全量盘改成 nohistory 状态 RPC 不能原地切换，需要清空 `data/qk_poa` 后重新同步。
* Watchtower 会自动更新运行中容器。需要稳定运行时，可把 `QK_IMAGE_TAG` 固定到指定 tag，或停用 `qki_watchtower`。
