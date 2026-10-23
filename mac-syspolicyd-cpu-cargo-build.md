---
title: "cargo 编译时 syspolicyd 占满 CPU：Gatekeeper 在逐个评估你刚编出来的程序"
date: 2026-10-23
tags: [macOS, 性能优化, 开发环境, Rust]
---

# cargo 编译时 syspolicyd 占满 CPU：Gatekeeper 在逐个评估你刚编出来的程序

上一篇讲了 [mds、fileproviderd 和 CloudTelemetryService](mac-mds-fileprovider-cloudtelemetry-cpu.md)，这篇是同一类问题的另一个主角：`syspolicyd`。

起因是在一台机器上跑 `cargo build`，同时看 `top`，rustc 在编译，但排在最前面的却是 `syspolicyd`。一次实测的数字：

| 进程 | CPU |
| --- | --- |
| `syspolicyd` | 118% – 136% |
| `XprotectService` | 29% 左右 |
| 系统负载 | 7 – 9 |

编译器只是其中一个，`syspolicyd` 一个进程就吃掉一个多核。这篇文章记录它是什么、为什么编译会触发它、能做什么、不能做什么。

## syspolicyd 是什么

它是 macOS 的系统策略守护进程，Gatekeeper 的执行部分，由 launchd 管理，一直在跑。一个可执行文件第一次运行时，它负责：

- 检查代码签名
- 检查公证（notarization）
- 检查隔离标记（quarantine）
- 维护系统策略数据库

`XprotectService`（恶意软件扫描）也由它协调，所以两个进程经常一起冒头。

看现场：

```bash
ps -axo pid,etime,pcpu,comm | grep -E "syspolicyd|XprotectService" | grep -v grep
top -o cpu
```

## 为什么 cargo 编译会触发它

原因我认为很可能是这样（根据现象和这个进程的职责推断，没有抓栈验证）：`cargo` 编译过程会不停地生成**新的、没有签名**的可执行文件，而且立刻就要运行它们：

- 构建脚本（`build.rs` 编译出来的程序）
- 过程宏（编译成动态库，由 rustc 加载）
- 测试二进制（每个 crate、每个集成测试一个）

每一个文件在第一次运行时都要被 `syspolicyd` 评估一遍，评估排起队来，CPU 就上去了。这是开发机上最常见的 `syspolicyd` 高占用来源。

## 能不能把它关掉

不建议，也基本关不掉。

- `sudo killall syspolicyd`：会被 launchd 立刻重新拉起，而且它的缓存丢了，重启后反而更慢。
- 永久禁用（`launchctl bootout` 或 `disable`）：需要先关闭 SIP，应用启动和系统安全都会跟着出问题，不值得。

所以思路和上一篇一样：不去追着进程本身，而是让它少评估一些东西。

## 真正有效的做法

### 1. 把终端和 IDE 加进"开发者工具"白名单

`系统设置 → 隐私与安全性 → 开发者工具`，勾选或添加 iTerm2（以及你用的 IDE）。从这些 App 里启动的进程，运行本地新编译出来的程序时，不再被 Gatekeeper 逐个评估。

用的是系统自带的 Terminal.app，可以用命令加：

```bash
sudo spctl developer-mode enable-terminal
```

我在本机用 `spctl developer-mode` 确认过这个子命令存在，它的说明是 "Add Terminal as a developer tool"。注意它只添加 Terminal.app，iTerm2 和 IDE 要在设置界面里手动加。

### 2. 注意 CI agent 启动的构建

如果构建是由 CI agent 启动的（比如 Woodpecker 的 `woodpecker-agent`），它的进程不是从 iTerm2 里起的，上一步的白名单不一定覆盖得到。这一条我**没有验证过**。想让它也生效，可以试：

- 从已经加入白名单的终端里启动 agent
- 把 agent 的可执行文件本身也加进"开发者工具"

哪种有效要自己实测。

### 3. 最后手段：全局关闭 Gatekeeper 评估

```bash
sudo spctl --global-disable
```

这是 macOS 15 以后的写法，老版本是 `--master-disable`，我在 macOS 26 上确认过 `--global-disable` 可用。它会关掉 Gatekeeper 对应用的评估，但：

- `syspolicyd` 进程本身还在跑，公证和来源检查也还在
- 能把 CPU 降多少，我没有测过
- 安全性会下降

所以不推荐，前两步没效果再考虑。

## 怎么验证有没有效果

做完第 1 步之后，跑一次 `cargo build`（最好先 `cargo clean` 或换个 target 目录，让它重新生成那些中间程序），同时开着 `top -o cpu`。`syspolicyd` 的占用应该明显低于上面的 118%+。

## 它还会影响测试的稳定性

这点是被坑出来的。我们有一套带毫秒级时间窗的故障注入测试，对机器负载很敏感，前面几次压力测试失败，就是机器负载高造成的。

`syspolicyd` 这种后台吃 CPU 的进程，和同一台机器上的其他流水线一样，都会影响测试结果。所以：

- 对时间敏感的测试，最好在已经做过上面第 1 步的机器上跑
- 测试失败时，先看一眼 `top` 里有没有 `syspolicyd` 在占 CPU，再决定是不是代码的问题

## 小结

1. `syspolicyd` 是 Gatekeeper 的执行部分，编译时它吃 CPU，是因为每个新生成的无签名程序第一次运行都要被评估。
2. 它关不掉，也不该关。
3. 把终端和 IDE 加进"开发者工具"白名单，是收益最大、风险最小的一步。
4. CI agent 启动的构建能否被白名单覆盖，以及 `--global-disable` 能降多少 CPU，我都没有验证，这两条以实测为准。
