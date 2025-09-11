---
title: proj1c - 迭代、比较器和对象方法
---


### 若想在一个类中实现多个比较逻辑，可以通过以下两种常用方式实现：

 **一、使用多个内部类实现 `Comparator` 接口**
```java
import java.util.Comparator;

// 假设要比较的实体类
class Student {
    String name;
    int age;
    double score;

    public Student(String name, int age, double score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }
}

// 主类（可以是任何类，无需直接实现Comparator）
class StudentComparators {
    // 比较器1：按年龄升序
    public static final Comparator<Student> BY_AGE = new Comparator<Student>() {
        @Override
        public int compare(Student s1, Student s2) {
            return Integer.compare(s1.age, s2.age);
        }
    };

    // 比较器2：按分数降序
    public static final Comparator<Student> BY_SCORE_DESC = new Comparator<Student>() {
        @Override
        public int compare(Student s1, Student s2) {
            return Double.compare(s2.score, s1.score); // 注意降序是s2在前
        }
    };

    // 比较器3：按姓名字典序
    public static final Comparator<Student> BY_NAME = new Comparator<Student>() {
        @Override
        public int compare(Student s1, Student s2) {
            return s1.name.compareTo(s2.name);
        }
    };
}

// 使用方式
public class Main {
    public static void main(String[] args) {
        Student s1 = new Student("Alice", 20, 90.5);
        Student s2 = new Student("Bob", 19, 88.0);

        // 按年龄比较
        int ageCompare = StudentComparators.BY_AGE.compare(s1, s2);
        
        // 按分数比较
        int scoreCompare = StudentComparators.BY_SCORE_DESC.compare(s1, s2);
    }
}
```
**二、Java 8+：使用 Lambda 表达式简化比较器**

```java
import java.util.Comparator;

class Student {
    String name;
    int age;
    double score;

    public Student(String name, int age, double score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }
}

// 直接用静态字段定义Lambda比较器
class StudentComparators {
    // 按年龄升序
    public static final Comparator<Student> BY_AGE = (s1, s2) -> Integer.compare(s1.age, s2.age);
    
    // 按分数降序
    public static final Comparator<Student> BY_SCORE_DESC = (s1, s2) -> Double.compare(s2.score, s1.score);
    
    // 按姓名字典序
    public static final Comparator<Student> BY_NAME = (s1, s2) -> s1.name.compareTo(s2.name);
}
```

### 不会执行嵌套类中的测试
注：使用的是 JUnit 等测试框架

![[Pasted image 20250825154341.png]]

**嵌套类（`listNextLastMax`）未被识别为 “测试类”**

测试框架（如 JUnit）对 “测试类” 有特定要求：
- 通常需要是**顶级类**（非嵌套类），或被 `@Nested` 注解标记的嵌套类（JUnit 5 支持）。
- 类名通常以 `Test` 结尾（框架默认扫描规则）。
你的 `listNextLastMax` 是**静态内部类**，且命名不含 `Test`，若未用 `@Nested` 标记（JUnit 5），测试框架可能**不会将其视为测试类**，自然不会执行其中的测试方法。

 **类中没有符合规范的 “测试方法”**

即使嵌套类被识别，测试方法也需满足框架要求（以 JUnit 为例）：
- 方法必须被 `@Test` 注解标记。
- 方法需是 `public void` 类型，无参数。
而你的 `listNextLastMax` 类中**只有 `compare` 方法**（实现 `Comparator` 的业务方法），**没有任何测试方法**（带 `@Test` 的方法）。测试框架扫描时发现没有可执行的测试方法，因此提示 “不会执行测试”。

![[Pasted image 20250825160734.png]]
搞定！


### 为什么无法传入参数
期望比较器能比较两个`MaxArrayDeque61B<T>`实例的`nextLast`参数。

![[Pasted image 20250825162459.png]]
**参数类型不匹配**，无法在 `MaxArrayDeque61B<T>` 类内部使用其自身的比较器来比较两个 `MaxArrayDeque61B<T>` 实例，相当于自己抱起自己。

`listNextLastTest` 实现了 `Comparator<MaxArrayDeque61B>`（比较的是 `MaxArrayDeque61B` 对象）
但创建的是 `MaxArrayDeque61B<Integer>`，它需要一个 `Comparator<Integer>`（比较的是 `Integer` 对象）

类型不匹配：`Comparator<MaxArrayDeque61B>` 不能赋值给 `Comparator<Integer>`


### 类型实参不能位基元类型
![[Pasted image 20250825194403.png]]
**类型参数（尖括号里的内容 ）必须是引用类型（如 `Double`、`String` 等类 ），不能是基本数据类型（如 `double`、`int`、`char` 等 ）**

