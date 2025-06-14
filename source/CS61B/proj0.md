---
title: proj0
date: 2025-06-14
cssclasses:
  - CS61B
---
这次的`proj` 整体挺简单的，让我惊讶的是相比CS61A，它会提示要用到的辅助方法，不需要像之前非得看完整个框架才能开始做，而且还有风格检查和`git` ,`GitHub` 相关，刚好弥补了我不知道该怎么练习的苦恼。 

---

这次的项目是一个叫[2048](https://play2048.co/) 的游戏。

## Task 1: Empty Space Exists

如果棋盘上有空格就返回true，否则放回false 。
遍历就解决了：

```java
public boolean emptySpaceExists() {  
    for (int x = 0; x < board.size(); x++) {  
        for (int y = 0; y < board.size(); y++) {  
            if (board.tile(x, y) == null) { //空方块则返回null  
                return true;  
            }  
        }  
    }  
    return false;  
}
```

## Task 2: Max Tile Exists

如果棋盘上的任何瓦片值为获胜值（MAX_PIECE = 2048），此方法应返回 true。
同样是遍历，但是要注意获取瓦片值前要确保瓦片存在：

```java
public boolean maxTileExists() {  
    for (int x = 0; x < board.size(); x++) {  
        for (int y = 0; y < board.size(); y++) {  
            if ((board.tile(x, y) != null) && (board.tile(x, y).value() == MAX_PIECE)) {  
                return true;  
            }  
        }  
    }  
    return false;  
}
```

## Task 3: At Least One Move Exists

如果存在任何有效的移动，这个方法应该返回 true。
存在有效移动的两种方式：
1. 棋盘上至少有一个空格。
2. 棋盘上有两个相邻（它们之间可以有空格）且值相同的方块。
第一种很好判断，直接丢给刚刚完成的`emptySpaceExists()` 就行，第二个条件我写的很丑...QAQ
也是基于遍历，分成横向遍历和纵向遍历，它们接下来的步骤差不多：创建一个‘先行者’(`x1`,`y1`),
比较基础瓦片和先行者的值。
感觉这题有点像力扣的一道棋盘题，不过那个是判断达到最终结果的最少步数，用的多叉树解决的。

```java
public boolean atLeastOneMoveExists() {  
    if (emptySpaceExists()) {  
        return true;  
    }  
    for (int x = 0; x < board.size(); x++) {  
        for (int y = 0; y < board.size() - 1; y++) {  
            for (int x1 = x + 1; x1 < board.size(); x1++) {  
                if (board.tile(x, y) != null && board.tile(x1, y) != null) {  
                    if (board.tile(x, y).value() == board.tile(x1, y).value()) {  
                        return true;  
                    } else {  
                        break;  
                    }  
                }  
            }  
            for (int y1 = y + 1; y1 < board.size(); y1++) {  
                if (board.tile(x, y) != null && board.tile(x, y1) != null) {  
                    if (board.tile(x, y).value() == board.tile(x, y1).value()) {  
                        return true;  
                    } else {  
                        break;  
                    }  
                }  
  
            }  
        }  
    }  
    return false;  
}
```

## ## Task 5: Move Tile Up (No Merging)

将位置 `(x, y)` 的方块尽可能向上移动到其所在列的最上方。
这里提示要用 `Board` 类的方法 `move(int x, int y, Tile tile)`，这个方法将给定的 `tile` 移动到棋盘上的 `(x, y)` 位置。而且要求`moveTileUpAsFarAsPossible` 解决方案应该恰好调用一次 `move` 方法。那就是先确定步数再用`move` 。
这里有一个小坑，如果你查看`Board` 类方法 `move` ，会发现：
```java
if (tile1 == null) {  
	...
} else {  
    if (tile.value() != tile1.value()) {  
    ...  
    }  
    next = Tile.create(2 * tile.value(), px, py);  
    tile1.setNext(next);  
}
```
也就是如果传入`move` 的瓦片如果值存在，就一定会更新值，无论它是否移动过。于是就要注意不能把顶层瓦片传入`move` :
```java
public void moveTileUpAsFarAsPossible(int x, int y) {  
    Tile currTile = board.tile(x, y);  
    int myValue = currTile.value();  
    int targetY = y;  
    int step = 0;  
    for (int i = targetY + 1; i < board.size(); i++) { //i = targetY + 1 从下一个方块开始比较  
        if (board.tile(x, i) == null) {  
            step += 1;  
        } else {  
            if (board.tile(x, i).value() == myValue && i != y) { //i != y 防止方块在顶层时加上自己的值  
                if (!board.tile(x, i).wasMerged()) {  
                    step += 1;  
                }  
            }  
            break;  
        }  
    }  
    if (step != 0) {  
        board.move(x, targetY + step, currTile); // 顶层方块不移动但会检测合并，防止重复加上自己的值  
    }  
}
```

## Task 6: Merging Tiles

瓦片可以通过空格向上移动。当瓦片遇到非空格时，如果那个格子里有另一个相同值的瓦片，并且那个瓦片还没有因为这个倾斜操作被合并过，那么这两个瓦片应该合并。
我发现我在上面的题里已经解决这个问题了，于是跳过。

## Task 7: Tilt Column

这个方法应该将给定坐标的列向上倾斜，将列中的所有方块移动到正确的位置，并合并需要合并的方块。
其实就是对`moveTileUpAsFarAsPossible`方法的抽象包装，把`moveTileUpAsFarAsPossible`方法用到给定`x`坐标的一列，注意要自上而下的调用：

```java
public void tiltColumn(int x) {  
    for (int y = board.size() - 1; y >= 0; y--) {  
        if (board.tile(x, y) != null) {  
            moveTileUpAsFarAsPossible(x, y);  
        }  
    }  
}
```

## Task 8: Tilt Up

这个方法应该将整个棋盘向上倾斜，将所有列中的所有方块移动到它们应有的位置，并合并需要合并的方块。
其实就是对`tiltColumn`方法的抽象包装，在棋盘的每一列调用`tiltColumn`方法：
```java
public void tilt(Side side) {  
    for (int x = 0; x < board.size(); x++) {  
        tiltColumn(x);  
    }  
}
```

## Task 9: Tilt in Four Directions

既然我们已经实现了向上方向的倾斜功能，现在我们必须对其他三个方向做同样的事情。
这里我的方法应该和官方期望的解法不同，我看官方的提示好像还要用到`dubug`，这里我看它给出
1. “ `Side` 类是一种特殊的类，称为 `Enum` ”
2. “枚举可以用类似 `Side s = Side.NORTH` 的语法赋值。请注意，我们不是使用 `new` 关键字，而是直接将 `Side` 值设置为四个值中的一个”
3. “如果你对 Java 枚举感兴趣，请查看 https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html 。”
这几个提示，结合起来就是：
```java
public void tiltWrapper(Side side) {  
    board.resetMerged();  
    switch (side) {  
        case NORTH:  
            board.setViewingPerspective(Side.NORTH);  
            tilt(side);  
            break;  
        case WEST:  
            board.setViewingPerspective(Side.WEST);  
            tilt(side);  
            board.setViewingPerspective(Side.NORTH);  
            break;  
        case SOUTH:  
            board.setViewingPerspective(Side.SOUTH);  
            tilt(side);  
            board.setViewingPerspective(Side.NORTH);  
            break;  
        case EAST:  
            board.setViewingPerspective(Side.EAST);  
            tilt(side);  
            board.setViewingPerspective(Side.NORTH);  
            break;  
        default:  
            break;  
    }  
}
```

## Task 10: Updating Score

实现的分数更新。
这个直接在`moveTileUpAsFarAsPossible`方法里插入一行就行了：

```java
public void moveTileUpAsFarAsPossible(int x, int y) {  
    Tile currTile = board.tile(x, y);  
    int myValue = currTile.value();  
    int targetY = y;  
    int step = 0;  
    for (int i = targetY + 1; i < board.size(); i++) { 
        if (board.tile(x, i) == null) {  
            step += 1;  
        } else {  
            if (board.tile(x, i).value() == myValue && i != y) { 
                if (!board.tile(x, i).wasMerged()) {  
                    this.score += board.tile(x, y).value() * 2;  // 插入这里
                    step += 1; 
                }  
            }  
            break;  
        }  
    }  
    if (step != 0) {  
        board.move(x, targetY + step, currTile); 
}
```