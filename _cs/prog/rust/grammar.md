---
layout: default
title: Rust Grammar
nav_order: 1
description: ""
permalink: /cs/prog/rust/grammar
has_children: false
grand_parent: a46c73a3-f86b-4e8b-b199-9facf2d95e9d
parent: 83c46c7d-8fa0-4360-9b5f-88cd9089dd57
guid: d31f088e-d029-4e63-b05b-ccb246c327e9
---

# Rust 语法记录
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 所有权
- 一个变量不可以同时进行多个 mutable 租借 (Rust 对可变引用的这种设计主要出于对并发状态下发生数据访问碰撞的考虑)
```rust
let mut s : String = String::from("broadcast");
let sub1 : &mut String = &mut s;  // 第一次租借 为 mut
let sub2 : &mut String = &mut s;  // 第二次租借 这里因为 已经租借一次给 
                                  // sub1 为 mut 类型，所以这里会提示错误
```
- 一个变量不可以同时租借为mutable 又租借为 immutable ( 对 immutable 产生了逻辑错误 ) <br />
例子一
```rust
let mut s : String = String::from("broadcast");
let sub1 : &mut String = &mut s;  // 第一次租借 为 mutable
let sub2: &String = &s; // 第二次租借 为 immutable, 报错
```
例子二
```rust
let mut s : String = String::from("broadcast");
let sub: &String = &s; // 第一次租借 为 immutable
s.push_str("test"); // 报错，因为 push_str 作为 String
                    // 的成员方法，有一个隐藏的参数，租借自己为可变类型
```

## 结构体
- 结构体定义
```rust
#[derive(Debug)] 
struct StructName {
    Member0: String,
    Member1: i32,
    Member2: u8,
}
impl StructName {
    // 不修改成员 用 &self
    fn get_member(&self) -> &String {
        return &self.Member0;
    }
    // 需要修改成员 &mut self
    fn modify_member(&mut self, target: &String) {
        self.Member0 = target.clone();
    }
    // 结构体关联方法
    fn create() -> StructName {
        let v: StructName = StructName {
            Member0: String::from("Member0"),
            Member1: 1,
            Member2: 2,
        };
        return v;
    }
}
trait AbstractStructMember {
    fn GetAbstractMember1(&self) -> i32;
    fn ModifyAbstractMember1(&mut self, target: i32);
}
impl AbstractStructMember for StructName {
    fn GetAbstractMember1(&self) -> i32
    {
        return self.Member1;
    }

    fn ModifyAbstractMember1(&mut self, target: i32)
    {
        self.Member1 = target;
    }
}
trait AbstractStructMember2 {
    fn GetAbstractMember2(&self) -> u8;
    fn ModifyAbstractMember2(&mut self, target: u8);
}
impl AbstractStructMember2 for StructName {
    fn GetAbstractMember2(&self) -> u8
    {
        return self.Member2;
    }

    fn ModifyAbstractMember2(&mut self, target: u8)
    {
        self.Member2 = target;
    }
}
fn CallBack(object: &mut impl AbstractStructMember)
{
    object.ModifyAbstractMember1(100);
}
fn CallBack2<T>(object: &mut T)
where 
    T: AbstractStructMember + AbstractStructMember2
{
    object.ModifyAbstractMember1(1000);
    object.ModifyAbstractMember2(200);
}
fn main() {
    let mut vv: StructName = StructName::create();
    println!("{:?}", vv);
    vv.modify_member(&String::from("target"));
    vv.ModifyAbstractMember1(10);
    println!("{}, {}", vv.GetMember(), vv.GetAbstractMember1());
    CallBack(&mut vv);
    println!("{}, {}", vv.GetMember(), vv.GetAbstractMember1());
    CallBack2(&mut vv);
    println!("{}, {}, {}", vv.GetMember(), vv.GetAbstractMember1(), vv.GetAbstractMember2());
}
```

## 枚举类型
```rust
#[derive(Debug)]
enum Book {
    papery(u32),
    electronic(String)
}
let books: Book = Book::papery(123);
let ret: String = match books { //match 语法还可以用来匹配普通类型
    Book::papery(_) => {
        String::from("value1")
    },
    Book::electronic(_) => {
        String::from("value2")
    }
    _ => {
        String::from("value3")
    }
};
let opt_pre =String::from("value_pre").to_owned();
let opt: Option<&String> = Option::Some(&opt_pre);
match opt {
    Option::Some(some_value) =>{
        println!("some_value: {}", some_value);
    },
    Option::None => println!("None") // let opt = Option::None
}
```

