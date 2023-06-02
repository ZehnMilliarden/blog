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
fn main() {
    let mut vv: StructName = StructName::create();
    println!("{:?}", vv);
    vv.modify_member(&String::from("target"));
    println!("{}", vv.get_member());
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