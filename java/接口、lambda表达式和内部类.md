### 1.接口

在java中，接口不是类，而是对类的一组需求描述，这些类要遵从接口描述的统一格式进行定义。

接口中的所有方法自动地属于public,因此，在接口中声明方法时，不必提供关键字public.

接口中可以定义常量，但是，接口中不能含有实例域。接口中可以实现简单的方法，但是这些方法不能引用实例域–接口没有实例。

提供实例域和方法实现的任务应该有实现接口的那个类来完成。因此，可以将接口看成是没有实例域的抽象类。

为了让类实现实现一个接口，通常需要下面两个步骤:

1)将类声明为实现给定的接口

2）对接口中的所有方法进行定义

要将类声明为实现某个接口，需要使用关键字implements：

```java
class Employee implements Comparable
```

在实现接口的方法时，必须把方法声明为public,

##### 接口的特性

接口不是类，尤其不能使用new运算符实例化一个接口，但是可以声明接口的变量。接口变量必须引用实现了接口的类对象。

可以使用instance检查一个对象是否实现了某个特定的接口:

```java
if(anObject instanceof Comparable){
    
}
```

与可以建立类的继承关系一样，接口也可以被扩展。这里允许存在多条从具有较高通用性的接口到较高专用性的接口的链(即多继承)

虽然在接口中不能包含实例域或静态方法，但却可以包含常量

```java
public interface Powered extends Moveable{
    double milesPerGallon();
    double SPEED_LIMIT = 95;//a public static final constant
}
```

与接口中的方法都自动地被设置为public一样，接口中的域将被自动设为public static final.

每个类只能拥有一个超类，但却可以实现多个接口

#### 接口与抽象类

因为每个类只能扩展一个类，所以需要接口来作为补充

##### 静态方法

在java SE8中，允许在接口中增加静态方法。理论上讲，没有任何理由认为这是不合法的，只是这有违于将接口作为抽象规范的初衷。

##### 默认方法

可以为接口方法提供一个默认实现。必须用default修饰符标记这样一个方法

```java
public interface Comparable<T>
{
    default int compareTo(T other){return 0;}
}
```

在Java API中，你会看到很多接口都有相应的伴随类，这个伴随类中实现了相应接口的部分或所有方法，如Collection/AbstractCollection或MouseListener/MouseAdapter在Java SE8中，这个技术已经过时。现在可以直接在接口中实现方法。

##### 解决默认方法冲突

如果先在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义了同样的方法，会发生什么情况? java的解决冲突的规则要简单得多:

1) 超类优先。如果超类提供了一个具体方法，同名而且有相同参数类型的默认方法会被忽略

2）接口冲突，如果一个超接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型(不论是否是默认参数)相同的方法，必须覆盖这个方法来解决冲突。

##### Comparator接口

在java中，比较器是实现了Comparator接口的类的实例

```java
public interface Comparator<T>{
    int compare(T first,T second);
}

//自定义一个比较器
class LengthComaprator implements Comparator<String>{
    
}
```

##### 对象克隆

Cloneable接口指示一个类提供了一个安全的clone方法。

如果希望copy是一个新对象，它的初始状态与original相同，但是之后他们各自会有自己不同的状态，这种情况下就可以使用clone方法

对于每一个类，需要确定:

1)默认的clone方法是否满足要求

2)是否可以在可变的子对象上调用clone来修补默认的clone方法

3)是否不该使用clone

实际上第3个选择是默认选项。如果选择第一项或第二项，类必须

1)实现Cloneable接口

2)重新定义clone方法，并指定public访问修饰符

在这里，Cloneable接口的出现于接口的正常使用并没有关系。具体来说，它没有指定clone方法，这个方法是从Object类继承的。这个接口只是作为一个标记，指示类设计者了解克隆过程。对象对于克隆很"偏执"，如果一个对象请求克隆，但没有实现这个接口，就会生成一个受查异常。

即使clone的默认(浅拷贝)实现能够满足要求，还是需要实现Cloneable接口，将clone重新定义为public,再调用super.clone()