## 生命周期
```rust
fn longer<'live>(s1: &'live str, s2: &'live str) -> &'live str {
    if s2.len() > s1.len() {
        s2
    } else {
        s1
    }
}
fn main()
{
    let r;
    {
        let s1 = "rust";
        let s2 = "ecmascript";
        r = longer(s1, s2);
    }
    println!("{} is longer", r);
}
```

# 待整理
## Rust如何实现多文件开发
Rust多文件开发可以使用以下步骤实现：

1. 创建一个新的 Rust 项目，可以使用以下命令：

```
cargo new my_project --bin
```

2. 在项目的根目录下创建一个 src 目录，在此目录中创建多个 Rust 源代码文件，例如：

```
src/
  main.rs
  lib.rs
```

3. 在 main.rs 文件中引入 lib.rs 文件中的 Rust 模块，例如：

```rust
mod lib;

fn main() {
  lib::hello_world();
}
```

4. 在 lib.rs 文件中编写 Rust 模块，例如：

```rust
pub fn hello_world() {
  println!("Hello, world!");
}
```

5. 在 vscode 中打开项目文件夹，点击左侧的资源管理器中的 src 目录，在右侧的编辑器中打开需要编辑的文件。

6. 在 vscode 中编写 Rust 代码时，可以使用代码补全、语法高亮、错误检查等功能，方便代码编写。

7. 在 vscode 中使用 cargo 命令进行构建、运行、测试等操作，例如：

```
cargo build
cargo run
cargo test
```

8. 在 vscode 中使用调试器进行调试，例如：

- 在 main.rs 中设置断点
- 点击左侧的调试器图标，选择 "Run"，开始调试

以上就是在实现Rust多文件开发的步骤。

## Rust中子目录下rs文件如何引用父目录rs文件中的方法
在 Rust 中，可以使用相对路径来引用父目录的模块和方法。例如，假设有如下目录结构：

```
my_project/
├── src/
│   ├── main.rs
│   └── lib.rs
└── sub_dir/
    └── sub_file.rs
```

在 `sub_file.rs` 中，如果要引用 `lib.rs` 中的方法，可以使用如下代码：

```rust
// 使用相对路径引用父目录的模块
use super::lib;

fn some_function() {
    // 调用父模块中的方法
    lib::some_method();
}
```

在 `use` 语句中，使用 `super` 关键字表示引用父目录的模块。然后，就可以使用 `lib::some_method()` 来调用父模块中的方法了。

## Rust如何引用父目录的父目录中的模块呢
如果要引用父目录的父目录中的模块，可以使用多个 `super` 关键字来表示相对路径。例如，假设有如下目录结构：

```
my_project/
├── src/
│   ├── main.rs
│   └── lib.rs
└── sub_dir/
    ├── sub_file.rs
    └── sub_sub_dir/
        └── sub_sub_file.rs
```

在 `sub_sub_file.rs` 中，如果要引用 `lib.rs` 中的方法，可以使用如下代码：

```rust
// 使用相对路径引用父目录的父目录中的模块
use super::super::lib;

fn some_function() {
    // 调用父目录的父模块中的方法
    lib::some_method();
}
```

在 `use` 语句中，使用 `super` 关键字表示引用父目录的模块，在 `super` 关键字前加上一个 `super` 关键字，则表示引用父目录的父目录的模块，以此类推。然后，就可以使用 `lib::some_method()` 来调用父目录的父模块中的方法了。

## 除了使用super 关键字还有别的办法吗
除了使用 `super` 关键字表示相对路径之外，还可以使用绝对路径来引用父目录的模块和方法。

使用绝对路径可以确保代码在多个文件和目录之间移动时，依赖关系不会被打破。要使用绝对路径，可以使用 `crate` 关键字代替 `super` 关键字。

例如，假设有如下目录结构：

```
my_project/
├── src/
│   ├── main.rs
│   └── lib.rs
└── sub_dir/
    ├── sub_file.rs
    └── sub_sub_dir/
        └── sub_sub_file.rs
```

在 `sub_sub_file.rs` 中，如果要引用 `lib.rs` 中的方法，可以使用如下代码：

```rust
// 使用绝对路径引用父目录中的模块
use crate::lib;

fn some_function() {
    // 调用父模块中的方法
    lib::some_method();
}
```

在 `use` 语句中，使用 `crate` 关键字表示引用当前 crate（即整个项目）的模块。然后，就可以使用 `lib::some_method()` 来调用父模块中的方法了。

