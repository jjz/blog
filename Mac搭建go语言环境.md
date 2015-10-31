# Mac上go环境配置

##使用Homebrew 安装go语言sdk
>brew install  go

==> Downloading https://homebrew.bintray.com/bottles/go-1.5.1.el_capitan.bottle.
Already downloaded: /Library/Caches/Homebrew/go-1.5.1.el_capitan.bottle.tar.gz
==> Pouring go-1.5.1.el_capitan.bottle.tar.gz
==> Caveats
As of go 1.2, a valid GOPATH is required to use the `go get` command:
  https://golang.org/doc/code.html#GOPATH

You may wish to add the GOROOT-based install location to your PATH:
  export PATH=$PATH:/usr/local/opt/go/libexec/bin
==> Summary
🍺  /usr/local/Cellar/go/1.5.1: 5330 files, 273M


##配置GOPATH
查看go 的环境变量设置
>go env 

    GOARCH="amd64"
    GOBIN=""
    GOEXE=""
    GOHOSTARCH="amd64"
    GOHOSTOS="darwin"
    GOOS="darwin"
    GOPATH=""
    GORACE=""
    GOROOT="/usr/local/Cellar/go/1.5.1/libexec"
    GOTOOLDIR="/usr/local/Cellar/go/1.5.1/libexec/pkg/tool/darwin_amd64"
    GO15VENDOREXPERIMENT=""
    CC="clang"
    GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fno-common"
    CXX="clang++"
    CGO_ENABLED="1"

需要设置环境变量`GOPATH` ,`GOBIN` 以及把GOBIN加入到`PATH`中
>vim .bash_profile

    export GOPATH=/usr/local/Cellar/go/1.5.1
    export GOBIN=$GOPATH/bin
    export PATH=$PATH:$GOBIN

>source .bash_profile
 
 再看下go的环境变量
 >go env
 
    GOARCH="amd64"
    GOBIN="/usr/local/Cellar/go/1.5.1/bin"
    GOEXE=""
    GOHOSTARCH="amd64"
    GOHOSTOS="darwin"
    GOOS="darwin"
    GOPATH="/usr/local/Cellar/go/1.5.1"
    GORACE=""
    GOROOT="/usr/local/Cellar/go/1.5.1/libexec"
    GOTOOLDIR="/usr/local/Cellar/go/1.5.1/libexec/pkg/tool/darwin_amd64"
    GO15VENDOREXPERIMENT=""
    CC="clang"
    GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fno-common"
    CXX="clang++"
    CGO_ENABLED="1"

环境变量设置成功!







