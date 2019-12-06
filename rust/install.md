## 安装
```
Rustup：Rust安装器和版本管理工具

安装 Rust 的主要方式是通过 Rustup 这一工具，它既是一个 Rust 安装器又是一个版本管理工具。

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

Cargo：Rust 的构建工具和包管理器
在安装 Rustup 时，也会安装 Rust 构建工具和包管理器的最新稳定版，即 Cargo。Cargo 可以做很多事情：

cargo build 可以构建项目
cargo run 可以运行项目
cargo test 可以测试项目
cargo doc 可以为项目构建文档
cargo publish 可以将库发布到 crates.io。

要检查您是否安装了 Rust 和 Cargo，可以在终端中运行(可能需要配置PATH环境变量)：
cargo --version
```