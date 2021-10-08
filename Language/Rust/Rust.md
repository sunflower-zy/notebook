# Cargo 基本命令

- `cargo build` 可以构建项目
- `cargo run` 可以运行项目
- `cargo test` 可以测试项目
- `cargo doc` 可以为项目构建文档
- `cargo publish` 可以将库发布到 [crates.io](https://crates.io/)。

# 变量与常量

> 变量默认不可变，可加 **`mut` **修饰来使其可变，
>
> const 声明**常量**且需指定类型，命名风格为首字母大写加下划线

```rust
let x = 'x';
let mut x = 'x';
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

> 可以通过重复声明变量来隐藏之前的变量
>
> 与 `mut` 的区别是 mut 不能重新复制不能改变变量的类型，而重复声明获得的是一个新的变量

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}

```

```rust
let mut spaces = "   ";
spaces = spaces.len();
// 报错，不能改变变量的类型
```

# 数据类型

> 两类数据类型子集：标量（scalar）和复合（compound）。

> Rust 是 **静态类型**（_statically typed_）语言，也就是说在编译时就必须知道所有变量的类型。

## 标量类型

**标量**（_scalar_）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。

### [整型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#整型)

**整数** 是一个没有小数部分的数字。我们在第二章使用过 `u32` 整数类型。该类型声明表明，它关联的值应该是一个占据 32 比特位的无符号整数（有符号整数类型以 `i` 开头而不是 `u`）。表格 3-1 展示了 Rust 内建的整数类型。在有符号列和无符号列中的每一个变体（例如，`i16`）都可以用来声明整数值的类型。

表格 3-1: Rust 中的整型

| 长度    | 有符号  | 无符号  |
| ------- | ------- | ------- |
| 8-bit   | `i8`    | `u8`    |
| 16-bit  | `i16`   | `u16`   |
| 32-bit  | `i32`   | `u32`   |
| 64-bit  | `i64`   | `u64`   |
| 128-bit | `i128`  | `u128`  |
| arch    | `isize` | `usize` |

每一个变体都可以是有符号或无符号的，并有一个明确的大小。**有符号** 和 **无符号** 代表数字能否为负值，换句话说，这个数字是否有可能是负数（有符号数），或者永远为正而不需要符号（无符号数）。这有点像在纸上书写数字：当需要考虑符号的时候，数字以加号或减号作为前缀；然而，可以安全地假设为正数时，加号前缀通常省略。有符号数以[补码形式（two’s complement representation）](https://en.wikipedia.org/wiki/Two's_complement) 存储。

每一个有符号的变体可以储存包含从 -(2n - 1) 到 2n - 1 - 1 在内的数字，这里 _n_ 是变体使用的位数。所以 `i8` 可以储存从 -(27) 到 27 - 1 在内的数字，也就是从 -128 到 127。无符号的变体可以储存从 0 到 2n - 1 的数字，所以 `u8` 可以储存从 0 到 28 - 1 的数字，也就是从 0 到 255。

另外，`isize` 和 `usize` 类型依赖运行程序的计算机架构：64 位架构上它们是 64 位的， 32 位架构上它们是 32 位的。

可以使用表格 3-2 中的任何一种形式编写数字字面值。请注意可以是多种数字类型的数字字面值允许使用类型后缀，例如 `57u8` 来指定类型，同时也允许使用 `_` 做为分隔符以方便读数，例如`1_000`，它的值与你指定的 `1000` 相同。

表格 3-2: Rust 中的整型字面值

| 数字字面值                    | 例子          |
| ----------------------------- | ------------- |
| Decimal (十进制)              | `98_222`      |
| Hex (十六进制)                | `0xff`        |
| Octal (八进制)                | `0o77`        |
| Binary (二进制)               | `0b1111_0000` |
| Byte (单字节字符)(仅限于`u8`) | `b'A'`        |

那么该使用哪种类型的数字呢？如果拿不定主意，Rust 的默认类型通常是个不错的起点，数字类型默认是 `i32`。`isize` 或 `usize` 主要作为某些集合的索引。

> ##### [整型溢出](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#整型溢出)
>
> 比方说有一个 `u8` ，它可以存放从零到 `255` 的值。那么当你将其修改为 `256` 时会发生什么呢？这被称为 “整型溢出”（“integer overflow” ），关于这一行为 Rust 有一些有趣的规则。当在 debug 模式编译时，Rust 检查这类问题并使程序 _panic_，这个术语被 Rust 用来表明程序因错误而退出。第九章 [“`panic!` 与不可恢复的错误”](https://kaisery.github.io/trpl-zh-cn/ch09-01-unrecoverable-errors-with-panic.html) 部分会详细介绍 panic。
>
> 在 release 构建中，Rust 不检测溢出，相反会进行一种被称为二进制补码包装（_two’s complement wrapping_）的操作。简而言之，值 `256` 变成 `0`，值 `257` 变成 `1`，依此类推。依赖整型溢出被认为是一种错误，即便可能出现这种行为。如果你确实需要这种行为，标准库中有一个类型显式提供此功能，[`Wrapping`](https://doc.rust-lang.org/std/num/struct.Wrapping.html)。 为了显式地处理溢出的可能性，你可以使用标准库在原生数值类型上提供的以下方法:
>
> - 所有模式下都可以使用 `wrapping_*` 方法进行包装，如 `wrapping_add`
> - 如果 `check_*` 方法出现溢出，则返回 `None`值
> - 用 `overflowing_*` 方法返回值和一个布尔值，表示是否出现溢出
> - 用 `saturating_*` 方法在值的最小值或最大值处进行饱和处理

### [浮点型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#浮点型)

Rust 也有两个原生的 **浮点数**（_floating-point numbers_）类型，它们是带小数点的数字。Rust 的浮点数类型是 `f32` 和 `f64`，分别占 32 位和 64 位。默认类型是 `f64`，因为在现代 CPU 中，它与 `f32` 速度几乎一样，不过精度更高。

这是一个展示浮点数的实例：

文件名: src/main.rs

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

浮点数采用 IEEE-754 标准表示。`f32` 是单精度浮点数，`f64` 是双精度浮点数。

### [数值运算](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#数值运算)

Rust 中的所有数字类型都支持基本数学运算：加法、减法、乘法、除法和取余。整数除法会向下舍入到最接近的整数。下面的代码展示了如何在 `let` 语句中使用它们：

文件名: src/main.rs

```rust
fn main() {
    // 加法
    let sum = 5 + 10;

    // 减法
    let difference = 95.5 - 4.3;

    // 乘法
    let product = 4 * 30;

    // 除法
    let quotient = 56.7 / 32.2;
    let floored = 2 / 3; // 结果为 0

    // 取余
    let remainder = 43 % 5;
}
Playground Communication: timeout
```

这些语句中的每个表达式使用了一个数学运算符并计算出了一个值，然后绑定给一个变量。[附录 B](https://kaisery.github.io/trpl-zh-cn/appendix-02-operators.html) 包含 Rust 提供的所有运算符的列表。

### [布尔型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#布尔型)

正如其他大部分编程语言一样，Rust 中的布尔类型有两个可能的值：`true` 和 `false`。Rust 中的布尔类型使用 `bool` 表示。例如：

文件名: src/main.rs

```rust
fn main() {
    let t = true;

    let f: bool = false; // 显式指定类型注解
}
```

使用布尔值的主要场景是条件表达式，例如 `if` 表达式。在 [“控制流”（“Control Flow”）](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#控制流) 部分将介绍 `if` 表达式在 Rust 中如何工作。

### [字符类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#字符类型)

目前为止只使用到了数字，不过 Rust 也支持字母。Rust 的 `char` 类型是语言中最原生的字母类型，如下代码展示了如何使用它。（注意 `char` 由单引号指定，不同于字符串使用双引号。）

文件名: src/main.rs

```rust
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

Rust 的 `char` 类型的大小为四个字节(four bytes)，并代表了一个 Unicode 标量值（Unicode Scalar Value），这意味着它可以比 ASCII 表示更多内容。在 Rust 中，拼音字母（Accented letters），中文、日文、韩文等字符，emoji（绘文字）以及零长度的空白字符都是有效的 `char` 值。Unicode 标量值包含从 `U+0000` 到 `U+D7FF` 和 `U+E000` 到 `U+10FFFF` 在内的值。不过，“字符” 并不是一个 Unicode 中的概念，所以人直觉上的 “字符” 可能与 Rust 中的 `char` 并不符合。第八章的 [“使用字符串存储 UTF-8 编码的文本”](https://kaisery.github.io/trpl-zh-cn/ch08-02-strings.html#使用字符串存储-utf-8-编码的文本) 中将详细讨论这个主题。

## 复合类型

### 1.元组（tuple）

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
```

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}

```

除了使用模式匹配解构外，也可以使用点号（`.`）后跟值的索引来直接访问它们。例如：

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

这个程序创建了一个元组，`x`，并接着使用索引为每个元素创建新变量。跟大多数编程语言一样，元组的第一个索引值是 0。

没有任何值的元组 `()` 是一种特殊的类型，只有一个值，也写成 `()` 。该类型被称为 **单元类型**（_unit type_），而该值被称为 **单元值**（_unit value_）。如果表达式不返回任何其他值，则会隐式返回单元值。

### 2.数组（array）

数组中的每个元素的类型必须相同。

Rust 中的数组是固定长度的：一旦声明，它们的长度不能增长或缩小。

可以像这样编写数组的类型：在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

以这种方式编写数组的类型看起来类似于初始化数组的另一种语法：如果要为每个元素创建包含相同值的数组，可以指定初始值，后跟分号，然后在方括号中指定数组的长度，如下所示：

```rust
let a = [3; 5];
```

变量名为 `a` 的数组将包含 `5` 个元素，这些元素的值最初都将被设置为 `3`。这种写法与 `let a = [3, 3, 3, 3, 3];` 效果相同，但更简洁。

数组是可以在堆栈上分配的已知固定大小的单个内存块。可以使用索引来访问数组的元素，

访问超过数组长度的内存空间会报错并程序退出，

# 函数

## 定义函数

fn 关键字 全部为小写字母且下划线链接 main 函数为程序入口 无所谓函数定义在哪，定义了就行，都可以调用

## 函数表达式与语句

表达式又返回值，语句没有

表达式不要以 `;` 结尾

函数定义是一个语句

函数调用是表达式，宏调用是表达式，打括号来声明作用域也是表达式

函数默认返回最后的表达式的结果，也可以使用 `return` 来提前返回

在箭头（`->`）后声明返回值的类型。

```rust
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}

```

# 注释

```rust
// 这是注释，只有单行，多行就在每行前都加 //
```

# 流程控制

## If 表达式

```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}

```

if 只接受一个布尔值，给它别的类型会报错

因为 if 也是一个表达式，所以也可以赋值给变量

```rust
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number);
}

```

If else 所有可能的返回值必须是同一种类型

## 循环

### loop

loop 表示代码一直循环执行

### 退出循环

如果存在嵌套循环，`break` 和 `continue` 应用于此时最内层的循环。你可以选择在一个循环上指定一个 **循环标签**（_loop label_），然后将标签与 `break` 或 `continue` 一起使用，使这些关键字应用于已标记的循环而不是最内层的循环。下面是一个包含两个嵌套循环的示例

```rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {}", count);
        let mut remaining = 10;

        loop {
            println!("remaining = {}", remaining);
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {}", count);
}

```

### 从循环返回值

`loop` 的一个用例是重试可能会失败的操作，比如检查线程是否完成了任务。然而你可能会需要将操作的结果传递给其它的代码。如果将返回值加入你用来停止循环的 `break` 表达式，它会被停止的循环返回：

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

### while

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number = number - 1;
    }

    println!("LIFTOFF!!!");
}

```

## for

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
}

