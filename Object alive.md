> Heap堆中存放着Java中几乎所有的对象，垃圾回收前，如何确定对象已死，对象需要被回收？

## 引用计数算法
> 给对象添加一个引用计数器，被引用了计数器+1，引用失效计数器-1.当计数器为0时，表示对象不能被使用，已经死亡了。但是这个方法不能解决对象循环引用。
## 可达性算法
> 通过一组`GC ROOT`对象作为起点，从节点向下搜索，当一个对象到GCROOT之间没有任何引用链时，对象不可用，对象已死亡。  
> 引用链：GCROOT向下搜索所走过的路径
### 可作为GCROOT的对象
- 虚拟机栈中的引用对象
- 方法区中静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中的JNI引用的对象
### 不可达对象是否一定会被回收？
> 答案是否定的，因为一个对象对象被回收，会有两个标记过程，第一个标记过程即是可达性分析时的一次标记;对这些对象进行一次筛选，筛选条件是是否有必要执行`finalize`方法,如果对象未覆盖`finalize`或者对象的`finalize`已经被执行过的，虚拟机将不再执行`finalize`;将筛选通过的对象放置在`F-Queue`中，有虚拟机创建低等级`finalize`线程触发`finalize()`,在这个时候如果对象与引用链有联系上了，那么该对象不会被回收。  
> 一个对象只会调用一次`finalize（）`  
> `finalize`线程触发`finalize()`，但是虚拟机不会等待其执行完成。原因，如果阻塞的话，当某个对象的回收出现问题，将会引用全局的回收。

## 谈引用
### 强引用
> 对象普通的创建方式就是强引用
```java
 Object obj = new Object();
```
> 回收：只要引用存在，就不会回收
### 软引用
> SoftReference方式创建
```java
String a = "This is Demo";
SoftReference<String> soft = new SoftReference<>(a);
```
> 回收：当内存溢出异常之前，回收软引用对象，如果回收之后依旧内存溢出，就会跑出OutOfMemory
### 弱引用
> WeakReference 创建
```java
String a = "This is Demo";
WeakReference<String> soft = new WeakReference<>(a);
```
> 回收：只要有垃圾收集，这类对象一定被回收
### 虚引用
> WeakReference 创建
```java
String a = "This is Demo";
PhantomReference<String> soft = new PhantomReference<>(a);
```
> 这个引用没有任何的影响。唯一目的是能在这个对象被收集器回收时收到一个系统通知