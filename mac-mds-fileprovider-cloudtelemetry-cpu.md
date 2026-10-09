---
title: "写代码时 Mac 风扇狂转：mds、fileproviderd、CloudTelemetryService 都在忙什么"
date: 2026-10-09
tags: [macOS, 性能优化, 开发环境]
---

# 写代码时 Mac 风扇狂转：mds、fileproviderd、CloudTelemetryService 都在忙什么

前阵子在 Mac 上跑 `npm install`，风扇直接起飞。打开活动监视器，排在前面的不是 node，也不是编辑器，而是 `mds_stores`、`fileproviderd`，过一会儿又冒出一个 `CloudTelemetryService`。

这几个都是系统自带的进程，名字看着也不熟。这篇文章把它们是什么、为什么在开发时特别能吃 CPU、怎么压住它们，按我自己排查的过程记下来。

## 先看一眼现场

```bash
ps -axo pid,pcpu,comm | grep -E "mds|mdworker|fileproviderd|CloudTelemetry" | grep -v grep
```

平时它们的占用都很低。问题出在你让它们"有活干"的时候：装依赖、切分支、编译。三个进程的共同点是，它们都在盯着文件系统的变化。开发恰好是文件系统变化最剧烈的场景，后面会专门讲。

## mds：Spotlight 的索引进程

`mds`（metadata server）是 Spotlight 的核心。它监听文件系统的变化，维护一份全盘索引，你按 `⌘ + 空格` 能秒搜到文件靠的就是它。相关的进程有三个：

- `mds`：调度，接收文件变化通知
- `mds_stores`：读写索引库
- `mdworker_shared`：真正去打开文件、提取内容和元数据的工人，忙的时候会同时拉起好几个

Spotlight 是按"文档"的使用习惯设计的：文件不多，改得也不频繁。开发目录正好反过来。一个中等大小的前端项目，`node_modules` 里有几万到十几万个文件。每次 `npm install`、`git checkout`，都会让成千上万个文件被创建、删除、改写，`mds` 每一个都要排队处理。更尴尬的是，这些索引结果对你毫无用处，没人会用 Spotlight 去搜 `node_modules/lodash/_baseGet.js`。

想看它具体在读写什么：

```bash
sudo fs_usage -w -f filesys mds_stores
```

### 怎么压住它

最稳妥的做法是只排除开发目录，其他功能照常用。`系统设置 → Spotlight → 搜索隐私权`，把代码目录加进去即可。

另一个办法是给目录名加 `.noindex` 后缀，Spotlight 会直接跳过它。这个办法对"可以随便改名的构建输出目录"很合适，下一节会用到。

如果你平时本来就用 Raycast、Alfred 这类启动器，不依赖 Spotlight 搜文件，直接全关也行：

```bash
sudo mdutil -a -i off     # 关闭所有卷的索引
sudo mdutil -E /          # 清掉旧索引（可选）
sudo mdutil -a -i on      # 想恢复时
```

关掉之后 `⌘ + 空格` 的文件搜索就没了，邮件、备忘录等应用内的搜索也可能受影响，按需取舍。关闭后 `mds` 进程仍然在，只是 CPU 占用会降到接近 0（我这台机器上就是这个状态，`mdutil -s /` 显示 `Indexing disabled`，`mds` 占用 0.1%）。

## fileproviderd：云盘同步框架

`fileproviderd` 是 macOS File Provider 框架的守护进程。Finder 里的 iCloud Drive，以及新版 OneDrive、Dropbox、Google Drive 等客户端，都是通过这个框架接入系统的。占位文件（带云朵图标的那种）、按需下载、监听本地改动并上传，都归它管。旁边的 `FPCKService` 是配套的 CloudKit 同步服务。

最容易踩的坑是：**项目放在了被同步的目录里**。

开了"桌面与文稿文件夹"同步之后，`~/Desktop` 和 `~/Documents` 下的所有东西都会被同步，包括 `node_modules`、`.git`。`fileproviderd` 得给每个文件登记状态、计算、排队上传。我自己的工作目录就在 `~/Documents/workpace` 下面，是典型的中招路径。

这种情况下的症状很固定：`npm install` 完之后，`fileproviderd` 和 `bird` 要忙很久；编译时偶尔读不到文件，因为文件被"优化存储"换成了云端占位符，系统要先下载才能读。

### 怎么处理

**最彻底的办法：把代码挪出同步目录。**

```bash
mkdir -p ~/dev
mv ~/Documents/workpace ~/dev/
```

`~/dev` 这类自建目录不会被 iCloud 同步。这一步做完，`fileproviderd` 基本就安静了。

**不想挪的话：**

- 关掉"桌面与文稿文件夹"同步：`系统设置 → Apple ID → iCloud → iCloud Drive`
- 只让依赖目录不同步：iCloud 会忽略 `.nosync` 结尾的目录
  ```bash
  mv node_modules node_modules.nosync
  ln -s node_modules.nosync node_modules
  ```
  个别工具对软链接支持不好，出问题就回到"挪目录"那条路
- 用的是第三方云盘的话，把代码目录设成排除或仅本地

进程卡死时可以 `killall fileproviderd` 让系统重新拉起，只是应急，治不了本。别想着用 `launchctl` 把它永久禁掉，它在 SIP 保护范围内，而且云盘和 Finder 的不少行为都依赖它。

