## 循环
```
Rust提供了3中循环:loop、while、for
```

### loop
```
loop{
    println!("{}", "hello");
}
```

### for
```
for x in 0..10 {
    println!("{}", x);
}

for (index,value) in (5..10).enumerte() {
    println!("{},{}", index,value);
}

迭代器:
let lines = "hello\nworld".lines();
for (index,value) in lines.enumerte() {
    println!("{},{}", index,value);
}

标签循环:
'outer':for x in 0..9 {
    'inner':for y in 0..9 {
       if x%2 == 0 {
           continue 'outer';
       }
       if y%2 == 0 {
           continue 'inner';
       }
       println!("{},{}", x, y);
    }
}
```