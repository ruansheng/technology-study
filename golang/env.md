##  go env
```
查询环境变量
~ go env
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/Users/ruansheng/Library/Caches/go-build"
GOENV="/Users/ruansheng/Library/Application Support/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/Users/ruansheng/gowork/qingsongchou:/Users/ruansheng/gowork/test"
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/Users/ruansheng/go"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/Users/ruansheng/go/pkg/tool/darwin_amd64"
GCCGO="gccgo"
AR="ar"
CC="clang"
CXX="clang++"
CGO_ENABLED="1"
GOMOD=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/8d/7gqkp93s4zv94kqp3w59p4nh0000gn/T/go-build320527161=/tmp/go-build -gno-record-gcc-switches -fno-common"
```

设置环境变量
```
方法一:
go env -w GOPROXY=https://goproxy.cn,direct

方法二:
export GOPROXY=https://goproxy.cn,direct
```
