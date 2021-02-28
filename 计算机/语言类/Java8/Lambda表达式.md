# Lambda表达式

什么是lambada表达式，可以理解成一个匿名函数，没有名字，但是有参数，有函数体，有返回值，还能抛出异常。

## 函数接口

数字可以作为值传递给一个方法，参数是int，Lambda表达式在传递的时候参数类型是什么呢？java用函数接口支持。

函数接口是只有一个抽象方法的接口，用作Lambda表达式的类型。

定义方法

```java
@FunctionalInterface
interface GreetingService 
{
    void sayMessage(String message);
}

```

JDK中也有很多函数接口,JDK 1.8 之前已有的函数式接口:

- java.lang.Runnable
- java.util.concurrent.Callable
- java.security.PrivilegedAction
- java.util.Comparator
- java.io.FileFilter
- java.nio.file.PathMatcher
- java.lang.reflect.InvocationHandler
- java.beans.PropertyChangeListener
- java.awt.event.ActionListener
- javax.swing.event.ChangeListener

JDK 1.8 新增加的函数接口：

- java.util.function

java.util.function 它包含了很多类，用来支持 Java的 函数式编程，该包中的函数式接口有:

| 序号 | 接口 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **BiConsumer<T,U>**代表了一个接受两个输入参数的操作，并且不返回任何结果 |
| 2    | **BiFunction<T,U,R>**代表了一个接受两个输入参数的方法，并且返回一个结果 |
| 3    | **BinaryOperator<T>**代表了一个作用于于两个同类型操作符的操作，并且返回了操作符同类型的结果 |
| 4    | **BiPredicate<T,U>**代表了一个两个参数的boolean值方法        |
| 5    | **BooleanSupplier**代表了boolean值结果的提供方               |
| 6    | **Consumer<T>**代表了接受一个输入参数并且无返回的操作        |
| 7    | **DoubleBinaryOperator**代表了作用于两个double值操作符的操作，并且返回了一个double值的结果。 |
| 8    | **DoubleConsumer**代表一个接受double值参数的操作，并且不返回结果。 |
| 9    | **DoubleFunction<R>**代表接受一个double值参数的方法，并且返回结果 |
| 10   | **DoublePredicate**代表一个拥有double值参数的boolean值方法   |
| 11   | **DoubleSupplier**代表一个double值结构的提供方               |
| 12   | **DoubleToIntFunction**接受一个double类型输入，返回一个int类型结果。 |
| 13   | **DoubleToLongFunction**接受一个double类型输入，返回一个long类型结果 |
| 14   | **DoubleUnaryOperator**接受一个参数同为类型double,返回值类型也为double 。 |
| 15   | **Function<T,R>**接受一个输入参数，返回一个结果。            |
| 16   | **IntBinaryOperator**接受两个参数同为类型int,返回值类型也为int 。 |
| 17   | **IntConsumer**接受一个int类型的输入参数，无返回值 。        |
| 18   | **IntFunction<R>**接受一个int类型输入参数，返回一个结果 。   |
| 19   | **IntPredicate**：接受一个int输入参数，返回一个布尔值的结果。 |
| 20   | **IntSupplier**无参数，返回一个int类型结果。                 |
| 21   | **IntToDoubleFunction**接受一个int类型输入，返回一个double类型结果 。 |
| 22   | **IntToLongFunction**接受一个int类型输入，返回一个long类型结果。 |
| 23   | **IntUnaryOperator**接受一个参数同为类型int,返回值类型也为int 。 |
| 24   | **LongBinaryOperator**接受两个参数同为类型long,返回值类型也为long。 |
| 25   | **LongConsumer**接受一个long类型的输入参数，无返回值。       |
| 26   | **LongFunction<R>**接受一个long类型输入参数，返回一个结果。  |
| 27   | **LongPredicate**R接受一个long输入参数，返回一个布尔值类型结果。 |
| 28   | **LongSupplier**无参数，返回一个结果long类型的值。           |
| 29   | **LongToDoubleFunction**接受一个long类型输入，返回一个double类型结果。 |
| 30   | **LongToIntFunction**接受一个long类型输入，返回一个int类型结果。 |
| 31   | **LongUnaryOperator**接受一个参数同为类型long,返回值类型也为long。 |
| 32   | **ObjDoubleConsumer<T>**接受一个object类型和一个double类型的输入参数，无返回值。 |
| 33   | **ObjIntConsumer<T>**接受一个object类型和一个int类型的输入参数，无返回值。 |
| 34   | **ObjLongConsumer<T>**接受一个object类型和一个long类型的输入参数，无返回值。 |
| 35   | **Predicate<T>**接受一个输入参数，返回一个布尔值结果。       |
| 36   | **Supplier<T>**无参数，返回一个结果。                        |
| 37   | **ToDoubleBiFunction<T,U>**接受两个输入参数，返回一个double类型结果 |
| 38   | **ToDoubleFunction<T>**接受一个输入参数，返回一个double类型结果 |
| 39   | **ToIntBiFunction<T,U>**接受两个输入参数，返回一个int类型结果。 |
| 40   | **ToIntFunction<T>**接受一个输入参数，返回一个int类型结果。  |
| 41   | **ToLongBiFunction<T,U>**接受两个输入参数，返回一个long类型结果。 |
| 42   | **ToLongFunction<T>**接受一个输入参数，返回一个long类型结果。 |
| 43   | **UnaryOperator<T>**接受一个参数为类型T,返回值类型也为T。    |

