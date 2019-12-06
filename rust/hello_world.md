## Hello World
```
创建第一个rust项目
#cargo new hello-rust

这会生成一个名为 hello-rust 的新目录，其中包含以下文件：
hello-rust
|- Cargo.toml
|- src
  |- main.rs

Cargo.toml 为 Rust 的清单文件。其中包含了项目的元数据和依赖库。
src/main.rs 为编写应用代码的地方。

运行项目:
#cargo run
   Compiling hello-rust v0.1.0 (/Users/ruansheng/rust/hello-rust)
    Finished dev [unoptimized + debuginfo] target(s) in 1.56s
     Running `target/debug/hello-rust`
Hello, world!
```