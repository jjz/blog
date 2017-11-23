早上的时候看到某币圈大V的微博，他说因为Bitcoin.com把Bitcoin Cash Wallet做为默认钱包造成了用户的丢币。我赶紧更新了bitcoin.com钱包试用了下。
![bitcoin.com wallet](http://upload-images.jianshu.io/upload_images/22188-e295931dea4dc6d5.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

钱包的改动很简单，就把Bitcoin Cash Wallets作为首选，这样就造成用户丢币了......
其实这事和Roger Ver真的没有啥关系，估计该大V现在还不清楚BCH是如何丢失的吧！（该大V还宣称数百万的BTC因此而丢失）

## 第一个问题丢币的到底是什么币种？
先来看第一个问题，哪个币种的币丢失了，是BTC还是BCH。如果连这个都搞不清楚后面的问题就更难说明白了，这里先声明，只考虑最近出现的因为发错地址而丢币的情况，不包含用户私钥丢失，密码忘记等情况。
我们知道BCH去掉了SW交易的地址，另外还有区块大小，动态难度调整等。在地址上和BTC是没有区别除了SW地址以外，那么就可以得出一个结论包含了SW交易的BTC的地址集合是要大于BCH的地址集合的，意思就是说你在BCH上生成的地址都可以在BTC上生成而可以使用。
BTC和BCH使用同一种地址编码技术，一种可能出现的情况是用户会发错地址，本来是想构建交易发送BCH到A地址的，最后发现A地址是BTC的，这种情况下只要有对应的私钥，无论是BTC或者BCH上面都可以生成对应的地址找回币。
而刚刚说了BTC的地址集合是大于BCH的，也就是说如果BCH上面有私钥的地址肯定可以在BTC上生成的，反过来却不是。BCH上面不支持SW的地址，BTC上面生成的SW地址BCH不可使用，丢币问题也是和SW的地址有关。错误的把BCH在发送到SW地址上才可能会出现BCH丢失的情况。

 ## 第二个问题什么样的地址会造成丢币？
上面已经说了把BCH打到SW地址上才可能会出现丢币的问题，这里还是要简单做下介绍。
可以简单的地址分为三类：普通地址（1开头），多种签名地址（3开头），另外的就是SW地址，当前使用的SW地址都是镶嵌在P2SH中的P2WPKH地址它也是3开头的地址。
>P2SH（bip16)是Gavin Andresen提出的一种pay to script Hash的方法，允许发送者构建更加丰富的交易类型。

P2SH的锁定脚本例子:
> OP_HASH160 86107606107baa4d1fc6711e22de7f0ef2056766 OP_EQUAL

而在赎回脚本里面，需要提供一段脚本(redeemScript)，通过的条件需要满足该脚本可执行返回true，且其hash值与后面的值相同，最终脚本类似于:
> redeemScript OP_HASH160 86107606107baa4d1fc6711e22de7f0ef2056766 OP_EQUAL

我们常常使用的多种签名其实是P2SH的一种方式，当前另外一种使用P2SH比较多的就是SW地址。上面例子中的P2SH脚本其实就是一个SW地址的锁定脚本。该地址在支持SW交易的BTC中可以发送SW交易解锁，而在BCH链上的只会识别成一个P2SH的地址，地址是合法的（可以简单认为在BCH就认为是一个多重签名的地址）。
当有人把BCH发送到这种SW地址上面的时候麻烦就出现了，BCH上面不支持SW交易，无法生成对应的解锁脚本。
那么把BCH误发到SW地址上是不是就没有办法挽回了呢？答案是否定的。

