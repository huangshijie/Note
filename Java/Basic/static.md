Static 

静态包导入

概述　　
　　java中的类中，方法前面有static修饰的是类方法，反之为实例方法

区别
　　实例方法既能对类变量操作又能对实例变量操作，既能调用类方法又能调用实例方法；

　　而类方法只能对类变量进行操作，而不能直接操作实例变量，不能直接调用实例方法。

类方法的特点
调用类方法应使用类名做前缀。

该方法属于整个类，而不属于某个对象。

该方法只能处理类变量或方法内的局部变量。

类变量或类方法可以直接使用，无须创建类的对象

 