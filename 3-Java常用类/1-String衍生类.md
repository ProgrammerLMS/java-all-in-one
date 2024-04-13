# String衍生类

## StringBuffer

`StringBuffer`类是Java早期提供用于处理字符串的一个类，比`String`类更高效，特别是对字符串进行连接操作时，使用`StringBuffer`类可以大大提高程序的执行效率。

`StringBuffer`是线程安全的，因为它的源码中的方法大多数都被`synchronized`修饰了。（关于多线程的内容会在后面提到）

### 使用+来拼接字符串的问题

考察下面的循环代码：

```java
String s = "";
for (int i = 0; i < 1000; i++) {
    s = s + "," + i;
}
```

虽然可以直接拼接字符串，但是，在循环中，每次循环都会创建新的字符串对象，然后扔掉旧的字符串。这样，绝大部分字符串都是临时对象，不但浪费内存，还会影响垃圾回收效率。

### 常用方法

- append 追加
- delete 删除
- insert 插入
- reverse 反转

```java
public class Main {
    public static void main(String[] args) {
        String str1 = "let there "; 
        StringBuffer sb = new StringBuffer(str1); //根据str1创建一个StringBuffer对象
        sb.append("be light"); //在最后追加
        System.out.println(sb);
        sb.delete(4, 10);//删除-10之间的字符
        System.out.println(sb);
        sb.insert(4, "there ");//在4这个位置插入 there
        System.out.println(sb);
        sb.reverse(); //反转
        System.out.println(sb);
    }
}
```

### 为什么StringBuffer可以实现字符串变长？

和String**内部是一个字符数组**一样，`StringBuffer`也维护了一个字符数组。 但是，这个字符数组，**留有冗余长度**。

比如说`new StringBuffer("the")`，其内部的字符数组的长度，是19，而不是3，这样调用插入和追加，在现成的数组的基础上就可以完成了。

如果追加的长度超过了19，就会分配一个新的数组，长度比原来多一些，把原来的数据复制到新的数组中，**看上去**数组长度就变长了。如果进入源码可以发现下面两个属性变量，分别代表了当前容纳字符串的长度和分配的总空间大小。

- length: “the”的长度 3
- capacity: 分配的总空间 19

```java
public class Main {  
    public static void main(String[] args) {
        String str1 = "the"; 
        StringBuffer sb = new StringBuffer(str1);
        System.out.println(sb.length()); //内容长度
        System.out.println(sb.capacity());//总空间
    }
  
}
```

注意：关于19这个数字，在不同版本的JDK中是不一样的，无需特别记忆

## StringBuilder

### 链式操作

为了能高效拼接字符串，Java标准库还提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象：

```java
StringBuilder sb = new StringBuilder(1024);
for (int i = 0; i < 1000; i++) {
    sb.append(',');
    sb.append(i);
}
String s = sb.toString();
```

`StringBuilder`还可以进行链式操作：

```java
// 链式操作
public class Main {
    public static void main(String[] args) {
        var sb = new StringBuilder(1024);
        sb.append("Mr ")
          .append("Bob")
          .append("!")
          .insert(0, "Hello, ");
        System.out.println(sb.toString());
    }
}
```

如果我们查看`StringBuilder`的源码，可以发现，进行链式操作的关键是，定义的`append()`方法会返回`this`，这样，就可以不断调用自身的其他方法。

注意：对于普通的字符串`+`操作，并不需要我们将其改写为`StringBuilder`，因为Java编译器在编译时就自动把多个连续的`+`操作编码为`StringConcatFactory`的操作。在运行期，`StringConcatFactory`会自动把字符串连接操作优化为数组复制或者`StringBuilder`操作。

`StringBuffer`实际上可以理解为Java早期的一个`StringBuilder`的线程安全版本，它通过同步来保证多个线程操作`StringBuffer`也是安全的，但是同步会带来执行速度的下降。

`StringBuilder`和`StringBuffer`接口完全相同，如果不是多线程场景，基本没有必要使用`StringBuffer`。

### 小结

`StringBuilder`是可变对象，用来高效拼接字符串；

`StringBuilder`可以支持链式操作，实现链式操作的关键是返回实例本身；

`StringBuffer`是`StringBuilder`的线程安全版本，现在很少使用。

## StringJoiner

要高效拼接字符串，应该使用`StringBuilder`。

很多时候，我们拼接的字符串像这样：

```java
public class Main {
    public static void main(String[] args) {
        String[] names = {"Bob", "Alice", "Grace"};
        var sb = new StringBuilder();
        sb.append("Hello ");
        for (String name : names) {
            sb.append(name).append(", ");
        }
        // 注意去掉最后的", ":
        sb.delete(sb.length() - 2, sb.length());
        sb.append("!");
        System.out.println(sb.toString());
    }
}
```

### 分隔符指定

类似用分隔符拼接数组的需求很常见，所以Java标准库还提供了一个`StringJoiner`来干这个事：

```java
public class Main {
    public static void main(String[] args) {
        String[] names = {"Bob", "Alice", "Grace"};
        var sj = new StringJoiner(", ");
        for (String name : names) {
            sj.add(name);
        }
        System.out.println(sj.toString());
    }
}
```

不过，用`StringJoiner`的结果少了前面的`"Hello "`和结尾的`"!"`。遇到这种情况，需要给`StringJoiner`指定“开头”和“结尾”：

```java
public class Main {
    public static void main(String[] args) {
        String[] names = {"Bob", "Alice", "Grace"};
        var sj = new StringJoiner(", ", "Hello ", "!");
        for (String name : names) {
            sj.add(name);
        }
        System.out.println(sj.toString());
    }
}
```

`String`还提供了一个静态方法`join()`，这个方法在内部使用了`StringJoiner`来拼接字符串，在不需要指定“开头”和“结尾”的时候，用`String.join()`更方便：

```java
String[] names = {"Bob", "Alice", "Grace"};
var s = String.join(", ", names);
```

### 小结

用指定分隔符拼接字符串数组时，使用`StringJoiner`或者`String.join()`更方便；

用`StringJoiner`拼接字符串时，还可以额外附加一个“开头”和“结尾”。

