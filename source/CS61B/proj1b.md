---
title: proj1b
date: 2025-06-27
cssclasses:
  - CS61B
---
---
本来没打算写这篇博客的，但是这里的扩/缩容挺有意思的，于是就简单写写

---

**目标：** 
* 理解数据结构中后备数组的实现。
* 通过测试和测试驱动开发来验证这些数据结构的正确性，获得更多经验。  

这次的`proj`就是实现一个循环数组，这里不放图了

---
#### Resizing Up 

扩/缩容的底层逻辑就是新建一个数组，把旧数组的数据迁移过去就好了。
不过我写的不太优雅，这里不多解释，只是浅浅提一下要注意的地方

```java
public class ArrayDeque61B<T> implements Deque61B<T> {  
    int size;  
    int nextFirst;  
    int nextLast;  
    T[] lst;  
  
    public ArrayDeque61B() {  
        this.size = 0;  
        this.nextFirst = 0;  
        this.nextLast = 1;  
        this.lst = (T[]) new Object[8];  
    }
```

```java 
// 扩容
if (((double) this.lst.length / this.size) < 1.7) {  
    T[] lst1 = (T[]) new Object[2 * this.lst.length];  
    for (int i = 1; i < this.lst.length; i++ ) {  
        lst1[i] = this.lst[ Math.floorMod((this.nextFirst + i), this.lst.length)]; 
    }  
    this.lst = lst1;  
    lst1 = null;  
    this.nextLast = this.size + 1; // 插入从索引1开始  
    this.nextFirst = 0;  
}
```

* `if ((this.lst.length / this.size) > 0.6)`: 
	* `/ 运算符`:当两个操作数均为整数类型（如 `int`、`long`）时，会自动截断小数部分，返回整数商。
	* `((double) ...)`: 将数组容量转换为 double 类型，避免整数除法截断小数部分。
* ` for (int i = 1; i < this.lst.length; i++ )`: 理论上应该不会发生当`i`遍历到`this.nextLast`后，添加入本不应该包含的元素(删除时没有更改元素值位`null`,只是移动了`this.nestLast`),但是保险起见还是像下面缩容的写法最好
* `lst1 = null;`: 释放内存  
* `this.nextLast = ... ; this.nextFirst = ... ;`： 注意要改,最好和声明时保持一致，不然测试很麻烦


```java 
// 缩容
if ((((double)this.lst.length / this.size) > 3) && this.size != 0) {  
    int num = Math.max(this.lst.length / 2 ,3);  
    T[] lst2 = (T[]) new Object[num];  
    for (int i = 0; i <= this.size; i++){  
        lst2[i] = this.lst[Math.floorMod((this.nextFirst + 1 + i),this.lst.length)];  
    }  
    this.lst = lst2;  
    lst2 = null;  
    this.nextLast = this.size;  
    this.nextFirst = this.lst.length - 1;  
}```

* 这里和上面扩容差不多，就不多写了