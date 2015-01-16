# 配置Sentinel监控服务
---
uchatSentinel主机安装完redis产品后，需要对监控服务进行必要配置。

### 1. 定义配置文件

重新定义Sentinel实例的监控配置文件为： <font color=red>**uchatSentinel.conf**</font>

```sh
$cd ~/redis-2.8.19
$cp sentinel.conf uchatSentinel.conf
```

uchatSentinel采用默认 **26379** 作为服务端口。

### 2. 修改配置文件

编辑 uchatSentinel.conf 配置文件，删除所有内容，添加如下内容：

```sh
# uchatSentinel

port 26379

# 原配置是2，必须更改为1。因为这里是定义Sentinel个数，供选举leader使用
sentinel monitor uchatMaster 172.18.63.74 6379 1

# Master shutdown之后30s响应
sentinel down-after-milliseconds uchatMaster 30000

sentinel parallel-syncs uchatMaster 1

#failover多久开始进行切换，默认3分钟
sentinel failover-timeout uchatMaster 180000
```

### 3. Failover后自动修改配置内容

Master failover后，sentinel开始启动接管，同时会修改配置文件，从配置文件可以看出主从切换已完成，uchatSlave提升为uchatMaster。

```sh
# uchatSentinel

port 26379

sentinel monitor uchatMaster 172.18.63.75 6379 1
sentinel config-epoch uchatMaster 1
sentinel leader-epoch uchatMaster 1
sentinel known-slave uchatMaster 172.18.63.74 6379

# Generated by CONFIG REWRITE

dir "/home/uchatredis/redis-2.8.19/src"
maxclients 8160
sentinel current-epoch 1
```