```

特定次数的循环，用到了 `.. `和 `rev `方法

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}

```

### 无限循环

```rust
loop {
	// ...
}
```

## 分支

```rust
match .... {
	XXXX::Less =>{
    //...
  },
  XXXX::Greater =>{
		//... continue
  },
  XXXX::Equal =>{
    // ... break
  }
}
```

# 所有权

## 所有权规则

1. Rust 中的每一个值都有一个被称为其 **所有者**（_owner_）的变量。
2. 值有且只有一个所有者。
3. 当所有者（变量）离开作用域，这个值将被丢弃

引用类型，一个堆内存只能 被一个栈内存饮用，一旦引用改变，原来保存引用的那个变量所保存的引用就会失效

## 引用

- 在任意给定时间，**要么** 只能有一个可变引用，**要么** 只能有多个不可变引用。
- 引用必须总是有效的。

```rust
let s = String::from("hello")
let str = &s;
// 不可变引用，只能读值
```

```rust
let mut s = String::from("hello")
let str = &mut s;
// 可变引用，可以改变值
```

不能同时有两个可变引用，也不能同时有可变和不可变引用

## slice

```rust
// slice 表示对字符串中部分的引用，字符串字面量本身也是slice，是对二进制部分的引用
fn main() {
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
}

```

```rust
// 数组slice
fn main() {
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
}

```

