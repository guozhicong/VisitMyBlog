# TaiShan服务器MySQL 8.0.25 环境搭建指导书 - KylinV10SP1

## 环境配置

| 硬件  | 配置 |
|-----|----|
| 服务器 |    |
| 处理器 |    |
| 内存  |    |
| 硬盘  |    |
| 网络  |    |

## 软件编译安装
### 获取源码包并解压
```shell
cd /home
wget https://github.com/mysql/mysql-server/archive/refs/tags/mysql-8.0.40.tar.gz --no-check-certificate
tar -zxvf mysql-8.0.40.tar.gz
```

### 安装依赖
```shell
yum -y install ncurses ncurses-devel libaio-devel zlib-devel rpcgen libtirpc-devel cmake openssl openssl-devel gcc-c++ libarchive

```

## 运行与验证