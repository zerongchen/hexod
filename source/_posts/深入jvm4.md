---
title: 深入jvm4
date: 2019-06-07 15:51:56
tags:
---
本文的目的是介绍一个工具ASM，从字节码层面分析代码，修改字字节码修改框架，开始ASM之前，先了解下访问者模式
## 访问者模式（Visitor Pattern）
意图：主要将数据结构与数据操作分离。

主要解决：稳定的数据结构和易变的操作耦合问题。

##### 何时使用：需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，使用访问者模式将这些封装到类中。

如何解决：在被访问的类里面加一个对外提供接待访问者的接口。

关键代码：在数据基础类里面有一个方法接受访问者，将自身引用传入访问者。

show me the code

```
interface Element {
   // 1. accept(Visitor) interface
   public void accept( Visitor v ); // first dispatch
}

class This implements Element {
   // 1. accept(Visitor) implementation
   public void   accept( Visitor v ) {
     v.visit( this );
   } 
   public String thiss() {
     return "This";
   }
}

class That implements Element {
   public void   accept( Visitor v ) {
     v.visit( this );
   }
   public String that() {
     return "That";
   }
}

class TheOther implements Element {
   public void   accept( Visitor v ) {
     v.visit( this );
   }
   public String theOther() {
     return "TheOther"; 
   }
}

// 2. Create a "visitor" base class with a visit() method for every "element" type
interface Visitor {
   public void visit( This e ); // second dispatch
   public void visit( That e );
   public void visit( TheOther e );
}

// 3. Create a "visitor" derived class for each "operation" to perform on "elements"
class UpVisitor implements Visitor {                   
   public void visit( This e ) {
      System.out.println( "do Up on " + e.thiss() );
   }
   public void visit( That e ) {
      System.out.println( "do Up on " + e.that() );
   }
   public void visit( TheOther e ) {
      System.out.println( "do Up on " + e.theOther() );
   }
}

class DownVisitor implements Visitor {
   public void visit( This e ) {
      System.out.println( "do Down on " + e.thiss() );
   }
   public void visit( That e ) {
      System.out.println( "do Down on " + e.that() );
   }
   public void visit( TheOther e ) {
      System.out.println( "do Down on " + e.theOther() );
   }
}

class VisitorDemo {
   public static Element[] list = { new This(), new That(), new TheOther() };

   // 4. Client creates "visitor" objects and passes each to accept() calls
   public static void main( String[] args ) {
      UpVisitor    up   = new UpVisitor();
      DownVisitor  down = new DownVisitor();
      for (int i=0; i < list.length; i++) {
         list[i].accept( up );
      }
      for (int i=0; i < list.length; i++) {
         list[i].accept( down );
      }
   }
}
```
以上最终的结果是

```
do Up on This                do Down on This
do Up on That                do Down on That
do Up on TheOther            do Down on TheOther
```
### ASM 里面正是使用了访问者的模式
被访问者（Acceptors） 有 ClassReader class, the MethodNode class

访问者（Visitors interface）有 ClassVisitor, AnnotationVisitor, FieldVisitor, MethodVisitor.



