# classpath和jar包

## 1.什么是classpath？

`classpath`是JVM用到的一个环境变量，它用来指示JVM如何搜索`class`。

因为Java是编译型语言，源码文件是`.java`，而编译后的`.class`文件才是真正可以被JVM执行的字节码。因此，JVM需要知道，如果要加载一个`abc.xyz.Hello`的类，应该去哪搜索对应的`Hello.class`文件。

所以，`classpath`就是一组目录的集合，它设置的搜索路径与操作系统相关。例如，在Windows系统上，用`;`分隔，带空格的目录用`""`括起来，可能长这样：

```
C:\work\project1\bin;C:\shared;"D:\My Documents\project1\bin"
```

现在我们假设`classpath`是`.;C:\work\project1\bin;C:\shared`，当JVM在加载`abc.xyz.Hello`这个类时，会依次查找：

- <当前目录>\abc\xyz\Hello.class
- C:\work\project1\bin\abc\xyz\Hello.class
- C:\shared\abc\xyz\Hello.class

如果JVM在某个路径下找到了对应的`class`文件，就不再往后继续搜索。如果所有路径下都没有找到，就报错。

## 2.classpath的设定方法

- 在系统环境变量中设置`classpath`环境变量，不推荐，那样会污染整个系统环境；
- 在启动JVM时设置`classpath`变量，推荐。

在启动JVM时设置`classpath`实际上就是给`java`命令传入`-classpath`或`-cp`参数：

```powershell
java -classpath .;C:\work\project1\bin;C:\shared abc.xyz.Hello
```

或

```
java -cp .;C:\work\project1\bin;C:\shared abc.xyz.Hello
```

没有设置系统环境变量，也没有传入`-cp`参数，那么JVM默认的`classpath`为`.`，即当前目录。

## 3.IDE中的classpath

在IDE中运行Java程序，IDE自动传入的`-cp`参数是当前工程的`bin/out/target`目录和引入的jar包。一般eclipse编译产生的字节码在bin文件夹而idea产生在out或target文件夹

默认的当前目录`.`对于绝大多数情况都够用了。

## 4.什么是jar包

如果有很多`.class`文件，散落在各层目录中，肯定不便于管理。如果能把目录打一个包，变成一个文件，就方便多了。

jar包就是用来干这个事的，它可以把按照`package`组织的目录层级，以及各个目录下的所有文件（包括`.class`文件和其他文件）都打成一个jar文件，这样一来，无论是备份，还是发给客户，就简单多了。

jar包实际上就是一个zip格式的压缩文件，而jar包相当于目录。如果我们要执行一个jar包的`class`，就可以把jar包放到`classpath`中：

```
java -cp ./hello.jar abc.xyz.Hello
```

这样JVM会自动在`hello.jar`的文件里，按需去搜索某个类。

## 5.如何创建jar包

虽然jar包本质上就是压缩文件, 但是一般的压缩工具并不可取因为可能会缺乏一些关键文件. 

常用的打包工具就是maven, 后面将会介绍. 同时主流的IDE如idea或eclipse都提供了生成jar包的功能