## Rust如何把任意一个目录作为模块搜索路径
在 Rust 中，你可以使用 `std::env::set_var` 函数来设置环境变量，从而将任意一个目录作为模块搜索路径。

以下是示例代码：

```rust
use std::env;

fn main() {
    // 设置环境变量
    env::set_var("RUST_PATH", "/path/to/my/modules");
    
    // 加载模块
    // ...
}
```

在示例中，我们将环境变量 `RUST_PATH` 设置为 `/path/to/my/modules`，然后可以在代码中使用模块路径 `/path/to/my/modules/my_module` 来加载模块。

请注意，这种方法并非最佳实践。在实际开发中，通常使用 Cargo 构建工具来管理模块依赖和路径。

## 如何将一个自己开发的模块 加入 到 Cargo中进行管理呢
将自己开发的模块加入到 Cargo 中进行管理，需要按照以下步骤进行操作：

1. 创建一个新的 Rust 项目，可以使用 `cargo new` 命令创建一个新项目，例如：

   ```sh
   cargo new myproject
   ```

2. 在项目中创建一个新的 Rust 模块，例如：

   ```rust
   // 在 myproject/src/my_module.rs 中创建模块
   pub fn hello() {
       println!("Hello, world!");
   }
   ```

3. 修改 `myproject/Cargo.toml` 文件，将新创建的模块添加到依赖中，例如：

   ```toml
   [dependencies]
   my_module = { path = "src/my_module.rs" }
   ```

   在这里，我们将 `my_module` 添加到依赖中，并且指定了模块的路径是 `src/my_module.rs`。

4. 在需要使用模块的 Rust 代码中，引入模块并使用它，例如：

   ```rust
   // 在 src/main.rs 中使用模块
   use my_module::hello;
   
   fn main() {
       hello();
   }
   ```

现在，你可以使用 `cargo build` 命令来构建你的 Rust 项目，Cargo 会自动将你的模块编译到可执行文件中。如果你需要发布你的 Rust 项目，可以使用 `cargo publish` 命令将你的模块发布到 crates.io 上，供其他开发者使用。

## rust中怎么使用子目录下的模块
在 Rust 中，可以使用 `mod` 关键字来引入子目录下的模块。具体步骤如下：

1. 在子目录中创建一个名为 `mod.rs` 的文件，该文件用于声明子目录中的模块。

2. 在父目录的 Rust 文件中使用 `mod` 关键字来引入子目录中的模块。例如，如果子目录名为 `subdir`，则可以使用以下代码引入该子目录中的 `mod1.rs` 模块：

   ```rust
   mod subdir {
       mod mod1;
   }
   ```

3. 在子目录中的 `mod.rs` 文件中使用 `pub` 关键字来声明需要在父目录中使用的模块。例如，如果需要在父目录中使用 `mod1.rs` 模块，则可以在 `subdir/mod.rs` 文件中添加以下代码：

   ```rust
   pub mod mod1;
   ```

这样，在父目录的 Rust 文件中就可以直接使用子目录中的模块了。例如，可以使用以下代码来使用 `mod1.rs` 模块中的函数：

```rust
use subdir::mod1::some_function;
```

## 如果子目录的层次很深怎么处理，有什么办法可以优化吗
当子目录层次很深时，使用 `mod` 关键字来引入子目录中的模块会变得很麻烦。为了优化代码结构，可以使用 Rust 的模块路径别名（module path alias）功能来简化代码。模块路径别名允许我们为模块路径定义一个简短的别名，然后在代码中使用别名来引用模块。

具体步骤如下：

1. 在父目录的 Rust 文件中使用 `mod` 关键字来引入子目录中的模块。例如，如果子目录层次很深，可以使用以下代码引入子目录中的模块：

   ```rust
   mod my_deeply_nested_module {
       pub mod my_module;
   }
   ```

2. 在代码的开头使用 `use` 关键字来为模块路径定义别名。例如，可以使用以下代码为 `my_deeply_nested_module::my_module` 定义一个别名：

   ```rust
   use my_deeply_nested_module::my_module as my_module;
   ```

3. 现在就可以在代码中使用别名 `my_module` 来引用 `my_deeply_nested_module::my_module` 模块了。例如，可以使用以下代码来使用 `my_module` 模块中的函数：

   ```rust
   use my_deeply_nested_module::my_module as my_module;

   fn main() {
       my_module::some_function();
   }
   ```

使用模块路径别名可以使代码更加简洁易读，特别是当子目录层次很深时。