所有数组类型都有一个public的clone方法，而不是protected.可以用这个方法建立一个新数组，包含原数组所有元素的副本。

```java
int[] luckNumbers={2,3,5,7,11,13};
int[] cloned = luckNumbers.clone();
cloned[5]=12;//不会改变luckNumbers[5];
```

##### lambda表达式

lambda表达式就是一个代码块，以及必须传入代码的变量规范。

```java
(String first,String second)->first.length()-second.length()
```

如果代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在{}中，并包含显示地return语句，例如:

```java
(String frist,String second)->
{
    if(first.length()<second.length()) return -1;
    else if(first.length()>second.length())return 1;
    else return 0;
}
```

及时lambda表达式没有参数，仍然要提供空括号，就像无参数方法一样:

```java
()->{for(int i=100;i>=0;i--)System.out.println(i);}
```

如果可以推导出一个lambda表达式的参数类型，则可以忽略其类型。例如

```java
Comparator<String> comp=(first,second)->first.length()-second.length();
```

在这里，编译器可以推导出first和second必然是字符串，因为这个lambda表达式将赋给一个字符串比较器。

如果方法只有一个参数，而且这个参数的类型可以推导得出，那么甚至还可以省略小括号

```java
ActionListener listener=event->System.out.println("The time is"+ new Date());
```

无需指定lambda表达式的返回类型。lambda表达式的返回类型总是会由上下文推导得出。

如果一个lambda表达式只在某些分支返回一个值，而在另外一些分支不返回值，这是不合法的。例如,(int x)->{if(x>=0) return 1;}就不合法

##### 函数式接口

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个lambda表达式，这种接口称为函数式接口

例如，对于Arrays.sort方法，它的第二个参数需要一个Comparator实例，Comparator就是只有一个方法的接口，所以可以提供一个lambda表达式:

```java
Arrays.sort(words,(first,second)->first.length()-second.length());
```

lambda表达式可以捕获外围作用域中变量的值。在java中，要确保所捕获的值是明确定义的，这里有一个重要的限制。在lambda表达式中，只能引用值不会改变的变量。例如，下面的做法是不合法的:

```java
public static void countDown(int start,int delay){
    ActionListener listener = event->{
        start--;
        System.out.println(start);
    };
    new Timer(delay,listener).start();
}
```

lambda表达式中捕获的变量必须实际上是最终变量，实际上的最终变量是指，这个变量初始化之后就不会再为它赋新值。

lambda表达式的体与嵌套块有相同的作用域，这里同样适用命名冲突和遮蔽的有关规则。在lambda表达式中声明于一个局部变量同名的参数或局部变量是不合法的

```java
Path first=Paths.get("/usr/bin");
//错误，first已经被定义了
Comparator<String> comp=(first,second)->first.length()-second.length();
```

在方法中，不能有两个同名的局部变量，因此，lambda表达式中同样也不能有同名的局部变量.

在一个lambda表达式中使用this关键字时，是指创建这个lambda表达式的方法的this参数

```java
public class Application(){
    public void init(){
        ActionListener listener = event->{
            System.out.println(this.toString());
        }
    }
}
```

表达式this.toString()会调用Application对象的toString方法，而不是ActionListener实例的方法。



### 2内部类

内部类是定义在另一个类中的类。需要使用内部类的原因有三点:

* 内部类方法可以访问该类定义所在的作用域中的数据，包括私有数据
* 内部类可以对同一个包中的其他类隐藏起来
* 当想要定义一个回调函数且不想编写大量代码时，使用匿名内部类比较便捷

内部类既可以访问自身的数据域，也可以访问创建它的外围类对象的数据域

内部类中声明的所有静态域都必须是final.原因很简单，我们希望一个静态域只有一个实例，不过对于每个外部对象，会分别有一个单独的内部类实例，如果这个域不是final,它可能就不是唯一的

内部类不能有static方法，也可以允许有静态方法，但只能访问外围类的静态域和方法。

##### 局部内部类

局部类不能用public或private访问说明符进行声明。它的作用域被限定在声明这个局部类的块中。