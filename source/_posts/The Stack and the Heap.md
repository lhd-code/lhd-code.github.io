---
title: The_Stack_and_the_Heap
date: 2020-10-07 21:01:55
tags: [translation, rust]
categories: programming




---

[原文链接](https://doc.rust-lang.org/1.22.0/book/first-edition/the-stack-and-the-heap.html#the-stack-and-the-heap)

（英文渣，理解优先）

<!--more-->

# The Stack and the Heap

作为一门系统语言 Rust 会像低级语言一样运行。所以如果你习惯于高级语言，将会对一些系统编程的方面感到陌生。其中最重要的是内存怎么通过栈和堆工作。如过你对类C语言如何实现栈分配有一定了解，那么本章可以作为复习。如果没有那么你将学习这个普遍概念，不过更侧重于 Rust。

与大多数事情一样，我们将从简单的模型开始来学习它们。这样就可以把握一些基础知识，而不至于陷入一些目前不相关的细节。我们使用的例子也许并不是 100% 准确，但是可以代表我们现在的学习水平。一旦掌握了基础，知道了如何实现分配器，虚拟内存，还有其他的高级主题的信息，将会对这个抽象物窥见一斑。

## 内存管理

栈和堆——与内存管理有关，是用来帮助你确定何时分配或取消内存的抽象概念。

这里是一个高级的比较：

栈处理速度非常快，而且是 Rust 中默认分配的地方。但是对于函数调用，只能进行本地分配，而且对于内存大小有限制。

相对的，堆处理速度较慢，而且需要由你的程序进行分配。但是它对于大小没有限制，还可以全局访问。不过需要注意的是，以任意顺序分配任意大小的内存块的堆与堆数据结构的含义是有区别的。

### 栈

看一个 Rust 程序：

```rust
fn main() {
	let x = 42;
}
```

这段程序，有一个变量绑定，`x` 。这个内存需要被分配到某个地方。Rust 默认情况下进行“栈分配”，这意味着基础数据“分配到栈”上。这意味着什么？

当一个函数被调用，会为其所有的局部变量和一些其他的信息分配内存空间。这被称为“栈框架”，然后因为这个教程的主要目的，我们将省略掉那些多余的信息只考虑那些被分配的局部变量。所以在这种情况，当 `main()` 运行时，