在 Rust 编程语言中，宏（Macros）是一种强大的工具，用于元编程（metaprogramming），可以生成代码。Rust 提供了声明式宏（Declarative Macros，使用 `macro_rules!` 定义）和过程宏（Procedural Macros，包括自定义派生、属性宏和函数宏）。在声明式宏中，模式匹配是核心机制，而模式标记（pattern tokens）用于指定匹配规则。以下是常见的模式标记及其解释：

### 1. **`$ident`**
- **含义**: 匹配一个标识符（identifier），例如变量名、函数名或类型名。
- **解释**: 用于捕获 Rust 中的合法标识符（如 `x`、`foo`、`MyStruct`）。
- **示例**:
  ```rust
  macro_rules! example {
      ($x:ident) => {
          fn $x() { println!("Hello"); }
      };
  }
  example!(test); // 生成 fn test() { println!("Hello"); }
  ```

### 2. **`$expr`**
- **含义**: 匹配一个表达式（expression）。
- **解释**: 可以捕获任何有效的 Rust 表达式，例如 `1 + 2`、`foo()` 或 `x`。
- **示例**:
  ```rust
  macro_rules! example {
      ($e:expr) => {
          println!("{}", $e);
      };
  }
  example!(1 + 2); // 打印 3
  ```

### 3. **`$ty`**
- **含义**: 匹配一个类型（type）。
- **解释**: 用于捕获 Rust 中的类型，如 `i32`、`String`、`Vec<T>` 等。
- **示例**:
  ```rust
  macro_rules! example {
      ($t:ty) => {
          let x: $t = Default::default();
      };
  }
  example!(i32); // 生成 let x: i32 = Default::default();
  ```

### 4. **`$block`**
- **含义**: 匹配一个代码块（block）。
- **解释**: 捕获由 `{}` 包裹的语句序列，例如 `{ let x = 1; x + 2 }`。
- **示例**:
  ```rust
  macro_rules! example {
      ($b:block) => {
          $b
      };
  }
  example!({ println!("Hi"); }); // 执行 println!("Hi");
  ```

### 5. **`$stmt`**
- **含义**: 匹配一个语句（statement）。
- **解释**: 捕获单个语句，例如 `let x = 1;` 或 `foo();`，但不包括代码块。
- **示例**:
  ```rust
  macro_rules! example {
      ($s:stmt) => {
          $s
      };
  }
  example!(let x = 5;); // 生成 let x = 5;
  ```

### 6. **`$pat`**
- **含义**: 匹配一个模式（pattern）。
- **解释**: 用于捕获模式匹配中的模式，例如 `Some(x)`、`1..=5` 或 `_`。
- **示例**:
  ```rust
  macro_rules! example {
      ($p:pat) => {
          match 42 {
              $p => println!("Matched"),
              _ => println!("No match"),
          }
      };
  }
  example!(40..=50); // 匹配 42，打印 "Matched"
  ```

### 7. **`$literal`**
- **含义**: 匹配一个字面量（literal）。
- **解释**: 捕获字面值，如 `42`、`3.14`、`"hello"` 或 `true`。
- **示例**:
  ```rust
  macro_rules! example {
      ($l:literal) => {
          println!("{}", $l);
      };
  }
  example!("hello"); // 打印 "hello"
  ```

### 8. **`$item`**
- **含义**: 匹配一个项（item）。
- **解释**: 捕获 Rust 中的顶级定义，如函数（`fn`）、结构（`struct`）、模块（`mod`）等。
- **示例**:
  ```rust
  macro_rules! example {
      ($i:item) => {
          $i
      };
  }
  example!(fn foo() { println!("Foo"); }); // 生成函数 foo
  ```

### 9. **`$meta`**
- **含义**: 匹配一个属性元信息（attribute metadata）。
- **解释**: 用于捕获属性中的内容，例如 `#[derive(Debug)]` 中的 `Debug`。
- **示例**:
  ```rust
  macro_rules! example {
      (#[$m:meta]) => {
          println!(stringify!(#$m));
      };
  }
  example!(#[test]); // 打印 "#[test]"
  ```

### 10. **`$tt` (Token Tree)**
- **含义**: 匹配单个令牌树（token tree）。
- **解释**: 捕获任意单个 Rust 令牌，可以是标识符、操作符、分隔符（如 `()`、`[]`、`{}`）等，非常灵活。
- **示例**:
  ```rust
  macro_rules! example {
      ($t:tt) => {
          println!(stringify!($t));
      };
  }
  example!(+); // 打印 "+"
  ```

### 11. **重复匹配标记**
- **语法**: `$($var:token)*` 或 `$($var:token),*`
- **含义**: 用于匹配零个或多个重复的模式。
  - `*` 表示零个或多个。
  - `+` 表示一个或多个。
  - `,` 表示用逗号分隔的序列。
- **解释**: 常用于处理可变数量的参数。
- **示例**:
  ```rust
  macro_rules! example {
      ($($x:expr),*) => {
          $(println!("{}", $x);)*
      };
  }
  example!(1, 2, 3); // 依次打印 1, 2, 3
  ```

### 总结
这些模式标记是 `macro_rules!` 的核心组成部分，允许开发者灵活地匹配和生成代码。以下是简要对照表：

| 标记       | 匹配内容          | 示例                 |
|------------|-------------------|----------------------|
| `$ident`   | 标识符           | `x`, `foo`          |
| `$expr`    | 表达式           | `1 + 2`, `foo()`    |
| `$ty`      | 类型             | `i32`, `Vec<T>`     |
| `$block`   | 代码块           | `{ let x = 1; }`    |
| `$stmt`    | 语句             | `let x = 1;`        |
| `$pat`     | 模式             | `Some(x)`, `_`      |
| `$literal` | 字面量           | `"hello"`, `42`     |
| `$item`    | 项               | `fn foo() {}`       |
| `$meta`    | 属性元信息       | `test` in `#[test]` |
| `$tt`      | 令牌树           | `+`, `()`, `foo`    |

