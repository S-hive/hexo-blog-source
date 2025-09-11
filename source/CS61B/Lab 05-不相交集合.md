---
url: https://sp24.datastructur.es/labs/lab05/
title: Lab 05-不相交集合
---
实现加权快速联合+路径压缩

不相交集合数据结构表示一组不相交的集合，这意味着该数据结构中的任何元素最多只存在于一个集合中。

对于不相交集合，我们通常仅限于两种主要操作：`union` and `find`（并集和查找）。`union`操作会将两个集合合并为一个集合。`find`操作会接收一个元素，并告诉我们该元素属于哪个集合。通过这些操作，我们能够轻松检查两个元素是否相互关联。

---

## Java 如何抛出异常？

在 Java 中，抛出异常（Exception）主要通过 `throw` 关键字实现，配合异常类来表示程序运行中的错误情况。以下是具体方式和相关说明：

#### 1. 基本语法：使用 `throw` 关键字

**`throw`**：用于**手动抛出一个具体的异常对象**，直接触发异常。

```java
throw new 异常类名(异常信息);
```

- 作用：手动触发一个异常，中断当前方法的执行流程，将异常传递给调用者处理。
例如：当检测到无效参数时，主动抛出 `IllegalArgumentException`。
```java
if (age < 0) {
    throw new IllegalArgumentException("年龄不能为负数"); // 抛出具体异常
}
```


#### 2. 声明方法可能抛出的异常：`throws` 关键字

如果方法内部抛出的是**受检异常**（Checked Exception，如 `IOException`），必须在方法声明处用 `throws` 声明该异常，否则会编译报错。
```java
import java.io.FileNotFoundException;

// 声明方法可能抛出 FileNotFoundException
public void readFile(String path) throws FileNotFoundException {
    if (path == null) {
        throw new FileNotFoundException("文件路径不能为空");
    }
    // ... 读取文件的逻辑
}
```

- **受检异常**：编译器强制要求处理的异常（如 `IOException`、`ClassNotFoundException`），必须通过 `try-catch` 捕获或 `throws` 声明。
- **非受检异常**：继承自 `RuntimeException` 的异常（如 `IllegalArgumentException`、`NullPointerException`），可无需声明，编译器不强制处理。

#### 3. 异常处理流程

当 `throw` 抛出异常后，程序会：
1. 立即终止当前方法的执行。
2. 寻找最近的 `try-catch` 块来捕获并处理该异常。
3. 如果没有任何 `try-catch` 捕获，异常会向上传递给调用者，直至 JVM 处理（此时程序会崩溃并打印异常信息）。

#### 4. 自定义异常

除了使用 Java 内置的异常类，还可以自定义异常类，通常继承 `Exception`（受检）或 `RuntimeException`（非受检）。

```java
// 自定义非受检异常
class InvalidScoreException extends RuntimeException {
    // 构造方法，传入异常信息
    public InvalidScoreException(String message) {
        super(message);
    }
}

// 使用自定义异常
public void checkScore(int score) {
    if (score < 0 || score > 100) {
        throw new InvalidScoreException("分数必须在 0-100 之间: " + score);
    }
}
```

#### 总结

- `throw`：用于在方法内部手动抛出一个具体的异常对象。
- `throws`：用于在方法声明处说明该方法可能抛出的异常类型（主要针对受检异常）。
- 异常的作用：清晰地传递错误信息，分离 "正常逻辑" 和 "错误处理逻辑"，提高代码的健壮性。