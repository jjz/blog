由于大家都知道的原因，国内的访问国外网站会比较慢，或者根本不能访问。
所以gem的源替换成http://ruby.taobao.org/

先来看看当前使用的gem源
>gem source -l

    \*\*\* CURRENT SOURCES \*\*\*
    https://rubygems.org/

把当前的gem源删除
>gem sources --remove http://rubygems.org/
   
    https://rubygems.org/ removed from sources

添加淘宝的gem源
>gem sources -a http://ruby.taobao.org/

    http://ruby.taobao.org/ added to sources

再看下当前使用的源
>gem source -l
    \*\*\* CURRENT SOURCES \*\*\*

    http://ruby.taobao.org/
    
淘宝gem源替换成功

