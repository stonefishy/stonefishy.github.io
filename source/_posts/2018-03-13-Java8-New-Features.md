---
title: Java 8 的一些新特性
date: 2018-03-13 09:30:51
categories: Backend
tags: [Java]
---

`Java 8` 引入了很多重要的新特性，极大增强了语言的表达力和可读性。下面是一些常用的 Java 8 新特性：

## 1. Lambda 表达式
Lambda 表达式允许你将行为传递给方法，它使得代码更简洁、可读性更强。{% pbg danger, Lambda 表达式是函数式编程的核心。%}

#### 语法：
``` java
(parameters) -> expression
```

#### 示例：
```java
// 使用 Lambda 表达式简化代码
public class LambdaExample {
    public static void main(String[] args) {
        // 未使用 Lambda 表达式创建一个线程
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("This thread r1 is not created by Lambda expression");
            }
        };
        new Thread(r1).start();

        // 使用 Lambda 表达式创建一个线程
        Runnable r2 = () -> System.out.println("This thread r2 is created by Lambda expression");
        new Thread(r2).start();
    }
}
```

从上面的代码可以看出，使用 Lambda 表达式可以简化代码，使得代码更加易读、易写。 


## 2. 函数式接口 (Functional Interface)
Java 8 引入了函数式接口的概念。{% pbg danger, 函数式接口是指仅有一个抽象方法的接口，可以用 Lambda 表达式来表示。%}。使用注解`@FunctionalInterface`来修饰一个接口，函数式接口的定义如下：

#### 语法
``` java
@FunctionalInterface
interface MyInterface {
    void myMethod();
}
```

#### 示例：
``` java
// 定义一个函数式接口，只能包含一个抽象方法
@FunctionalInterface
interface Calculator {
    int calc(int a, int b);
}

public class FunctionalInterfaceExample {
    public static void main(String[] args) {

        // 函数式接口可以用Lambda来表示，简化代码
        Calculator addCalculator = (a, b) -> a + b;
        System.out.println(addCalculator.calc(1, 2)); // 输出 3

        Calculator minusCalculator = (a, b) -> a - b;
        System.out.println(minusCalculator.calc(1, 2)); // 输出 -1
    }
}

```

`@FunctionalInterface` 注解用于明确标示该接口是一个函数式接口，它只能包含一个抽象方法。

## 3. Streams API
Java 8 引入了 `Streams API`，使得集合的操作变得更加简洁、声明式。Streams 允许你以声明性方式处理集合，支持串行和并行的聚合操作。
#### 语法
``` java
stream.filter(predicate)
stream.map(function)
stream.reduce(accumulator, combiner)
stream.collect(collector)
...
```

#### 示例：
``` java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamExample {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("Java", "C#", "Python", "Go", "JavaScript");

        // 将List转为stream，使用stream api操作
        String result = list.stream()
                .filter(s -> s.length() > 2)
                .map(s -> s.toUpperCase())
                .collect(Collectors.joining(", "));

        System.out.println(result); // 输出： JAVA, PYTHON, JAVASCRIPT
    }
}

```

通过流操作，可以像 SQL 查询一样进行集合的过滤、排序、映射等操作, 极大的简化了代码的编写。

## 4. 默认方法 (Default Methods)
{% pbg danger, Java 8 允许接口有默认方法，实现了接口的类可以不再必须提供该方法的实现，这为接口的演化提供了更好的向后兼容性。%}。默认方法允许接口新增方法，不强制要求所有实现类修改代码。
#### 语法
``` java
default return-type method-name(parameters) {
    // method body
}
```

#### 示例
``` java
interface Flyable {
    // 定义默认方法
    default void fly() {
        System.out.println("something flying");
    }
}

class Bird implements Flyable {
    // Bird可以不需要提供fly()的实现
}

public class DefaultMethodExample {
    public static void main(String[] args) {
        Bird bird = new Bird();
        bird.fly(); // 输出： something flying
    }
}
```

通过默认方法，接口的新增功能不再强制要求所有实现类修改代码。为接口的演化提供了更好的向后兼容性。

## 5. 方法引用 (Method References)
{% pbg danger, 方法引用是一种更简洁的 Lambda 表达式写法，可以直接引用已有方法来代替 Lambda 表达式。%}。
#### 语法
``` java
Class::staticMethod
object::instanceMethod
Class::instanceMethod
```

#### 示例：
``` java
import java.util.Arrays;
import java.util.List;

public class MethodReferenceExample {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("Java", "C#", "Python", "JavaScript");

        // 使用Lambda表达式
        list.forEach(s -> System.out.println(s));

        // 使用方法引用来替代 Lambda 表达式
        list.forEach(System.out::println);
    }
}
```

上面代码中的 `System.out::println` 是对 `System.out.println` 的方法引用。它等价于 `(s) -> System.out.println(s)`。

## 6. Optional 类
{% pbg danger, `Optional` 类是 Java 8 引入的新类，用于避免空指针异常（NullPointerException）。%} 。它提供了一个容器，可能包含值，也可能为空，避免直接操作 null 值。

