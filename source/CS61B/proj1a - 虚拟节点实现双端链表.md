---
title: proj1a - 虚拟节点实现双端链表
date: 2025-06-16
cssclasses:
  - CS61B
---
**目标：** 
* 理解数据结构中辅助链表的使用。
* 有使用测试和测试驱动开发来评估自己数据结构正确性的经验。

我参考的结构：
![Pastedimage20250617210149.png](/images/Pastedimage20250617210149.png)

这个`proj1a` 整体不难，这个结构挺有意思的，我在写`python` 的时候用的结构都是头尾两个`sentinel`节点，第一次实现这个结构，发现这个结构更加优雅简单。

---

### JUnit Tests
 you’ll need to create a `Node` class and introduce one or more instance variables.

刚开始我希望实现一个基于数组的链表：
![Pastedimage20250616212809.png](/images/Pastedimage20250616212809.png)
提示运行错误：
![[Pastedimage20250616212818.png]](/images/Pastedimage20250616212818.png)
我发现应该是要实现一个双链表结构(认真读题真的很重要)：
![[Pastedimage20250616213051.png]](/images/Pastedimage20250616213051.png)
提示：
![[Pastedimage20250616213103.png]](/images/Pastedimage20250616213103.png)
就是内部类`Node`不该自己搞泛型，用外部类的泛型，把内部类的泛型声明去掉 ：
![[Pastedimage20250616213139.png]](/images/Pastedimage20250616213139.png)

[707. 设计链表 - 力扣](https://leetcode.cn/problems/design-linked-list/submissions/603212018/)参考了下这个。

### Writing and Verifying 
下面都是先编写测试，然后实现它，就是“测试驱动开发”。我不详细写了，写一些有意思的：
### The Remaining Methods
[Truth测试库](https://truth.dev/api/latest/index.html?overview-summary.html)
这里要求用递归实现`get()`方法：
```java
    @Override  
    public T getRecursive(int index) {  
        if (index >= this.length || index < 0) {  
            return null;  
        } else {  
            List<T> lst = toList();  
            return getRecindex(lst,index);  
            }  
        }  
  
    public T getRecindex(List<T> lst,int idx) {  
        if (idx == 0) {  
            return lst.get(idx);  
        }else {  
            List<T> newLst = lst.subList(1,lst.size());  
            getRecindex(newLst,idx-1);  
        }  
        return null;  
    }  
}
```
然后测试一直返回`null`:
![[Pastedimage20250619132929.png]](/images/Pastedimage20250619132929.png)
其实是老毛病了，写`python`的时候也经常犯，递归调用的返回值被直接丢弃了，方法最终会执行 return null;。因此，无论递归过程中是否正确找到了目标元素，最终都会返回 null。  
```java
@Override  
public T getRecursive(int index) {  
    if (index >= this.length || index < 0) {  
        return null;  
    } else {  
        List<T> lst = toList();  
        return getRecindex(lst,index);  
        }  
    }  
  
public T getRecindex(List<T> lst,int idx) {  
    if (idx == 0) {  
        return lst.get(idx);  
    }else {  
        List<T> newLst = lst.subList(1,lst.size());  
        // getRecindex(newLst,idx-1); 
        return getRecindex(newLst,idx-1);  
    }  
}
```
最后放个胜利结算画面
![[Pastedimage20250619142148.png]](/images/Pastedimage20250619142148.png)