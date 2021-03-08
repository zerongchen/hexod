---
title: 深入java9
date: 2019-07-08 23:33:47
tags:
---

@Target({ ElementType.TYPE, ElementType.FIELD })
@Target用来限定目标注解所能标注的 Java 结构 (比如上面的类和字段；该注解可以作用在类和字段上)


注解共有三种不同的生命周期：SOURCE，CLASS或RUNTIME，分别表示注解只出现在源代码中，只出现在源代码和字节码中，以及出现在源代码、字节码和运行过程中。