# 结构体

## 定义并实例化结构体

```rust
// 定义并实例化结构体
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

```rust
#![allow(unused_variables)]
fn main() {
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn build_user(email: String, username: String) -> User {
    User {
        email,// 变量和字段名相同时可以简写
        username,
        active: true,
        sign_in_count: 1,
    }
}
}

```

## 修改结构体的值

改变结构体的值需要在实例化结构体时将其设为`mut`，然后通过`.` 操作符来访问修改

```rust
// 从另一个结构体实例来实例化新的结构体实例
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};

```

## 元组结构体

```rust
#![allow(unused_variables)]
fn main() {
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
}

```

### [没有任何字段的类单元结构体](https://rust.bootcss.com/ch05-01-defining-structs.html#没有任何字段的类单元结构体)

我们也可以定义一个没有任何字段的结构体！它们被称为 **类单元结构体**（_unit-like structs_）因为它们类似于 `()`，即 unit 类型。类单元结构体常常在你想要在某个类型上实现 trait 但不需要在类型中存储数据的时候发挥作用。我们将在第十章介绍 trait。

## 方法

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 { // 结构体方法第一个参数为self或者self的引用
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area() // 通过 结构体实例.方法() 的方式来使用
    );
}

```

较多参数的方法定义

```rust
#![allow(unused_variables)]
fn main() {
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
}

```

