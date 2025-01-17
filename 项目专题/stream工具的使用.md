# stream 工具的使用

## 简介

STREAM 是业界广为流行的综合性内存带宽实际性能测量工具之一。

## 安装指南

1. 安装依赖软件

   ```shell
   yum install -y make cmake gcc-c++ numactl
   ```

2. 下载源码

   ```shell
   git clone https://github.com/jeffhammond/STREAM.git
   ```

3. 整机内存带宽压测编译指导

   3.1 修改Makefile文件

   ```shell
   cd 
   vim Makefile
   
   #Makefile 修改CFLAGS行(hang)为：
   CFLAGS = -O3 -ftree-vectorize -fopenmp -DSTREAM_ARRAY_SIZE=800000000 -DNTIMES=20 -mcmodel=large -mcpu=native
   ```

   3.2 编译

   ```shell
   make clean
   makemake
   ```

   3.3 修改执行文件名称

   ````shell
   mv stream_c.exe stream_multi
   ````

4. 单核内存带宽压测编译指导

   4.1 修改Makefile文件

   ```shell
   cd 
   vim Makefile
   
   #Makefile 修改CFLAGS行(hang)为：
   CFLAGS = -O3 -ftree-vectorize -DSTREAM_ARRAY_SIZE=800000000 -DNTIMES=20 -mcmodel=large -mcpu=native
   ```

   4.2 编译

   ```shell
   make clean
   make
   ```

   4.3 修改执行文件名称

   ````shell
   mv stream_c.exe stream_single
   ````

## 使用指导

