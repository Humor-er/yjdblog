---
layout: post
title:  "翻译CS 61B Lecture 7 - 列表"
date:   2014-03-25 22:53:49
month:  "03"
day:    25
moto: “追求的终极永远是朦胧的，要逃避痛苦，最常见的就是躲进未来。在时间的轨道上，人们总想象有一条线，超脱了这条线，当前的痛苦也许就会永不复存在。” ---- 《米兰昆德拉》
---

                              CS 61B: Lecture 7
                         Wednesday, February 5, 2014

Today's reading:  Goodrich & Tamassia, Section 3.2.

LISTS
=====
Let's consider two different data structures for storing a list of things:
an array and a linked list.

	今天我们会探讨用于存储列表事物的两个不同的数据结构，它们分别是数组和链表。

An array is a pretty obvious way to store a list, with a big advantage:  it
enables very fast access of each item.  However, it has two disadvantages.

	数组用于存储列表数据有一个很明显的优点：快速定位和访问每一个数据项。但是它也有两个劣势。

First, if we want to insert an item at the beginning or middle of an array, we
have to slide a lot of items over one place to make room.  This takes time
proportional to the length of the array.

	第一，如果我们想在数组的起始和中间位置插入一个元素，我们就必须挪动数组中许多元素的位置来
	腾出一个空间来存储这个新的元素。这个操作消耗的时间与数组的长度成正比。

Second, an array has a fixed length that can't be changed.  If we want to add
items to the list, but the array is full, we have to allocate a whole new array
and move all the ints from the old array to the new one.

	第二，一个数组通常是有一个固定大小。如果我们想增加元素到列表中，但是数组已经满了，我们就
	必须分配一个全新的数组，并把所有的数据从旧的数组搬到新的数组中。

  <br />


```java
public class AList {
  int a[];
  int lastItem;

  public AList() {
    a = new int[10];                           // The number "10" is arbitrary.
    lastItem = -1;
  }

  public void insertItem(int newItem, int location) {
    int i;

    if (lastItem + 1 == a.length) {               // No room left in the array?
      int b[] = new int[2 * a.length];  // Allocate a new array, twice as long.
      for (i = 0; i <= lastItem; i++) {      // Copy items to the bigger array.
        b[i] = a[i];
      }
      a = b;                   // Replace the too-small array with the new one.
    }
    for (i = lastItem; i >= location; i--) {       // Shift items to the right.
      a[i + 1] = a[i];
    }
    a[location] = newItem;
    lastItem++;
  }
}
```

LINKED LISTS (a recursive data type) (一种递归式的数据类型)
============
We can avoid these problems by choosing a Scheme-like representation of lists.
A linked list is made up of _nodes_.  Each node has two components:  an item,
and a reference to the next node in the list.  These components are analogous
to "car" and "cdr".  However, our node is an explicitly defined object.

	我们可以通过使用类似Scheme的列表形式来规避这种问题。链表是由多个节点组成，
	并且每个节点都包含两个属性：数据项和指向链表的下一个节点的引用。这两个属性
	有点类似于Lisp(Scheme)的"car"(一个list的car就是这个list的第一个元素)和"cdr"
	(一个list的cdr就是这个list的除第一个元素以外的其余元素)函数。但是，节点又不同
	于"car"和"cdr"，它是明确地被定义为对象而不是函数。


```java
public class ListNode {          // ListNode is a recursive type
  public int item;
  public ListNode next;          // Here we're using ListNode before
}                                //   we've finished declaring it.
```

Let's make some ListNodes.

	先创建一些链表节点。

```java
ListNode l1 = new ListNode(), l2 = new ListNode(), l3 = new ListNode();
l1.item = 7;
l2.item = 0;
l3.item = 6;
```

  <br/>

	       -------------         -------------         -------------
	       |     ----- |         |     ----- |         |     ----- |
	       | item| 7 | |         | item| 0 | |         | item| 6 | |
	  l1-->|     ----- |    l2-->|     ----- |    l3-->|     ----- |
	       |     ----- |         |     ----- |         |     ----- |
	       | next| ? | |         | next| ? | |         | next| ? | |
	       |     ----- |         |     ----- |         |     ----- |
	       -------------         -------------         -------------

