这个proj总体来说不难，属于谜底就在谜面上

---
### Problem 1 (1 pt)

**Part A**:    为`HarvesterAnt`和`ThrowerAnt`类添加两个类属性`food_cost`,`health` ，直接在类名下添加就好了，这里就不写出来了

**Part B**：    为`HarvesterAnt`实现`action`方法，使蚁巢的食物加1
```java
class HarvesterAnt(Ant):
    """收获蚁(HarvesterAnt)每回合为蚁群多生产1单位食物。"""

    name = 'Harvester'
    implemented = True
    food_cost = 2
    health = 1
    # OVERRIDE CLASS ATTRIBUTES HER
    def action(self, gamestate: GameState):
        """Produce 1 additional food for the colony.
        gamestate -- The GameState, used to access game state information.
        """
        # BEGIN Problem 1
        gamestate.food += 1
        # END Problem 1
```

### Problem 2 (1 pt)

完善 `Place.__init__`, 添加两个指针，标志前后，整个路程的开端从蚁巢开始，我们通过
`self.exit`来寻找`self.entrance`如果`place` 刚刚被创建（`Bee`仍在巢穴里）此时`self.exit`和`self.entrance`都被设置为`None`，只有`Bee`在战场中，才标记他们
```java
        if (self.exit != None) :
            exit.entrance = self
        else:
            self.entrance = None
```

>**为什么`place` 刚刚被创建时`self.exit`和`self.entrance`都被设置为`None`？**
>从这两个参数的功能来，上来说，它们的所指的都应该是战场上的方位，而不会包括它们巢穴的方位。那从后文的实现上来看，在后文中投掷蚁它是需要通过`Bee.entrance`来实现向前探寻的动作。然而它们不会攻击到这个蚁巢的位置(`Bee`在巢穴中被保护)。
>相应的从代码来看， `Bee`的父类是`Insect`， 而 `Insect` 它需要传入一个 `place` 参数，但是 `Bee`类 并没有要传入一个` place` 参数，所以在 `Bee` 刚创建时 `place` 参数这一块就一定是`None`。只有在在 'Bee' 被放置在战场上时，它这个参数才会有效，从而产生 `Bee.entrance` 和`Bee.exit`这两个位置。

>为什么`self.exit`是`Bee`的前一个位置，那`self.entrance`不指向`Bee` 的后一个位置？
>我刚开始觉得`Bee`不会后退，所以不需要`self.entrance`指向`Bee` 的后一个位置，后来一想，如果这样的话，`self.entrance`本身就没有任何意义了，知道我完成这个proj，我才理解到其中的精妙，我不直接给出答案，先给一个提示：在后文中投掷蚁它是需要通过`Bee.entrance`来实现向前探寻的动作。所以，如果`self.entrance`指向`Bee` 的后一个位置，`self.exit`和`self.entrance`的联系就断开了，那就没法通过`place.entrance`访问了

### Problem 3 (2 pt)

这里根据所说的写就行
```java
    def nearest_bee(self) -> Bee | None:
        p = self.place
        while p.is_hive == False:
            bee = random_bee(p.bees)
            if  bee != None:
                return bee
            else:
                p = p.entrance
        return None
```

### Problem 4 (2 pt)

实现`ShortThrower`和`LongThrower`类，就是加上类实例就好

### Problem 5 (3 pt)

这里要求重写父类的`reduce_health` 方法，就是在原来的方法的基础上加上死亡后的反伤逻辑

```java
    def reduce_health(self, amount: float):
        """Reduce health by AMOUNT, and remove the FireAnt from its place if it
        has no health remaining.
        """
        # BEGIN Problem 5
        p = self.place
        super().reduce_health(amount)
        if self.health <= 0:
            amount += self.damage
        if p.bees != None:
            for bee in p.bees[:]:     
                bee.reduce_health(amount)
```

### Problem 6 (1 pt)

这个按照要求写一个类头，类名，构造方法就行。

### Problem 7 (3 pt)

