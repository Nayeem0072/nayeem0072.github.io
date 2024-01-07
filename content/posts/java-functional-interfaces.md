---
title: "Java Functional Interfaces"
date: 2024-01-07T19:00:00+06:00
description: "Understanding Java Functional Interfaces"
tags: [java, function, lambda, interface, supplier, consumer, predicate]
draft: false
---

Introduced in Java SE 8, functional interface is an interface that contains only one abstract method, hence they are sometimes called **SAM (Single Abstract Method) interfaces**. Introduction of functional interfaces was primarily to enable the use of **Lambda Expressions** and the new functional features added in Java 8. 

Though functional interfaces introduced in Java 8, the SAM interfaces were already quite popular before then (like `Comparable, Runnable, EventListener, Comparator` etc). They help programmer to pass around block of codes as functions, resulting in precise encapsulation and readability of codes. Functional interfaces allows Java programmers to use **Lambda Expressions** with them, which means they can pass a Lambda Expression to these functional interfaces. 

Here is an example of a `Comparator` implementation, with passing code block as parameter:
```java
Collections.sort(list, new Comparator() {
   public int compare(String s1, String s2) {
      return s1.length() - s2.length();
   }
});
```

The `@FunctionalInterface` annotation is used to declare a functional interface. While it's not mandatory to use this annotation when creating a functional interface, it is a good practice to add it. The compiler can then verify that the interface has only one abstract method, and if it has more than one, it will result in a compilation error.

Here's an example of a functional interface:
```java 
@FunctionalInterface
public interface MyFunctionalInterface {
    void myAbstractMethod();

    // Other default or static methods are allowed in a functional interface
    default void myDefaultMethod() {
        // implementation
    }
    
    static void myStaticMethod() {
        // implementation
    }
}
```

Below listed some of the functional interfaces that are being widely used in Java.

## Consumer
A `Consumer` is a functional interface in the `java.util.function` package. A `Consumer` is designed to represent an operation that takes an **input parameter** and performs some operation on that parameter **without returning any result**. It is essentially a function that takes an argument and performs some side-effect, such as modifying the argument or performing an action. `Consumer` is generally used for perform a static action on each element of a collection.

Here is the declaration of the `Consumer` interface:
```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);

    // Other default and static methods
}
```

Here is an example demonstrating the use of a `Consumer` with a list:
```java
public class ConsumerExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

        // Using a Consumer to print each element of the list
        Consumer<String> printConsumer = name -> System.out.println("Hello, " + name);
        names.forEach(printConsumer);
    }
}

// Output:
// Hello, Alice
// Hello, Bob
// Hello, Charlie
// Hello, David
``` 

## Supplier
A `Supplier` is a functional interface in the `java.util.function` package. A `Supplier` is designed to represent an operation that **does not take** an argument, but **returns a value** of type `T`. It contains only one method. `Supplier` is generally used for producing computationally expensive results.

Here is the declaration of the `Supplier` interface:
```java
@FunctionalInterface
public interface Supplier<T> {
    T get();

    // Other default and static methods 
}
```

Here is an example demonstrating the use of a `Supplier`:
```java
public class SupplierExample {
    public static void main(String[] args) {
        // Using a Supplier to generate a random integer when needed
        Supplier<Integer> randomIntegerSupplier = () -> (int) (Math.random() * 100);
        
        // Get and print a random integer
        int randomValue = randomIntegerSupplier.get();
        System.out.println("Random Integer: " + randomValue);
    }
}

// Output:
// Random Integer: 678300
```

## Predicate
A `Predicate` is a functional interface in the `java.util.function` package. A `Predicate` is designed to represent an operation that **takes an input parameter** and **returns a `Boolean`**. Usually, it is used to apply in a filter for a collection of objects.

Here is the declaration of the `Predicate` interface:
```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);

    // Other default and static methods 
}

```

Here is an example demonstrating the use of a `Predicate`:
```java
public class PredicateExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Using a Predicate to filter even numbers
        Predicate<Integer> isEven = num -> num % 2 == 0;

        numbers.stream()
               .filter(isEven)
               .forEach(System.out::println);
    }
}

// Output:
// false
// true
// false
// true
// false
// true
// false
// true
// false
// true
```

## Function
A `Function` is a functional interface in the `java.util.function` package. A `Function` is designed to represent an operation that **takes an input parameter** and **returns a value**. A common use of `Function` is when you want to transform or convert elements in a collection or stream.

Here is the declaration of the `Function` interface:
```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);

    // Other default and static methods 
}

```

Here is an example demonstrating the use of a `Function`:
```java
public class FunctionExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "orange", "grape");

        // Using a Function to get the length of each word
        Function<String, Integer> getLength = word -> word.length();
        words.stream()
             .map(getLength)
             .forEach(System.out::println);
    }
}

// Output:
// 5
// 6
// 6
// 5
```

## Other Functional Interfaces

Apart from the four functional interfaces described above, there are also `UnaryOperator` and `BinaryOperator`, which are special cases of functions that take one or two arguments, respectively, and produce a result of the same type.

## Conclusion

Learning about functional interfaces is of great importances as they work as the stepping stones for various important fields related to Java, like Streams, Composing Functions, Concurrency, Functional Programming etc. Hopefully this guide will be helpful for you in your future explorations.  