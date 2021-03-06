# JAVA异常

## What

Java中异常提供了一种识别及响应错误情况的一致性机制，有效地异常处理能使程序更加健壮、易于调试。异常之所以是一种强大的调试手段，在于其回答了以下三个问题：

- 什么出了错?
- 在哪出的错?
- 为什么出错?

在有效使用异常的情况下，异常类型回答了“什么”被抛出，异常堆栈跟踪回答了“在哪“抛出，异常信息回答了“为什么“会抛出，如果你的异常没有回答以上全部问题，那么可能你没有很好地使用它们。有三个原则可以帮助你在调试过程中最大限度地使用好异常，这三个原则是：

- 具体明确(通过异常体系的分层结构，可以提供明确的异常信息)
- 提早抛出(在java内部抛出异常之前，就对用户输入信息进行判断，提前在自己的程序中抛出相应异常，可以提供用户清晰准确的异常。)
- 延迟捕获(延迟捕获，就是将异常交给上游调用者来捕获处理)

### Java异常体系

Java异常体系的层次结构，分化了具体的异常类型。**具体明确**

[Throw System](!https://github.com/huangshijie/ImgRep/blob/master/ThrowableSys.png)

#### Throwable类

是整个异常体系的父类，实现序列化接口，支持将其对象作为输出流进行输出。其下一共有三个子类Error、Exception和不常见的StackRecorder。

#### Error类

主要是用于描述**Java虚拟机错误**以及**线程死锁**的情况信息。

#### Exception类

Exception则主要针对程序运行过程中遇到的异常的情况。

- CheckedException, 检查异常就是在需要在编写代码的时候，需要使用try-catch(-finally)来进行异常捕获，并且进行相应的逻辑处理。

- RuntimeException， 运行时异常，不需要处理该异常，代码也能正常编译通过。但是也比较麻烦，在写代码的时候应该多考虑这方面可能会产生的问题。从逻辑上解决并改进代码。

### Try-Catch-Finally

- try中包含可能会产生异常的代码块。一旦try中有异常产生，则将跳到catch中的异常处理程序。
- catch执行的顺序，按照子类异常在前，父类异常类在后的顺序。
- finally，最终肯定会执行的代码。

### Throw Throws

- throw, 将产生的异常抛出，是抛出异常的一个**动作**。
- throws, **声明**将要抛出何种类型的异常。交由上层调用者来处理遇到的异常

```word
何时处理遇到的异常，何时只是声明抛出的异常，将异常交给上层处理。

```

### 自定义异常

如果是想抛出检查异常，则继承Exception。
如果是想抛出运行时异常，则继承RuntimeException。

### 异常链

异常链是一种面向对象编程技术，指将捕获的异常包装进一个新的异常中并重新抛出的异常处理方式。原异常被保存为新异常的一个属性（比如cause）。这样做的意义是一个方法应该抛出定义在相同的抽象层次上的异常，但不会丢弃更低层次的信息。

异常需要封装和传递，我们在进行系统开发的时候，不要“吞噬”异常，也不要“赤裸裸”的抛出异常，封装后在抛出，或者通过异常链传递，可以达到系统更健壮、友好的目的。
提供友好的异常信息。

## Where

## When

## Who

## Why

## How

1. 处理运行时异常时，采用逻辑去合理规避同时辅助try-catch处理
2. 在多重catch块后面，可以加一个catch（Exception）来处理可能会被遗漏的异常
3. 对于不确定的代码，也可以加上try-catch，处理潜在的异常
4. 尽量去处理异常，切记只是简单的调用printStackTrace（）去打印
5. 具体如何处理异常，要根据不同的业务需求和异常类型去决定
6. 尽量添加finally语句块去释放占用的资源

```word
在Java异常处理的过程中，你遵循的那些最好的实践是什么？
这个问题在面试技术经理是非常常见的一个问题。因为异常处理在项目设计中是非常关键的，所以精通异常处理是十分必要的。异常处理有很多最佳实践，下面列举集中，它们提高你代码的健壮性和灵活性：
1) 调用方法的时候返回布尔值来代替返回null，这样可以避免NullPointerException。由于空指针是java异常里最恶心的异常。
2) catch块里别不写代码。空catch块是异常处理里的错误事件，因为它只是捕获了异常，却没有任何处理或者提示。通常你起码要打印出异常信息，当然你最好根据需求对异常信息进行处理。
3)能抛受控异常就尽量不抛受非控异常。通过去掉重复的异常处理代码，可以提高代码的可读性。
4) 绝对不要让你的数据库相关异常显示到客户端。由于绝大多数数据库和SQLException异常都是受控异常，在Java中，你应该在DAO层把异常信息处理，然后返回处理过的能让用户看懂并根据异常提示信息改正操作的异常信息。
5) 在Java中，一定要在数据库连接，数据库查询，流处理后，在finally块中调用close()方法。

```

1. 什么情况下，需要包装异常？
2. 什么情况下，抛出runtime exception，什么情况下抛需要被catch的异常？
[When to choose checked and unchecked exceptions](https://stackoverflow.com/questions/27578/when-to-choose-checked-and-unchecked-exceptions)
[In Java, when should I create a checked exception, and when should it be a runtime exception? [duplicate]](https://stackoverflow.com/questions/499437/in-java-when-should-i-create-a-checked-exception-and-when-should-it-be-a-runti)
[Proper use of RuntimeException? [duplicate]](https://stackoverflow.com/questions/4233793/proper-use-of-runtimeexception)
3. 什么情况下，需要handle exception，什么情况，又需要向上抛出
[When to catch the Exception vs When to throw the Exceptions?](https://stackoverflow.com/questions/18679090/when-to-catch-the-exception-vs-when-to-throw-the-exceptions#comment27513178_18679131)
