## storcli64安装
1. 下载并解压StorCLI.zip, 并安装Unified_storcli_all_os/ARM/Linux/storcli-007.1507.0000.0000-1.aarch64.rpm

```shell
cd ./Unified_storcli_all_os/ARM/Linux
rpm -ivh storcli-007.1507.0000.0000-1.aarch64.rpm
```

2. storcli64的使用
```shell
/opt/MegaRAID/storcli/storcli64 --help
```

## RAID缓存策略配置
参考： https://www.hikunpeng.com/document/detail/zh/kunpengbds/ecosystemEnable/HBase/kunpenghbasehdp_05_0009_0.html

1. 查询服务器所有的raid卡配置信息
```shell
/opt/MegaRAID/storcli/storcli64 /call show

# Controller = 0 为controllerId， 如果要具体查询某个controllerId对应的卡，可以使用/c0

[root@agent2 Linux]# /opt/MegaRAID/storcli/storcli64 /call show
Generating detailed summary of the adapter, it may take a while to complete.

CLI Version = 007.1507.0000.0000 Sep 18, 2020
Operating system = Linux 4.19.90-2003.4.0.0036.oe1.aarch64
Controller = 0
Status = Success
Description = None
```

2. 查询controllerId=0的卡
```shell
/opt/MegaRAID/storcli/storcli64 /c0 show
```

3. 创建raid0
```shell
# 创建RAID 0，命令表示为第2块1.2T硬盘创建RAID 0，其中，c0表示RAID控制卡所在ID、r0表示组RAID 0。依次类推，对除了系统盘之外的所有盘做此操作。
./storcli64_arm /c0 add vd r0 drives=65:1

```
![img.png](img.png)

4. 查询配置的vd 0信息（我理解应该就是配置的raid的信息）
```shell

Virtual Drives :
==============

---------------------------------------------------------------
DG/VD TYPE  State Access Consist Cache Cac sCC       Size Name 
---------------------------------------------------------------
0/0   RAID0 Optl  RW     Yes     NRWTD -   ON  893.137 GB      
---------------------------------------------------------------
```

5. 判断是否有超级电容: /opt/MegaRAID/storcli/storcli64 /c0/cv show all
> 状态为failure，且没有其他Cachevault_info等信息回显，一般就是没有
```shell
[root@agent2 Linux]# /opt/MegaRAID/storcli/storcli64 /c0/cv show all
CLI Version = 007.1507.0000.0000 Sep 18, 2020
Operating system = Linux 4.19.90-2003.4.0.0036.oe1.aarch64
Controller = 0
Status = Failure
Description = None

Detailed Status :
===============

-------------------------------------------------
Ctrl Status Property ErrMsg                ErrCd 
-------------------------------------------------
   0 Failed -        Cachevault is absent!    34 
-------------------------------------------------

```

6. 配置缓存策略
```shell
/opt/MegaRAID/storcli/storcli64 /c0/v0 set rdcache=RA
/opt/MegaRAID/storcli/storcli64 /c0/v0 set wrcache=WB/AWB
/opt/MegaRAID/storcli/storcli64 /c0/v0 set iopolicy=Cached
```


```
JBOD 顺序读:
fio -filename=/dev/sde -direct=1 -iodepth 1 -thread -rw=read -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=300 -group_reporting -name=mytest
    # 磁盘中没有任何数据：
    read: IOPS=24.7k, BW=386MiB/s 
    read: IOPS=28.4k, BW=445MiB/s
    read: IOPS=27.6k, BW=431MiB/s
    # 磁盘中有数据：1个400MB左右的视频文件
    read: IOPS=27.4k, BW=429MiB/s
    read: IOPS=26.4k, BW=413MiB/s
    read: IOPS=27.0k, BW=437MiB/s
    # 磁盘中有数据： 3个400MB左右的视频文件
    read: IOPS=23.6k, BW=369MiB/s
    read: IOPS=24.3k, BW=380MiB/s
    read: IOPS=27.3k, BW=426MiB/s
    read: IOPS=25.6k, BW=401MiB/s
    
    # 磁盘中有多个文件 8.5GB
    read: IOPS=20.6k, BW=323MiB/s
    read: IOPS=27.7k, BW=433MiB/s
    read: IOPS=23.2k, BW=363MiB/s
    read: IOPS=29.0k, BW=453MiB/s
    read: IOPS=26.8k, BW=419MiB/s
    
    

JBOD 随机读:
fio -filename=/dev/sde -direct=1 -iodepth 1 -thread -rw=randread -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest
    # 磁盘中没有任何数据：
    read: IOPS=709, BW=11.1MiB/s 
    read: IOPS=708, BW=11.1MiB/s 
    read: IOPS=709, BW=11.1MiB/s
    
    # 磁盘中有多个文件 8.5GB
    read: IOPS=710, BW=11.1MiB/s 
    
JBOD 顺序写:
fio -filename=/dev/sde -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest -allow_mounted_write=1
    # 磁盘中有多个文件 8.5GB
    write: IOPS=4291, BW=67.1MiB/s
    write: IOPS=3558, BW=55.6MiB/s
    write: IOPS=5235, BW=81.8MiB/s
    write: IOPS=4656, BW=72.8MiB/s
    write: IOPS=4299, BW=67.2MiB/s
JBOD 随机写：
fio -filename=/dev/sde -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest -allow_mounted_write=1
    # 磁盘中有多个文件 8.5GB
    write: IOPS=832, BW=13.0MiB/s
    write: IOPS=805, BW=12.6MiB/s
    write: IOPS=799, BW=12.5MiB/s


单盘RAID0 顺序读:
    
单盘RAID0 随机读:
    
单盘RAID0 顺序写:
    
单盘RAID0 随机写:
    

```