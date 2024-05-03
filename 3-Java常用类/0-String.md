# String

## 1 创建字符串

在Java中，`String`是一个引用类型，它本身也是一个`class`。但是，Java编译器对`String`有特殊处理，即可以直接用`"..."`的字面值来表示一个字符串，如：

```java
String s1 = "Hello!";
```

实际上字符串在`String`内部是通过一个`char[]`数组表示的，因此，按下面的写法也是可以的：

```java
String s2 = new String(new char[] {'H', 'e', 'l', 'l', 'o', '!'});
```

因此，常见创建字符串方法有：

1. 每当有一个**字面值**出现的时候，虚拟机就会创建一个字符串
2. 调用String的**构造方法**创建一个字符串对象（传入字面值或字符数组）
3. 通过+加号进行字符串拼接也会创建新的字符串对象

```java
public class Main {
    public static void main(String[] args) {
        String garen ="盖伦"; //字面值,虚拟机碰到字面值就会创建一个字符串对象 
        String teemo = new String("提莫"); //创建了两个对象，分别位于堆内存和常量池      
        char[] cs = new char[]{'崔','斯','特'};
        String hero = new String(cs);//  通过字符数组创建一个字符串对象
        String hero3 = garen + teemo;//  通过+加号进行字符串拼接
    }
}
```

