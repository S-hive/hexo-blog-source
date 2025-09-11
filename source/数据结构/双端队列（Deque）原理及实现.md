---
date: 2025-08-17
title: 双端队列（Deque）原理及实现
---
## [基本原理](https://labuladong.online/algo/data-structure-basic/deque-implement/#%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86) 

```java
class MyDeque<E> {
    // 从队头插入元素，时间复杂度 O(1)
    void addFirst(E e);

    // 从队尾插入元素，时间复杂度 O(1)
    void addLast(E e);

    // 从队头删除元素，时间复杂度 O(1)
    E removeFirst();

    // 从队尾删除元素，时间复杂度 O(1)
    E removeLast();

    // 查看队头元素，时间复杂度 O(1)
    E peekFirst();

    // 查看队尾元素，时间复杂度 O(1)
    E peekLast();
}
```

[标准队列](#队列、栈基本原理)只能在队尾插入元素，队头删除元素，而双端队列的队头和队尾都可以插入或删除元素。

## [用链表实现双端队列](https://labuladong.online/algo/data-structure-basic/deque-implement/#%E7%94%A8%E9%93%BE%E8%A1%A8%E5%AE%9E%E7%8E%B0%E5%8F%8C%E7%AB%AF%E9%98%9F%E5%88%97) 

```java
import java.util.LinkedList;

public class MyListDeque<E> {
    private LinkedList<E> list = new LinkedList<>();

    // 从队头插入元素，时间复杂度 O(1)
    void addFirst(E e) {
        list.addFirst(e);
    }

    // 从队尾插入元素，时间复杂度 O(1)
    void addLast(E e) {
        list.addLast(e);
    }

    // 从队头删除元素，时间复杂度 O(1)
    E removeFirst() {
        return list.removeFirst();
    }

    // 从队尾删除元素，时间复杂度 O(1)
    E removeLast() {
        return list.removeLast();
    }

    // 查看队头元素，时间复杂度 O(1)
    E peekFirst() {
        return list.getFirst();
    }

    // 查看队尾元素，时间复杂度 O(1)
    E peekLast() {
        return list.getLast();
    }

    public static void main(String[] args) {
        MyListDeque<Integer> deque = new MyListDeque<>();
        deque.addFirst(1);
        deque.addFirst(2);
        deque.addLast(3);
        deque.addLast(4);

        System.out.println(deque.removeFirst()); // 2
        System.out.println(deque.removeLast()); // 4
        System.out.println(deque.peekFirst()); // 1
        System.out.println(deque.peekLast()); // 3
    }
}
```

## [用数组实现双端队列](https://labuladong.online/algo/data-structure-basic/deque-implement/#%E7%94%A8%E6%95%B0%E7%BB%84%E5%AE%9E%E7%8E%B0%E5%8F%8C%E7%AB%AF%E9%98%9F%E5%88%97) 
也很简单吧，直接复用我们在 [环形数组技巧](环形数组技巧及实现)中实现的 `CycleArray` 提供的方法就行了。环形数组头尾增删元素的复杂度都是 O(1)O(1)：

```java
class MyArrayDeque<E> {
    private CycleArray<E> arr = new CycleArray<>();

    // 从队头插入元素，时间复杂度 O(1)
    void addFirst(E e) {
        arr.addFirst(e);
    }

    // 从队尾插入元素，时间复杂度 O(1)
    void addLast(E e) {
        arr.addLast(e);
    }

    // 从队头删除元素，时间复杂度 O(1)
    E removeFirst() {
        return arr.removeFirst();
    }

    // 从队尾删除元素，时间复杂度 O(1)
    E removeLast() {
        return arr.removeLast();
    }

    // 查看队头元素，时间复杂度 O(1)
    E peekFirst() {
        return arr.getFirst();
    }

    // 查看队尾元素，时间复杂度 O(1)
    E peekLast() {
        return arr.getLast();
    }
}
```