---
title: Leb03
date: 2025-06-15
cssclasses: CS61B
---
![Pastedimage20250615154243](/images/Pastedimage20250615154243.png)
### Debug `BeeCountingStage` 

#### Expected lines modified: 1
![Pasted20image2020250615170843](/images/Pastedimage20250615170843.png)
跳转到`BeeCountingStage.java:54`,看到：
```java
this.input.add(input);
```
也就是这个类有一个input变量和input局部变量，`because "this.input" is null`看起来应该是`this.input`的问题，向上看到：
```java
import java.util.List; // this.input是个List类
...
private List<String> input; // 猜对了，有声明
public BeeCountingStage(In in) {  
    this.in = in;  
    this.responses = Map.of("go", new SpeciesListStage(in));  // 没有实例化
}
...
this.input.add(input); // this.input应该是个List类(毕竟目前只讲到这个),看看引入的工具包
```
那加个实例化就好了，这里卡了我好一会，不明白官方怎么做到只改一行代码的：
```java
import java.util.ArrayList; // 加上
...
public BeeCountingStage(In in) {  
    this.in = in;  
    this.responses = Map.of("go", new SpeciesListStage(in));  
    this.input = new ArrayList<>(); // 加上
}
```

#### Expected lines modified: 1
![Pasted image 20250615172020.png](/images/Pastedimage20250615172020.png)
简单的超出索引异常，这里比较不同的是不能根据堆栈跟踪的第一行直接判断出错的位置，只能通过调试工具找，最终修改：
```java
private int sumInput() {  
    int sum = 0;  
    for (int i = 0; i < this.input.size(); i++) {  
    // 这里的 i <= this.input.size() 就是导致异常的原因
        sum += Integer.parseInt(this.input.get(i));  
    }  
    return sum;  
}
```
修改之后就可以通过测试了：
![Pasted image 20250615172423.png](/images/Pastedimage20250615172423.png)

复盘时看到官方说：
```jobs 
修正SpeciesListStage中的错误。如果你在发生异常的方法内部（堆栈跟踪的第一行）没有发现问题所在，通常查看第二行，了解该方法是从何处被调用以及使用了哪些参数，这是个不错的办法。 
```
回去看了看出错点，的确就在下面的第三行有提到。

### Debug `PalindromeStage`
#### Expected lines modified: 3
![Pasted image 20250615173326.png](/images/Pastedimage20250615173326.png)
查了下含义，是算术异常，表示程序尝试执行**除以零**的操作。
直接跳转到`SpeciesListStage.java:102`，改一下就好：
```java
    public static int arraySimilarity(List<String> listOne, List<String> listTwo) {  
        ...
        for (String o : listTwo) {  
		...
        }  
        if (similarObjects > 0) {  
            return 1;  
        }else {  
            return 0;  
        }  
        //return similarObjects / listOne.size();  
    }  
}
```


#### Expected lines modified: 2-5
![Pasted image 20250615175444.png](/images/Pastedimage20250615175444.png)
跳转到：
![Pasted image 20250615175613.png](/images/Pastedimage20250615175613.png)
先`Ctl+F`找一下`Preconditions`类，发现是引入的工具包，那直接一整个复制搜索的大操作，然后从这里开始调试，但是会发现很多次返回错误点，那就很难判断到底是哪次的错误了，于是点开被折叠的堆栈跟踪。
![Pasted image 20250615211618.png](/images/Pastedimage20250615211618.png)
从第一条蓝色标注的开始看(虽然不知道什么原理，但是从上面的经验来看，标蓝的最有可能)
后来复盘时查了下，写在下面：
* 在 IDE的报错堆栈信息里，标蓝的部分一般是**与你当前项目代码直接相关的类和方法** ，也就是你自己编写的代码文件里的内容，方便你快速定位到项目中出错的具体位置
* 不标蓝的，是 **JDK 内部的类和方法** 。这些属于 Java 标准库的代码，不是你项目里自己写的代码。IDE 通常不会把它们标蓝突出显示，因为一般情况下，我们不会去修改 JDK 源码，主要是聚焦在自己项目代码里找问题，它们更多是辅助说明错误发生时的调用链路。
然后点旁边的灯泡，会看到提示：
![Pasted image 20250615184116.png](/images/Pastedimage20250615184116.png)
这个比较好改，仔细点就行：
```java
private static IntList digitsToIntList(String s) { 
    int[] a = new int[s.length()];  
    for (int i = s.length(); i >= 1; i--) {  
        a[s.length() - i] = Character.getNumericValue(s.charAt(i-1));  
    } 
    return IntList.of(a);  
}
```

接下来先看官方提示：
```
如果你还没有，请通读上面的注释。查看调用`digitsToIntList`的方法。如果你在该方法中设置断点并通过调试器运行，我们是否曾经退出`while`循环？我们需要满足什么条件才能跳出`while`循环？这个条件是否得到满足？尝试结合使用Java可视化工具，这样你就可以借助可视化工具更仔细地检查这个错误。
```
直接`ctl+F`找到调用`digitsToIntList` 的方法`playStage`，大概看一遍就能找到第一个比较明显的错误：
![Pasted image 20250615213033.png](/images/Pastedimage20250615213033.png)
一看就是死循环的料，我的修改方案是引入一个`boolean`值：
```java
    boolean finish = false;  // 修改
    while (!finish) {  // 修改
        ...
        while (!AdventureUtils.isInt(input)) {  
            ...
        }  
        ...
        if (numLst.equals(reversedLst)) {  
            System.out.println("Wow, nice room number!");  
            finish = true;  // 修改
            break;  
        }
        ...
    }  
}
```

