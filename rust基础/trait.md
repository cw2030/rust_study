### trait
#### trait的多种形式
##### 标记trait
在std::marker模块中定义的特征被称为标记特征(marker trait)。这种特征不包含任何方法，声明时只是提供特征名称和空的函数体。
如：Copy、 Send 、 Sync、Debug等。因为它们用于简单地将类型标记为属于特定的组群，以获得一定程度的编译保障。

##### 简单trait
就是我们通常所定义的trait，是对一类对象行为的抽象，如：

```rust
trait Foo {
    fn foo() ;
}
```

##### 泛型trait

trait也可是泛型，这样用户希望为多种类型实现trait的情况下非常有用，如：

```rust
pub trait From<T> {
    fn from(T);
}
```

另外泛型trait在实现的时候，可以指定具体的类型，如

```rust
struct MyContainer(u32);

impl From<String> for MyContainer {
    fn from(item: String) {
        println!("{}", item);
    }
}
```

##### 关联类型trait

例：

```rust
trait Foo {
    type Out;
    fn get_value(self) -> Self::Out;
}
```

上述代码中trait Foo声明的Out类型，它具有较少的类型签名，其优点在于，在实际编程中，它们允许用户一次性声明关联类型，并在任何trait方法或函数中使用Self::Out作为返回类型或参数。如下：

```rust
trait Foo2 {
    type T;
    fn println_t(item: Self::T) -> Self::T;
}

struct MyContainer(String);

impl Foo2 for MyContainer {
    type T = u32;

    fn println_t(item: u32) -> u32 {
        item +1
    }
}
```

##### 继承trait

类似于java语言中的接口继承，但在rust中，本质是组合关系，具体trait实现要分别实现所组合的triat，如下:

```rust

trait Foo {
    fn go_foo();
}

trait Bar: Foo {
    fn go_bar();
}
#[derive(Debug)]
struct MyStruct(u32,String);

impl Bar for MyStruct {
    fn go_bar() {
        println!("go bar");
    }
}
// 注意这里必须要单独实现Foo trait
// 不能因为Bar声明继承了Foo，就在impl Bar里面直接实现Foo的方法
impl Foo for MyStruct {
    fn go_foo() {
        println!("go foo");
    }
}

fn main() {
    let my = MyStruct(2,String::from("hello"));
    println!("{:#?}", &my);
}
```