Now let's link them together.

    然后再把它们链接起来。

```java
l1.next = l2;
l2.next = l3;
```

What about the last node?  We need a reference that doesn't reference anything.
In Java, this is called "null".

    最后一个节点是怎么处理的呢？一般我们会把最后一个节点的指向下一节点的引用不指向
    任何节点。在Java，我们称之为"null"。

```java
l3.next = null;
```

  <br/>

         -------------         -------------         -------------
         |     ----- |         |     ----- |         |     ----- |
         | item| 7 | |         | item| 0 | |         | item| 6 | |
    l1-->|     ----- |    l2-->|     ----- |    l3-->|     ----- |
         |     ----- |         |     ----- |         |     ----- |
         | next| .-+-+-------->| next| .-+-+-------->| next| X | |
         |     ----- |         |     ----- |         |     ----- |
         -------------         -------------         -------------

To simplify programming, let's add some constructors to the ListNode class.

    为了简化程序，我们可以在链表节点类中定义一些构造方法。

```java
public ListNode(int i, ListNode n) {
  item = i;
  next = n;
}

public ListNode(int i) {
  this(i, null);
}
```

These constructors allow us to emulate Scheme's "cons" operation.

    这些构造方法可以使得我们能够模仿Scheme的"cons"(用于构造列表的方法)函数。

```java
ListNode l1 = new ListNode(7, new ListNode(0, new ListNode(6)));
```

Linked lists vs. array lists (链表 vs 数组)
----------------------------
Linked lists have several advantages over array-based lists.  Inserting an item
into the middle of a linked list takes just a small constant amount of time, if
you already have a reference to the previous node (and don't have to walk
through the whole list searching for it).  The list can keep growing until
memory runs out.

    链表比起数组有不少优势。如果已经知道了前一节点的引用对象，那么插入一个元素到
    链表中间只需要付出很少的时间代价(常数时间)，链表能持续扩展直到内存用完为止。


The following method inserts a new item into the list immediately after "this".
    
    下面的方法是在当前元素的下一个位置插入一个新元素。

```java
public void insertAfter(int item) {
  next = new ListNode(item, next);
}

l2.insertAfter(3);
```
         -------------       -------------   -------------       -------------
         |     ----- |       |     ----- |   |     ----- |       |     ----- |
         | item| 7 | |       | item| 0 | |   | item| 3 | |       | item| 6 | |
    l1-->|     ----- |  l2-->|     ----- |   |     ----- |  l3-->|     ----- |
         |     ----- |       |     ----- |   |     ----- |       |     ----- |
         | next| .-+-+------>| next| .-+-+-->| next| .-+-+------>| next| X | |
         |     ----- |       |     ----- |   |     ----- |       |     ----- |
         -------------       -------------   -------------       -------------

However, linked lists have a big disadvantage compared to arrays.  Finding the
nth item of an array takes a tiny, constant amount of time.  Finding the nth
item of a linked list takes time proportional to n.  You have to start at the
head of the list and walk forward n - 1 nodes, one "next" at a time.

    但是，链表依然存在一个相对于数组较不利的劣势。当我们要寻找数组第n个元素时，
    只需要消耗几乎可以忽略不算的时间(常数时间)。但对于链表而言，查询时间是与链
    表的大小成正比。你必须从头开始遍历n - 1个节点，每次只能递进一个节点。

Many of the data structures we will study in this class will be attempts to
find a compromise between arrays and linked lists.  We'll learn data structures
that are fast for both arbitrary lookups (like arrays) _and_ arbitrary
insertions (like linked lists).
    
    我们将要学习的许多数据结构都将去尝试结合从数组和链表两者，得出一个折中的方案。
    既能做到快速的随机查询(像数组一样)，也能做到快速的随机插入(像链表一样)。

Lists of Objects
----------------
For greater generality, let's change ListNodes so that each node contains not
an int, but a reference to any Java object.  In Java, we can accomplish this by
declaring a reference of type Object.

    更通用的做法是把节点原本只包含int类型的数据项改成任意一个Java对象的引用。在Java，
    我们通常声明一个Object类型的引用作为节点的数据项属性。

