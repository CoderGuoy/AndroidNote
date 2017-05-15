## 设计模式之策略模式 | Strategy Pattern

从名字上我们很难理解这个设计模式适用于什么情况下，什么情况下使用这个设计模式更合理，他有哪些优点，所以我们在最后再对策略模式的概念所出解释

### 首先，让我们从一个例子的需求入手，一步一步的来了解如何用策略模式解决开发中遇到的问题

模拟鸭子游戏：要求游戏中会出现各种鸭子，一边游泳戏水，一边呱呱叫。现在我们使用标准的OO原则，设计一个鸭子超类，并让各种鸭子继承此超类

![strategy01](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy01.png)

OK，对于这个需求我们可以很容的就实现，那么现在我们又有了新的需求，让鸭子能飞，这似乎并不难

我们只需要在Duck的类中加上fly()方法，然后所有鸭子都会继承fly()

![strategy02](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy02.png)

但是，可怕的问题发生了，游戏中的很多"橡皮鸭子"在屏幕上飞来飞去，这样就很不合理

![strategy03](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy03.png)

当涉及到"维护"时，为了"复用"目的而使用继承，结果并不完美

![strategy04](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy04.png)

当然，我们可以把橡皮鸭中的fly()方法覆盖掉，就好像覆盖quack()的做法一样

![strategy05](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy05.png)

可是，如果以后加入木头鸭呢？木头鸭又不会飞也不会叫

![strategy06](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy06.png)

这是继承层次中另一个类，注意：木头鸭不会飞也不会叫，可是橡皮鸭会叫不会飞

利用继承来提供Duck的行为，会导致以下四个缺点：

- 代码在多个子类中重复

- 运行时的行为不容易改变

- 很难知道所有鸭子的全部行为

- 改变会牵一发而动全身，造成其它鸭子不想要的改变

### 利用接口如何？

每当有新的鸭子子类出现，我们就要检查并可能需要覆盖fly()和quark()，这简直就是无穷无尽的噩梦

所以，我们需要一个更清晰的方法，让"某些"(不是全部)鸭子类型可飞或可叫

我们可以把fly()从超类中提取出来，放进一个"FlayAble接口"中，这么一来，只有会飞的鸭子才实现此接口，同样的方式，也可以用来设计一个"QuackAble接口"，因为不是所有的鸭子都会叫

![strategy07](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy07.png)

仔细想一想，并非"所有"的子类都具有飞行和呱呱叫的行为，所以接口可以解决"一部分"的问题，但是却造成代码无法复用。

### 封装变化

现在发现继承并不能很好的解决问题，因为鸭子的行为在子类里不断的改变，并且让所有的子类都有这些行为是不恰当的，接口一开始似乎还不错，解决了一部分的问题，但是java接口不具有实现代码，所以继承接口无法达到代码的复用，这意味着：无论何时修改某个行为，必须得往下追踪并在每一个定义此行为的类中修改它。

设计原则：封装变化(找出可能需要变化之处，把他们独立出来，不要和那些不需要变化的代码混在一起)

换句话说，如果每次新的需求一来，都会使某方面的代码发生变化，那么你就可以确定，这部分的代码需要被抽出来，和其他稳定的代码有所区分。

目前所知，除了fly()和quack()的问题之外，Duck类还算一切正常，所以除了某些小改变之外，不对Duck类进行太多处理。

我们知道Duck类内的fly()和quack()会随着鸭子的不同而改变，现在，为了要分开"变化和不会变化的部分"，我们准备建立两组类()，一个是"fly"相关的，一个是"quack"相关的，每一组类将实现各自的动作。

![strategy08](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy08.png)

### 设计鸭子的行为

如何设计那组实现飞行和呱呱叫的行为的类呢？

我们希望一切能有弹性，毕竟，正式因为一开始鸭子行为没有弹性，才让我们走上现在这条路。我们还想能够"指定"行为到鸭子的实例，并指定特定"类型"的飞行行为给它，可以让鸭子的行为动态的改变。换句话说，我们应该在鸭子类中包含设定行为的方法，这样就可以在"运行时"动态地"改变"绿色鸭子的飞行行为。

设计原则:针对接口编程，而不是针对实现编程

![strategy09](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy09.png)

"针对实现编程"
Dog d = new Dog();
d.dark();

