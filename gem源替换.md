由于大家都知道的原因，国内的访问国外网站会比较慢，或者根本不能访问。
所以需求把gem的源替换成国内的源:[https://ruby.taobao.org/](https://ruby.taobao.org/)

先来看看当前使用的gem源:
>gem source -l

```
    \*\*\* CURRENT SOURCES \*\*\*
    https://rubygems.org/
```
当前使用的是`rubygems.org`的源。
把当前的gem源删除：
>gem sources --remove https://rubygems.org/
   
```
    https://rubygems.org/ removed from sources
```
添加淘宝的gem源:
>gem sources -a https://ruby.taobao.org/

```
    https://ruby.taobao.org/ added to sources
```
再看下当前使用的源:
>gem source -l

```
    \*\*\* CURRENT SOURCES \*\*\*
    https://ruby.taobao.org/
   ```
淘宝gem源替换成功!

