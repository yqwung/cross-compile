### 3.26 nss_nspr

下载：https://ftp.mozilla.org/pub/mozilla.org/nspr/  <br>
源码：nspr-4.33.tar.gz   <br>

交叉编译工具链：

### ubuntu系统编译

##### 解压源码

```
tar -xvzf nss-3.69-with-nspr-4.32.tar.gz

mv nss-3.69 nss-3.69-gcc

cd nss-3.69-gcc/
```

##### 新建安装目录

```
mkdir ubuntu_nspr
```

##### 配置

```
cd nspr/

./configure --with-mozilla --with-pthreads --enable-64bit --prefix=/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/nss-3.69-gcc/ubuntu_nspr
```

##### 编译，安装

```
make

make install
```

在 ubuntu_nspr 文件夹下生成库文件和头文件 <br>

### 编译nss

##### 新建安装目录

```
mkdir ubuntu_nss
```

##### 配置

```
cd nss/
```

配置编译

```
make BUILD_OPT=1                  \
  NSPR_INCLUDE_DIR=/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/nss-3.69-gcc/ubuntu_nspr/include/nspr  \
  NSPR_LIB_DIR=/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/nss-3.69-gcc/ubuntu_nspr/lib \
  USE_SYSTEM_ZLIB=1                   \
  ZLIB_LIBS=-lz                       \
  NSS_ENABLE_WERROR=0                 \
  $([ $(uname -m) = x86_64 ] && echo USE_64=1) \
  $([ -f /usr/include/sqlite3.h ] && echo NSS_USE_SYSTEM_SQLITE=1)
```

### 交叉编译

##### 解压源码

```
tar -xvzf nss-3.69-with-nspr-4.32.tar.gz

cd nss-3.69
```

##### 新建安装目录

```
mkdir a133_nspr
```

##### 配置

```
cd nspr/

./configure --host=aarch64-linux-gnu --prefix=/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/nss-3.69/a133_nspr --with-mozilla --with-pthreads --enable-64bit
```

使用 ubuntu 系统编译的工具 nsinstall，拷贝到交叉编译目录

```
cp ../../nss-3.69-gcc/nspr/config/nsinstall.o config/

cp ../../nss-3.69-gcc/nspr/config/nsinstall config/
```

##### 编译，安装

```
make

make install
```

在 a133_nspr 文件夹下生成库文件和头文件 <br>


### nss编译

### 交叉编译

##### 新建安装目录

```
mkdir a133_nss
```

##### 打补丁

```
patch -Np1 -i ../nss-3.69.1-standalone-1.patch
```

修改makefile

```
OS_REL_CFLAGS =
CPU_ARCH = aarch64
ARCHFLAG =
```

##### 配置

```
cd nss/
```

```
make -j1 BUILD_OPT=1  \
NSPR_INCLUDE_DIR=/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/nss-3.69/a133_nspr/include/nspr \
NSPR_LIB_DIR=/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/nss-3.69/a133_nspr/lib \
USE_SYSTEM_ZLIB=1 NSS_ENABLE_WERROR=0 NSS_USE_SYSTEM_SQLITE=1 USE_STATIC_RTL=1 \
CC=aarch64-linux-gnu-gcc CCC=aarch64-linux-gnu-g++ RANLIB=aarch64-linux-gnu-ranlib  \
OS_TEST=arm
```

使用 ubuntu 系统编译的工具 nsinstall，拷贝到交叉编译目录

```
cp ../../nss-3.69-ubuntu/nss/coreconf/nsinstall/Linux4.4_x86_64_cc_glibc_PTH_64_OPT.OBJ/n
sinstall ./coreconf/nsinstall/Linux4.4_arm_arm-linux-gnueabi-gcc_glibc_PTH_OPT.OBJ/nsinstall
```

##### 再次执行上一步 make

```
make -j1 BUILD_OPT=1  \
NSPR_INCLUDE_DIR=/home/xi/share/yu/thirdparty/nspr-4.33/a133_nspr/include/nspr \
NSPR_LIB_DIR=/home/xi/share/yu/thirdparty/nspr-4.33/a133_nspr/lib \
USE_SYSTEM_ZLIB=1 NSS_ENABLE_WERROR=0 NSS_USE_SYSTEM_SQLITE=1 USE_STATIC_RTL=1  \
CC=arm-linux-gnueabi-gcc CCC=arm-linux-gnueabi-g++ RANLIB=arm-linux-gnueabi-gcc-ranlib  \
OS_TEST=arm
```

##### 编译完成后，安装库

安装库文件

```

```

安装头文件

```
../dist/Linux5.3_arm_arm-himix200-linux-gcc_glibc_PTH_OPT.OBJ
install -v -m755 lib/*.so ../../a133_nss/lib
install -v -m755 -d ../../a133_nss/include/
cp -v -RL ../{public,private}/nss/* ../../a133_nss/include/


install -v -m644 lib/{*.chk,libcrmf.a} /lib/nss/lib
install -v -m755 -d                    /lib/nss/include
cp -v -RL ../{public,private}/nss/*    /lib/nss/include
chmod -v 644                           /lib/nss/*
nstall -v -m755 bin/{certutil,nss-config,pk12util} /opt/hisi-linux/x86-arm/arm-himix200-linux/bin
install -v -m644 lib/pkgconfig/nss.pc  /opt/hisi-linux/x86-arm/arm-himix200-linux/target/usr/lib/pkgconfig

```



