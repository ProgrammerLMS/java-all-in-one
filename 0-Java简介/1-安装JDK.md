# 安装JDK

## 1.进入Oracle官网下载安装

[Java Downloads | Oracle 中国](https://www.oracle.com/cn/java/technologies/downloads/#java8-windows)

在网页中选择合适的操作系统与安装包，找到Java SE 8的下载链接进行下载安装即可。Windows优先选`x64 MSI Installer`，Linux和macOS要根据自己电脑的CPU是ARM还是x86选择合适的安装包。具体的下载步骤省略;)

## 2.设置环境变量

安装完JDK后，需要设置一个`JAVA_HOME`的环境变量，它指向JDK的安装目录。在Windows下，它是安装目录，类似：

```
C:\Program Files\Java\jdk-21
```

然后，把`JAVA_HOME`的`bin`目录附加到系统环境变量`PATH`上。在Windows下，它长这样：

```
Path=%JAVA_HOME%\bin;<现有的其他路径>
```

把`JAVA_HOME`的`bin`目录添加到`PATH`中是为了在任意文件夹下都可以运行`java`。打开命令提示符窗口，输入命令`java -version`，如果一切正常，你会看到如下输出（或类似内容）：

```
java version "1.8.0_301"
Java(TM) SE Runtime Environment (build 1.8.0_301-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.301-b09, mixed mode)
```

具体的设置步骤省略;)

## 3. 安装多个版本的JDK

通过`java -version`指令可以查看当前JDK的版本，那么如果有多个版本的JDK又需要如何配置呢，可以进行如下操作：

| 序号 | 变量名     | 变量值                              |
| ---- | ---------- | ----------------------------------- |
| 1    | JAVA_HOME  | %JAVA8_HOME%                        |
| 2    | JAVA6_HOME | E:\worktools\java\Java6\jdk1.6.0_45 |
| 3    | JAVA8_HOME | C:\Program Files\Java\jdk1.8.0_101  |

其中序号1的变量名是固定的，序号2,3的变量名可以自行决定，这里就以jdk的版本命名了。

序号1的变量值其实就是序号2或3的变量名，然后两边用%包裹，可以按需自由选择修改。

具体的操作可见：[同时安装多个JDK的环境变量配置及切换方式-CSDN博客](https://blog.csdn.net/Bombradish/article/details/129086434)

## 4. 观察一下文件夹

在`JAVA_HOME`的`bin`目录下找到很多可执行文件（.exe文件）：

- java：这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码；
- javac：这是Java的编译器，它用于把Java源码文件（以`.java`后缀结尾）编译为Java字节码文件（以`.class`后缀结尾）；
- jar：用于把一组`.class`文件打包成一个`.jar`文件，便于发布；
- javadoc：用于从Java源码中自动提取注释并生成文档；
- jdb：Java调试器，用于开发阶段的运行调试。