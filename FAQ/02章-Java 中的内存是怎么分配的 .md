# Java 中的内存区域

Java 中的内存主要分为内存堆，方法栈和方法区。当然还有一些别的内存，比如PC寄存器，和 CPU 的 PC 寄存器一样，作用是指向下一个要执行的指令，但是我们写 Java 程序的没关系，一般不需要管。下面我们就说说内存堆，方法栈和方法区。

# 方法区

方法区是一块连续的内存，存放的是Class文件加载到Java虚拟机内部之后的数据。表现在我们写程序的地方，方法区里的东西，也就是我们里面说的Class类的实例。方法区里的东西，包含类中的变量的定义，静态变量的值和定义，方法（所有方法，不管是静态还是非静态）的字节码等数据。我们可以通过每个类的Class实例来获取这些数据，还记得反射吗？

所以方法区里的数据是相对来说静止的。因为一个类一旦加载进来，就不会变了。唯一会变的可能就是静态变量的值了吧。

所以说，一个类无论创建多少个实例，它都不会增加方法区占用的内存。因为方法区就好想一个模版，模子，印章，不因为复制出来多少个实例而变大或者变小。

# 内存堆（Heap）

好，接着是内存堆。

```
其实方法区也是一个堆。什么是堆呢？堆的英文名字叫做heap。是数据结构里一种标准的结构。

和链表（List），Map，队列（Queue）是一样的。

Java的内存堆，就是把一块连续的内存，按照堆这种数据结构组织起来。

堆的结构如其名。可以简单的认为就是一堆东西，下面大上面小。就好像一堆麦子什么的。树（Tree）这个数据结构就是堆的一种。
```

这里我们不去扯这些数据结构的东西。从帮助我们理解Java内存堆的作用的角度，我们可以再简化一点。可以认为Java内存堆就是一卷纸带，纸带上印着一个个大小相同的格子，比如一厘米一个格子（一个格子代表一个字节），每个格子都有一个编号（也就是内存地址）。

那么，我们写程序 new 一个类的对象，就是在这卷纸带上扯一段纸带，用来存放这个类的成员变量等数据。然后返回的就是这个这段纸带的开始第一个字节的地址。

比如说，`A a = new A();` 这段代码。new A()就是按照A占用的内存大小扯一段纸带，然后返回这段纸带的地址。紧接着把这个地址赋值给引用a。

这里补充一个知识点，一个对象占用的内存，是固定的。

先说数组，每种数据类型的数据，占用的内存大小是固定的，数组的长度也是创建的时候就确定的，所以一个数组，占用的内存就是数据类型大小 x 数组长度。当然，还有多位数组，但是我们说过，多维数组里，出了最低的一个维，其余的都是引用而已，指向低一个维度的数组对象引用。所以无论是多少维的数组，占用的内存都是一样的。

再说普通的类的对象。讲方法区的时候说过，方法字节码的数据，静态数据，变量定义等数据都在方法区里。那么一个对象占用的额外的数据，其实也就是成员变量。一个类里有几个成员变量是固定的。如果一个类就一个long，一个int，一个String引用，一个类A的引用，那么一个类应该占用多少内存呢？long 8 + int 4 + 引用 4 * 2 = 20个字节。当然每个对象还有一些额外的内存占用，这个所有的对象都是一样的，我们按下不表。可能还有一个问题没相同，就是那俩引用类型。这里我们要理解，String和类A的引用，其实是个引用，而不是对象本身。比如说类A的引用这个成员变量，这个引用可以指向A类的实例，A类的实例占用多少内存，这包含这个引用的对象没关系。当然A类的实例也可以是null，但是null也占内存。总言之，一个类的对象占用多少内存是固定的。

总结一下，堆就是给对象分配内存。堆里的数据可以通过内存地址访问。视频课里，我们说内存堆是挂满小本子的公告板，其实也是一个意思，小本子就是一段纸带，公告板的意思就是，这个纸带有地址，能根据地址访问。

# 方法栈（Stack）

好，说完了内存堆，剩下就是执行代码了。执行方法需要的内存，都是在栈里分配的。栈，就是stack，是一种先进后出的数据结构。就好像摞盘子一样，最先摞的最后拿出来，最后摞的最先拿出来。

执行方法需要什么内存呢？参数和局部变量。每次执行一个方法，都要为这个方法的执行创建其中定义的参数和局部变量。

那么这里我们再来想通一个事情。那就是每次执行一个方法，占用的内存都是一样的。因为一个方法的参数和定义的局部变量的数量是一样的。可能有一个疑问就是，循环中定义的局部变量怎么办？其实细想，循环中定义的变量，其实是被重用的。一个for循环，里面定义了一个 int 变量。无论循环多少次，新的循环开始的时候，就可以重用之前定义的变量占用的内存，因为一个循环一旦结束，它占用的内存就没用了，可以被下次循环重新使用。

Stack的这个先进后出的特性，和方法调用时一样的。比如m1方法调用m2方法，m2方法调用m3方法，那么过程会是这样的。

 - 先调用m1，根据m1的代码，确定m1执行的时候需要多少内存，在栈上分配相应大小的内存供m1执行使用，同时将分配的内存进栈；
 - 执行过程中调用了m2，同样的过程，在栈上分配m2需要的内存，然后内存进栈；
 - 执行m2的时候，m2调用m3，还是分配m3需要的内存，然后内存进栈。
 - 好了，m3执行完毕，释放m3占用的内存（内存出栈），然后是m2继续执行，执行完毕内存释放，最后是m1执行，执行完毕内存释放

那么，如何访问栈上内存呢？既然每个方法执行需要的内存都是固定的，那么在分配的这一截内存上，每个变量的地址都是固定的。正所谓冥冥中自有定数，正是此意。所以这个和堆上的内存是不一样的。堆上的内存需要根据引用，找到堆上对应的对象，然后每个成员变量对应的内存地址，也都是冥冥中自有定数了。


***

这篇文章来自极客时间推出的[《零基础学Java》](https://time.geekbang.org/course/intro/181)中的FAQ。除了在每节视频课下方回答大家的问题之外，针对大家提出的优质问题或者普遍问题，如果需要更大篇幅的文章解答，则会在FAQ中以文章的方式给出回答。带你零基础入门，夯实Java，课程地址：https://time.geekbang.org/course/intro/181