### [关联函数](https://rust.bootcss.com/ch05-03-method-syntax.html#关联函数)

`impl` 块的另一个有用的功能是：允许在 `impl` 块中定义 **不** 以 `self` 作为参数的函数。这被称为 **关联函数**（_associated functions_），因为它们与结构体相关联。它们仍是函数而不是方法，因为它们并不作用于一个结构体的实例。你已经使用过 `String::from` 关联函数了。

关联函数经常被用作返回一个结构体新实例的构造函数。例如我们可以提供一个关联函数，它接受一个维度参数并且同时作为宽和高，这样可以更轻松的创建一个正方形 `Rectangle` 而不必指定两次同样的值：

文件名: src/main.rs

```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}
```

使用结构体名和 `::` 语法来调用这个关联函数：比如 `let sq = Rectangle::square(3);`。这个方法位于结构体的命名空间中：`::` 语法用于关联函数和模块创建的命名空间。第七章会讲到模块。

### 多个 impl 块

每个结构体都允许拥有多个 `impl` 块。例如，示例 5-16 中的代码等同于示例 5-15，但每个方法有其自己的 `impl` 块。

```rust
#![allow(unused_variables)]
fn main() {
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
}

```

# 枚举

## 定义枚举

枚举允许你通过列举可能的 **成员**（_variants_） 来定义一个类型。

```rust
// 定义枚举
#![allow(unused_variables)]
fn main() {
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}
// 访问枚举成员并赋值
let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
}

```

### 枚举方法

```rust
#![allow(unused_variables)]
fn main() {
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

impl Message {
    fn call(&self) {
        // 在这里定义方法体
    }
}

let m = Message::Write(String::from("hello"));
m.call();
}

```

### Option<T> 保存 `存在` 或 `不存在值` 两种状态的枚举

```rust
#![allow(unused_variables)]
fn main() {
enum Option<T> {
    Some(T),
    None,
}
}

```

## Match

```rust
#![allow(unused_variables)]
fn main() {
#[derive(Debug)]
enum UsState {
   Alabama,
   Alaska,
}

enum Coin {
   Penny,
   Nickel,
   Dime,
   Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
}
}

```

### 匹配 Option`<T>`

```rust
#![allow(unused_variables)]
fn main() {
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
}

```

### \_\_通配符

rust 的 match 必须处理所有可能的情况，所以有了通配符

```rust
#![allow(unused_variables)]
fn main() {
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),
}
}

```

## if let 简洁控制流，只关心一种情况

```rust
#![allow(unused_variables)]
fn main() {
#[derive(Debug)]
enum UsState {
   Alabama,
   Alaska,
}

enum Coin {
   Penny,
   Nickel,
   Dime,
   Quarter(UsState),
}
let coin = Coin::Penny;
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
}

```

# 使用包管理器

- **包**（_Packages_）： Cargo 的一个功能，它允许你构建、测试和分享 crate。
- **Crates** ：一个模块的树形结构，它形成了库或二进制项目。
- **模块**（_Modules_）和 **use**： 允许你控制作用域和路径的私有性。
- **路径**（_path_）：一个命名例如结构体、函数或模块等项的方式

## 包

一个包简单来说就是一个项目

crate 是一个二进制项或者库。_crate root_ 是一个源文件，Rust 编译器以它为起始点，并构成你的 crate 的根模块。_包_（_package_） 是提供一系列功能的一个或者多个 crate。

一个包会包含有一个 _Cargo.toml_ 文件，阐述如何去构建这些 crate。

- 一个包中至多 **只能** 包含一个库 crate(library crate)；
- 包中可以包含任意多个二进制 crate(binary crate)；

- 包中至少包含一个 crate，无论是库的还是二进制的。

_src/main.rs_ 就是一个与包同名的二进制 crate 的 crate 根。

如果包目录中包含 _src/lib.rs_，则包带有与其同名的库 crate，且 _src/lib.rs_ 是 crate 根。

crate 根文件将由 Cargo 传递给 `rustc` 来实际构建库或者二进制项目。

通过将文件放在 _src/bin_ 目录下，一个包可以拥有多个二进制 crate：每个 _src/bin_ 下的文件都会被编译成一个独立的二进制 crate。

允许你命名项的 _路径_（_paths_）；

用来将路径引入作用域的 `use` 关键字；