关于上面代码中，使用构造方法创建了几个对象的问题，可以参考这里：[java中的String a = new String("a")创建的是几个对象? - 知乎](https://www.zhihu.com/question/602293345/answer/3038690988?utm_id=0)

## 2 字符串的不可变性

**不可改变**的具体含义是指：

- 不能增加长度
- 不能减少长度
- 不能插入字符
- 不能删除字符
- 不能修改字符

一旦创建好这个字符串，里面的内容 **永远** 不能改变，String 的表现就像是一个**常量**

Java字符串的一个重要特点就是字符串*不可变*。这种不可变性是通过内部的`private final char[]`字段，以及没有任何修改`char[]`的方法实现的。（因为String类被修饰为`final`，所以是**不能被继承**的）

我们来看一个例子：

```java
public class Main {
    public static void main(String[] args) {
        String s = "hello";
        System.out.println(s); // 显示 hello
        s = "world";
        System.out.println(s); // 显示 world
    }
}
```

观察执行结果，难道字符串`s`变了吗？其实变的不是字符串，而是变量`s`的“指向”。

执行`String s = "hello";`时，JVM虚拟机先创建字符串`"hello"`，然后，把字符串变量`s`指向它：

紧接着，执行`s = "world";`时，JVM虚拟机先创建字符串`"world"`，然后，把字符串变量`s`指向它：

原来的字符串`"hello"`还在，只是我们无法通过变量`s`访问它而已。因此，字符串的不可变是指字符串内容不可变。至于变量，可以一会指向字符串`"hello"`，一会指向字符串`"world"`。

## 3 对字符串的常用操作

注意所有操作都是返回一个新的字符串对象：

- 字符串操作不改变原字符串内容，而是返回新字符串；
- 常用的字符串操作：提取子串、查找、替换、大小写转换等；

### 类型转换

要把任意基本类型或引用类型转换为字符串，可以使用静态方法`valueOf()`。这是一个重载方法，编译器会根据参数自动选择合适的方法：

```java
String.valueOf(123); // "123"
String.valueOf(45.67); // "45.67"
String.valueOf(true); // "true"
String.valueOf(new Object()); // 类似java.lang.Object@636be97c
```

要把字符串转换为其他类型，就需要根据情况。例如，把字符串转换为`int`类型：

```java
int n1 = Integer.parseInt("123"); // 123
int n2 = Integer.parseInt("ff", 16); // 按十六进制转换，255
```

把字符串转换为`boolean`类型：

```java
boolean b1 = Boolean.parseBoolean("true"); // true
boolean b2 = Boolean.parseBoolean("FALSE"); // false
```

#### 数字转字符串

```java
public class Main {
    public static void main(String[] args) {
        int i = 5;  
        //方法1
        String str = String.valueOf(i);
        //方法2
        Integer it = i;
        String str2 = it.toString();
        //方法3
        String str3 = "" + i;
    }
}
```

#### 字符串转数字

```java
public class Main {
    public static void main(String[] args) {
        String str = "999";
        int i= Integer.parseInt(str);
        System.out.println(i);
    }
}
```

#### 与char[]数组的转换

`String`和`char[]`类型可以互相转换，方法是：

```java
char[] cs = "Hello".toCharArray(); // String -> char[]
String s = new String(cs); // char[] -> String
```

如果修改了`char[]`数组，`String`并不会改变：

```java
public class Main {
    public static void main(String[] args) {
        char[] cs = "Hello".toCharArray();
        String s = new String(cs);
        System.out.println(s);
        cs[0] = 'X';
        System.out.println(s);
    }
}
```

这是因为通过`new String(char[])`创建新的`String`实例时，它并不会直接引用传入的`char[]`数组，而是会复制一份，所以，修改外部的`char[]`数组不会影响`String`实例内部的`char[]`数组，因为这是两个不同的数组。

从`String`的不变性设计可以看出，如果传入的对象有可能改变，我们需要复制而不是直接引用。

### 定位子串与提取字串

`String`类提供了多种方法来搜索子串、提取子串。常用的方法有：

```java
// 是否包含子串:
"Hello".contains("ll"); // true
```

注意到`contains()`方法的参数是`CharSequence`而不是`String`，因为`CharSequence`是`String`实现的一个接口。

搜索子串的更多的例子如下：

```java
"Hello".indexOf("l"); // 2
"Hello".lastIndexOf("l"); // 3
"Hello".startsWith("He"); // true
"Hello".endsWith("lo"); // true
```

提取子串的例子：

```java
"Hello".substring(2); // "llo"
"Hello".substring(2, 4); "ll"
```

注意索引号是从`0`开始的。

### 去除首尾空白字符

使用`trim()`方法可以移除字符串首尾空白字符。空白字符包括空格，`\t`，`\r`，`\n`：

```java
"  \tHello\r\n ".trim(); // "Hello"
```

注意：`trim()`并没有改变字符串的内容，而是返回了一个新字符串。

另一个`strip()`方法也可以移除字符串首尾空白字符。它和`trim()`不同的是，类似中文的空格字符`\u3000`也会被移除：

```java
"\u3000Hello\u3000".strip(); // "Hello"
" Hello ".stripLeading(); // "Hello "
" Hello ".stripTrailing(); // " Hello"
```

`String`还提供了`isEmpty()`和`isBlank()`来判断字符串是否为空和空白字符串：

```java
"".isEmpty(); // true，因为字符串长度为0
"  ".isEmpty(); // false，因为字符串长度不为0
"  \n".isBlank(); // true，因为只包含空白字符
" Hello ".isBlank(); // false，因为包含非空白字符
```

### 替换子串

要在字符串中替换子串，有两种方法。一种是根据字符或字符串替换：

```java
String s = "hello";
s.replace('l', 'w'); // "hewwo"，所有字符'l'被替换为'w'
s.replace("ll", "~~"); // "he~~o"，所有子串"ll"被替换为"~~"
s.replaceFirst("l","o");//只替换第一个
```

另一种是通过正则表达式替换：

```java
String s = "A,,B;C ,D";
s.replaceAll("[\\,\\;\\s]+", ","); // "A,B,C,D"
```

上面的代码通过正则表达式，把匹配的子串统一替换为`","`。关于正则表达式的用法我们会在后面详细讲解。

### 分割字符串

要分割字符串，使用`split()`方法，并且传入的也是正则表达式：

```java
String s = "A,B,C,D";
String[] ss = s.split("\\,"); // {"A", "B", "C", "D"}
```

### 拼接字符串

拼接字符串使用静态方法`join()`，它用指定的字符串连接字符串数组：

```java
String[] arr = {"A", "B", "C"};
String s = String.join("***", arr); // "A***B***C"
```

### 大小写转换

主要使用`toLowerCase`和`toUpperCase`方法

```java
public class Main {
    public static void main(String[] args) {
        String sentence = "Garen";
        //全部变成小写
        System.out.println(sentence.toLowerCase());
        //全部变成大写
        System.out.println(sentence.toUpperCase());
    }
}
```

## 4 字符串的格式化输出

如果不使用字符串格式化，就需要进行字符串连接，如果变量比较多，拼接就会显得繁琐，使用**字符串格式化**，就可以**简洁明了**

字符串提供了`formatted()`方法和`format()`静态方法，可以传入其他参数，替换占位符，然后生成新的字符串：

```java
public class Main {
    public static void main(String[] args) {
        String s = "Hi %s, your score is %d!";
        System.out.println(s.formatted("Alice", 80));
        System.out.println(String.format("Hi %s, your score is %.2f!", "Bob", 59.5));
    }
}
```

有几个占位符，后面就传入几个参数。参数类型要和占位符一致。我们经常用这个方法来格式化信息。常用的占位符有：

- `%s`：显示字符串；
- `%d`：显示整数；
- `%x`：显示十六进制整数；
- `%f`：显示浮点数。

占位符还可以带格式，例如`%.2f`表示显示两位小数。如果你不确定用啥占位符，那就始终用`%s`，因为`%s`可以显示任何数据类型。要查看完整的格式化语法，请参考[JDK文档](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/util/Formatter.html#syntax)。

### printf和format

除了使用前面的方法外，还可以直接使用`System.out.printf`和`System.out.format`

```java
public class Main {
    public static void main(String[] args) {
        String name ="盖伦";
        int kill = 8;
        String title="超神";
        String sentenceFormat ="%s 在进行了连续 %d 次击杀后，获得了 %s 的称号%n";
        //使用printf格式化输出
        System.out.printf(sentenceFormat,name,kill,title);
        //使用format格式化输出
        System.out.format(sentenceFormat,name,kill,title);
         
    }
}
```

### 常用的格式化方法

一般包括：总长度，左对齐，补0，千位分隔符，小数点位数，本地化表达

```java
public class Main {
    public static void main(String[] args) {
        int year = 2020;
        //总长度，左对齐，补0，千位分隔符，小数点位数，本地化表达
        //直接打印数字
        System.out.format("%d%n",year);
        //总长度是8,默认右对齐
        System.out.format("%8d%n",year);
        //总长度是8,左对齐
        System.out.format("%-8d%n",year);
        //总长度是8,不够补0
        System.out.format("%08d%n",year);
        //千位分隔符
        System.out.format("%,8d%n",year*10000);
        //小数点位数
        System.out.format("%.2f%n",Math.PI);
        //不同国家的千位分隔符
        System.out.format(Locale.FRANCE,"%,.2f%n",Math.PI*10000);
        System.out.format(Locale.US,"%,.2f%n",Math.PI*10000);
        System.out.format(Locale.UK,"%,.2f%n",Math.PI*10000);
          
    }

```

### 换行符——%n

**换行符**就是另起一行 --- '\n' 换行（newline）

**回车符**就是回到一行的开头 --- '\r' 回车（return）

Java是跨平台的编程语言，同样的代码，可以在不同的平台使用，比如Windows，Linux，Mac然而在不同的操作系统，换行符是不一样的

- 在DOS和Windows中，每行结尾是 “\r\n”；
- Linux系统里，每行结尾只有 “\n”；
- Mac系统里，每行结尾是只有 "\r"。

为了使得同一个java程序的换行符在所有的操作系统中都有一样的表现，使用%n，就可以做到平台无关的换行。

```java
public class Main {
    public static void main(String[] args) {
        System.out.printf("这是换行符%n");
        System.out.printf("这是换行符%n");
    }
}
```

## 5 字符串的比较

当我们想要比较两个字符串是否相同时，要特别注意，我们实际上是想比较字符串的内容是否相同。必须使用`equals()`方法而不能用`==`。

### 是否同一个对象

str1和str2的内容一定是一样的！但是，并不是同一个字符串对象，原理参考前文创建字符串的内容。

```java
public class Main {
    public static void main(String[] args) {
        String str1 = "hello";
        String str2 = new String(str1);
        //==用于判断是否是同一个字符串对象
        System.out.println( str1  ==  str2);    
    }
}
```

### 字符串常量池

字符串常量池用于存储**编译期间**存在的所有字符串实例的引用，以及运行时动态添加的引用。字符串常量池是全局的，只有一个。当我们以 `String str = "123"`的字面量形式创建字符串实例时，首先会去判断字符串常量池中是否有引用指向相同内容的实例，如果有则返回该实例。否则在堆中创建 String 对象并将引用驻留在字符串常量池中。

简单来说可以这样认为，只要是双引号引起来的字符串字面量，都会存放在常量池中。

### 常量池的作用

下面看一个巧合的情况：

```java
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "hello";
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
    }
}
```

从表面上看，两个字符串用`==`和`equals()`比较都为`true`，但实际上那只是Java编译器在编译期，会自动把所有字面量相同的字符串当作一个对象放入常量池，自然`s1`和`s2`的引用就是相同的。

所以，这种`==`比较返回`true`纯属巧合。换一种写法，`==`比较就会失败：

```java
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "HELLO".toLowerCase();
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
    }
}
```

因为`toLowerCase()`方法返回了一个新的String对象，s2指向String对象，String对象再指向字面量"hello"。关于这里更详细的例子，可以参见：[Java字符串常量池详解-CSDN博客](https://blog.csdn.net/qq_63218110/article/details/127550024)

### 忽略大小写进行比较

使用equals进行字符串内容的比较，必须大小写一致。而equalsIgnoreCase，忽略大小写判断内容是否一致

```java
public class Main {
    public static void main(String[] args) {
        String str1 = "hello";
        String str2 = new String(str1);
        String str3 = str1.toUpperCase();
        //==用于判断是否是同一个字符串对象
        System.out.println( str1  ==  str2);
        System.out.println(str1.equals(str2));//完全一样返回true
        System.out.println(str1.equals(str3));//大小写不一样，返回false
        System.out.println(str1.equalsIgnoreCase(str3));//忽略大小写的比较，返回true
    }
}
```

### 是否以子字符串开始或者结束

- startsWith //以...开始

- endsWith //以...结束

```java
public class Main {
    public static void main(String[] args) {
        String str1 = "hello world";
        String start = "hello";
        String end = "world";
        System.out.println(str1.startsWith(start));//以...开始
        System.out.println(str1.endsWith(end));//以...结束  
    }
  
}
```

## 6 编码

在早期的计算机系统中，为了给字符编码，美国国家标准学会（American National Standard Institute：ANSI）制定了一套英文字母、数字和常用符号的编码，它占用一个字节，编码范围从`0`到`127`，最高位始终为`0`，称为`ASCII`编码。例如，字符`'A'`的编码是`0x41`，字符`'1'`的编码是`0x31`。

如果要把汉字也纳入计算机编码，很显然一个字节是不够的。`GB2312`标准使用两个字节表示一个汉字，其中第一个字节的最高位始终为`1`，以便和`ASCII`编码区分开。例如，汉字`'中'`的`GB2312`编码是`0xd6d0`。

类似的，日文有`Shift_JIS`编码，韩文有`EUC-KR`编码，这些编码因为标准不统一，同时使用，就会产生冲突。

为了统一全球所有语言的编码，全球统一码联盟发布了`Unicode`编码，它把世界上主要语言都纳入同一个编码，这样，中文、日文、韩文和其他语言就不会冲突。

`Unicode`编码需要两个或者更多字节表示，我们可以比较中英文字符在`ASCII`、`GB2312`和`Unicode`的编码：

英文字符`'A'`的`ASCII`编码和`Unicode`编码：

```ascii
         ┌────┐
ASCII:   │ 41 │
         └────┘
         ┌────┬────┐
Unicode: │ 00 │ 41 │
         └────┴────┘
```

英文字符的`Unicode`编码就是简单地在前面添加一个`00`字节。

中文字符`'中'`的`GB2312`编码和`Unicode`编码：

```ascii
         ┌────┬────┐
GB2312:  │ d6 │ d0 │
         └────┴────┘
         ┌────┬────┐
Unicode: │ 4e │ 2d │
         └────┴────┘
```

那我们经常使用的`UTF-8`又是什么编码呢？因为英文字符的`Unicode`编码高字节总是`00`，包含大量英文的文本会浪费空间，所以，出现了`UTF-8`编码，它是一种变长编码，用来把固定长度的`Unicode`编码变成1～4字节的变长编码。通过`UTF-8`编码，英文字符`'A'`的`UTF-8`编码变为`0x41`，正好和`ASCII`码一致，而中文`'中'`的`UTF-8`编码为3字节`0xe4b8ad`。

`UTF-8`编码的另一个好处是容错能力强。如果传输过程中某些字符出错，不会影响后续字符，因为`UTF-8`编码依靠高字节位来确定一个字符究竟是几个字节，它经常用来作为传输编码。

在Java中，`char`类型实际上就是两个字节的`Unicode`编码。如果我们要手动把字符串转换成其他编码，可以这样做：

```java
byte[] b1 = "Hello".getBytes(); // 按系统默认编码转换，不推荐
byte[] b2 = "Hello".getBytes("UTF-8"); // 按UTF-8编码转换
byte[] b2 = "Hello".getBytes("GBK"); // 按GBK编码转换
byte[] b3 = "Hello".getBytes(StandardCharsets.UTF_8); // 按UTF-8编码转换
```

注意：转换编码后，就不再是`char`类型，而是`byte`类型表示的数组。转换编码就是将`String`和`byte[]`转换，需要指定编码

如果要把已知编码的`byte[]`转换为`String`，可以这样做：

```java
byte[] b = ...
String s1 = new String(b, "GBK"); // 按GBK转换
String s2 = new String(b, StandardCharsets.UTF_8); // 按UTF-8转换
```

始终牢记：Java的`String`和`char`在内存中总是以Unicode编码表示。

