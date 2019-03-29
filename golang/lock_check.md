### race检测
```
golang在1.1之后引入了竞争检测的概念

有可能会造成两个goroutine对同一个变量进行竞争的情况
```

### 检测方式
```
可以使用go run -race 或者 go build -race 来进行竞争检测
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