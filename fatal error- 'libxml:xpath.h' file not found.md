fatal error: 'libxml/xpath.h' file not found

在MacOS上面安装readability-lxml的时候，遇到了一个问题

    src/lxml/includes/etree_defs.h:14:10: fatal error: 'libxml/xmlversion.h' file not found
    #include "libxml/xmlversion.h"
             ^
    1 error generated.

问题的原因是我刚刚升级了Xcode 没有安装command line tools for Xcode

在Xcode内安装：
>Xcode -> Preferences... ->  Locations

使用命令行安装：
>xcode-select --install

再次安装readability-lxml成功！
