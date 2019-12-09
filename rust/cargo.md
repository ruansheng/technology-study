## Cargo
```
cargo new hello_world --bin
创建一个项目,--bin表示创建的是一个可执行文件项目,而不是一个库项目

cargo build
编译之后的文件在target/debug/hello_world

cargo build --release
优化编译项目,使Rust代码运行的更快,启用该选项会让程序编译时间更长

cargo run
```

### Cargo.toml
```
这个文件使用TOML(Tom's Obvious,Minimal Language),类似于INI,但有些改进
[package]
name = hello_world
version = "0.1.1"
authors = ["Your name <you@example.com>"]
```