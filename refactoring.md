如果它没坏，就不要动它。－－谚语

### 第一章：重构，第一个案例

重构的节奏：测试、小修改、测试、小修改......

### 第二章：重构原则

重构（名词）：对软件内部结构的一种调整，目的是在不改变软件可观察行为的前提下，提高其可理解性，降低其修改成本

重构（动词）：使用一系列重构手法，在不改变软件可观察行为的前提下，调整其结构

......

**重构与设计**

它们彼此互补。

预先设计--&gt;编码--&gt;重构

优点：预先设计无需一开始就找出正确的解决方案，只需要得到一个合理的解决方案即可

......

**重构与性能**

首先写出可调的软件，然后调整它以求获得足够的速度

### 第三章：代码的坏味道 code smell

3.1、Duplicated Code 重复代码

设法将它们合而为一，程序会变得更好

3.2、Long Method 过长函数

每当感觉需要以注释来说明点什么的时候，我们就把需要说明的东西写进一个独立的函数中，并以其用途（而非实现手法）命名

3.3、Large Class 过大的类

最简单的解决方案是把多余的东西消弭于内部类

3.4、Long Parameter List 过长参数列

参数对象

3.5、Divergent Change 发散式变化

一个类受多种变化的影响

如果某个类经常因为不同的原因在不同的方向上发生变化，例如新出现了一种工具，必须修改四个函数，需要对其提炼

3.6、Shotgun Surgery 霰弹式修改

一种变化引发多个类相应的修改

可以把一系列相关行为放进一个同一个类

3.7、Feature Envy 依恋情结

对象技术的要点：将数据和对数据的操作行为包装在一起

函数对某个类的兴趣高过对自己所处类的兴趣－－移到它该去的地方。

3.8、Data Clumps 数据泥团

减少字段和参数的个数，提炼到一个独立的对象中

3.9、Primitive Obsession 基本类型偏执

3.10、Switch Statements Switch惊悚现身

少用switch语句，可以考虑使用多态来替换它

3.11、Parallel Inheritance Hierarchies 平行继承体系

是Shotgun surgery的特殊情况，在这种情况下，每当你为某个类增加一个子类，必须也为另一个类相应增加一个子类。

3.12、Lazy Class 冗赘类

对于没怎么用的组件，可以以Inline Class对付

3.13、Speculative Generality 夸夸其谈未来性

3.14、Temporary Field 令人迷惑的暂时字段

3.15、Mesage Chains 过度耦合的消息链

3.16、Middle Man 中间人

委托动作

3.17、Inappropriate Intimacy 狎昵关系

对于两个过于亲密的类

3.18、Alternative Classes with Different Interfaces 异曲同工的类

......

3.22、Comments 过多的注释

......

### 第四章 构筑测试体系

junit

### 第五章 重构列表

重构的记录格式

* name名称

* summary该要

* motivation动机

* mechanics做法

* examples范例


### 第六章 重新组织函数

6.1、Extract Method 提炼函数

6.2、Inline Method 内联函数

6.3、Inline Temp 内联临时变量