"针对接口/超类型编程"
Animal animal = new Dog();
animal.makeSound();

更棒的是，子类实例化的动作不再需要在代码中硬编码，例如new Dog(),而是"在运行时才指定具体实现的对象"。

a = getAnimal();
a.makeSound();

我们不知道实际的子类型是"什么"~~~ 我们只关心他知道如何正确进行makeSound()的动作就够了。

### 实现鸭子的行为

我们有两个接口，FlyBehavior和QuackBehavior，还有他们对应的类，负责实现具体的行为：

![strategy10](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy10.png)

- 这样的设计，可以让飞行和呱呱叫的动作被其他的对象复用，以为这些行为已经和鸭子类无关了。

- 而我们可以新增一些行为，不会影响到既有的行为类，也不会影响"使用"到飞行行为的鸭子类。

- Question:是不是一定要先把系统做出来，再看看有哪些地方需要变化，然后才回头去吧这些地方分离&封装？

- Answer:设计系统时，预先考虑到有哪些地方未来坑能需要变化，于是提前在代码中加入这些弹性。

### 整合鸭子的行为

关键在于，鸭子现在会将飞行和呱呱叫的动作"委托"别人处理，而不是实用定义在Duck类(或子类)内的呱呱叫和飞行方法。

#### 1.首先，在Duck类中"加入两个实例变量"

分别为"flyBehavior"与"QuackBehavior"，声明为接口类型而不是具体类实现类型，每个鸭子对象都会动态地设置这些变量以在运行时引用正确的行为类型(例如:FlyWithThings、Squeak)。
我们也必须将Duck类中与其所有子类中的fly()和quack()删除，因为这些行为已经被搬到FlyBehavior与QuackBehavior类中了。
我们利用两个相似的方法performFly()和performQuack()取代Duck类中的fly()与quack()。稍后就会知道为什么这么做

![strategy11](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy11.png)

#### 2.现在，我们来实现performQuack():
```java
  public void Duck{
    QuackBehavior quackBehavior;//每只鸭子都会引用实现QuackBehavior接口的对象
    //还有更多
    
    public void performQuack(){//鸭子对象不亲自处理呱呱叫的行为，而是委托给quackBehavior引用的对象。
      quackBehavior.quack();
    }
```

Duck对象只要交quackBehavior对象去呱呱叫就可以了，这部分的代码中，我们不在乎quackBehavior接口的对象到底是什么，我们只关心该对象知道如何进行呱呱叫就够了。

#### 3.ok,现在来关心"如何设定flyBehavior与quackBehavior的实例变量"

```java
public class MallardDuck extends Duck {

    public MallardDuck() {
        mQuackBehavior = new Quack(); //绿色鸭子使用Quack()类处理呱呱叫，所以当performQuack()被调用时，叫的职责被委托给Quack对象，而我们得到了真正的呱呱叫
        mFlyBehavior = new FlyWithThings(); //使用FlyWithThings作为其FlyBehavior类型
    }
    
    public void display() {
        System.out.print("我是一只绿色鸭子\n");
    }
}
```
所以，绿色鸭子会真的"呱呱叫",这是怎么办到的呢？当MallardDuck实例化时，他的构造器会把继承来的quackBehavior实例变量初始化成Quack()类型的新实例(Quack是QuackBehavior的具体实现类)。
同样的处理方式也可以用在飞行行为上：MallardDuck的构造器将flyBehavior实例变量初始化成FlyWithThings类型的实例(FlyWithThings是FlyBehavior的具体实现类)

### 测试Duck的代码

#### 1.Duck类
```java
public abstract class Duck {
    FlyBehavior mFlyBehavior;
    QuackBehavior mQuackBehavior;

    public Duck(){ }

    public abstract void display();

    public void performFly(){
        mFlyBehavior.fly();
    }
    public void performQuack(){
        mQuackBehavior.quack();
    }
    public void swim(){
        System.out.print("所有鸭子都会游泳");
    }
}
```

