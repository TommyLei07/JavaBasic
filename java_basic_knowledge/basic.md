***这里用来记录Java刷题中碰到的一些问题以及日常的总结***
<!-- TOC -->

- [关于基本类型](#关于基本类型)
    - [八大基本类型](#八大基本类型)
    - [缓存池](#缓存池)
    - [装箱拆箱的细节](#装箱拆箱的细节)
- [String](#string)
    - [不可变有什么好处呢？](#不可变有什么好处呢)
    - [String StringBuffer StringBuilder](#string-stringbuffer-stringbuilder)
- [抽象类 接口](#抽象类-接口)
    - [抽象类](#抽象类)
    - [接口](#接口)
        - [接口与类的区别：](#接口与类的区别)
        - [接口特性](#接口特性)
        - [抽象类和接口的区别](#抽象类和接口的区别)

<!-- /TOC -->

# 关于基本类型

## 八大基本类型
- boolean/1
- byte/8
- char/16
- short/16
- int/32
- float/32
- long/64
- double/64

基本类型都有对应的包装类型（引用类型），基本类型和其对应的包装类型之间的赋值使用自动装箱和拆箱完成。

## 缓存池
`new Integer(123)` 与 `Integer.valueOf(123)`的区别在于：  
`new Integer(123)`每次都会新建一个对象，而`Integer.valueOf(123)`会取得同一个对象的引用。
```
Integer x=new Integer(123);
Integer y=new Integer(123);
System.out.println(x==y);   //false
Integer z=Integer.valueOf(123);
Integer k=Integer.valueOf(123);
System.out.println(z==k);   //true
```
编译器在自动装箱的过程中会调用`valueOf()`方法，因此多个`Integer`实例使用自动装箱来创建并且值相同，那么就会引用相同的对象。
```
Integer m=123;
Integer n=123;
System.out.println(m==n);   //true
```
`valueOf()方法`：先判断值是否在缓存池中，如果在的话就直接使用缓存池的内容。

在Java8中，Integer缓存池的大小默认为 -128~127

## 装箱拆箱的细节  
*稍后补充。。*

# String  
1. String 被申明为`final`，因此不可继承。内部使用char数组存储
```
public final class String implements java,io.Serializable,Comparable<String>,CharSequence{
        private final char value[];
    }
```
## 不可变有什么好处呢？   

|     |   |
|  ----  | ----  |
| 缓存hash  | 比如String用作HashMap的key,不可变性可以使得hash值不可变 |
| String pool的需要  | 如果一个String对象已经被创建过了，name就会从String pool中取得引用，只有String不可变才能使用String pool |
| 安全性 | String经常作为参数，不可变性也保证参数不变，比如在网络连接中。 |
| 线程安全 | 不可变性具有线程安全 |


## String StringBuffer StringBuilder

前面说了，String是不可变的，因为使用了final修饰符。而StringBuffer 和StringBuilder都继承 `AbstractStringBuilder` 类，在`AbstractStringBuilder`中虽然也是使用字符数组保存字符串`char[] value`，但是没哟用final关键字修饰，所以可变。二者的构造方法都是调用父类构造方法，即`AbstractStringBuilder`实现的。

- 线程安全性  


|     |   |
|  ----  | ----  |
| String  | 安全 |
| StringBuffer  | 同步锁，安全 |   
| StringBuilder  | 没有加同步锁，不安全 |   

- 总结

1. 少量数据：String
2. 单线程大量数据：StringBuilder
3. 多线程大量数据：StringBuffer

# 抽象类 接口

## 抽象类
- 抽象类除了不能实例化对象之外，类的其它功能依然存在，**成员变量、成员方法和构造方法**的访问方式和普通类一样。由于抽象类不能实例化对象，所以抽象类必须被继承，才能被使用。

- 抽象方法：如果你想设计这样一个类，该类包含一个特别的成员方法，该方法的具体实现由它的子类确定，那么你可以在父类中声明该方法为抽象方法。Abstract 关键字同样可以用来声明抽象方法，抽象方法只包含一个方法名，而没有方法体。抽象方法没有定义，方法名后面直接跟一个分号，而不是花括号。
```
public abstract class Employee
{
   private String name;
   private String address;
   private int number;
   
   public abstract double computePay();
   
   // 其余代码
}
```
- 总结

||
|---|
|抽象类不能被实例化，如果被实例化，就会报错，编译无法通过。只有抽象类的非抽象子类可以创建对象。|
|抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。|
| 抽象类中的抽象方法只是声明，不包含方法体。|
|构造方法，类方法（用 static 修饰的方法）不能声明为抽象方法。|
| 抽象类的子类必须给出抽象类中的抽象方法的具体实现，除非该子类也是抽象类。|

***
## 接口

接口（英文：Interface），在 JAVA 编程语言中是一个抽象类型，是抽象方法的集合，接口通常以 interface 来声明。一个类通过继承接口的方式，从而来继承接口的抽象方法。  

接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。

除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。

### 接口与类的区别：

1. 接口不能用于实例化对象。
2. 接口没有构造方法。
3. 接口中所有的方法必须是抽象方法。
4. 接口不能包含成员变量，除了 static 和 final 变量。
5. 接口不是被类继承了，而是要被类实现。
6. 接口支持多继承。

### 接口特性

1. 接口中每一个方法也是隐式抽象的，接口中的方法会被隐式的指定为 `public abstract`（只能是 public abstract，其他修饰符都会报错）。
2. 接口中可以含有变量，但是接口中的变量会被隐式的指定为 `public static final` 变量（并且只能是 public，用 private 修饰会报编译错误）。
3. 接口中的方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法。

### 抽象类和接口的区别
1. 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的。
3. 接口中不能含有静态代码块以及静态方法 (用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。