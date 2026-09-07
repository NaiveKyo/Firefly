---
title: Getting started with Rust
published: 2026-08-08
description: "搭建 Rust 学习环境"
image: "./8-image-1.png"
tags: ["Rust"]
category: "Rust"
draft: false
slug: getting-started-with-rust
---

## Intro

[官网](https://rust-lang.org/)

[rustup: a command line tool for managing Rust versions and associated tools.](https://rust-lang.github.io/rustup/index.html)

[The Rust Programming Language](https://doc.rust-lang.org/book/title-page.html)

[Rust package manager: Cargo](https://doc.rust-lang.org/stable/cargo/)


> Caution

MacOS 平台建议不要使用 homebrew 安装 rustup，推荐使用官方提供的 install 脚本。因为 brew 只能同时管理一个版本的软件，而开发 Rust 程序经常需要在多个版本进行切换。

## Rustup

注：后续操作都是在 macos 系统上。

### Install

首先安装 rustup ，该工具是一个跨平台的用于管理不同 Rust 及其工具链组件版本的官方工具。


终端执行以下脚本（不要使用 sudo 安装）：

```bash
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

成功安装后终端会提示这个：

```
Rust is installed now. Great!
```

在安装过程中可能会遇到文件写入权限不足导致失败的情况，这是因为 rustup 的安装脚本会检测并修改当前用户家目录下可能存在的配置文件，比如 \~/.profile、\~/.base_profile、\~/.baserc、\~/.zshenv（zsh），如果权限不足可以执行以下命令调整配置文件权限：

```bash
sudo chown $(whoami) ~/.profile
```

MacOS 现在默认使用 zsh，其他配置文件如果不需要可以删除，也可以调整权限后保留。

最后重启当前 shell，重新加载 PATH。

Rustup 的 metadata 和 toolchain 都会放在 /Users/${username}/.rustup 目录下，当然也可以通过修改环境变量 RUSTUP_HOME 来调整位置。

Cargo 的家目录在 /Users/${username}/.cargo 目录下，环境变量为 CARGO_HOME。

cargo、rustc、rustup 和其他工具都在 Cargo 的 bin 目录中，即 ${CARGO_HOME}/bin 。

执行完安装操作后，相关命令会自动添加到 PATH 路径中，如果需要卸载 rustup 可以执行 rustup self uninstall，上面的操作都会被移除。

成功安装 rustup 后，还需要一个 linker（链接器）才可以将编译产物打包为一个文件，比如 macos 的 ld，如果没有安装则可以通过以下命令安装 xcode：

```bash
$ xcode-select --install
```

Linux 平台一般是 GCC 或 Clang，根据系统发行版本选择合适的 linker。

使用以下命令验证是否正确安装 Rust：

```bash
$ rustc --version
rustc 1.93.0 (254b59607 2026-01-19)
```

### Updating And Uninstalling

使用 rustup 安装和管理 Rust 时，升级版本或卸载操作非常简单，只需执行以下命令：

```bash
# 升级到最新版本

$ rustup update

# 卸载 Rust 和 rustup

$ rustup self uninstall

# 查看本地的 Rust 文档

$ rustup doc
```

## Rust Development Environment

可以使用 IDE 或者文本编辑器开发 Rust。

- Visual Code + rust analyzer
- Zed + Rust LSP
- neovim + LazyVim

这里以 neovim 为例，macos 可以通过 brew 命令安装 neovim，然后通过 LazyVim 这个基于 lazy.nvim 的 Neovim setup 来快速开始编写 Rust 代码，后续如果对 neovim 有更深的配置定制需求也可以使用 kickstart.nvim。

[Neovim plugin manager：lazy.nvim](https://github.com/folke/lazy.nvim)
[kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim)
[LazyVim](https://www.lazyvim.org/)

### Install LazyVim

https://www.lazyvim.org/installation

1、先备份当前 neovim 的配置

```bash
# required

mv ~/.config/nvim{,.bak}

# optional but recommended

mv ~/.local/share/nvim{,.bak}
mv ~/.local/state/nvim{,.bak}
mv ~/.cache/nvim{,.bak}
```

2、然后克隆 LazyVim 提供的预先配置好的 Neovim 的配置模版

克隆模版仓库

```bash
git clone https://github.com/LazyVim/starter ~/.config/nvim

# 移除 git 相关的目录

rm -rf ~/.config/nvim/.git
```

3、启动 Neovim

```bash
nvim
```

执行命令后会进入 lazy.nvim 的管理页面，开始下载 LazyVim 预设的插件

### Hello World

rust 代码文件以 .rs 为后缀，如果文件名有多个单词，推荐以下划线连接多个单词。

新建目录，最后在 hello_world 文件夹下新建 main.rs 文件：

```bash
$ mkdir ~/projects
$ cd ~/projects
$ mkdir hello_world
$ cd hello_world

# 可以使用 IDE，也可以使用编辑器，这里以 nvim 为例

$ nvim main.rs
fn main() {
println!("Hello, world!");
}

$ rustc main.rs
$ ./main
Hello, world!
```

> 几个注意点：

- Rust 代码文件后缀是 rs

- main 函数很特殊

- 每个 Rust 文件中叫做 main 的函数是第一个被执行的

println! 叫做 Rust macro，如果去掉 ! 就是调用一个函数，注意宏和函数的规则不一定一样，Rust macro 是一种用来扩展 Rust 语法的代码。

rustc 是 Rust 的编译器，能将 rs 文件编译输出为一个可执行文件，Rust 是一种 ahead-of-time compiled language（预编译语言），需要先将代码进行编译，然后才能运行程序，这一点和其他动态语言不太一样，比如 Ruby、Python、JavaScript。

如果代码或项目不太复杂，rustc 已能满足需求，如果项目较为复杂，可以使用 cargo。

## Hello Cargo

Cargo 是 Rust 的构建系统和包管理器。通过该工具处理大量任务，比如代码构建、依赖下载、构建库。

注：代码依赖的 libraries 一般称为 dependencies。

前面编写的简单的 Rust 代码没有用到其他 dependencies，也没有用到 Cargo，如果要用 Cargo 实现 hello word 案例，其实只需要用到 Cargo 构建代码的能力。

使用官方推荐的安装方式安装 Rust 时会同时安装 Cargo:

```bash
$ cargo --version
cargo 1.93.0 (083ac5135 2025-12-15)
```

### Create a Project with Cargo

创建 cargo 项目：

```bash
[projects]$ cargo new hello_cargo
[projects]$ cd hello_cargo
[hello_cargo]$ ls
Cargo.toml src
```

使用 cargo new 命令会默认安装内置的 binary application 模版创建项目，包含 toml 和 src/main.rs 文件
[TOML](https://toml.io/en/) （Tom's Obvious, Minimal Language）格式的文件是 Cargo 使用的项目配置文件。

```bash
[hello_cargo]$ cat Cargo.toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2024"

[dependencies]
```

[package] 块下面可以写 package 级别的配置，这里配置了项目的 name、version 和 Rust 的 edition。

最后一行 [dependencies] 后面会列出来项目使用的依赖，在 Rust 中代码库也被叫做 crates，目前这个项目还没有使用其他 dependencies。

接着可以查看 src/main.rs 文件：

```bash
[hello_cargo]$ cat src/main.rs
fn main() {
println!("Hello, world!");
}
```

Cargo 会自动生成 hello world 程序，和前面编写的代码相比，使用 Cargo 生成的项目，代码都在 src 目录下，同时也会生成一个 Cargo.toml 文件。还有一个快速生成 Cargo.toml 文件的方式就是执行 cargo init 命令，它会自动生成 toml 文件。

### Building and Running a Cargo Project

输入以下命令来打包项目：

```bash
[hello_cargo]$ cargo build
Compiling hello_cargo v0.1.0 (/.../projects/hello_cargo)
Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.10s
```

该命令会构建项目输出可执行文件 target/debug/hello_cargo，放到 debug 目录下是因为默认的构建就是 debug build，可以直接执行该文件：

```bash
[hello_cargo]$ target/debug/hello_cargo
Hello, world!
```

第一次执行 cargo build 命令，会产生一个 Cargo.lock 文件，该文件的作用是用于跟踪项目使用的 dependencies 的版本信息，我们无需手动操纵该文件内容，它由 cargo 来管理。

上述操作是先 build 再执行文件，也可以通过 cargo run 一个命令来实现：

```bash
[hello_cargo]$ cargo run
Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.00s
Running `target/debug/hello_cargo`
Hello, world!
```

除此之外，Cargo 还提供了 cargo check 命令用来检查项目代码是否可以正常编译，因为 Rust 编译对代码安全有严格要求。

当项目变得复杂时，可以周期性执行 cargo check，在检查代码问题的时候也会生成一些中间产物放在 target 目录下，然后在执行 cargo build，这样 build 时就可以利用 target 下的资源来加速构建，不过这也会产生一些问题，target 目录会变得很大。

到目前为止已经了解了一下几个命令：

- cargo new 新建项目
- cargo build 构建项目
- cargo run 可以在构建后立即执行
- cargo check 可以对代码做检查，而不产生可执行文件
- cargo 默认会将构建结果放在 target/debug 目录下，而不是作为项目代码

### Building for Release

当项目开发完毕，需要进行发布，此时应该执行 cargo build --release 命令，在 build 时加上 --release 参数，最后生成的文件会放在 target/release 目录下，注意在开发阶段 build 的产物放在 target/debug 目录。

release 构建时间会比 debug 要长，因为内部做了一些优化操作，可以让程序运行的更快，这是因为在开发阶段期望能快速构建来验证结果，而发布阶段则希望程序性能更好。

### Leveraging Cargo's Conventions

对于简单的项目，使用 Cargo 带来的收益相比使用 rustc 其实没提升多少，但是随着项目变得复杂，文件变得更多且引入其他依赖，此时使用 Cargo 来管理项目就会变得更方便。
