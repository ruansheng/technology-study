### 经典的GC算法
```
引用计数(reference counting)
标记-清扫(mark & sweep)
复制收集(Copy and Collection)
```

### golang gc 里程碑
```
v1.1 STW  (stop the world)
v1.3 Mark STW, Sweep 并行
v1.5 三色标记法
v1.8 hybrid write barrier
```

### 标记-清扫(mark & sweep)
```
第一步: 找出不可达的对象，然后做上标记。
第二步: 回收标记好的对象。
但是有一点需要额外注意：mark and sweep算法在执行的时候，需要程序暂停！即stop the world。 也就是说，这段时间程序会卡在那

存在的问题:
1.STW，stop the world；让程序暂停，程序出现卡顿
2.标记需要扫描整个heap
3.清除数据会产生heap碎片
```

### 三色标记法
```
Golang的三色标记法的大体流程:
1. 程序创建的对象都标记为白色
2. gc开始扫描所有可到达的对象，标记为灰色
3. 从灰色对象中找到其引用对象标记为灰色，把灰色对象本身标记为黑色
4. 监视对象中的内存修改，并持续上一步的操作，直到灰色标记的对象不存在
5. 此时，gc回收白色对象
6. 最后，将所有黑色对象变为白色，并重复以上所有过程。
```

### STW原理
```
标记-清除(mark and sweep)算法的STW(stop the world)操作，就是runtime把所有的线程全部冻结掉，
所有的线程全部冻结意味着用户逻辑是暂停的。这样所有的对象都不会被修改了，这时候去扫描是绝对安全的。
```