以及使项变为公有的 `pub` 关键字。

我们还将讨论 `as` 关键字、

外部包

glob 运算符。

## 模块

```rust
#![allow(unused_variables)]
fn main() {
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn server_order() {}

        fn take_payment() {}
    }
}
}


crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment


```

## 路径

路径有两种形式：

- **绝对路径**（_absolute path_）从 crate 根开始，以 crate 名或者字面值 `crate` 开头。
- **相对路径**（_relative path_）从当前模块开始，以 `self`、`super` 或当前模块的标识符开头。

绝对路径和相对路径都后跟一个或多个由双冒号（`::`）分割的标识符。

Rust 的 _私有性边界_（_privacy boundary_）：这条界线不允许外部代码了解、调用和依赖被封装的实现细节。

Rust 中默认所有项（函数、方法、结构体、枚举、模块和常量）都是私有的。父模块中的项不能使用子模块中的私有项，但是子模块中的项可以使用他们父模块中的项。这是因为子模块封装并隐藏了他们的实现详情，但是子模块可以看到他们定义的上下文

通过使用 `pub` 关键字来创建公共项，使子模块的内部部分暴露给上级模块。

使模块公有并不使其内容也是公有的

> 即，你可以访问我，但访问不了我的儿子内容，
>
> pub 修饰只是让我的父级可以访问我，
>
> 没有 pub 修饰只有我的儿子和兄弟可以访问我

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
fn main() {}

```

## super

```rust
fn serve_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::serve_order();
    }

    fn cook_order() {}
}
fn main() {}

```

## 结构体和枚举

```rust
#![allow(unused_variables)]
fn main() {
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // Order a breakfast in the summer with Rye toast
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Change our mind about what bread we'd like
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // The next line won't compile if we uncomment it; we're not allowed
    // to see or modify the seasonal fruit that comes with the meal
    // meal.seasonal_fruit = String::from("blueberries");
}
}

```

如果我们在一个结构体定义的前面使用了 `pub` ，这个结构体会变成公有的，但是这个结构体的字段仍然是私有的。我们可以根据情况决定每个字段是否公有。

如果我们将枚举设为公有，则它的所有成员都将变为公有。我们只需要在 `enum` 关键字前面加上 `pub`，

```rust
#![allow(unused_variables)]
fn main() {
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}
}

```

## Use

在作用域中增加 `use` 和路径类似于在文件系统中创建软连接（符号连接，symbolic link）。通过在 crate 根增加 `use crate::front_of_house::hosting`，现在 `hosting` 在作用域中就是有效的名称了，如同 `hosting` 模块被定义于 crate 根一样。通过 `use` 引入作用域的路径也会检查私有性，同其它路径一样。

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
fn main() {}

```

```rust
#![allow(unused_variables)]
fn main() {
use std::fmt;
use std::io;

fn function1() -> fmt::Result {
    // --snip--
    Ok(())
}

fn function2() -> io::Result<()> {
    // --snip--
    Ok(())
}
}

```

## 使用 as 提供新的名称

```rust
#![allow(unused_variables)]
fn main() {
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
    Ok(())
}

fn function2() -> IoResult<()> {
    // --snip--
    Ok(())
}
}

```

## 使用 pub use 导出新的路径

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
fn main() {}

```

通过 `pub use`，现在可以通过新路径 `hosting::add_to_waitlist` 来调用 `add_to_waitlist` 函数。如果没有指定 `pub use`，`eat_at_restaurant` 函数可以在其作用域中调用 `hosting::add_to_waitlist`，但外部代码则不允许使用这个新路径。

当使用 `use` 关键字将名称导入作用域时，在新作用域中可用的名称是私有的。如果为了让调用你编写的代码的代码能够像在自己的作用域内引用这些类型，可以结合 `pub` 和 `use`。这个技术被称为 “_重导出_（_re-exporting_）”，因为这样做将项引入作用域并同时使其可供其他代码引入自己的作用域。

使用 `pub use`，我们可以使用一种结构编写代码，却将不同的结构形式暴露出来

## 使用外部包

标准库（`std`）对于你的包来说也是外部 crate。因为标准库随 Rust 语言一同分发，无需修改 _Cargo.toml_ 来引入 `std`，不过需要通过 `use` 将标准库中定义的项引入项目包的作用域中来引用它们，

### 嵌套路径

```rust
#![allow(unused_variables)]
fn main() {
use std::cmp::Ordering;
use std::io;
// ---snip---
}

// 花括号嵌套
#![allow(unused_variables)]
fn main() {
use std::{cmp::Ordering, io};
// ---snip---
}

```

```rust
#![allow(unused_variables)]
fn main() {
use std::io;
use std::io::Write;
}

