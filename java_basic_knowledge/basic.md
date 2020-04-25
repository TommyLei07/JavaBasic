***这里用来记录Java刷题中碰到的一些问题以及日常的总结***
<!-- TOC -->

- [关于基本类型](#关于基本类型)
    - [八大基本类型](#八大基本类型)
    - [缓存池](#缓存池)

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



