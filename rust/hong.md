## 宏
```

```

### println!()
```

```

### panic!()
```
使当前的执行线程崩溃并返回指定的信息，它拥有一个类型!
fn test() ->! {
 panic!("This function never returns!");
}

可以设置一个环境变量来获取到更多信息，该环境变量也被用在cargo run中
#RUST_BACKTRACE= 1
```

### assert_eq!()
```
比较2个值，当不相等的时候panic!()
```

### assert!()
```
传递一个值，为false的时候panic!()
```