## 中间文件：开发场景下真正的放大器

前面两节讲的是"为什么盯着开发目录会累"。这一节说的是，开发过程本身会制造一类特别折磨它们的文件：**中间文件**。

写代码时磁盘上其实有两类东西：你写的源码，和工具生成的东西。后者数量往往是前者的几十上百倍：

- 前端：`node_modules`、`.next`、`dist`、`.vite`、`.turbo`、各种缓存
- Rust：`target/`，一个中等项目编译一次就是上万个 `.o`、`.rlib`、`.d` 文件
- JVM：`build/`、`.gradle/`、`.class` 文件
- Xcode：`DerivedData`，加上 `.o`、`.swiftmodule`、索引库
- Python：`__pycache__`、`.pytest_cache`、虚拟环境
- 日志、sourcemap、测试产出的临时文件

这类文件有几个特点，每一个都正好戳中上面三个进程的弱点。

**1. 数量极大。** 索引和同步的开销，基本和文件数量成正比，和文件大小关系不大。一万个 1KB 的文件，比一个 10MB 的文件难处理得多。

**2. 变化极快，而且反复。** 热更新、增量编译、watch 模式下，同一批文件几秒就被改写一次。每次改写都会产生文件系统事件，`mds` 要重新排队索引，`fileproviderd` 要重新标记为"待上传"。你保存一次代码，可能就触发几十个中间文件的更新。

**3. 生命周期很短。** 很多中间文件创建出来几秒后就被删掉或覆盖。对 `mds` 来说，刚入队的索引任务，文件已经没了，白做功。对云同步来说，可能刚开始上传，这个文件已经过期了。这种"创建—删除"的抖动，比稳定的大目录更消耗资源。

**4. 内容对索引和同步完全没价值。** 这些文件随时可以重新生成，没必要备份到云端，也没必要被搜索。纯属白白消耗。

**5. 会连带放大遥测。** 云同步活动越多，下一节讲的 `CloudTelemetryService` 要处理的事件也越多。

所以同样是 10 万个文件，一个静止的文档库几乎没有负担，一个正在编译的工程却能把 CPU 吃满，差别就在"变化"这两个字上。

### 对中间文件的处理思路

**让构建产物远离索引和同步，而不是去追着进程调参数。**

1. **构建输出放进 `.noindex` 目录。** 很多工具支持自定义输出路径：
   ```bash
   # Rust：把 target 指到一个不被索引的位置
   export CARGO_TARGET_DIR=~/dev/.build.noindex/myproject
   ```
   Xcode 可以在 `Settings → Locations` 里改 DerivedData 路径；Gradle、CMake 等也都能指定 build 目录。
2. **整个代码目录加进 Spotlight 隐私列表**，一次性解决所有工程的 `node_modules`、`target` 问题，不用一个个改。
3. **工程目录不要放在云同步路径下**，这条前面已经说过，对中间文件尤其重要。
4. **定期清理没用的产物。** 废弃项目的 `node_modules` 和 `target` 留着也是负担，可以隔段时间扫一遍。

## CloudTelemetryService：先说清楚我不确定的部分

这个进程我花的时间最多，因为资料最少。我在本机上看到的情况是：

```
/System/Library/PrivateFrameworks/CloudTelemetry.framework/
    Versions/A/XPCServices/CloudTelemetryService.xpc
```

它是 `CloudTelemetry.framework` 里的一个 XPC 服务，位于 `/System` 下，属于 Apple 私有框架。Apple 没有公开文档，所以它具体采集什么、上报到哪，我无法给出确切答案。从名字看，它负责云服务相关的遥测，大概率和 iCloud / CloudKit / FileProvider 这条线有关。

我的推测（这是推测，不是实测）是：它的工作量跟云同步活动的量挂钩。排查时我注意到它和 `fileproviderd` 常常同时出现在高占用榜单上，如果这个推测成立，那么先把 `fileproviderd` 的负载降下来，它自然会跟着降。

因为它受 SIP 保护，不要想着删或者禁用它。能做的是：

1. 先把云同步的负载降下来（上面 fileproviderd 和中间文件两节）
2. 在 `系统设置 → 隐私与安全性 → 分析与改进` 里关掉分析数据共享。这一步能否直接减少这个进程的活动，我没有做前后对比，不敢保证
3. 如果做完之后它仍长期高占用，抓一份样本去向 Apple 反馈：
   ```bash
   sudo sample CloudTelemetryService 5 -file ~/Desktop/ctservice.txt
   ```

## 我的调整顺序

如果你也被这几个进程折腾，我建议按这个顺序来，前面的收益最大：

1. 把项目挪到 `~/dev` 之类的非同步目录
2. 在 Spotlight 隐私列表里加上这个目录（或者直接关索引）
3. 把构建输出指到 `.noindex` 目录
4. 关掉"桌面与文稿"同步和分析共享
5. 用开头那条 `ps` 命令观察一阵，看哪个进程还在冒头

顺带一提：这些都是 Apple 签名、位于 `/System/Library` 的系统组件，高占用基本不是中毒。真正要警惕的是路径不在系统目录、或者没有 Apple 签名的同名进程。

做完这些之后，`npm install` 和整包编译时风扇安静了很多。它们本身没有毛病，只是默认配置是给普通用户准备的，而开发者的目录刚好是它们最不擅长的那种。
