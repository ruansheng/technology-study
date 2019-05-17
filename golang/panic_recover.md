### panic recover
一般情况下我们应该避免使用panic和recover，尽可能使用errors
只有在程序无法继续执行的情况下才应该使用panic和recover

### panic典型应用场景
```
1.不可恢复的错误，让程序不能继续进行。
比如说Web服务器无法绑定到指定端口。在这种情况下，panic是合理的，因为如果端口绑定失败接下来的逻辑继续也是没有意义的。

2.coder的人为错误
假设我们有一个接受指针作为参数的方法，然而使用了nil作为参数调用此方法。在这种情况下，我们可以用panic，因为该方法需要一个有效的指针。
```

### func panic(interface{})
```
package main

import (
    "fmt"
)

func fullName(firstName *string, lastName *string) {
    if firstName == nil {
        panic("runtime error: first name cannot be nil")
    }
    if lastName == nil {
        panic("runtime error: last name cannot be nil")
    }
    fmt.Printf("%s %s\n", *firstName, *lastName)
    fmt.Println("returned normally from fullName")
}

func main() {
    firstName := "Elon"
    fullName(&firstName, nil)
    fmt.Println("returned normally from main")
}

输出:
panic: runtime error: last name cannot be nil
goroutine 1 [running]:
main.fullName(0x1040c128, 0x0)
    /tmp/sandbox135038844/main.go:12 +0x120
main.main()
    /tmp/sandbox135038844/main.go:20 +0x80
```

### defer 和 panic
```
当函数遇到panic时，将会终止panic后面代码的执行，如果函数体包含有defer函数的话会执行完defer函数。
然后返回其调用者。此过程一直持续到当前goroutine的所有函数都返回，此时程序打印出panic内容，然后是堆栈跟踪信息，然后终止。

package main

import (
    "fmt"
)

func fullName(firstName *string, lastName *string) {
    defer fmt.Println("deferred call in fullName")
    if firstName == nil {
        panic("runtime error: first name cannot be nil")
    }
    if lastName == nil {
        panic("runtime error: last name cannot be nil")
    }
    fmt.Printf("%s %s\n", *firstName, *lastName)
    fmt.Println("returned normally from fullName")
}

func main() {
    defer fmt.Println("deferred call in main")
    firstName := "Elon"
    fullName(&firstName, nil)
    fmt.Println("returned normally from main")
}

输出:
deferred call in fullName
deferred call in main
panic: runtime error: last name cannot be nil

goroutine 1 [running]:
main.fullName(0x1042bf90, 0x0)
    /tmp/sandbox060731990/main.go:13 +0x280
main.main()
    /tmp/sandbox060731990/main.go:22 +0xc0
```

### recover函数
```
recover是一个内置函数，用于goroutine从panic的中断状况中恢复。 函数定义如下:
func recover() interface{}

recover只有在defer函数内部调用时才有效。defer函数内通过调用recover可以让panic中断的程序恢复正常执行，
调用recover会返回panic的内容。如果在defer函数之外调用recover，它将不会停止panic序列。

package main

import (
    "fmt"
)

func recoverName() {
    if r := recover(); r!= nil {
        fmt.Println("recovered from ", r)
    }
}

func fullName(firstName *string, lastName *string) {
    defer recoverName()
    if firstName == nil {
        panic("runtime error: first name cannot be nil")
    }
    if lastName == nil {
        panic("runtime error: last name cannot be nil")
    }
    fmt.Printf("%s %s\n", *firstName, *lastName)
    fmt.Println("returned normally from fullName")
}

func main() {
    defer fmt.Println("deferred call in main")
    firstName := "Elon"
    fullName(&firstName, nil)
    fmt.Println("returned normally from main")
}

输出:
recovered from  runtime error: last name cannot be nil
returned normally from main
deferred call in main
```

### Panic, Recover 和 Goroutines
```
recover仅在从同一个goroutine调用时才起作用。
从不同的goroutine触发的panic中recover是不可能的。再来一个例子来加深理解。

package main

import (
    "fmt"
    "time"
)

func recovery() {
    if r := recover(); r != nil {
        fmt.Println("recovered:", r)
    }
}

func a() {
    defer recovery()
    fmt.Println("Inside A")
    go b()
    time.Sleep(1 * time.Second)
}

func b() {
    fmt.Println("Inside B")
    panic("oh! B panicked")
}

func main() {
    a()
    fmt.Println("normally returned from main")
}

输出:
Inside A
Inside B
panic: oh! B panicked

goroutine 5 [running]:
main.b()
    /tmp/sandbox388039916/main.go:23 +0x80
created by main.a
    /tmp/sandbox388039916/main.go:17 +0xc0
```

### 运行时的panic
```
panic还可能由运行时的错误引起，例如数组越界访问。这相当于使用由接口类型runtime.Error定义的参数调用内置函数panic。
runtime.Error接口的定义如下:
type Error interface {
    error
    // RuntimeError is a no-op function but
    // serves to distinguish types that are run time
    // errors from ordinary errors: a type is a
    // run time error if it has a RuntimeError method.
    RuntimeError()
}

runtime.Error接口满足内置接口类型error

package main

import (
    "fmt"
)

func a() {
    n := []int{5, 7, 4}
    fmt.Println(n[3])
    fmt.Println("normally returned from a")
}
func main() {
    a()
    fmt.Println("normally returned from main")
}

输出:
panic: runtime error: index out of range

goroutine 1 [running]:
main.a()
    /tmp/sandbox780439659/main.go:9 +0x40
main.main()
    /tmp/sandbox780439659/main.go:13 +0x20

可以恢复:
package main

import (
    "fmt"
)

func r() {
    if r := recover(); r != nil {
        fmt.Println("Recovered", r)
    }
}

func a() {
    defer r()
    n := []int{5, 7, 4}
    fmt.Println(n[3])
    fmt.Println("normally returned from a")
}

func main() {
    a()
    fmt.Println("normally returned from main")
}

输出:
Recovered runtime error: index out of range
normally returned from main
```

### recover后获取堆栈信息
```
恢复了panic，但是丢失了这次panic的堆栈调用的信息。
有一种方法可以解决这个，就是使用Debug包中的PrintStack函数打印堆栈跟踪信息
package main

import (
    "fmt"
    "runtime/debug"
)

func r() {
    if r := recover(); r != nil {
        fmt.Println("Recovered", r)
        debug.PrintStack()
    }
}

func a() {
    defer r()
    n := []int{5, 7, 4}
    fmt.Println(n[3])
    fmt.Println("normally returned from a")
}

func main() {
    a()
    fmt.Println("normally returned from main")
}

输出:
Recovered runtime error: index out of range
goroutine 1 [running]:
runtime/debug.Stack(0x1042beb8, 0x2, 0x2, 0x1c)
    /usr/local/go/src/runtime/debug/stack.go:24 +0xc0
runtime/debug.PrintStack()
    /usr/local/go/src/runtime/debug/stack.go:16 +0x20
main.r()
    /tmp/sandbox949178097/main.go:11 +0xe0
panic(0xf0a80, 0x17cd50)
    /usr/local/go/src/runtime/panic.go:491 +0x2c0
main.a()
    /tmp/sandbox949178097/main.go:18 +0x80
main.main()
    /tmp/sandbox949178097/main.go:23 +0x20
normally returned from main
```