适用几个典型的：

### Predicate 用法

 java.util.function.Predicate接口定义了一个名叫test的抽象方法，它接受泛型 T对象，并返回一个boolean

```java
public class PredicateTest {


    public static void main(String[] args) {
        List<Integer> numberList = Arrays.asList(1,2,3,4,5,6,7,8,9);

        System.out.println("输出所有的数据");
        eval(numberList,number -> true);

        System.out.println("输入偶数");
        eval(numberList,number -> number % 2 == 0);

        System.out.println("输出大于3的");
        eval(numberList,number -> number > 3);
    }


    public static void  eval(List<Integer> list, Predicate<Integer> predicate ){
        for (Integer integer: list){
            if (predicate.test(integer)){
                System.out.printf(integer.toString());
            }
        }
    }
}
```

### Consumer 用法

java.util.function.Consumer定义了一个名叫accept的抽象方法，它接受泛型T 的对象，没有返回(void)。你如果需要访问类型T的对象，并对其执行某些操作，就可以使用 这个接口。

```java
public class ConsumerUse {

    public static void main(String[] args) {
        List<Integer> numberList = Arrays.asList(1,2,3,4,5,6,7,8,9);
        changelist(numberList,number -> System.out.printf(number + "加上数字标\n"));
    }

    public static <T> void changelist(List<T> list , Consumer<T> c){
        for (T item :
                list) {
            c.accept(item);
        }
    }
}
```

### Function用法

ava.util.function.Function<T, R>接口定义了一个叫作apply的方法，它接受一个 泛型T的对象，并返回一个泛型R的对象。如果你需要定义一个Lambda，将输入对象的信息映射 到输出，就可以使用这个接口。

````java
public class FunctionUse {

    public static void main(String[] args) {
        List<String> strings = Arrays.asList("first","second","third");
               print(changeType(strings,s -> s.length()),
                       stringLength -> System.out.println("字符串的长度"+stringLength));

    }

    public static<T,R> List<R> changeType(List<T> list , Function<T,R> function){
        List<R> result = new ArrayList<>();
         for (T item : list){
             result.add(function.apply(item));
         }
        return result;
    }

    public static<T> void print(List<T> list, Consumer<T> c) {
        for (T item:list){
            c.accept(item);
        }
    }
}
````

### Supplier用法

Supplier<T>具有唯一一个抽象方法叫作get，代表的函数描述符是()-> T，没有参数但是可以有一个返回值。

````java
public class SupplierUse {

    public static void main(String[] args) {

       Car c =  build(()-> new Car());
        System.out.println(c);

    }


    public static<T> T build(Supplier<T> supplier){
        return supplier.get();
    }
}
````

### 自己声明一个例子

这个用lambda表达式套lambda表达式来进行一个能力的锻炼。

````java
public class LambdaUse {


    public static void main(String[] args) {
        List<String> strings = Arrays.asList("first","second","third","lambda");

        System.out.println("let's lambda");
        eval(strings,((agrOne, predicate) -> { if (predicate.test(agrOne)) System.out.println(agrOne);}));
    }


    public static void  eval(List<String> list,FunctionByMe function){
        for (String s : list) {
            function.apply(s,string->string.equals("lambda"));
        }
    }

    @FunctionalInterface
    public interface FunctionByMe<T>{
       void apply(T agrOne, Predicate predicate);
    }
}
````

这个eval 方法中的一个参数是FunctionByMe这个函数接口，这个函数接口的一个参数是Predicate函数接口，所以eval中在调用FunctionByMe的时候需要明确Predicate的lambda表达式，为string->string.equals("lambda")。在调用eval的时候需要需要明确FunctionByMe的lambda表达式**((agrOne, predicate) -> { if (predicate.test(agrOne)) System.out.println(agrOne);})**，者须还需要解释的是，在明确FunctionByMe的lambda表达式的过程中，就可以使用作为参数的Predicate函数接口，可以进行调用Predicate的函数接口，然后进行实现具体的逻辑。