# Lambda表达式的方法引用

使用Lambda表达式，我们就可以不必编写`FunctionalInterface`接口的实现类，从而简化代码：

```java
Arrays.sort(array, (s1, s2) -> {
    return s1.compareTo(s2);
});
```

## 1.引入静态方法

实际上，除了Lambda表达式，我们还可以直接传入**方法引用**。例如：

```java
public class Main {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, Main::cmp);
        System.out.println(String.join(", ", array));
    }

    static int cmp(String s1, String s2) {
        return s1.compareTo(s2);
    }
}
```

上述代码在`Arrays.sort()`中直接传入了静态方法`cmp`的引用，用`Main::cmp`表示。

因此，所谓方法引用，是指如果**某个方法签名和接口内定义方法恰好一致**，就可以直接传入方法引用。

因为`Comparator<String>`接口定义的方法是`int compare(String, String)`，和静态方法`int cmp(String, String)`相比，除了方法名外，方法参数一致，返回类型相同，因此，我们说两者的方法签名一致，可以直接把方法名作为Lambda表达式传入：

```java
Arrays.sort(array, Main::cmp);
```

❗❗*e注意**：在这里，方法签名只看参数类型和返回类型，不看方法名称，也不看类的继承关系。

## 2.引入实例方法

我们再看看如何引用实例方法。如果我们把代码改写如下：

```java
public class Main {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, String::compareTo);
        System.out.println(String.join(", ", array));
    }
}
```

不但可以编译通过，而且运行结果也是一样的，这说明`String.compareTo()`方法也符合Lambda定义。

观察`String.compareTo()`的方法定义：

```java
public final class String {
    public int compareTo(String o) {
        ...
    }
}
```

这个方法的签名只有一个参数，为什么和`int Comparator<String>.compare(String, String)`能匹配呢？

因为实例方法有一个隐含的`this`参数，`String`类的`compareTo()`方法在实际调用的时候，第一个隐含参数总是传入`this`，相当于静态方法：

```java
public static int compareTo(String this, String o);
```

所以，`String.compareTo()`方法也可作为方法引用传入。

## 3.构造方法引用

除了可以引用静态方法和实例方法，我们还可以引用构造方法。

我们来看一个例子：如果要把一个`List<String>`转换为`List<Person>`，应该怎么办？

```java
class Person {
    String name;
    public Person(String name) {
        this.name = name;
    }
}

List<String> names = List.of("Bob", "Alice", "Tim");
List<Person> persons = ???
```

传统的做法是先定义一个`ArrayList<Person>`，然后用`for`循环填充这个`List`：

```java
List<String> names = List.of("Bob", "Alice", "Tim");
List<Person> persons = new ArrayList<>();
for (String name : names) {
    persons.add(new Person(name));
}
```

要更简单地实现`String`到`Person`的转换，我们可以引用`Person`的构造方法：

```java
public class Main {
    public static void main(String[] args) {
        List<String> names = List.of("Bob", "Alice", "Tim");
        List<Person> persons = names.stream().map(Person::new).collect(Collectors.toList());
        System.out.println(persons);
    }
}

class Person {
    String name;
    public Person(String name) {
        this.name = name;
    }
    public String toString() {
        return "Person:" + this.name;
    }
}

```

后面我们会讲到`Stream`的`map()`方法。现在我们看到，这里的`map()`需要传入的FunctionalInterface的定义是：

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

把泛型对应上就是方法签名`Person apply(String)`，即传入参数`String`，返回类型`Person`。而`Person`类的构造方法恰好满足这个条件，因为构造方法的参数是`String`，而构造方法虽然没有`return`语句，但它会隐式地返回`this`实例，类型就是`Person`，因此，此处可以引用构造方法。构造方法的引用写法是`类名::new`，因此，此处传入`Person::new`。

## 4.示例

### 4.1使用静态方法

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
   
import charactor.Hero;
   
public class TestLambda {
    public static void main(String[] args) {
        Random r = new Random();
        List<Hero> heros = new ArrayList<Hero>();
        for (int i = 0; i < 5; i++) {
            heros.add(new Hero("hero " + i, r.nextInt(1000), r.nextInt(100)));
        }
        System.out.println("初始化后的集合：");
        System.out.println(heros);
           
        HeroChecker c = new HeroChecker() {
            public boolean test(Hero h) {
                return h.hp>100 && h.damage<50;
            }
        };
          
        System.out.println("使用匿名类过滤");
        filter(heros, c);
        System.out.println("使用Lambda表达式");
        filter(heros, h->h.hp>100 && h.damage<50);
        System.out.println("在Lambda表达式中使用静态方法");
        filter(heros, h -> TestLambda.testHero(h) );
        System.out.println("直接引用静态方法");
        filter(heros, TestLambda::testHero);
    }
       