一个 `HungryAnt` 必须花费 3 轮咀嚼才能再次进食，就是需要一个实例属性标记咀嚼剩余的次数(`self.cooldown`)，一个类属性标记咀嚼总共所需的时间(`chew_cooldown`)。
如果咀嚼次数不为0就不攻击，同时剩余次数减1，否则就攻击

```java
class HungryAnt(Ant):

    implemented = True
    food_cost = 4
    name = 'Hungry'
    chew_cooldown = 3
    def __init__(self, health = 1):
        super().__init__(health)
        self.cooldown = 0
    def action(self, gamestate: GameState):
        if self.cooldown != 0:
            self.cooldown -= 1
            return
        else:
            bee = self.place.bees
            if bee == []:
                return
            else:
                Abee = random_bee(bee)
                Abee.reduce_health(Abee.health)
                self.cooldown = self.chew_cooldown
```

### Problem 8 (3 pt)

#### Problem 8a
有三个方法需要编写：
- `store_ant`方法：如果`ContainerAnt`可以容纳另一个`Ant`,就将`ant_contained`设为传入的`Ant`。
- `action`方法：若当前`ContainerAnt`有容纳`Ant`，则执行`Ant`的攻击。
- `can_contain`方法：接受`other`为参数，满足以下 2 个条件时返回`True`：①当前`ContainerAnt`未包含其他蚂蚁；②`other`不是容器
```java
    def can_contain(self, other: Ant) -> bool:
        # BEGIN Problem 8a
        "*** YOUR CODE HERE ***"
        if self.ant_contained == None and other.is_container == False:
            return True
        return False
        # END Problem 8a
  
    def store_ant(self, ant: Ant):
        # BEGIN Problem 8a
        "*** YOUR CODE HERE ***"
        if self.can_contain(ant):
            self.ant_contained = ant
        # END Problem 8a
        
	def action(self, gamestate: GameState):
        # BEGIN Problem 8a
        "*** YOUR CODE HERE ***"
        if self.ant_contained != None:
            self.ant_contained.action(gamestate)
        # END Problem 8a
```

#### Problem 8b

实现`Ant.add_to` 方法，按照说明做就好。可以想一想为什么如果有两个`Ant`类，`place`会链接到`ContainerAnt`类

```java
    def add_to(self, place: Place):
        if place.ant is None:
            place.ant = self
        else:
            # BEGIN Problem 8b
            if self.is_container == True and self.can_contain(place.ant):
                self.store_ant(place.ant)
                place.ant = self
                Insect.add_to(self, place)
                return
            elif place.ant.is_container == True and place.ant.can_contain(self):
                place.ant.store_ant(self)
                Insect.add_to(self, place)
                return
            assert place.ant is None, 'Too many ants in {0}'.format(place)
            # END Problem 8b
```

#### Problem 8c

实现`ProtectorAnt` 类，注意它继承自`ContainerAnt`,否则刚刚写的方法不就没用了嘛

### Problem 9 (2 pt)

`TankAnt`就是起到一个血包的作用，比较有意思的是`它保护它所在位置的一只蚂蚁`,就是说如果它还有血，攻击就会先让它扣血，如何实现它呢，我一开始想到的是直接修改`Bee`的攻击方法，在攻击前判断是否有多个`Ant`类，如果有就找出`ContainerAnt`类，就是检查它们的`is_container`类属性，最后在对其攻击，但是这样会很麻烦，需要有一个方法跟踪每一个`Ant`的数量，而且每一轮都要更新，每个`Ant` 都要记住自己的位置，要修改`Ant`的`add_to`和`remove`方法，但是这里却很好的避免了这些麻烦，就是上面的`place`会优先链接到`ContainerAnt`类的做法，这样就实现了优先攻击，那如果`ContainerAnt`死亡，怎么把攻击转移到被保护的`Ant`类呢？就靠`ContainerAnt`的`ant_contained`实例属性了，不过当前问题不会涉及到这个。
代码不是很难我就不放出来了。