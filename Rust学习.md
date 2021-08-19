### Rust安装配置

+ Archlinux安装rust，`yay -S rustup` ,rustup是一个管理rust版本的相关命令行工具。
+ `rustup install stable`
+ `rustup default stable`
+ 安装nightly，`rustup install ngihtly`, `rustup default nightly`
+ 补全需要
  + `rustup component add rust-src`
  + `cargo install racer`
  + `cargo install rustfmt`
+ cargo是rust的构建系统和包管理工具，它可以为你做很多事情，比如构建代码，下载依赖库并且编译这些库等。

+ 新建一个rust项目，`cargo new 项目名`
+ 编译一个rust项目，`cargo build`
+ 运行一个rust项目，`cargo run`
+ 编译运行一个rust程序，`rustc 程序文件名`
+ 在Clion上面运行rust，Linux下面直接在插件那里下载rust插件，然后就可以直接打开一个rust项目运行了。
+ 最后，Welcome to Rust !



### Rust基本语法

###### 基本数据类型

+ 整型，i8,i16,i32,i64,i128,usize(根据系统定义)
+ 浮点型，f32和f64
+ 布尔型，true和false
+ 字符类型
+ 复合类型
+ 数组类型

###### 函数

+ 函数统一以fn开头，后面接上这个函数的函数名
+ 对于需要进行传参的，每个参数都必须要指定这些参数的函数名
