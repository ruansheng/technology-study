## 依赖
```
Rust包的仓库地址 https://crates.io/

在 Rust 中，我们通常把包称作“crates”。
```

### 添加依赖
```
我们使用了名为 ferris-says 的库。
在 Cargo.toml 文件中添加以下信息（从 crate 页面上获取）
[dependencies]
ferris-says = "0.1"

接着运行
#cargo build

之后 Cargo 就会下载安装该依赖

运行此命令会创建一个新文件 Cargo.lock，该文件记录了本地所用依赖库的精确版本。
```

### 使用依赖
```
要使用该依赖库，我们可以打开 main.rs，然后在其中添加下面这行代码：
use ferris_says::say;

这样我们就可以使用 ferris-says crate 中导出的 say 函数了。

编辑main.rs文件:
use ferris_says::say; // from the previous step
use std::io::{stdout, BufWriter};

fn main() {
    let stdout = stdout();
    let out = b"Hello fellow Rustaceans!";
    let width = 24;

    let mut writer = BufWriter::new(stdout.lock());
    say(out, width, &mut writer).unwrap();
}

执行cargo run输出如下:
----------------------------
| Hello fellow Rustaceans! |
----------------------------
              \
               \
                 _~^~^~_
             \) /  o o  \ (/
               '_   -   _'
               / '-----' \

```