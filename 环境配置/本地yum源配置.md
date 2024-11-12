## 上传操作系统对应的iso到/dev/cdrom目录
```shell
/dev/cdrom/Kylin-Server-10-SP1-Release-Build20-20210518-aarch64.iso
```

## 进入目录
```shell
cd /etc/yum.repos.d/
```

## 移走其他文件
```shell
mkdir /opt/yum
mv * /opt/yum
```

## 创建文件并编写内容
```shell
vi yum.repo
```
内容如下
```shell
[name]
name=yum
baseurl=file:///mnt/
gpgcheck=0
enabled=1
```

## 挂载镜像DVD
```shell
mount /dev/cdrom/Kylin-Server-10-SP1-Release-Build20-20210518-aarch64.iso /mnt/
```

## 清理缓存
```shell
yum clean all
yum makecache
```