// 使用self来代指自己
#![allow(unused_variables)]
fn main() {
use std::io::{self, Write};
}

```

### glob 运算符 \*

```rust
// * 表示全部
#![allow(unused_variables)]
fn main() {
use std::collections::*;
}

```

## 模块分割

```rust
mod front_of_house; // 在 mod front_of_house 后使用分号，而不是代码块，这将告诉 Rust 在另一个与模块同名的文件中加载模块的内容。

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}

```

# 集合（数据结构）

大部分其他数据类型都代表一个特定的值，不过集合可以包含多个值。不同于内建的数组和元组类型，这些集合指向的数据是储存在堆上的，这意味着数据的数量不必在编译时就已知，并且还可以随着程序的运行增长或缩小。

- _vector_ 允许我们一个挨着一个地储存一系列数量可变的值
- **字符串**（_string_）是一个字符的集合。我们之前见过 `String` 类型，不过在本章我们将深入了解。
- **哈希 map**（_hash map_）允许我们将值与一个特定的键（key）相关联。这是一个叫做 _map_ 的更通用的数据结构的特定实现。

## vector 存储一系列的值

`Vec<T>`，也被称为 _vector_。vector 允许我们在一个单独的数据结构中储存多于一个的值，它在内存中彼此相邻地排列所有的值。

> vector 只能储存相同类型的值

### 新建 vector

```rust
#![allow(unused_variables)]
fn main() {
let v: Vec<i32> = Vec::new();  //调用new函数
}

#![allow(unused_variables)]
fn main() {
let v = vec![1, 2, 3];  // vec! 宏来创建vector
}

```

### 添加数据

```rust
#![allow(unused_variables)]
fn main() {
let mut v = Vec::new();
// 用 push() 来添加数据
v.push(5);
v.push(6);
v.push(7);
v.push(8);
}

```

> 类似于任何其他的 `struct`，vector 在其离开作用域时会被释放
>
> 当 vector 被丢弃时，所有其内容也会被丢弃，这意味着这里它包含的数据将被清理。

### 读取 vector 中的数据

```rust
#![allow(unused_variables)]
fn main() {
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];  // 使用 & 和 [] 返回一个引用；
println!("The third element is {}", third);

match v.get(2) {          //使用 get 方法以索引作为参数来返回一个 Option<&T>。
    Some(third) => println!("The third element is {}", third),
    None => println!("There is no third element."),
}
}

```

> 引用方法获取数据时访问不存在的索引时会触发 panic 报错
>
> get 方法获取不存在的索引时会返回 None 可以继续进行处理

### 遍历 vector

```rust
#![allow(unused_variables)]
fn main() {
let v = vec![100, 32, 57];
for i in &v {					// 获取不可变引用并打印
    println!("{}", i);
}
}

```

```rust
#![allow(unused_variables)]
fn main() {
let mut v = vec![100, 32, 57];
for i in &mut v {    // 获取可变引用并改变值
    *i += 50;				// * 为解引用运算符
}
}

```

### 使用 枚举 enum 来使 vector 存储不同类型 的值

枚举的成员都被定义为相同的枚举类型，所以当需要在 vector 中储存不同类型值时，我们可以定义并使用一个枚举

```rust
#![allow(unused_variables)]
fn main() {
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
}

```

## String

字符串就是作为字节的集合外加一些方法实现的，

称作 `String` 的类型是由标准库提供的，而没有写进核心语言部分，它是可增长的、可变的、有所有权的、UTF-8 编码的字符串类型。

当 Rustacean 们谈到 Rust 的 “字符串”时，它们通常指的是 `String` 和字符串 slice `&str` 类型，而不仅仅是其中之一。\

`String` 和字符串 slice 都是 UTF-8 编码的。

### 新建 String

```rust
#![allow(unused_variables)]
fn main() {
let mut s = String::new(); //通过 new 来创建 String
}

```

```rust
#![allow(unused_variables)]
fn main() {
let data = "initial contents";

let s = data.to_string();  // 将字符串字面量（str slice）变为String

// 该方法也可直接用于字符串字面值：
let s = "initial contents".to_string();
}

```

### 更新字符串

#### push_str

```rust
#![allow(unused_variables)]
fn main() {
let mut s1 = String::from("foo");
let s2 = "bar";
s1.push_str(s2); // 采用 字符串slice 不会夺取所有权
println!("s2 is {}", s2);
}

```

#### push

```rust
#![allow(unused_variables)]
fn main() {
let mut s = String::from("lo");
s.push('l'); // push 方法接受一个字符 ，而不是一个字符串
}

```

#### +

```rust
#![allow(unused_variables)]
fn main() {
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // 注意 s1 被移动了，不能继续使用
}

```

#### format!

```rust
#![allow(unused_variables)]
fn main() {
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3);
}

