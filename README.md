# Tron Profanity

![](https://img.shields.io/github/actions/workflow/status/sponsord/profanity-tron/release.yml)
![](https://img.shields.io/badge/baseon-gpu-yellowgreen.svg)
![](https://img.shields.io/badge/language-c,c++-orange.svg)
![](https://img.shields.io/badge/platform-windows,linux-yellow.svg)

波场（TRX）地址生成器，利用 `GPU` 进行加速。代码开源，安全可靠 🔥

<img width="100%" src="https://github.com/sponsord/profanity-tron/raw/main/screenshot/demo.png?raw=true"/>

需要以太坊（ETH）地址生成的，请前往：[profanity-ether](https://github.com/sponsord/profanity-ether)

## 运行

### Windows

前往 [Release](https://github.com/profanity-pro/tron-address-gpu/releases/) 页面下载发布包（window.zip），本地解压后直接运行 `start.bat`。

> 请参考下文 `命令 & 参数` 章节说明，自行编辑 `start.bat` 配置运行参数。

> 运行的设备如果有集成显卡，请添加 `--skip 1` 把集成显卡过滤之，否则可能会导致：1. 跑不起来，2. 生成的地址和私钥不匹配。

> 如果提示 `vcruntime140_1.dll` 相关异常，请安装 `visual studio` 应用程序，官方下载链接：[https://visualstudio.microsoft.com/zh-hans/vs/](https://visualstudio.microsoft.com/zh-hans/vs/)

> 如果提示 `OpenCL 找不到`，请安装 `cuda` 驱动。 

### Mac

下载源码，然后定位到目录下执行 `make`，接着运行 `./profanity.x64 [OPTIONS]`。

### Linux

先安装 `cuda` 驱动，再安装 `g++`，再下载源码，最后解压后进入目录运行：

```bash
g++ Dispatcher.cpp Mode.cpp precomp.cpp profanity.cpp SpeedSample.cpp -ICurl -IOpenCL -o profanity.x64
```

> 关于 `g++` 的使用，请自行谷歌。

## 命令介绍

```bash
Usage: ./profanity [OPTIONS]

  Help:
    --help              Show help information

  Modes with arguments:
    --matching          Matching input, file or single address.

  Matching configuration:
    --prefix-count      Minimum number of prefix matches, default 0
    --suffix-count      Minimum number of suffix matches, default 6
    --quit-count        Exit the program when the generated number is greater than, default 0

  Device control:
    --skip              Skip device given by index

  Output control:
    --output            The file to output the results to
    --post              The url to post the results to

Examples:

  ./profanity --matching profanity.txt
  ./profanity --matching profanity.txt --skip 1
  ./profanity --matching profanity.txt --output result.txt
  ./profanity --matching profanity.txt --post http://127.0.0.1:7001/api
  ./profanity --matching profanity.txt --prefix-count 1 --suffix-count 8
  ./profanity --matching profanity.txt --prefix-count 1 --suffix-count 10 --quit-count 1
  ./profanity --matching TUqEg3dzVEJNQSVW2HY98z5X8SBdhmao8D --prefix-count 2 --suffix-count 4 --quit-count 1

About:

  Profanity is a vanity address generator for Tron: https://tron.network
  The software is modified based on ethereum profanity: https://github.com/johguse/profanity
  Please make sure the program you are running is download from: https://github.com/sponsord/profanity-tron
  Author: telegram -> @dontond

Fbi Warning:

  Before using a generated vanigity address, always verify that it matches the printed private key.
  And always multi-sign the address to ensure account security.
```

### 命令说明

|  参数  | 说明  |
|  ----  | ----  |
|--help|查看帮助说明|
|--matching|固定写法，后面跟上匹配规则文件|
|--prefix-count|最少匹配前缀位数，默认 0。最大可设置为 10|
|--suffix-count|最少匹配后缀位数，默认 0。最大可设置为 10|
|--quit-count|生成的地址达到指定的数量，即退出程序。比如你就想匹配一个地址，那就配置为 1。出于计算性能考虑，系统默认退出数量为 120|
|--output|将生成的地址输出到文件（追加）。一行一个，格式为：privatekey,address|
|--post|将生成的地址，发送到（GET）指定的 url，每生成一条就会发送一次。数据格式为：privatekey=xx&address=yy。这个配置主要便于其它系统的集成|
|--skip|跳过指定索引的 `GPU` 设备，如启动软件出现异常，请使用此参数跳过设备集成显卡|

> 说明：对于 `--prefix-count` 和 `--suffix-count` 配置的值，大于该值的匹配也会一并输出。比如你配置 `--suffix-count 6`，那如果跑出来7位的号，也会一并输出。

> 说明：首次运行软件，建议可先将 `--suffix-count` 设置为一个比较低的值（比如6位，6位容易出结果），观察是否有结果输出（有输出说明软硬件都是 ok 的）。不要一上来就设置一个比较大的值，对于比较大的值，有可能你跑一天都不会出结果，就会疑惑是软件的问题？还是确实太难了跑不出来？

### 匹配规则

> 目前 `--matching` 参数支持指定单个地址或一个文件。

#### 单个地址

```bash
# 匹配前3后5
profanity.exe --matching TUqEg3dzVEJNQSVW2HY98z5X8SBdhmao8D --prefix-count 3 --suffix-count 5
```

#### 文件

```bash
# 匹配后8
profanity.exe --matching profanity.txt --suffix-count 8 --quit-count 100
```

匹配文件里面，目前支持两种写法，可参考内置 `profanity.txt`。举个例子：

```plaintext
TTTTTTTTTTZZZZZZZZZZ
TUqEg3dzVEJNQSVW2HY98z5X8SBdhmao8D
```

上面这两条匹配规则：
- 第一条，是匹配以字母 `Z` 结尾的地址。
- 第二条，是匹配这条地址的前后 `10` 位，实际运行的时候，会自动修正为：TUqEg3dzVE8SBdhmao8D。

有了匹配规则，再结合 `prefix-count`（最少匹配前缀数量） & `suffix-count`（最少匹配后缀数量），即可实现任意规则地址生成。


## 速度

本程序使用阿里云配置：`GPU 计算型 8 vCPU 32 GiB x 1 * NVIDIA V100`。运行速度在 `2.2亿 H/s` 左右：

<img width="100%" src="screenshot/demo.png?raw=true"/>

> 本程序除了在开发机（一台老旧的 Mac），以及上述 `NVIDIA v100` 显卡上经过测试外，未在其它设备上进行速度测试。

> 请不要纠结于对比各种设备、各种平台差异化的运行速度。没意义。

最后，关于速度的问题再多提几句：

- 理论上 `2.2亿 H/s` 左右的速度，跑 8 位的号码，可能一晚上出几个，也有可能一个都不出。
- 你如果想跑 `10` 位的地址，不论是10连的后缀，还是前6后5，没个几天，估计你是跑不出来的。
- 追求速度的，推荐使用 `nvdia 4090` 卡跑，去淘宝租，价格 `3K/月` 左右。速度可达 `5-6亿` 左右。
