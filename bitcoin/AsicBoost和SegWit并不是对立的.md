自从 [Gregory Maxwell的邮件](https://www.mail-archive.com/bitcoin-dev@lists.linuxfoundation.org/msg05055.html)出来之后，关于`AsicBoost`的争论越来越多，最简单的论述就是：
>为什么要阻止隔离见证，原来就是使用了AsicBoost

结论容易下，但并不表示就一定是对的，有没有想过`AsicBoost`和`SegWit`并不是对立的，来整理下思路，看看这两件事情究竟是什么样的？又是如何关联在一起的？
聊这两件事情，离不开一个关键词：`挖矿`，先来简述下什么是挖矿吧！
## 挖矿
比特币的挖矿机制：比特币挖矿机制采用的是SHA256算法，但SHA256算法并不是针对整个块做的，而是只是针对块头（Block Header）做SHA256,下图是block的组成：
![block](http://upload-images.jianshu.io/upload_images/22188-1ee9b581b9a5d8a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从上图中可以看到Block hash是从哪些字段组合而得到的，其中黄色背景的字段组合就是块头，他包含:

>1.  版本号  
>2. 上一个块的hash 
>3.  Merkle root  
>4. timestamp(时间戳) 
>5.  bits(难度)
>6.  Nonce  (随机数)

在一轮挖矿过程中，其中版本号、上一块的hash、难度都是确定的，矿工需要做的就是不断的修改Nonce，以改变当前块的hash值以找到小于当前难度的`Block Header`。
但Nonce的可用搜索空间是不够的，原因就是Nonce的位数只有**4bytes**。Block Header中各字段所占的位数：
![Block Header位数](http://upload-images.jianshu.io/upload_images/22188-9b7589845daf933a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
**4bytes的Nonce**的意味着他的搜索概率空间为`2^32`，也就意味着8G次的hash运算就能遍历完，对于当前的单个矿机来说也就是一瞬间就可以完成的事情。因此Nonce的可搜索空间是远远不够用的。
在Nonce的搜索空间不够的情况下，就只剩下`timestamp`和`Merkle root`可以改变了，timestamp是可以微调前后几秒，但是搜索空间还是不够。
因此矿工可以通过修改Coinbase交易、或者交易顺序、或者其他方式，获取到新的`Merkle Root`,再重新做一次2^32次Nonce的变更。
比特币的挖矿机制：
>简单来说比特币挖矿就是通过不断更改Nonce来改变块hash以寻找小于当前难度的块头，但是Nonce的搜索空间太小了，在做完2^32次哈希没有找到对应的块头就需要变更Merkle Root重新计算。

上面说了挖矿机制，那`AsicBoost`又是怎么回事呢？
## AsicBoost
`AsicBoost`:是和`SHA256的计算`、`块头的结构`有关的一种优化，在比特币开始计算块头的时候需要补齐到128bytes，而块头只有80bytes，还需要使用固定的48byte填充到128bytes。
而在计算hash的过程是分两个过程进行的，前64bytes一起计算，后64bytes再一起做运算:
![块头SHA256计算](http://upload-images.jianshu.io/upload_images/22188-955a8b80562fb235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
这样一个填充过的块头被分成了两个部分，这里比较有意思的是Merkle Root，其中Merkle Root的32个bytes中前28bytes被放在前部分计算，后4个bytes被放在后部分计算，计算公式为：
```
SHA256=F(Chunk1)+B(Chunk2)
Chunk1=(version)+(Previous hash)+F28(Merkle root)
Chunk2=B4(Merkle Root)+Timetamp+Bits+Nonce+padding
```
结合上面说的块hash的计算，就出现了一个现象:
>每次更改Nonce的值的时候，Chunk1的值保持不变，意味着每次变化Nonce的时候只需要重新`B(Chunk2)`再结合上一次计算的`F(Chunk1)`即可。

这是一种优化比特币挖矿的方式，优化之后每一轮变更Nonce的计算SHA256就变成了:
>SHA256=F(Chunk1)(不变)+B(Chunk2)`

基本上所有的矿机都做了这个优化。而`AsicBoost`在这个基础上面又找了一个优化的方法：
>既然可以保持`Chunk 1`不变，有没有办法保持`Chunk 2`不变呢？从前面的公式中可以看到只要保持`Merkle Root`的最后四位、时间戳、和Nonce不变即可保持`Chunk 2`不变。

如果能够找到`Merkle Root`后四位是相同的话，那么在同一timestamp和Nonce不变的情况下，就可以得到另一个优化公式：
>SHA256=F(Chunk1)`+B(Chunk2)(不变)

对于timestamp基本上是不变的，而Nonce是在2^32内的搜索空间内遍历的，剩下的问题就是要找到足够多的后四位相同的`Merkle Root`,这样在每次变更Nonce就可以复用前后两部分的计算结果，这样就有效的减少了计算，提高了找到块hash的概率。
那要找到后4位相同的hash的概率是多少呢？根据"Birthday attack"，后4位相同的bytes就是32 bits相同的概率，它的概率是：
![碰撞概率](http://upload-images.jianshu.io/upload_images/22188-6fd0841bc4b18a55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
大概变换77000次就有50%的概率会出现后四位相同的hash，而这样的碰撞能提高多少的效率呢？AsicBoost白皮书中给出现的结果：
![碰撞概率](http://upload-images.jianshu.io/upload_images/22188-0ea02571bfe8e8ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
这种优化理论上能够提高20%的碰撞效率,而合并的性能提升大概是7%左右。`AsicBoost`可以在软件上实现，也是通过芯片（硬件）上实现。实现的方式变更`Merkle Root`的方式:
1.  修改Coinbase交易，白皮书认为不够高效
2. 另一种就是更新Merkle树的排序
3. ...其他方式

AsicBoost白皮书的作者认为第二种方式更加的高效。更改Coinbase的交易在现在的矿机中也是有使用的，不过并不是用来做`AsicBoost`了，只是用来变更`Merkle Root`。
可以看出`AsicBoost`只是一种基于现在比特币块头格式、以及对SHA256算法做出的优化，不是漏洞。
## AsicBoost不是漏洞
漏洞：
>漏洞是在硬件、软件、协议的具体实现或系统安全策略上存在的缺陷，从而可以使攻击者能够再未授权的情况下访问或破坏系统。-来自[百度百科](http://baike.baidu.com/link?url=RqcqqajdkHpUygKH8weor_XTANYOcwd_SbTDnZEJRiEPYVVEIeljQJ8azrCkfMKnviXqKorZ0AERNaV0bfZf_zoBldxHgsx_60ZoyNKlw4W)

很明显AsicBoost并不是漏洞，他没有破坏现在的比特币协议，也不会产出无效的块，也不会出现比特币的安全问题。
反而如果`AsicBoost`的大规模应用只会让比特币的算力得到更快的增长，算力的增加意味着比特币更加的安全。
类似的比特币历史上面对于挖矿的优化是在持续的发生，比如硬件上的升级：
1.  CPU
2.  GPU
3.  FPGA
4.  ASIC

而基于SHA256算法的优化也有好几次:
1.  前边提到的变更Nonce的时候，前半部分F(Chunk1)并不需要重新计算
2.  而后部分的前三轮也是可以优化的参考[ms3steps](https://github.com/dustinfineout/cgminer/blob/master/libbitfury.c#L17)
3.  还有现在的AsicBoost

而`AsicBoost`降低挖矿成本造成不平等的说法也是没有道理的，因为降低挖矿成本的方式有很多方式：
1.  矿池挖矿是增加并不是最有效的，自由采矿成本会下降很多，算力会增长更多
2. 电费便宜挖矿成本就会低
3. 采用更小纳米的芯片
4.  寒冷地区
5. ....

难道这些方式都造成不平等了吗？
总而言之：
>`AsicBoost`是一个优化算法，只是在原有的比特币协议基础上增加了碰撞几率，用来找到更合适的`Block Header`，提高找到hash的概率，并不是漏洞

那么AsicBoost既然能够优化计算，怎么样才能使用呢？
## AsicBoost使用的条件
上面已经说了`AsicBoost`并不是什么漏洞，只是一种优化的算法，而这种优化方式有没有被采用只是一个技术和经济问题而已，`AsicBoost`技术可以通过硬件或软件两种方式来实现，对于硬件实现所要面临的问题可以参考KnC创始人`Sam Cole`的文章[AsicBoost](https://hackernoon.com/asicboost-655a73d48ae4)，巴比特上也有翻译[KNC创始人关于AsicBoost的声明](http://8btc.com/thread-50972-1-1.html),他详细说明了在矿机上实现AsicBoost所面临的问题:
>要想利用这一技术，池服务器只能把AsicBoost处理的部分工作传递给芯片。这意味着任何使用AsicBoost的芯片都不会连接到公共池服务器,只会单纯的返回大量无效的份额。

而他对于可以把AsicBoost隐藏再芯片内的说法的回复是：
>这个进程的主要优势之一是在晶片上节省空间，如果一个产品有两个核心芯片，一个用于AsicBoost，另一个用于标准的SHA,那么这将意味着有近一半的可用的晶体核心被浪费。我可以告诉你们所有人,要是在比特币空间中有这样一个浪费一半核心的芯片，那就闹笑话了。对任何一个优秀的工程师而言，这都是无意义的。

可见当前情况下硬件上实现AsicBoost并不可行，而如果要再软件层实现`AsicBoost`也面临着其他的问题，假设需要使用`AsicBoost`的话，就需要预先缓存大量的Merkle Root的值，而且事先并不知道下一个Merkle Root的后4位是多少，所以就需要记住每次变更交易顺序得到的Merkle Root，而计算Merkel Root也是需要进行多步SHA256的。这样就要面临下面的问题：
1.  因为不知道下次出现的Merkle Root后4位的值是多少，每次计算出来的Merkle Root的值都需要缓存起来，而把所有后4位不同的Merkle Root都缓存起来需要的空间是2^32*32 bytes 大概要256G。
2.  计算Merkle Root也是需要计算SHA256的，而Merkle Root值空间是2^256的搜索空间太大，在无法预知下一个Merkle Root的情况，计算块hash是不应该停止等待Merkle Root的计算，最好的解决方案是，计算Merkle Root和计算块hash同时进行。而资源是分配给Merkle Root还是块hash呢？

在硬件上实现面临的问题是需要定制芯片，而且必须要有矿池支持才可以，而软件上面面临着Merkle Root存储空间以及计算Merkle Root的问题。所以`AsicBoost`目前并还不具备使用的条件。
使用条件并不成熟，那`AsicBoost`和SegWit又有什么样的关系呢？
## SegWit
SegWit(Segregated Witness)即隔离验证，SegWit采用一种新的ID:`Witness ID`

![witnesstx](http://upload-images.jianshu.io/upload_images/22188-5155aa8e56515bae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
相应的新的 Witnesss ID会对应新的`Witness Merkle Tree`，继而就有了`Winess Merkel Root`，而`Winess Merkel Root`写在哪里呢？答案是`Coinbase`。
在`SegWit`协议中Coinbase会增加一个新的output,新的output为:
```
output_data = WITNESS_COMMITMENT_HEADER + ser_uint256(uint256_from_str(hash256(ser_uint256(witness_root)+ser_uint256(witness_nonce))))
```
而`Witness Merkle Root`的计算是不包括`Coinbase`的，这样就避免了Coinbase和`Witness Merkle Root`相互改变的死循环，下面的代码在计算Witness Merkle Root的时候是去掉了Coinbase的，Coinbase的index是0:
```
 def calc_witness_merkle_root(self):
        # For witness root purposes, the hash of the
        # coinbase, with witness, is defined to be 0...0
        hashes = [ser_uint256(0)]

        for tx in self.vtx[1:]:
            # Calculate the hashes with witness data
            hashes.append(ser_uint256(tx.calc_sha256(True)))

        return self.get_merkle_root(hashes)
```
这样就有了一个问题，如果在`SegWit`中变更任意交易位置就会导致`Witness Merkle Root`的变化，而Coinbase中是要包含`Witness Merkle Root`的，继而就会影响CoinBase的变化。导致整个块的Merkle Root发生了变化。
这样如果使用了AsicBoost是通过变更交易顺序获取新的Merkle Root了，效率就会降低，因为需要改变`Witness Merkle Root`和`Merkle Root`，进而降低`AsicBoost`的效率。
这也就是有人会认为因为使用了`AsicBoost`而反对`SegWit`的原因。但是这样的声音忽略了一条重要的事实:`SegWit`和`AsicBoost`并不是互斥的:
>只要块头结构不变的情况下`AsicBoost`的优化仍然存在，仍然有效，只不过是调整交易顺序的方式效果会降低而已。

而SegWit中，变更Coinbase获取`Merkle Root`的方式和现在的协议变更Coinbase的效果是一样的，因为`Witness Merkle Root`中并不包括Coinbase。
`AsicBoost白皮书`中表述的是现在协议下变更交易顺序比变更Coinbase效率更高，但是没有说会高效多少，而在SegWit中变更交易顺序和变更Coinbase谁的效率更高还不知道。
可以看出，SegWit并不是和AsicBoost是对立的，并不是在SegWit中就不存在AsicBoost的优化了。
## 总结
AsicBoost的原理:
>Merkle Root被分割成了两个部分计算，导致了如果使用后4位相同的Merkle Root去计算块hash的话，会提高降低挖矿的效率

而SegWit：
>SegWit 需要新的`Wintess TX ID`，继而有了新的`Witness Merkle Root`,而`Witness Merkle Root`是会写进Coinbase，Coinbase导致了`Merkle Root`的变化，而Coinbase本身不会写入`Witness Merkle Root`,但是因为软分叉的原因，块头的结构并没有变化。

根据上面所示可以得到以下结论:
* ### SegWit中AsicBoost的优化仍然存在
`AsicBoost`本质上是因为Block Header和SHA256特性而做的优化，这两个前提条件不变的情况下，`AsicBoost`会一直存在。
* ### SegWit会对于AsicBoost中交易互换的方式有影响
在SegWit中，每次的变更交易顺序都会导致Coinbase的变化，继而需要重新计算`Merkle Root`。交易顺序的变更会导致`Witness Merkle Root`和`Merkle Root`的变化。
* ### SegWit中有可能使用变更Coinbase的办法更加的有效
`AsicBoost`中明确了两种方式变更`交易顺序`和更改`Coinbase`，而在现有的比特币协议中变更`交易顺序`是更加有效的，在`SegWit`中变更`Coinbase`是有可能更加有效的，变更`Coinbase`不会影响`Witness Merkle Root`，且会变改`Merkle Root`。和现在协议中变更`Coinbase`的结果相同。
* ### 使用AsicBoost是需要具备条件的
其中一个条件是计算Merkle Root和计算块头Hash是并列进行，不应该出现相互等待的情况，另一个条件是计算Merkle Root的值必须能够缓存，矿机使用的时候能够方便快速的调用。当前这两个条件并没有同时满足。
* ### 当前如果有更好的优化AsicBoost的方式的话，SegWit中仍然可以采用
除了变更交易顺序更新Merkle Root的方式以外，其他优化AsicBoost的方式仍然可以使用的。
* ###AsicBoost和SegWit并不互斥


从上所述，`AsicBoost`只是一种优化挖矿的方式，并不是漏洞，且当前的条件下并不满足使用，而且在SegWit中`AsicBoost`优化也没有消失，因为块的结构并没有改变。反对SegWit，并不一定就是因为AsicBoost，因为SegWit下AsicBoost的优化并没有消失。
有时候看到了两个事物同时存在，不能就简单的得出一个事物就是另一个事物的原因这样的结论。同时存在并一定就存在因果关系，不要相信单方面的信息，要从事实和逻辑中得出结论。
巧的是今天看到了一个[微博](http://weibo.com/5243857939/EFHPai5AO)，分享下：
![微博截图](http://upload-images.jianshu.io/upload_images/22188-888ebd690b66fb8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


