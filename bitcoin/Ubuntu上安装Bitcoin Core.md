#Ubuntu上安装Bitcoin Core

在Ubuntu上安装Bitcoin Core除了在[官网](https://bitcoin.org/en/download)上面直接下载安装包之外，还可以使用PPA的方式安装。

## PPA 
PPA:Personal Package Archives ,在Ubuntu上允许编译和发布一个apt repository。Bitcoin团队维护了一个apt repository ，地址:
[https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin](https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin)。 使用PPA的好处可以使用`shell`直接安装，自动解决依赖，还可以选择性的安装`bitcoind`和`bitcoin-qt`。

## 安装Bitcoin Core 
首先需要添加bitcoin的源:
>sudo add-apt-repository ppa:bitcoin/bitcoin

```
Stable Channel of bitcoin-qt and bitcoind for Ubuntu, and their dependencies
 More info: https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin
Press [ENTER] to continue or ctrl-c to cancel adding it
......
gpg:               imported: 1  (RSA: 1)
OK
```
添加源成功之后，需要更新下源:
>sudo apt-get update

安装bitcoind:

>sudo apt-get install bitcoind

可以选择性的安装`bitcoin-qt`,在`ubuntu-service`版本上是不需要`bitcoin-qt`的，如果是在`ubuntu-desktop`上面想使用`bitcoin-qt`的话可以选择安装:
>sudo apt-get install bitcoin-qt

## 运行bitcoind
直接输入`bitcoind`命令可以让`bitcoind`在前台直接运行:
>bitcoind

也可以采用后台运行的方式:
>bitcoind --daemon

运行的时候我们可以使用命令查看`bitcoind`的运行情况:
>bitcoin-cli getinfo

```
{
  "version": 120100,
  "protocolversion": 70012,
  "walletversion": 60000,
  "balance": 0.00000000,
  "blocks": 32,
  "timeoffset": 0,
  "connections": 6,
  "proxy": "",
  "difficulty": 1,
  "testnet": false,
  "keypoololdest": 1472539508,
  "keypoolsize": 101,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "errors": ""
}
```
也可以直接查看`debug.log`,监控当前运行情况。
进入.bitcoin目录:
>cd $HOME/.bitcoin

使用`tail`命令：
>tail -f debug.log

## 区块链数据
在Linux中bitcoind的数据存在`$HOME/.bitcoin`目录下,该目录下有以下的文件。

* `bitcoind.pid` bitcoind运行的进程文件
* `blocks`  区块链数据文件
* `chainstate` 区块链状态的数据库使用LevelDB存储
* `db.log` 数据库日志文件
* `debug.log` 运行时的日志文件
* `wallet.dat` 钱包文件

`bitcoind`数据在其它平台存在的地址。

>Windows
* %APPDATA%\Bitcoin\ 例如:C:\Users\username\AppData\Roaming\Bitcoin

>Mac OSX
* $HOME/Library/Application Support/Bitcoin/ 例如:/Users/username/Library/Application Support/Bitcoin

打赏地址:1KCBSKeCb7HkqWmYmVkkBgEgvYcwWEG61A