```

### 索引字符串

Rust 的字符串不支持索引。

`String` 是一个 `Vec<u8>` 的封装。

索引操作预期总是需要常数时间 (O(1))。但是对于 `String` 不可能保证这样的性能，因为 Rust 必须从开头到索引位置遍历来确定有多少有效的字符。

```rust
#![allow(unused_variables)]
fn main() {
let hello = "Здравствуйте";

let s = &hello[0..4]; // 通过引用和range来获取字符串slice
}

```

### 遍历字符串

#### Chars()

> 如果你需要操作单独的 Unicode 标量值，最好的选择是使用 `chars` 方法。对 “नमस्ते” 调用 `chars` 方法会将其分开并返回六个 `char` 类型的值，接着就可以遍历其结果来访问每一个元素了：

```rust
#![allow(unused_variables)]
fn main() {
for c in "नमस्ते".chars() {
    println!("{}", c);
}
}

न
म
स
्
त
े

```

#### Bytes()

`bytes` 方法返回每一个原始字节，

```rust
#![allow(unused_variables)]
fn main() {
for b in "नमस्ते".bytes() {
    println!("{}", b);
}
}


224
164
// --snip--
165
135

```

> 注意 ⚠️：有效的 Unicode 标量值可能会由不止一个字节组成。

## hash map

> `HashMap<K, V>` 类型储存了一个键类型 `K` 对应一个值类型 `V` 的映射。它通过一个 **哈希函数**（_hashing function_）来实现映射，决定如何将键和值放入内存中。

### 新建 hash map

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
}

```

哈希 map 是同质的：所有的键必须是相同类型，值也必须都是相同类型

另一种创建 hash map 的方法

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let teams  = vec![String::from("Blue"), String::from("Yellow")];
let initial_scores = vec![10, 50];

let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect(); // _ 可以用来做范型的占位符
}

```

### hash map 的所有权

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
// 这里 field_name 和 field_value 不再有效，
// 尝试使用它们看看会出现什么编译错误！
}

```

对于像 `i32` 这样的实现了 `Copy` trait 的类型，其值可以拷贝进哈希 map。对于像 `String` 这样拥有所有权的值，其值将被移动而哈希 map 会成为这些值的所有者

当 `insert` 调用将 `field_name` 和 `field_value` 移动到哈希 map 中后，将不能使用这两个绑定。

如果将值的引用插入哈希 map，这些值本身将不会被移动进哈希 map。但是这些引用指向的值必须至少在哈希 map 有效时也是有效的。

### 获取 hash map 中的值

get 方法

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
let score = scores.get(&team_name); // get 方法返回一个 option，可以做后续处理
}

```

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

for (key, value) in &scores { // 遍历 hash map 中的键值对
    println!("{}: {}", key, value); // 这会以任意顺序打印出每一个键值对：
}
}

```

### 更新 hash map

#### 覆盖原来的值

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25); // 会覆盖原来的值

println!("{:?}", scores);
}

```

#### 原来无值则添加，有值则不变

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

scores.entry(String::from("Yellow")).or_insert(50); // 向其中添加 "Yellow": 50 并返回修改过的 scores
scores.entry(String::from("Blue")).or_insert(50); // bule 已存在，所以不变，并返回 scores

println!("{:?}", scores);
}

```

`Entry` 的 `or_insert` 方法在键对应的值存在时就返回这个值的 `Entry`，

如果不存在则将参数作为新值插入并返回修改过的 `Entry`。

#### 根据原来的值赋新值

```rust
#![allow(unused_variables)]
fn main() {
use std::collections::HashMap;

let text = "hello world wonderful world";

let mut map = HashMap::new();

for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0);
    *count += 1;
}

println!("{:?}", map);
}

```

这会打印出 `{"world": 2, "hello": 1, "wonderful": 1}`，`or_insert` 方法事实上会返回这个键的值的一个可变引用（`&mut V`）。这里我们将这个可变引用储存在 `count` 变量中，所以为了赋值必须首先使用星号（`*`）解引用 `count`。这个可变引用在 `for` 循环的结尾离开作用域，这样所有这些改变都是安全的并符合借用规则。

# 错误处理



## Panic! 介绍

Rust 将错误组合成两个主要类别：**可恢复错误**（*recoverable*）和 **不可恢复错误**（*unrecoverable*）。

可恢复错误通常代表向用户报告错误和重试操作是合理的情况，比如未找到文件。

不可恢复错误通常是 bug 的同义词，比如尝试访问超过数组结尾的位置。

大部分语言并不区分这两类错误，并采用类似异常这样方式统一处理他们。

Rust 并没有异常，但是，有可恢复错误 `Result<T, E>` ，和不可恢复(遇到错误时停止程序执行)错误 `panic!`。

