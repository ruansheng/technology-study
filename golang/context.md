### context
```
在 Go 语言中 context 包允许您传递一个 "context" 到您的程序。
Context 如超时或截止日期（deadline）或通道，来指示停止运行和返回。
例如，如果您正在执行一个 web 请求或运行一个系统命令，定义一个超时对生产级系统通常是个好主意。
因为，如果您依赖的API运行缓慢，你不希望在系统上备份（back up）请求，因为它可能最终会增加负载并降低所有请求的执行效率。
导致级联效应。这是超时或截止日期 context 派上用场的地方。

创建 context（Background 和 TODO）以及如何派生 context（WithValue，WithCancel，Deadline 和 Timeout）
```

### context.Background() Context
```
这个函数返回一个空 context。这只能用于高等级（在 main 或顶级请求处理中）。这能用于派生我们稍后谈及的其他 context。
ctx := context.Background()

var (
    background = new(emptyCtx)
    todo = new(emptyCtx)
)
```

### context.TODO() Context
```
这个函数也是创建一个空 context。也只能用于高等级或当您不确定使用什么 context，或函数以后会更新以便接收一个 context 。这意味您（或维护者）计划将来要添加 context 到函数。
ctx := context.TODO()

var (
    background = new(emptyCtx)
    todo = new(emptyCtx)
)
```

### context.WithValue(parent Context, key, val interface{}) (ctx Context, cancel CancelFunc)
```
此函数接收 context 并返回派生 context，其中值 val 与 key 关联，并通过 context 树与 context 一起传递。
这意味着一旦获得带有值的 context，从中派生的任何 context 都会获得此值。不建议使用 context 值传递关键参数，而是函数应接收签名中的那些值，使其显式化。
ctx := context.WithValue(context.Background(), key, "test")
```

### context.WithCancel(parent Context) (ctx Context, cancel CancelFunc)
```
此函数创建从传入的父 context 派生的新 context。父 context 可以是后台 context 或传递给函数的 context。

返回派生 context 和取消函数。只有创建它的函数才能调用取消函数来取消此 context。
如果您愿意，可以传递取消函数，但是，强烈建议不要这样做。这可能导致取消函数的调用者没有意识到取消 context 的下游影响。
可能存在源自此的其他 context，这可能导致程序以意外的方式运行。简而言之，永远不要传递取消函数。

ctx, cancel := context.WithCancel(context.Background())
```

### context.WithDeadline(parent Context, d time.Time) (ctx Context, cancel CancelFunc)
```
此函数返回其父项的派生 context，当截止日期超过或取消函数被调用时，该 context 将被取消。
例如，您可以创建一个将在以后的某个时间自动取消的 context，并在子函数中传递它。当因为截止日期耗尽而取消该 context 时，获此 context 的所有函数都会收到通知去停止运行并返回。

ctx, cancel := context.WithDeadline(context.Background(), time.Now().Add(2 * time.Second))
```

### context.WithTimeout(parent Context, timeout time.Duration) (ctx Context, cancel CancelFunc)
```
此函数类似于 context.WithDeadline。不同之处在于它将持续时间作为参数输入而不是时间对象。此函数返回派生 context，如果调用取消函数或超出超时持续时间，则会取消该派生 context。

ctx, cancel := context.WithTimeout(context.Background(), 2 * time.Second)
```



