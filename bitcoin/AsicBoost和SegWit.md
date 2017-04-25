关于AsicBoost和SegWit的讨论已经冷清了很多，但是我还是想从技术的角度尝试解释下：
1. AsicBoost是什么
2. AsicBoost和SegWit又有什么关系 

说这两件事情之前，离不开一个关键词：`挖矿`，就先来说说挖矿吧！
## 挖矿
比特币的挖矿机制：比特币挖矿机制采用的是SHA256算法，但SHA256算法并不是针对整个块做的，而是只是针对块头（Block Header）做SHA256，下图是block的组成：
![block](http://upload-images.jianshu.io/upload_images/22188-1ee9b581b9a5d8a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从上图中可以看到Block hash是从哪些字段组合而再做hash得到的，其中黄色背景的字段组合就是块头，它包含:

>1.  版本号  
>2. 上一个块的hash 
>3.  Merkle root  
>4. timestamp(时间戳) 
>5.  bits(难度)
>6.  Nonce  (随机数)

在一轮挖矿过程中，其中的版本号、上一块的hash、难度都是确定的，矿工需要做的就是不断的修改Nonce，以改变当前块的hash值以找到小于当前难度的`Block Header`。
但Nonce的可用搜索空间是不够的，原因就是Nonce的位数只有**4bytes**。Block Header中各字段所占的位数：
![Block Header位数](http://upload-images.jianshu.io/upload_images/22188-9b7589845daf933a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
**4bytes的Nonce**的意味着他的搜索概率空间为`2^32`，也就8G次的hash运算就能遍历完，对于当前的单个矿机来说也就是一瞬间就可以完成的事。
在Nonce的搜索空间不够的情况下，就只剩下`timestamp`和`Merkle root`可以改变了，timestamp可以微调前后几秒，但是搜索空间还是不够。
因此矿工通过修改Coinbase交易、或者交易顺序、或者其他的方式，获取新的`Merkle Root`,再重新做2^32次Nonce的遍历。
比特币的挖矿机制：
>简单来说比特币挖矿就是通过不断更改Nonce来改变块hash以寻找小于当前难度的Block Header，但是Nonce的搜索空间太小了，在做完2^32次哈希没有找到对应的块头就需要变更Merkle Root重新计算。

上面说了简单说了下挖矿机制，那`AsicBoost`又是怎么回事呢？
## AsicBoost
`AsicBoost`是和`SHA256的计算`、`块头的结构`有关的一种算法，在比特币开始计算块头hash的时候是需要补齐到128 bytes，而块头只有80 bytes，还需要使用固定的48 bytes填充到128 bytes。
而在计算hash的过程是分两个过程进行的，前64 bytes一起计算，后64 bytes再一起运算:
![块头SHA256计算](http://upload-images.jianshu.io/upload_images/22188-955a8b80562fb235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
这样一个填充过的块头就被分成了两个部分，比较有意思的是Merkle Root，Merkle Root的32个bytes中前28个bytes被放在前部分计算，后4个bytes被放在后部分计算，计算公式为：
```
SHA256=F(Chunk1)+B(Chunk2)
Chunk1=(version)+(Previous hash)+F28(Merkle root)
Chunk2=B4(Merkle Root)+Timetamp+Bits+Nonce+padding
```
结合上面说的块hash的计算，就出现了一个现象:
>每次更改Nonce的值的时候，Chunk1的值保持不变，意味着每次变化Nonce的时候只需要重新`B(Chunk2)`再结合上一次计算的`F(Chunk1)`即可。

这是一种优化挖矿的方法，优化之后每一轮变更Nonce的计算SHA256就变成了:
>SHA256=F(Chunk1)(不变)+B(Chunk2)`

基本上所有的矿机都做了这个优化。而`AsicBoost`在这个基础上面又延伸了思路找到了另一种优化方法：
>既然可以保持`Chunk 1`不变，有没有办法保持`Chunk 2`不变呢？从前面的公式中可以看到只要保持`Merkle Root`的最后四位、时间戳、和Nonce不变即可保持`Chunk 2`不变。

如果能够找到`Merkle Root`后四位是相同的话，那么在同一timestamp和Nonce不变的情况下，就可以得到另一个优化公式：
>SHA256=F(Chunk1)`+B(Chunk2)(不变)

对于timestamp来说它基本上是不变的，而Nonce是在2^32内的搜索空间内遍历的，剩下的问题就是要找到足够多的后四位相同的`Merkle Root`,这样在每次遍历Nonce就可以复用后部分的计算结果，这样就有效的减少了计算，提高了找到块hash的概率。
那要找到后4位相同的hash的概率是多少呢？根据"生日悖论"，后4位相同的bytes就是32 bits相同的概率，它的概率是：

![碰撞概率](http://upload-images.jianshu.io/upload_images/22188-6fd0841bc4b18a55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)

大概77000次就有50%的概率会出现后四位相同的hash，而这样的碰撞能提高多少效率呢？AsicBoost白皮书中给出现的结果：
![碰撞概率](http://upload-images.jianshu.io/upload_images/22188-0ea02571bfe8e8ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
这种优化理论上能够提高20%的碰撞效率，而合并的性能提升大概是7%左右。`AsicBoost`可以在软件上实现，也是通过芯片（硬件）上实现。实现的方式就是变更`Merkle Root`:
1.  修改Coinbase交易，白皮书认为不够高效
2. 另一种就是更新Merkle树的排序
3. ...其他方式

可以看出`AsicBoost`只是一种基于现在比特币块头、以及SHA256算法做出的优化，并不是一种攻击。
## AsicBoost只有一种技术优化
很明显AsicBoost既没有破坏现在的比特币协议，也没有生产出不可用的块，更不会出现针对比特币的安全问题。
而基于SHA256算法的优化也有好几次:
1.  前边提到的变更Nonce的时候，前半部分F(Chunk1)并不需要重新计算
2.  而后部分的前三轮也是可以优化的参考[ms3steps](https://github.com/dustinfineout/cgminer/blob/master/libbitfury.c#L17)
3.  还有现在的AsicBoost

所以的软件以及系统都是存在着优化的可能性，比特币的挖矿历史就是一部不断优化效率的过程，`AsicBoost`也不是刚刚出现的，白皮书早在去年就已经发布了。
究竟我们应该如何定义`优化`和`攻击`呢？这是一个要思考的问题。优化SHA256前64位的计算是允许的，优化后64位就是`攻击`了吗？
如果存在一种优化比特币挖矿的可能性，我更加希望的是矿工早日应用这种优化，当矿工使用的情况下，真正想要攻击比特币的和矿工相比就不具备优势了。

>`AsicBoost`是一个优化算法，只是在原有的比特币协议基础上提高了碰撞概率，用来找到更合适的`Block Header`，提高找到块hash的概率，并不是漏洞

那`AsicBoost`和SegWit又有什么样的关系呢？
## SegWit
SegWit(Segregated Witness)即隔离验证，SegWit将会采用一种新的TX ID:`Witness ID`

![witnesstx](http://upload-images.jianshu.io/upload_images/22188-5155aa8e56515bae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
相应地新的 Witnesss ID会对应新的`Witness Merkle Tree`，继而就有了`Winess Merkel Root`，而`Winess Merkel Root`写在哪里呢？答案是`Coinbase`。
在`SegWit`协议中Coinbase会增加一个新的output,新的output为:
```
output_data = WITNESS_COMMITMENT_HEADER + ser_uint256(uint256_from_str(hash256(ser_uint256(witness_root)+ser_uint256(witness_nonce))))
script = CScript([OP_RETURN, output_data])
```
新增的output包含：OP_RETURN+WITNESS信息+`Witness Merkle Root`的hash组成的Script。
而`Witness Merkle Root`的计算是不包括`Coinbase`的，这样就避免了Coinbase和`Witness Merkle Root`相互改变的而造成的死循环，在计算Witness Merkle Root的时候是去掉了Coinbase TX的。

这样就有了出现了一个问题，就是如果在`SegWit`中变更任意交易位置就会导致`Witness Merkle Root`的变化，而Coinbase中是要包含`Witness Merkle Root`的，继而就会影响Coinbase的变化。导致整个块的Merkle Root发生了变化。
这样如果在SegWit中使用的AsicBoost是通过变更交易顺序获取新的Merkle Root了，效率就会降低，因为需要同时计算`Witness Merkle Root`和`Merkle Root`，进而降低`AsicBoost`的效率。
这也就是有人会认为因为使用了`AsicBoost`就会反对`SegWit`的原因。但是这样的声音忽略了一条重要的事实:`SegWit`和`AsicBoost`并不是互斥的:
>只要块头结构不变的情况下`AsicBoost`的优化仍然存在，仍然有效，只不过调整交易顺序的方式效果会降低而已。

在SegWit中，变更Coinbase获取`Merkle Root`的方式和现在的协议变更Coinbase的效果是一样的，因为`Witness Merkle Root`中并不包括Coinbase TX。
可以看出，SegWit并不是和AsicBoost是互斥的，并不是在SegWit中就不存在AsicBoost的优化了。而在`SegWit`中使用`AsicBoost`也是在工程上也是可以进行优化的：计算Merkle Root也是要计算hash的，所以计算Merkle Root不应该阻塞块hash的计算，并行计算会是更好的方式，在并行的情况下使用变更`Coinbase`获取`Merkle Root`所带来的效率的降低并不明显。

## 总结
AsicBoost的原理:
>Merkle Root被分割成了两个部分计算，导致了如果使用后4位相同的Merkle Root去计算块hash的话，会提高挖矿的效率

SegWit：
>SegWit 需要新的`Wintess TX ID`，继而有了新的`Witness Merkle Root`,而`Witness Merkle Root`是会写进Coinbase，Coinbase导致了`Merkle Root`的变化，而Coinbase本身不会写入`Witness Merkle Root`，因为软分叉的原因，块头的结构并没有变化。

根据上面所示可以得出以下结论:
### AsicBoost本质上只是基于块头结构和SHA256算法其中的一种优化
###AsicBoost和SegWit并不互斥
块头结构和SHA256算法不变的前提条件，`AsicBoost`会一直存在。
### SegWit会对于AsicBoost中交易互换的方式有影响
在SegWit中，每次的变更交易顺序都会导致Coinbase的变化，继而需要重新计算`Merkle Root`。交易顺序的变更会导致`Witness Merkle Root`和`Merkle Root`的变化。
### 当前如果有更好工程化地优化AsicBoost的方式，在SegWit中仍然可以采用
除了变更交易顺序更新Merkle Root的方式效率会降低以外，使用工程化优化AsicBoost的方式仍然可以有效。比如并行计算等方式

从上所述，`AsicBoost`只是一种优化挖矿的方式，而且在SegWit中`AsicBoost`优化也没有消失，因为块的结构并没有改变。G Maxwell在邮件中提出了一种更改块头的方式让`AsicBoost`不能再继续使用，我并不反对这种提议，只是认为没有这个必要，如果不允许矿工优化后64为bytes的计算，那前64位bytes的优化计算是不是也应该想办法禁止掉呢？
>在给定的条件下，人类总是有办法找到方法A优于原来的方法B的，人类的历史就是效率不断被提高的历史

看到了一个[微博](http://weibo.com/5243857939/EFHPai5AO)，分享下：
![微博截图](http://upload-images.jianshu.io/upload_images/22188-888ebd690b66fb8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

