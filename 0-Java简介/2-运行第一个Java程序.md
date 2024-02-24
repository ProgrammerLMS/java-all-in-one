# 运行第一个Java程序

## 1.编写代码

新建一个txt文件，打开文本编辑器，写入以下代码：

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

在一个Java程序中，总能找到一个类似：

```
public class Hello {
    ...
}
```

的定义，这个定义被称为`class`（类），这里的类名是`Hello`，大小写敏感，`class`用来定义一个类，`public`表示这个类是公开的，`public`、`class`都是Java的关键字，必须小写，`Hello`是类的名字，按照习惯，首字母`H`要大写。而花括号`{}`中间则是类的定义。

注意到类的定义中，定义了一个名为`main`的方法：

```java
public static void main(String[] args) {
...
}
```

方法是可执行的代码块，一个方法除了方法名`main`，还有用`()`括起来的方法参数，这里的`main`方法有一个参数，参数类型是`String[]`（字符串数组），参数名是`args`，`public`、`static`用来修饰方法，这里表示它是一个公开的静态方法，`void`是方法的返回类型，而花括号`{}`中间的就是方法的代码。

方法的代码每一行用`;`结束，这里只有一行代码，就是：

```java
System.out.println("Hello, world!");
```

它用来打印一个字符串到屏幕上。

Java规定，某个类定义的`public static void main(String[] args)`是Java程序的固定入口方法，因此，Java程序总是从`main`方法开始执行。

最后，当我们把代码保存为文件时，文件名必须是`Hello.java`，其中后缀需要从txt改为java，而且文件名要和我们定义的类名`Hello`完全保持一致。

## 2.运行Java文件

Java源码本质上是一个文本文件，我们需要先用`javac`把`Hello.java`编译成字节码文件`Hello.class`，然后，用`java`命令执行这个字节码文件：

```a
                    ┌──────────────────┐
                    │    Hello.java    │◀── source code
                    └──────────────────┘
                              │ compile
                              ▼
                    ┌──────────────────┐
                    │   Hello.class    │◀── byte code
                    └──────────────────┘
                              │ execute
                              ▼
                    ┌──────────────────┐
                    │    Run on JVM    │
                    └──────────────────┘
```

其中，可执行文件`javac.exe`是编译器，而可执行文件`java.exe`就是虚拟机。

- 第一步，在保存`Hello.java`的目录下执行CMD命令`javac Hello.java`

  ```
  javac Hello.java
  ```

- 如果源代码无误，上述命令不会有任何输出，而当前目录下会产生一个`Hello.class`文件

- 第二步，执行`Hello.class`，使用命令`java Hello`。

  ```
  java Hello
  ```

  注意：给虚拟机传递的参数`Hello`是我们定义的类名，虚拟机自动查找对应的class文件并执行。

  在使用`java`命令运行Java程序时，不需要包括文件扩展名（如`.class`）。当你输入`java hello`时，Java运行时会自动查找名为`hello`的类，并尝试加载它。

需要注意的是，在实际项目中，单个不依赖第三方库的Java源码是非常罕见的，所以，绝大多数情况下，我们无法直接运行一个Java源码文件，原因是它需要依赖其他的库。