之后在运行就会一直进入死循环：
![Pasted image 20250615213603.png](/images/Pastedimage20250615213603.png)
通过调试工具发现问题在45行，结果一直为`false`:
```java
if (numLst.equals(reversedLst)) {  
    System.out.println("Wow, nice room number!");  
    finish = true;  
    break;  
}
```
就是这里卡了我好久，一个个说：
首先是：
```java
public void playStage() { 
        ...
    while (!finish) {  
        String input = in.readLine();
        while (!AdventureUtils.isInt(input)) {  
            ...
            input = this.in.readLine();  
        }  
        ...
    }
}
```
我不理解`in.readLine()`到底是什么，找资料可以知道这是'读取输入流从当前位置到下一行结束符（\n、\r或\r\n）的所有字符，并返回为字符串'的作用，但是我没有找到什么`输入流`，调试工具里看它们也是突然冒出来的，后来我以为`输入流`指的是开始时输出的一大串，然后通过调试工具发现，`input`的值和我预期的不同：
```
input值的变化：'404'->'va cafe'->'go'->'null'->一直为'null'
```
所以我的猜想是错的，后来我猜想这个程序是找到回文数的目的，那会不会准确的程序应该在`input = 404`时就结束，后来通过调试根据发现：
```java
public boolean equals(Object other) {  
    if (other instanceof IntList oL) {  
        if (first != oL.first) {  
            return false;  //本来改反悔true的值，却返回false
        } else if (rest == null && oL.rest == null) {  
            ...
        } else if (rest != null && oL.rest != null) {  
            ...  
        } else {  
            ... 
        }  
    }  
    return false;  
}
```
最后只要把对应的`return false;`改成`return true;`就行了。

复盘时，关于`输入流`,我想到个有意思的：
```java
public class ReadLineExample {
    public static void main(String[] args) {
        // 1. 第1次打印提示（仅输出，不阻塞）
        System.out.println("请输入一行文本：");  

        // 2. 创建Scanner（初始化扫描器，不触发输入）
        Scanner scanner = new Scanner(System.in); 

        // 3. 第2次打印提示（仅输出，不阻塞）
        System.out.println("请输入一行文本：");  

        // 4. 第3次打印提示（仅输出，不阻塞）
        System.out.println("请输入一行文本：");  // 第六行代码

        // 5. 关键：仅读取1次用户输入！
        // 程序会暂停，等待用户在控制台输入（无论前面打印多少次提示）
        String line = scanner.nextLine();  // 第七行代码

        // 6. 输出结果
        System.out.println("你输入的内容是：" + line);
        scanner.close();
    }
}
```
如果在计算机刚刚读取完第六行代码(上面代码里标注的一行)，在读取第七行前输入，那是不是就会卡bug了，用户的输入信息丢失。
查了下，遗憾地发现不会：
即使 “计算机刚刚读完第 6 行还没执行第 7 行”，用户此时在控制台输入内容：
- 输入的内容会被**暂存到系统的输入缓冲区**（属于 `System.in` 的一部分），不会丢失。
- 当程序执行到 `scanner.nextLine()` 时，会直接从缓冲区读取用户输入的内容，程序继续执行。

### Debug `MachineStage`
#### Expected lines modified: 2-5

这一块写的很顺利，逻辑也很流畅：
先运行一遍，通过调试工具的查看差异功能，定位到`machineResult`参数，通过参数的定义锁定`sumOfElementwiseMax(arrOne, arrTwo)`方法，在这里打上断点，开始调试：

![Pasted image 20250615223538.png](/images/Pastedimage20250615223538.png)
发现`maxes`数组错误，定位到`arrayMax`方法，打上断点，发现`biggerValue`错了：
![Pasted image 20250615223111.png](/images/Pastedimage20250615223111.png)
定位到`mysteryMax`方法，重写方法：
```java
public static int mysteryMax(int a, int b) {  
    int max;  
    if(a > b) {  
        max = a;  
    }else {  
        max = b;  
    }  
    return max;  
}
```
再运行程序，依然错，通过调试工具的查看差异功能，定位`machineResult`参数，再查看`sumOfElementwiseMax(arrOne, arrTwo)`方法，`maxes`数组没有问题，查看`arraySum`方法，通过调试工具发现`mysteryAdd`方法的错误，这里有个小坑，我一开始理所当然的以为`mysteryAdd`方法返回的应该时两数之和，但是运行错误，仔细看才发现不对：
```java
public static int mysteryAdd(int a, int b) {  
    return b;
```

最后运行程序成功：
![Pasted image 20250615230527.png](/images/Pastedimage20250615230527.png)

以上就是这次`Lab03`的内容。