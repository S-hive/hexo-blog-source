### 构造方法

1. **`PriorityQueue()`**：创建一个**默认**初始容量（为 11）的最小堆（优先队列）。


```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
```

>**`PriorityQueue`的初始容量和它最终能存储的元素数量没有必然限制**。
>1. 即使后续元素增多，`PriorityQueue` 会自动扩容，不影响功能

2. **`PriorityQueue(int initialCapacity)`**：创建一个**指定**初始容量的最小堆。

```java
PriorityQueue<Integer> pq = new PriorityQueue<>(20); // 初始容量为20
```

3. **`PriorityQueue(Comparator<? super E> comparator)`**：创建一个使用自定义比较器的优先队列，可以根据需求构建最小堆或最大堆。例如，构建最大堆：

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
```

4. **`PriorityQueue(Collection<? extends E> c)`**：根据一个集合来创建优先队列，集合中的元素会被添加到优先队列中。

```java
List<Integer> list = List.of(5, 3, 8, 1);
PriorityQueue<Integer> pqFromList = new PriorityQueue<>(list);
```

### 插入操作

- **`offer(E e)`：将指定元素插入到优先队列（堆）中。如果插入成功，返回 `true`；** 如果由于容量限制插入失败（在有界队列中，`PriorityQueue` 是无界的，一般不会出现这种情况），返回 `false`。它的时间复杂度为 O(log n)，其中 n 是堆中元素的数量。

- **`add(E e)`：也是用于将元素插入到优先队列中**，功能和 `offer` 类似。不过，当 `PriorityQueue` 是无界队列时，它总是返回 `true`；如果在有界队列中插入失败，会抛出 `IllegalStateException` 异常。
### 删除操作

- **`poll()`：移除并返回优先队列（堆）的队头元素**（即最小堆中的最小值，最大堆中的最大值）。如果优先队列为空，返回 `null`。时间复杂度为 O(log n)。

- **`remove(Object o)`**：从优先队列中移除指定元素的一个实例（如果存在）。返回 `true` 表示成功移除，`false` 表示元素不存在。时间复杂度为 O(n)，因为需要在堆中查找该元素。

### 获取操作

- **`peek()`：返回优先队列（堆）的队头元素，但不将其从队列中移除。**如果优先队列为空，返回 `null`。时间复杂度为 O(1)。

-  **`size()`**：返回优先队列中元素的数量，时间复杂度为 O(1)。

- **`isEmpty()`**：检查优先队列是否为空，如果为空返回 `true`，否则返回 `false` ，时间复杂度为 O(1)。

### 其他操作

- **`clear()`**：从优先队列中移除所有元素，将队列清空。时间复杂度为 O(n)，其中 n 是队列中元素的数量。