#### 2.FlyBehavior接口与两个行为实现类FlyWithThings与FlyNoWay
```java
public interface FlyBehavior {
    public void fly();
}
```
-----------------------------------------------------
```java
public class FlyWithThings implements FlyBehavior {
    @Override
    public void fly() {
        System.out.print("I can Fly!\n");
    }
}
```
------------------------------------------------------
```java
public class FlyNoWay implements FlyBehavior {
    @Override
    public void fly() {
        System.out.print("I can't Fly!\n");
    }
}
```
#### 3.QuackBehavior接口以及三个实现类
```java
public interface QuackBehavior {
    public void quack();
}
```
------------------------------------------------------
```java
public class Quack implements QuackBehavior {
    @Override
    public void quack() {
        System.out.print("鸭子呱呱叫\n");
    }
}
```
------------------------------------------------------
```java
public class MuteQuack implements QuackBehavior {
    @Override
    public void quack() {
        System.out.print("不会叫\n");
    }
}
```
------------------------------------------------------
```java
public class Squeak implements QuackBehavior {

    @Override
    public void quack() {
        System.out.print("橡皮鸭吱吱叫\n");
    }
}
```
#### 4.编译测试类
```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_strategy_design);
        Duck duck = new MallardDuck();
        duck.performQuack();
        duck.performFly();
    }
```
duck.performQuack()会调用MallardQuack()继承来的performQuack()方法，进而委托给该对象的QuackBehavior对象处理(也就是说，调用继承来的quackBehavior引用对象的quack())。
与duck.performFly()同样的道理。
#### 5.运行代码

```java
System.out: 鸭子呱呱叫
System.out: I can Fly!
```
### 动态设定行为

在鸭子里建立了一堆动态的功能没有用到，就太可惜了！假设我们想在鸭子子类中通过"设定方法(setter method)"来设定鸭子的行为，而不是在鸭子的构造器内实例化

#### 1.在Duck类中，加入两个新方法：
```java
  public void setFlyBehavior(FlyBehavior fb){
    flyBehavior = fb;
  }
  
  public void setQuackBehavior(QuackBehavior qb){
    quackBehavior = qb;
  }  
```
如此，我们可以"随时"调用这两个方法改变鸭子的行为。

#### 2.制造一个新的鸭子类型，模型鸭(ModeDuck.java)
```java
public class ModelDuck extends Duck {
    public ModelDuck() {
        mFlyBehavior = new FlyNoWay();//一开始，我们的模型鸭是不会飞的
        mQuackBehavior = new Quack();
    }

    @Override
    public void display() {
        System.out.println("我是模型鸭子\n");
    }
}
```
#### 3.建立一个新的FlyBehavior类型
```java
public class FlyRocketPowered implements FlyBehavior {
    @Override
    public void fly() {
        System.out.println("火箭推进系统");
    }
}
```
#### 4.改变测试类，加上模型鸭，并使模型鸭具有火箭动力

```java
   Duck modle = new ModelDuck();
   modle.performFly();  //第一次调用performFly()会被委托给FlyBehavior对象(也就是FlyNoWay实例)，该对象是在模型鸭构造器中内置的
   modle.setFlyBehavior(new FlyRocketPowered());//这会调用继承来的setter方法，把火箭动力飞行的行为设定到模型鸭中。
   modle.performFly();
```

#### 5.运行代码
```java
/System.out: I can't Fly!
/System.out: 火箭推进系统 
```
成功了，意味着模型鸭可动态地改变它的飞行行为，如果把行为的实现绑死在鸭子类中，就无法做到了。
在运行时，想要改变行为，只需要调用鸭子的setter方法就可以了

### 封装行为的大局观

Ok,我们已经深入研究鸭子模拟器的设计，该是探出睡眠，呼吸空气的时候了。现在就来看看整体的格局。

![strategy12](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/strategy12.png)

### "有一个"可能比"是一个"更好

"有一个"关系相当有趣:没一个鸭子都有一个FlyBehavior和一个QuackBehavior，好将飞行和呱呱叫委托给他们代为处理。
当将两个类结合起来使用，如同本例一般，这就是组合(composition)。这种做法和"继承"不同的地方在于，鸭子的行为不是继承来的，而是和适当的行为对象"组合"来的。
这是一个很重要的技巧。这个技巧原则是：多用组合，少用继承。
这样可以大大的提升代码的可维护性和可扩展性。

## 策略模式 正式定义

> 定义了算法族，分别封装起来，让他们之间可以相互替换，此模式让算法的变化独立于使用算法的客户

总结：此模式可以在运行时动态的改变行为，大大的提高了代码的灵活性！