```java
public class SListNode {
  public Object item;
  public SListNode next;
}
```

The "S" in "SListNode" stands for singly-linked.  This will make sense when we
contrast these lists with doubly-linked lists later.  You'll see the SListNode
class in next week's lab and homework.

    "SListNode"中的"S"代表的是单向的链表。当我们把这种类型的链表与双向链表对比的
    时候就会看到它的意义。我们将在下星期的每周实验和家庭作业中看到这样的对比。

A List Class
------------
There are two problems with SListNodes.

    单向链表存在两个问题。

(1)  Suppose x and y are pointers to the same shopping list.  Suppose we insert
     a new item at the beginning of the list thusly:

    假设x和y是同时指向shopping链表的两个指针。假如我们在链表的起始位置插入一个
    新的元素的时候：
    
```java
 x = new SListNode("soap", x);
```


y doesn't point to the new item; y still points to the second item in x's
list.  If y goes shopping for x, he'll forget to buy soap.
    
    y不是指向新的元素，而是仍然指向x链表中的第二个元素。那么当y帮x购买商品的时候，
    它将忘记买肥皂。(比喻当y遍历链表x的时候，将无法找到soap节点)

(2)  How do you represent an empty list?  The obvious way is "x = null".
     However, Java won't let you call a SListNode method--or any method--on
     a null object.  If you write "x.insertAfter(item)" when x is null, you'll
     get a run-time error, even though x is declared to be a SListNode.
     (There are good reasons for this, which you'll learn later in the course.)
    
    怎么去定义一个空链表？最简显得方法就是"x = null"。但是，Java不允许让你调用
    一个null对象上的任何一个方法。如果你写了这么一行代码"x.insertAfter(item)",
    但是x是null的话，就会抛出一个运行时异常，即使x的类型已经被声明为SListNode。

The solution is a separate SList class, whose job is to maintain the head
(first node) of the list.  We will put many of the methods that operate on
lists in the SList class, rather than the SListNode class.

    解决这个问题的方法是分离出一个SList类，使得它只需要维护链表的头结点(第一个节点)。
    那么我们就可以在SList类中添加对链表的不同操作的对应的方法，而不是定义在SListNode类。

```java
public class SList {
  private SListNode head;             // First node in list.
  private int size;                   // Number of items in list.

  public SList() {                    // Here's how to represent an empty list.
    head = null;
    size = 0;
  }

  public void insertFront(Object item) {
    head = new SListNode(item, head);
    size++;
  }
}
```
                 SList object                    SListNode object
                 -------------                     -------------  String object
      -----      |     ----- |                     |     ----- |     ----------
    x | .-+----->| size| 1 | |                     | item| .-+-+---->|  milk  |
      -----      |     ----- |                     |     ----- |     ----------
      -----      |     ----- |                     |     ----- |
    y | .-+----->| head| .-+-+-------------------->| next| X | |
      -----      |     ----- |                     |     ----- |
                 -------------                     -------------

Now, when you call x.insertFront("fish"), every reference to that SList can see
the change.

    现在，当你调用x.insertFront("fish")时，每一个SList的引用都能够及时的看到它的更新。

              SList           SListNode                SListNode
              -------------   -------------            -------------
      -----   |     ----- |   |     ----- |  --------  |     ----- |  --------
    x | .-+-->| size| 2 | |   | item| .-+-+->| fish |  | item| .-+-+->| milk |
      -----   |     ----- |   |     ----- |  --------  |     ----- |  --------
      -----   |     ----- |   |     ----- |            |     ----- |
    y | .-+-->| head| .-+-+-->| next| .-+-+----------->| next| X | |
      -----   |     ----- |   |     ----- |            |     ----- |
              -------------   -------------            -------------

Another advantage of the SList class is that it can keep a record of the
SList's size (number of SListNodes).  Hence, the size can be determined more
quickly than if the SListNodes had to be counted.
    
    另外一个好处是，SList类能够维护一个SList的大小(节点数)的属性。那么，当我们
    需要知道链表的大小的时候，我们就能直接从SList类中直接获取到，而不是还要去
    遍历所有的节点才能统计出这个大小。


