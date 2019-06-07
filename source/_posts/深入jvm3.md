---
title: 深入jvm3
date: 2019-06-06 00:48:32
tags:
---
java中重写和重载是基于参数类型和返回值类型的：统称方法描述符

重载：方法名称一样，描述符不一样；这个情况同样存在父类和子类拥有同样的方法名，但是描述符不一样也是重载；

重写也是依赖方法秒速符的：如果父类的方法描述和子类的方法描述符一样，说明子类的方法重写了父类的方法；
但是在java语言中和java虚拟机中，方法描述符不一样；这种情况下，虚拟机采用桥接弥补这个不足

比如：

```
public class Node<T>{
	public T data;
	public void setData(T data){
		this.data=data;
		System.out.print("node.setData");
	}
}

public class MyNode extends Node<String>{
	public String data;
	public void setData(String data){
		this.data=data;
		System.out.print("MyNode.setData");
	}
}

```
如上述例子中MyNode中的setData(String data) 重写了Node中的setData(Object data)这个对于写过java语音的程序员来说并不陌生，但是对于java虚拟机判定重写标准来说，这两个方法的方法描述并不一致。这时候就是java虚拟机中采用了桥接的方式弥补这个

javap -v class 文件查看MyNode中的字节码

```

Classfile /Users/chenzr/study/22 深入拆解 Java 虚拟机/test/less4/MyNode.class
  Last modified 2019-6-6; size 590 bytes
  MD5 checksum 7d939265a6973b1c1b1866ec806f93dd
  Compiled from "Brige.java"
class MyNode extends Node<java.lang.String>
  minor version: 0
  major version: 52
  flags: ACC_SUPER
Constant pool:
   #1 = Methodref          #9.#23         // Node."<init>":()V
   #2 = Fieldref           #8.#24         // MyNode.data:Ljava/lang/String;
   #3 = Fieldref           #25.#26        // java/lang/System.out:Ljava/io/PrintStream;
   #4 = String             #27            // MyNode.setData
   #5 = Methodref          #28.#29        // java/io/PrintStream.print:(Ljava/lang/String;)V
   #6 = Class              #30            // java/lang/String
   #7 = Methodref          #8.#31         // MyNode.setData:(Ljava/lang/String;)V
   #8 = Class              #32            // MyNode
   #9 = Class              #33            // Node
  #10 = Utf8               data
  #11 = Utf8               Ljava/lang/String;
  #12 = Utf8               <init>
  #13 = Utf8               ()V
  #14 = Utf8               Code
  #15 = Utf8               LineNumberTable
  #16 = Utf8               setData
  #17 = Utf8               (Ljava/lang/String;)V
  #18 = Utf8               (Ljava/lang/Object;)V
  #19 = Utf8               Signature
  #20 = Utf8               LNode<Ljava/lang/String;>;
  #21 = Utf8               SourceFile
  #22 = Utf8               Brige.java
  #23 = NameAndType        #12:#13        // "<init>":()V
  #24 = NameAndType        #10:#11        // data:Ljava/lang/String;
  #25 = Class              #34            // java/lang/System
  #26 = NameAndType        #35:#36        // out:Ljava/io/PrintStream;
  #27 = Utf8               MyNode.setData
  #28 = Class              #37            // java/io/PrintStream
  #29 = NameAndType        #38:#17        // print:(Ljava/lang/String;)V
  #30 = Utf8               java/lang/String
  #31 = NameAndType        #16:#17        // setData:(Ljava/lang/String;)V
  #32 = Utf8               MyNode
  #33 = Utf8               Node
  #34 = Utf8               java/lang/System
  #35 = Utf8               out
  #36 = Utf8               Ljava/io/PrintStream;
  #37 = Utf8               java/io/PrintStream
  #38 = Utf8               print
{
  public java.lang.String data;
    descriptor: Ljava/lang/String;
    flags: ACC_PUBLIC

  MyNode();
    descriptor: ()V
    flags:
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method Node."<init>":()V
         4: return
      LineNumberTable:
        line 9: 0

  public void setData(java.lang.String);
    descriptor: (Ljava/lang/String;)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: aload_1
         2: putfield      #2                  // Field data:Ljava/lang/String;
         5: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
         8: ldc           #4                  // String MyNode.setData
        10: invokevirtual #5                  // Method java/io/PrintStream.print:(Ljava/lang/String;)V
        13: return
      LineNumberTable:
        line 12: 0
        line 13: 5
        line 14: 13

  public void setData(java.lang.Object);
    descriptor: (Ljava/lang/Object;)V
    flags: ACC_PUBLIC, ACC_BRIDGE, ACC_SYNTHETIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: aload_1
         2: checkcast     #6                  // class java/lang/String
         5: invokevirtual #7                  // Method setData:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 9: 0
}
Signature: #20                          // LNode<Ljava/lang/String;>;
SourceFile: "Brige.java"
```

大家可以看到 flag包含 ACC_BRIDGE, ACC_SYNTHETIC 表示了桥接的意思，
在这里就是MyNode setData(Object)需要桥接保证重写语义（方法描述否）

## java 中方法的调用
#### 1，对于重载（方法描述符中能够确认的）非私有静态方法，编译后的java指令是invokestatic，即在编译后就已经确认了目标方法（静态绑定）
#### 2，java里对于非私有实例化方法，接口调用 编译后的java指令是invokevirtual invokeinterface 等指令，这既是java的虚方法调用。对于虚方法一般都是在java运行中才去动态绑定执行的。对于虚方法的调用，java采用了空间换区时间的方法；
（1） 方法表（参考Linux里，上层对内核的调用也是使用了方法表的调用。。。待补充）。每个类会生成一个方法表，子类方法表拥有父类的所有方法，而且其子类在方法表上的索引值和重写父类方法的索引一样

（2）内联缓存：一种加快动态版绑定的缓存。其中涉及单态，多态，超多态的感念。java运行阶段，大部分的虚方法调用都是一个情况下回使用单态，实现快速动态绑定；如果是多态和超多态的情况下（多个实现类。。。）会去轮训多态内联缓存，将调用的方法拿出来。实际多态的内联缓存和方法表的调用已经没有多大区别了，甚至会因为不断写缓存的情况而使得
