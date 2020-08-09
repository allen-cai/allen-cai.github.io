---
layout: post
title: "浅谈JAVA String"
date: 2020-08-09 
description: "java"
tag: 博客 
---

### 浅谈JAVA String

　　纯粹记录自己学习路上的一些胡思乱想，如是有任何问题愿您能及时联系我。

mail： `CL30240@163.COM`

#### 请问，下面代码输出结果是什么？

```
String firstName = "allen";
String lastName = "cai";
String fullNameAdd = "allen" + "cai";
String fullName = "allencai";
String fullName2 = firstName + lastName;
String fullName3 = firstName + "cai";
String fullName4 = "allen" + lastName;
String fullName5 = new String("allencai");


System.out.println(fullName == fullNameAdd);
System.out.println(fullNameAdd == fullName2);
System.out.println(fullNameAdd == fullName3);
System.out.println(fullNameAdd == fullName4);
System.out.println(fullNameAdd == fullName5);
```

输出结果： true，false，false， false， false

- `解析：`

 要弄懂上面的问题，其实只需要知道，java中的字符串是储存在字符串常量池中（常量池是专门用来储存字符串常量，可以提高内存的使用率，避免重复开辟空间存储相同的字符串。当创建字符串常量时，JVM会首先检查字符串常量池。如果字符串已经存在常量池中，就会返回池中实例的引用。若池中不存在该字符串，则实例化该字符串放到池中，并返回该实例引用。），String不是基本数据类型，具有不可变性，底层是char类型的value数组。fullName与fullNameAdd在编译时期，就已经将字符串加载到常量池中，且指向的地址是相同的，所以fullName == fullNameAdd。那为什么fullName2 != fullNameAdd？在编译时期只能识别出纯字符串相加，若有变量参杂，只能等到代码运行后，才能确定相加得到的对象保存的位置。纯字符串相加指向的是常量池，而fullName2指向的是一个String对象。所以，fullNameAdd不可能与fullName2，fullName3，fullName4，fullName5相等。
 

#### 请问，下面代码输出结果是什么？

```
String firstName = "allen";
String lastName = "cai";
String fullName = "allencai";
String fullName2 = firstName + lastName;

System.out.println(fullName == fullName2);
fullName2 = fullName2.intern();
System.out.println(fullName == fullName2);
```
输出结果： false，true


- `解析：`

这个问题，是对于上一个问题的扩展。fullName2指向的是一个对象的地址，而fullName指向的是常量池中字符串的地址，所以fullName != fullName2。但是fullName2 = fullName2.intern()之后，fullName == fullName2。这是因为，intern表示将该变量直接指向常量池中字符串，并返回该字符串的地址，所以，若字符串的值相等，他们在常量池中保存的位置必将相等。
  
#### 请问，多字符串拼接为什么推荐使用StringBuilder相加，而不推荐直接使用“+”相加？

- `先看一段代码：`


```
public class StringDeepLearning {
    public static void main(String[] args) {
        String fullNameAdd = "allen" + "cai";
    }
}

反编译后的代码如下：
Compiled from "StringDeepLearning.java"
public class StringDeepLearning {
  public StringDeepLearning();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String cai
       2: astore_1
       3: new           #3                  // class java/lang/StringBuilder
       6: dup
       7: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
      10: ldc           #5                  // String allen
      12: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      15: aload_1
      16: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      19: invokevirtual #7                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      22: astore_2
      23: return
}

```

- `解析：`

可以看到上面的代码反编译后，中间有3: new #3 // class java/lang/StringBuilder这么一行代码。也就是说，当字符串相加，字符串并非都是常量，相加其实就是new StringBuilder(),然后执行append方法，将字符串拼接起来。既然“+”会被翻译成StringBuilder，那么为啥还是推荐用StringBuilder，而不用“+”？
    
- `请看第二段代码：`

```
public class StringDeepLearning {
    public static void main(String[] args) {
        String firstName = "allen";

        String s1 = "mmmm";
        for(int i = 0; i < 10; i++) {
            s1  = s1 + firstName;
        }
        System.out.println(s1);
    }
}

反编译代码如下：

Compiled from "StringDeepLearning.java"
public class StringDeepLearning {
  public StringDeepLearning();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String allen
       2: astore_1
       3: ldc           #3                  // String mmmm
       5: astore_2
       6: iconst_0
       7: istore_3
       8: iload_3
       9: bipush        10
      11: if_icmpge     39
      14: new           #4                  // class java/lang/StringBuilder
      17: dup
      18: invokespecial #5                  // Method java/lang/StringBuilder."<init>":()V
      21: aload_2
      22: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      25: aload_1
      26: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      29: invokevirtual #7                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      32: astore_2
      33: iinc          3, 1
      36: goto          8
      39: getstatic     #8                  // Field java/lang/System.out:Ljava/io/PrintStream;
      42: aload_2
      43: invokevirtual #9                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      46: return
}
```

可以看到new关键字，是在iload_3到goto循环中的，也就是说“+”就表示，每一次循环会创建一个StringBuilder对象，并执行toString方法，将新生成的字符串存放到字符串常量池。那咱们再看看StringBuilder在循环中是怎样的。

- `请看第三段代码，代码如下：`

```
public class StringDeepLearning {
    public static void main(String[] args) {
        String firstName = "allen";

        StringBuilder s1 = new StringBuilder();
        for(int i = 0; i < 10; i++) {
            s1  = s1.append(firstName);
        }
        System.out.println(s1);
    }
}

反编译代码如下：

Compiled from "StringDeepLearning.java"
public class StringDeepLearning {
  public StringDeepLearning();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String allen
       2: astore_1
       3: new           #3                  // class java/lang/StringBuilder
       6: dup
       7: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
      10: astore_2
      11: iconst_0
      12: istore_3
      13: iload_3
      14: bipush        10
      16: if_icmpge     31
      19: aload_2
      20: aload_1
      21: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      24: astore_2
      25: iinc          3, 1
      28: goto          13
      31: getstatic     #6                  // Field java/lang/System.out:Ljava/io/PrintStream;
      34: aload_2
      35: invokevirtual #7                  // Method java/io/PrintStream.println:(Ljava/lang/Object;)V
      38: return
}
```
可以看到new关键字在iload_3外面，也就是说只会创建一个StringBuilder对象，append全部之后，执行一次toString方法，将字符串存放到常量池中。

- `可以得出结论：`

在循环拼接字符串中，使用StringBuilder方式去拼接字符串要好于直接使用“+”拼接，原因如下： “+”的方式，在每一次循环中，都会去新创建一个StringBuilder对象，且执行toString方法，增加了JVM垃圾回收的负担，也让很多垃圾字符串占据了字符串常量空间。所以，在循环时尽量使用StringBuilder对字符串进行拼接。但是在非循环的字符串拼接时，“+”和StringBuilder的拼接方式其实是一样的，没有什么太大的区别。