## SW交易是如何"骗"老节点的
想要找回发送到错误地址上的BCH，就需要明白SW交易是如何“骗”老节点的。
SW升级是一个软分叉，也就是意味着SW交易也需要在老版本节点上验证通过，SW交易要"骗"老节点，让老节点在不清楚SW交易具体结构的情况下还可以正确验证交易，那如何"骗"老节点呢？
简单来说SW的地址其实是RedeemScript（赎回脚本）的一个hash，比如我们拿一个最简单的sw的交易举例：[c586389e5e4b3acb9d6c8be1c19ae8ab2795397633176f5a6442a261bbdefc3a](https://btc.com/c586389e5e4b3acb9d6c8be1c19ae8ab2795397633176f5a6442a261bbdefc3a)
* 该交易的输入地址：35SegwitPieWKVHieXd97mnurNi8o6CM73
* 输入的脚本: OP_HASH160 2928f43af18d2d60e8a843540d8086b305341339 OP_EQUAL
* WitnessScript：160014a4b4ca48de0b3fffc15404a1acdc8dbaae226955
* Witness: 30450221008604ef8f6d8afa892dee0f31259b6ce02dd70c545cfcfed8148179971876c54a022076d771d6e91bed212783c9b06e0de600fab2d518fad6f15a2b191d7fbd262a3e01
039d25ab79f41f75ceaf882411fd41fa670a4c672c23ffaf0e361a969cde0692e8

只需要分析WitnessScript即可，这部分是用来让老版本验证该交易是合法的，这个脚本可以分为两部分
>`16`  0014a4b4ca48de0b3fffc15404a1acdc8dbaae226955

其中16是用来压栈的操作符，后面的数据（0014a4b4ca48de0b3fffc15404a1acdc8dbaae226955）的hash就是锁定脚本的hash值2928f43af18d2d60e8a843540d8086b305341339，这样把该脚本给老版本，老版本验证该脚本肯定是可以通过的。老版本的客户端运行的时候，该解锁脚本可以正确运行，老版本的客户端是不用关心公钥和签名数据的。这样就成功的"骗"了老版本的节点。

上面介绍的是SW交易如何"骗"老版本客户端的，这和BCH用户错误地发币到SW地址又有什么关系呢？
因为BCH客户端和BTC老版本客户端的验证方式是一样的，可以这么说SW交易有办法合法地在老版本客户端上验证通过，当然它也可以在BCH客户端上验证通过的。
这是一个btc.com帮忙找回的BCH例子[https://www.blocktrail.com/BCC/address/3DutBysquuSbQxYA6EEq3m8VBQDBqa55mw/transactions](https://www.blocktrail.com/BCC/address/3DutBysquuSbQxYA6EEq3m8VBQDBqa55mw/transactions)
其交易详情为：
```
{
  ...
      {
      "txid": "ac3db4411e1ce8cc76e3ebe2f7d0a538c6033fcf80484a97902eef7d6a5e34e6",
      "vout": 0,
      "scriptSig": {
        "asm": "00205c4b9ef7c8896cef0d2a8fd3693d3877e0f4d1d3904fbcaf9cac1bcfb324d9b2",
        "hex": "2200205c4b9ef7c8896cef0d2a8fd3693d3877e0f4d1d3904fbcaf9cac1bcfb324d9b2"
      },
      "sequence": 4294967295
    },
 ...
}

```
它在BCH上的解锁脚本(用来让老版本客户端通过验证的)是：2200205c4b9ef7c8896cef0d2a8fd3693d3877e0f4d1d3904fbcaf9cac1bcfb324d9b2，该交易在BTC链上也会动用过：[https://btc.com/b25ae18255104fa8e871f5b7bcca5e30b11b67a1720555e710f36dfe6ab85397](https://btc.com/b25ae18255104fa8e871f5b7bcca5e30b11b67a1720555e710f36dfe6ab85397) 可以找到该解锁脚本:
![WitnessScript](http://upload-images.jianshu.io/upload_images/22188-1c61766479d7d366.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)


## 总结
如果你使用的是普通地址的交易，或者多重签名的交易都是安全的，不会出现发送错误地址丢币的情况，无论是在BCH链上还是BTC链上，即使你本来想发BTC的发到了BCH地址上也是可以找回的，反之亦然。这里的前提是你有办法找到接受地址的私钥。
当你把BCH发送到SW地址上时，就没有办法使用对应的私钥找回币了，如果该SW地址上的币在BTC链上面动用过，发送的BCH有可能会直接被黑客盗走，只要发送过黑客就能拿到解锁脚本，拿走该地址上的BCH，没有BTC链上没有发送过币是安全的。
如果你不小心把BCH发送到SW地址上，首先做的是不要再做任何发币的操作，可以到[btc.com](btc.com)寻求帮助，可以找回你的BCH，如果你发送的SW地址的币在BTC链上动用过的话，你的BCH就有可能会被黑客盗走。
另外BCH社区准备升级地址格式，看样子这样做还是很有意义的。

打赏地址:  16uoPajbFeKcVXdwDSuGxb7unYy1X1rMss
![16uoPajbFeKcVXdwDSuGxb7unYy1X1rMss](http://upload-images.jianshu.io/upload_images/22188-a488acc2db7ddbf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





