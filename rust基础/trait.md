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

#### trait多态性

在Rust中可以通过trait类型实现的特殊形式实现真正的多态性。

##### 静态分发

当在编译期决定要调用的方法时，被称为静态分发或早期绑定。在Rust中，泛型展示了这种形式的分发，因为即使泛型函数可以接收许多参数，也会在编译期使用具体类型生成函数的专用副本。

##### 动态分发

在面向对象编程中，有时直到运行时才能确定调用的方法，这是因为具体类型被隐藏了，并且只有接口方法可用于调用该类型。

在Rust中通过`＆dyn trait`来实现，比如

```rust
pub trait Area {
    fn area(&self) -> f32;
}
struct Square(f32);
struct  Rectangle(f32,f32);
impl Area for Square {
    fn area(&self) -> f32{
        self.0 * self.0
    }
}
impl Area for Rectangle {
    fn area(&self) -> f32 {
        self.0 * self.1
    }
}
fn main() {
    let square = Square(2.3);
    let rectange = Rectangle(2.3,3.1);
    let v: Vec<&dyn Area> = vec![&square, &rectange];
}

```

通过`dyn`关键字声明Vec接收动态Area类型，但是由Rust编译器要求声明的对象类型大小不能是未知的，所以这里只能接收`&dyn Area`，或者使用其它智能指针类型，如：Box, Rc, Arc等。

另外也可以把dyn trait作为函数参数使用的形式，如下：

```rust
fn show_me(item: &dyn Display) {
    println!("{}", item);
}
```

当然也需要是`&dyn trait`的形式。