    public static boolean testHero(Hero h) {
        return h.hp>100 && h.damage<50;
    }
       
    private static void filter(List<Hero> heros, HeroChecker checker) {
        for (Hero hero : heros) {
            if (checker.test(hero))
                System.out.print(hero);
        }
    }
   
}
```

### 4.2 引用对象方法

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
 
import charactor.Hero;
 
public class TestLambda {
    public static void main(String[] args) {
        Random r = new Random();
        List<Hero> heros = new ArrayList<Hero>();
        for (int i = 0; i < 5; i++) {
            heros.add(new Hero("hero " + i, r.nextInt(1000), r.nextInt(100)));
        }
        System.out.println("初始化后的集合：");
        System.out.println(heros);
     
        System.out.println("使用引用对象方法  的过滤结果：");
        //使用类的对象方法
        TestLambda testLambda = new TestLambda();
        filter(heros, testLambda::testHero);
    }
     
    public boolean testHero(Hero h) {
        return h.hp>100 && h.damage<50;
    }
     
    private static void filter(List<Hero> heros, HeroChecker checker) {
        for (Hero hero : heros) {
            if (checker.test(hero))
                System.out.print(hero);
        }
    }
 
}
```

### 4.3 引用容器中对象的方法

首先为Hero添加一个方法

```java
public boolean matched(){
   return this.hp>100 && this.damage<50;
}
```

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
   
import charactor.Hero;
   
public class TestLambda {
    public static void main(String[] args) {
        Random r = new Random();
        List<Hero> heros = new ArrayList<Hero>();
        for (int i = 0; i < 5; i++) {
            heros.add(new Hero("hero " + i, r.nextInt(1000), r.nextInt(100)));
        }
        System.out.println("初始化后的集合：");
        System.out.println(heros);
         
        System.out.println("Lambda表达式：");       
        filter(heros,h-> h.hp>100 && h.damage<50 );
 
        System.out.println("Lambda表达式中调用容器中的对象的matched方法：");       
        filter(heros,h-> h.matched() );
  
        System.out.println("引用容器中对象的方法 之过滤结果：");       
        filter(heros, Hero::matched);
    }
       
    public boolean testHero(Hero h) {
        return h.hp>100 && h.damage<50;
    }
       
    private static void filter(List<Hero> heros, HeroChecker checker) {
        for (Hero hero : heros) {
            if (checker.test(hero))
                System.out.print(hero);
        }
    }
   
}
```

### 4.4 引用构造方法

有的接口中的方法会返回一个对象，比如**java.util.function.Supplier**提供了一个get方法，返回一个对象。

```java
public interface Supplier<T> {
    T get();
}
```


设计一个方法，参数是这个接口 

```java
public static List getList(Supplier<List> s){
  return s.get();
}
```

为了调用这个方法，有3种方式，第一种匿名类：

```java
Supplier<List> s = new Supplier<List>() {
	public List get() {
		return new ArrayList();
	}
};
List list1 = getList(s);
```

第二种：Lambda表达式

```java
List list2 = getList(()->new ArrayList());
```

第三种：引用构造器

```java
List list3 = getList(ArrayList::new);
```

全部代码：

```java
import java.util.ArrayList;
import java.util.List;
import java.util.function.Supplier;
 
public class TestLambda {
    public static void main(String[] args) {
    Supplier<List> s = new Supplier<List>() {
        public List get() {
            return new ArrayList();
        }
    };
 
    //匿名类
    List list1 = getList(s);
     
    //Lambda表达式
    List list2 = getList(()->new ArrayList());
     
    //引用构造器
    List list3 = getList(ArrayList::new);
 
    }
     
    public static List getList(Supplier<List> s){
        return s.get();
    }
      
}
```

## 小结

`FunctionalInterface`允许传入：

- 接口的实现类（传统写法，代码较繁琐）；
- Lambda表达式（只需列出参数名，由编译器推断类型）；
- 符合方法签名的静态方法；
- 符合方法签名的实例方法（实例类型被看做第一个参数类型）；
- 符合方法签名的构造方法（实例类型被看做返回类型）。

`FunctionalInterface`不强制继承关系，不需要方法名称相同，只要求方法参数（类型和数量）与方法返回类型相同，即认为方法签名相同。