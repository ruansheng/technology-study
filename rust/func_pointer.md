## 函数指针
```
可以创建指向函数的变量绑定
let f:fn(i32)->i32;
f是一个指向一个获取i32参数并返回i32的函数的变量绑定

例如:
fn  test(x:i32)->i32 {
  x+1
}

let f:fn(i32)->i32 = test;
或者
let f = test;
可以用f来调用这个函数
let g = f(3);
```