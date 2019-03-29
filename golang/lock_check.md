### race检测
```
有可能会造成两个goroutine对同一个变量进行竞争的情况

Go1.1包含了一个竞争检测器，一个全新的工具，用于在Go代码中找到竞争条件。
该工具当前在Linux,OS X 和Windows平台可用，只要CPU是64位的x86架构。
```
```
竞争检测器基于C/C++的ThreadSanitizer 运行时库，该库在Google内部代码基地和Chromium找到许多错误。
这个技术在2012年九月集成到Go中，从那时开始，它已经在标准库中检测到42个竞争条件。
现在，它已经是我们持续构建过程的一部分，当竞争条件出现时，它会继续捕捉到这些错误。
```

### 工作原理
```
竞争检测器集成在go工具链中。当使用了-race作为命令行参数后，编译器会插桩代码，使得所有代码在访问内存时，会记录访问时间和方法。
同时运行时库会观察对共享变量的未同步访问。当这种竞争行为被检测到，就会输出一个警告信息。
```

### 检测方式
```
可以使用go run -race 或者 go build -race 来进行竞争检测

go test -race mypkg    // 测试包
go run -race mysrc.go  // 编译和运行程序
go build -race mycmd   // 构建程序
go install -race mypkg // 安装程序
```

```
package main

import (
        "fmt"
        "sync"
)

var x = 0

func increment(wg *sync.WaitGroup) {
        x = x + 1
        wg.Done()
}
func main() {
        var w sync.WaitGroup
        for i := 0; i < 1000; i++ {
                w.Add(1)
                go increment(&w)
        }
        w.Wait()
        fmt.Println("final value of x", x)
}


➜  mutex ./1
==================
WARNING: DATA RACE
Write at 0x0000011d4318 by goroutine 7:
  main.increment()
      /test/mutex/1.go:11 +0x3a

Previous write at 0x0000011d4318 by goroutine 6:
  main.increment()
      /test/mutex/1.go:11 +0x3a

Goroutine 7 (running) created at:
  main.main()
      /test/mutex/1.go:18 +0xa2

Goroutine 6 (finished) created at:
  main.main()
      /test/mutex/1.go:18 +0xa2
==================
final value of x 1000
Found 1 data race(s)
```