Rust 有 `panic!`宏。当执行这个宏时，程序会打印出一个错误信息，展开并清理栈数据，然后接着退出。



**对应 panic 时的栈展开或终止**

当出现 panic 时，程序默认会开始 **展开**（*unwinding*），这意味着 Rust 会回溯栈并清理它遇到的每一个函数的数据，不过这个回溯并清理的过程有很多工作。

另一种选择是直接 **终止**（*abort*），这会不清理数据就退出程序。那么程序所使用的内存需要由操作系统来清理。

如果你需要项目的最终二进制文件越小越好，panic 时通过在 *Cargo.toml* 的 `[profile]` 部分增加 `panic = 'abort'`，可以由展开切换为终止。例如，如果你想要在release模式中 panic 时直接终止：

```toml
[profile.release]
panic = 'abort'
```



*acktrace* 是一个执行到目前位置所有被调用的函数的列表。Rust 的 backtrace 跟其他语言中的一样：阅读 backtrace 的关键是从头开始读直到发现你编写的文件。这就是问题的发源地。这一行往上是你的代码所调用的代码；往下则是调用你的代码的代码。



为了获取带有这些信息的 backtrace，必须启用 debug 标识。当不使用 `--release` 参数运行 cargo build 或 cargo run 时 debug 标识会默认启用，



## Result 与可恢复错误

```rust
// Result 枚举
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt"); // 返回 result 枚举

    let f = match f {  // 利用 match 来处理返回的 result 结果
        Ok(file) => file,
        Err(error) => {
            panic!("Problem opening the file: {:?}", error)
        },
    };
}

```

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() { // 用 match 来匹配不同的错误种类
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => panic!("Problem opening the file: {:?}", other_error),
        },
    };
}

// 上面代码更好的写法，利用了闭包等特性
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}


```



### Panic！ 的简写

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").unwrap(); // unwarp() 前面的操作没错则返回结果，有错则报错
}


use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt"); //expect() 与 unwarp() 基本一致，只是可以传入我们想显示的错误信息
}

```



### 传播错误

当编写一个其实现会调用一些可能会失败的操作的函数时，除了在这个函数中处理错误外，还可以选择让调用者知道这个错误并决定该如何处理。这被称为 **传播**（*propagating*）错误，这样能更好的控制代码调用，因为比起你代码所拥有的上下文，调用者可能拥有更多信息或逻辑来决定应该如何处理错误。





```rust

#![allow(unused_variables)]
fn main() {
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt"); // 返回一个Result

    let mut f = match f { // 对 Result 做 match 处理
        Ok(file) => file,
        Err(e) => return Err(e), 
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) { //同样返回一个 Result
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
}

```



 Rust 提供了 `?` 问号运算符来使错误传播更容易。

`Result` 值之后的 `?` 被定义为与示例 9-6 中定义的处理 `Result` 值的 `match` 表达式有着完全相同的工作方式。

如果 `Result` 的值是 `Ok`，这个表达式将会返回 `Ok` 中的值而程序将继续执行。

如果值是 `Err`，`Err` 中的值将作为整个函数的返回值，就好像使用了 `return` 关键字一样，这样错误值就被传播给了调用者。

示例 9-6 中的 `match` 表达式与问号运算符所做的有一点不同：

`?` 运算符所使用的错误值被传递给了 `from` 函数，它定义于标准库的 `From` trait 中，其用来将错误从一种类型转换为另一种类型。

当 `?` 运算符调用 `from` 函数时，收到的错误类型被转换为定义为当前函数返回的错误类型。

这在当一个函数返回一个错误类型来代表所有可能失败的方式时很有用，即使其可能会因很多种原因失败。

只要每一个错误类型都实现了 `from` 函数来定义如将其转换为返回的错误类型，`?` 运算符会自动处理这些转换。

```rust

#![allow(unused_variables)]
fn main() {
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?; // ? 后可以跟 . 链式调用

    Ok(s)
}
}

```





只能在返回 `Result` 或者其它实现了 `std::ops::Try` 的类型的函数中使用 `?` 运算符。

当你期望在不返回 `Result` 的函数中调用其他返回 `Result` 的函数时使用 `?` 的话，有两种方法修复这个问题。

- 一种技巧是将函数返回值类型修改为 `Result<T, E>`，如果没有其它限制阻止你这么做的话。

- 另一种技巧是通过合适的方法使用 `match` 或 `Result` 的方法之一来处理 `Result<T, E>`。



`main` 函数是特殊的，其必须返回什么类型是有限制的。

`main` 函数的一个有效的返回值是 `()`，同时出于方便，另一个有效的返回值是 `Result<T, E>`，













# 范型、trait 与生命周期