#### 语法
``` java
Optional.of(value)
Optional.empty()
Optional.ofNullable(value)
...
```

#### 示例：
``` java
import java.util.Optional;

public class OptionalExample {
    public static void main(String[] args) {
        // 创建一个Optional对象
        Optional<String> optional = Optional.of("Java 8");
        // 如果值存在，则打印，如果值不存在，则打印默认值
        System.out.println(optional.orElse("Java")); // 输出 Java 8

        Optional<String> nullOptional = Optional.ofNullable(null);
        System.out.println(nullOptional.orElse("Java")); // 输出 Java

        Optional<String> emptyOptional = Optional.empty();
        System.out.println(emptyOptional.orElse("Java")); // 输出 Java
    }
}

```

{% pbg info, Optional 强烈推荐用于方法的返回值，用来明确表达某个值可能为空。%}

## 7. 新的日期时间 API (java.time)
{% pbg danger, Java 8 引入了全新的日期时间 API，解决了老的 java.util.Date 和 java.util.Calendar 的一些缺陷。%} 新 API 更加灵活、清晰和不可变。

#### 示例：
``` java
import java.time.LocalDate;
import java.time.LocalDateTime;

public class DateTimeExample {
    public static void main(String[] args) {
        // 获取当前的日期和时间
        LocalDateTime now = LocalDateTime.now();
        System.out.println("Current date and time: " + now);

        // 格式化日期
        LocalDate date = LocalDate.of(2018, 03, 13);
        System.out.println("Formatted date: " + date);

        // 时间加法
        LocalDateTime nextWeek = now.plusWeeks(1);
        System.out.println("One week later: " + nextWeek);
    }
}

```

{%pbg success, `java.time` 包中的类是不可变的（immutable），因此它们天生是线程安全的。%}

## 8. 新的 java.util.function 包
`java.util.function` 包提供了大量的常用函数式接口，如 `Predicate`, `Function`, `Consumer`, `Supplier` 等，可以简化很多操作。

#### 示例：
``` java
import java.util.function.*;

public class FunctionExample {
    public static void main(String[] args) {
        // 使用 Function 接口进行映射操作, Function 接口是一个接受一个参数并返回一个结果的函数。
        Function<String, Integer> stringLength = s -> s.length();
        System.out.println(stringLength.apply("Hello"));  // 输出 5

        // 使用 Predicate 接口进行过滤操作, Predicate 接口是一个接受一个参数并返回一个布尔值的函数。
        Predicate<String> isNotEmpty = s -> !s.isEmpty();
        System.out.println(isNotEmpty.test("Hello"));  // 输出 true

        // 使用 Consumer 接口进行输出操作, Consumer 接口是一个接受一个参数但不返回值的函数。
        Consumer<String> printString = s -> System.out.println(s);
        printString.accept("Hello");  // 输出 Hello

        // 使用 Supplier 接口进行生成操作, Supplier 接口是一个不接受参数并返回一个值的函数。
        Supplier<String> helloSupplier = () -> "Hello";
        System.out.println(helloSupplier.get());  // 输出 Hello
    }
}
```

## 9. 重复注解 (Repeating Annotations)
Java 8 支持`重复注解`的定义，允许同一个注解在同一个元素上出现多次。使用 `@Repeatable` 注解来标记。

#### 示例：
``` java
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Repeatable(Schedules.class)
@interface Schedule {
    String month() default "January";
    String day() default "First";
    int hour();
}

@Retention(RetentionPolicy.RUNTIME)
@interface Schedules {
    Schedule[] value();
}

@Schedule(day = "Monday", hour=12)
@Schedule(month = "March", day = "Friday", hour=1)
class MySchedule{

}

public class RepeatingAnnotationExample {
    public static void main(String[] args) {
        Schedule[] annos = MySchedule.class.getAnnotationsByType(Schedule.class);
        for (Schedule s: annos) {
            System.out.printf("Month: %s, Day: %s, Hour: %d\n", s.month(), s.day(), s.hour());
        }
    }
}

```

通过重复注解，可以在同一个类或方法上应用多个相同的注解。

## 10. Nashorn JavaScript 引擎
Java 8 引入了新的 JavaScript 引擎 `Nashorn`，它比之前的 Rhino 引擎性能更高，并且可以更容易地与 Java 代码进行交互。

示例：
``` java
import javax.script.*;

public class NashornExample {
    public static void main(String[] args) throws ScriptException {
        ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
        engine.eval("print('Hello from JavaScript!')");
    }
}
```
通过 Nashorn，可以直接在 Java 中运行 JavaScript 代码，增强了 Java 与其他语言的集成能力。

## 总结
Java 8 引入了许多新特性，提升了语言的表达力，增加了函数式编程的支持，简化了代码的编写和可维护性。通过使用 `Lambda 表达式`、`函数式接口`、`方法引用`、`Streams API`、`默认方法`、`Optional 类`、`java.time` 等特性，Java 开发者可以编写更简洁、高效、易于理解的代码。这些特性不仅提升了代码的可读性，还增强了代码的性能和可扩展性。