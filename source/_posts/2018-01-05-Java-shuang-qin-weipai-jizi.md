---
title: Java的双亲委派机制是什么？有哪些方式可以打破双亲委派机制
date: 2018-01-05 10:43:22
categories: Backend
tags: [Java, BI]
---

### 双亲委派机制
`双亲委派机制`（Parent-Delegation Model）是Java类加载机制中的一个重要概念。它是一种类加载的层次结构和机制，用于确保类的安全性和一致性。根据这一机制，类加载器在加载类时首先尝试委托给其父类加载器，只有当父类加载器无法加载该类时，才会由当前类加载器来尝试加载。这一机制的目的是保护Java运行时环境的稳定性和安全性，防止用户自定义的类破坏Java核心库。

双亲委派机制通常分为以下三层：

**启动类加载器（Bootstrap ClassLoader）**：它是JVM的一部分，负责加载Java的核心类库，如java.lang和java.util等。这是类加载层次结构的根，是最顶层的类加载器，通常由Java虚拟机的本地实现提供，它不是Java代码实现的类加载器。

**扩展类加载器（Extension ClassLoader）**：它用于加载Java的扩展类，通常位于jre/lib/ext目录下。扩展类加载器负责加载javax包中的类等。它是由启动类加载器加载的。

**应用程序类加载器（Application ClassLoader）**：也称为系统类加载器，这是应用程序开发者最常接触的类加载器，它负责加载应用程序的类，通常从类路径（Classpath）中加载。它是由扩展类加载器加载的。

一旦一个类需要被加载，类加载器会首先尝试委派给父类加载器加载。如果父类加载器无法加载该类，那么子类加载器才会尝试加载。这种级联的加载机制确保了类的一致性和安全性。

如果您尝试编写一个与Java核心类（如java.lang包中的类）具有相同名称和完全相同的类，并将其放在类路径中，双亲委派加载机制将尝试加载这个自定义类。但由于Java核心类已经由启动类加载器加载，双亲委派加载机制会优先加载核心类。这意味着您的自定义类不会替代Java核心类，而会被忽略。

这种机制确保了Java核心类的唯一性和安全性，防止应用程序或开发者无意中替代或修改核心类，从而维护了Java平台的稳定性。如果要扩展或修改Java核心类的行为，通常需要通过其他手段，例如继承或实现接口，而不是直接替换核心类。

### 打破双亲委派机制
虽然双亲委派机制有利于确保Java核心库的稳定性，但有时候用户可能需要打破这一机制。有一些情况下可以考虑打破双亲委派机制：

**自定义类加载器**：如果需要实现自定义的类加载器，需要继承ClassLoader类并重写其loadClass方法，以实现自定义的类加载逻辑。

**动态代理类**：某些动态代理类或字节码生成框架可能需要在运行时生成和加载类，这可能需要打破双亲委派机制以实现特定的加载逻辑。

**模块化系统**：在Java 9及以后版本中，引入了模块化系统，该系统提供了更灵活的类加载和模块定义机制，使得可以更精细地控制类加载过程。

打破双亲委派机制需要谨慎处理，因为它可能会引入类加载冲突和安全问题。在自定义类加载器或使用动态代理等情况下，需要仔细考虑潜在的问题，确保不会破坏Java运行时环境的稳定性。



