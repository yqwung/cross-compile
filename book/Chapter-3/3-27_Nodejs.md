### 3.27 Node.js

官网：https://nodejs.org/en  <br>
源码：node-v12.22.12.tar.gz  <br>

编译说明：https://github.com/nodejs/node/blob/main/BUILDING.md  <br>

##### Step 1：安装依赖工具

```
sudo apt-get install python3 g++ make python3-pip
```

```
sudo apt install binutils-aarch64-linux-gnu \
gcc-aarch64-linux-gnu \
g++-aarch64-linux-gnu
```

##### Step 1：解压源码

```
tar -xvzf node-v12.22.12.tar.gz
cd node-v12.22.12/
```

##### Step 2：创建安装目录

```
mkdir arm_nodejs
```

##### Step 3：环境变量临时配置

```
export ARCH=aarch64
export LINK_host="g++"
export CXX_host="g++" 
export CC_host="gcc"
export AR_host="ar" 

export CROSS_COMPILE=aarch64-linux-gnu-
export CC=aarch64-linux-gnu-gcc 
export CXX=aarch64-linux-gnu-g++
export LD=aarch64-linux-gnu-ld
export RANLIB=aarch64-linux-gnu-ranlib
export AR=aarch64-linux-gnu-ar
export AS=aarch64-linux-gnu-as
```

##### Step 4：配置

```
./configure --dest-cpu=arm64 --dest-os=linux --without-snapshot --prefix=$PWD/arm_nodejs  --cross-compiling
```

./configure --without-snapshot --dest-cpu=arm --dest-os=linux --with-arm-float-abi=softfp --without-intl 

--without-intl

##### Step 5：编译安装

```
make
make install
```

编译可能有如下报错：

<font color='red'>报错1：</font>

```
/lib/ld-linux-aarch64.so.1: No such file or directory
tools/v8_gypfiles/generate_bytecode_builtins_list.target.mk:13: recipe for target '/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/generate-bytecode-output-root/builtins-generated/bytecodes-builtins-list.h' failed
make[1]: *** [/home/xi/share/work/gcc-7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/generate-bytecode-output-root/builtins-generated/bytecodes-builtins-list.h] Error 255
Makefile:104: recipe for target 'node' failed
make: *** [node] Error 2
```

>这里找不到的库在交叉编译器的 aarch64-linux-gnu/libc/lib 下，不知道到为什么交叉编译的时候直接在编译主机 /lib 下找，我将编译主机的 /lib 备份了一下，将交叉编译器的 aarch64-linux-gnu/libc/lib 下的文件全部拷贝到编译主机 /lib 下，重新编译即可，注意编译完成后，将主机的 /lib 目录还原。 <br>
>我相信有更好的方式解决这个问题，但是我尝试了一些方法都没有成功。


```
cd gcc-linaro-7.5.0-2019.12-x86_64_aarch64-linux-gnu/aarch64-linux-gnu/libc/lib
cp -rf ld-* /lib
```

-rwxr-xr-x 1 11827 9000  1205280 Dec  4  2019 ld-2.25.so*
lrwxrwxrwx 1 11827 9000       10 Dec  4  2019 ld-linux-aarch64.so.1 -> ld-2.25.so*
-rwxr-xr-x 1 11827 9000   194320 Dec  4  2019 libdl-2.25.so*
lrwxrwxrwx 1 11827 9000       13 Dec  4  2019 libdl.so.2 -> libdl-2.25.so*

##### Step 6：板端部署

将编译安装输出 bin 目录下 node 拷贝到板端根文件系统 /usr/bin 下 。<br>

##### Step 7：测试体验

查看 Node.js 版本

```
node -v
```

Node.js 体验

```
root@dragonboard:/mnt/usb/bin# ./node -v
v12.22.12
root@dragonboard:/mnt/usb/bin# ./node 
> console.log('Hello World!')
Hello World!
undefined
> process.exit(0)
root@dragonboard:/mnt/usb/bin# 
```

### Step 8：网页测试

创建一个 test.js 文件，写入以下代码

```
var http = require('http');

http.createServer(function (request, response) {

    // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200, {'Content-Type': 'text/plain'});

    // 发送响应数据 "Hello World"
    response.end('This is a Node.js test project!\n');
}).listen(8888);

// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');
```

使用 node 测试上面 test.js 网页

```
/usr/bin/node test.js
```

打开浏览器访问 http://127.0.0.1:8888/ ，可以看到一个显示 “This is a Node.js test project!” 的网页，如下图



```
* rbnf: 0 items
* region: 0 items
* ROOT: 1 items
* unit: 1 items
* zone: 1 items
/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/icutmp/lang/res_index.txt:62: warning: Encountered empty table
en
en
/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/icutmp/rbnf/res_index.txt:20: warning: Encountered empty table
Traceback (most recent call last):
  File "../../deps/v8/tools/run.py", line 12, in <module>
    sys.exit(subprocess.call(sys.argv[1:]))
  File "/usr/lib/python2.7/subprocess.py", line 172, in call
    return Popen(*popenargs, **kwargs).wait()
  File "/usr/lib/python2.7/subprocess.py", line 394, in __init__
    errread, errwrite)
  File "/usr/lib/python2.7/subprocess.py", line 1047, in _execute_child
    raise child_exception
OSError: [Errno 2] No such file or directory
/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/icutmp/brkitr/res_index.txt:8: warning: Encountered empty table
/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/icutmp/brkitr/res_index.txt:11: warning: Encountered empty table
tools/v8_gypfiles/generate_bytecode_builtins_list.target.mk:13: recipe for target '/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/generate-bytecode-output-root/builtins-generated/bytecodes-builtins-list.h' failed
make[1]: *** [/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/generate-bytecode-output-root/builtins-generated/bytecodes-builtins-list.h] Error 1
make[1]: *** Waiting for unfinished jobs....
en
/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/icutmp/region/res_index.txt:62: warning: Encountered empty table
en
en
rm 8e5ac809175bbc388b95a42a36356c2355dc494e.intermediate
Makefile:104: recipe for target 'node' failed
make: *** [node] Error 2
```


#### 报错2

```
Traceback (most recent call last):
  File "../../deps/v8/tools/run.py", line 12, in <module>
    sys.exit(subprocess.call(sys.argv[1:]))
  File "/usr/lib/python2.7/subprocess.py", line 172, in call
    return Popen(*popenargs, **kwargs).wait()
  File "/usr/lib/python2.7/subprocess.py", line 394, in __init__
    errread, errwrite)
  File "/usr/lib/python2.7/subprocess.py", line 1047, in _execute_child
    raise child_exception
OSError: [Errno 2] No such file or directory
tools/v8_gypfiles/generate_bytecode_builtins_list.target.mk:13: recipe for target '/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/generate-bytecode-output-root/builtins-generated/bytecodes-builtins-list.h' failed
make[1]: *** [/home/xi/share/release/gcc_7.5.0_aarch64/3rdparty/node-v12.22.12/out/Release/obj/gen/generate-bytecode-output-root/builtins-generated/bytecodes-builtins-list.h] Error 1
make[1]: *** Waiting for unfinished jobs....
rm 8e5ac809175bbc388b95a42a36356c2355dc494e.intermediate
Makefile:104: recipe for target 'node' failed
make: *** [node] Error 2
```



