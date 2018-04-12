+++
title = "Lambda expressions and Stream API with Groovy or JAVA8"
date = "2016-11-11T17:29:55+09:00"
categories = ["groovy", "programming"]
tags = ["groovy", "lambda expressions", "stream api"]

+++

### Lambda expressions and Stream API with Groovy and JAVA 8
#### Iteration
- groovy

```groovy
def numbers = [1, 2, 3, 4, 5, 6]
numbers.each { e -> println e }
numbers.each { println it }
```
- java

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
numbers.forEach(e -> System.out.println(e));
numbers.forEach(System.out::println);
```

#### collect
- groovy

```
def numbers = [1, 2, 3, 4, 5, 6]
numbers.collect { it * 2 }.each { e -> println e }
println numbers.collect { it * 2 }
```
- java 8

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
numbers.stream()
 .map(e -> e * 2)
 .forEach(System.out::println);

System.out.println(
  numbers.stream()
   .map(e -> e * 2)
   .collect(Collectors.toList()));
```

#### find
- groovy

```groovy
def numbers = [1, 2, 3, 4, 5, 6]
println numbers.find { it % 2 == 0}
```
- java 8

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

System.out.println(
    numbers.stream()
           .filter(e -> e % 2 == 0)
           .findFirst());

System.out.println(
    numbers.stream()
           .filter(e -> e % 2 == 0)
           .findFirst()
           .get());

System.out.println(
    numbers.stream()
           .filter(e -> e % 2 == 0)
           .findFirst()
           .orElse(0));
```

#### findAll
- groovy

```groovy
def numbers = [1, 2, 3, 4, 5, 6]
println numbers.findAll { it % 2 == 0}
```

- java 8

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
System.out.println(
  numbers.stream()
             .filter(e -> e % 2 == 0)
             .collect(Collectors.toList()));
```


#### inject
- groovy

```groovy
def numbers = [1, 2, 3, 4, 5, 6]
println numbers.collect { it * 2 }.inject(0) { c, e -> c + e }
```

- java 8

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

System.out.println(
  numbers.stream()
             .map(e -> e * 2)
             .reduce(0, (c, e) -> c + e));

System.out.println(
  numbers.stream()
             .map(e -> e * 2)
             .reduce(0, Math::addExact));
```

#### join
- groovy

```
def names = [‘Jack’, ‘Sara’, ‘Jill’]
println names.collect {it.toUpperCase() }.join(‘, ‘)
```

- java 8

```java
List<String> names = Arrays.asList(“Jack”, “Sara”, “Jill”);

System.out.println(
  names.stream()
           .map(String::toUpperCase)
           .collect(joining(“, “)));
```

#### lazy evalution

- java 8

```java
public class Sample {
  public static boolean isGT3(int number) {
    System.out.println(“isGT3 ” + number);
    return number > 3;
  }

  public static boolean isEven(int number) {
    System.out.println(“isEven ” + number);
    return number % 2 == 0;
  }

  public static int doubleIt(int number) {
    System.out.println(“doubleIt ” + number);
    return number * 2;
  }

  public static void main(String[] args) {
    List<Integer> numbers = Arrays.asList(1, 2, 3, 5, 4, 6, 7, 8, 9, 10);
    int result = numbers.stream()
                        .filter(Sample::isGT3)
                        .filter(Sample::isEven)
                        .map(Sample::doubleIt)
                        .findFirst()
                        .get();

    System.out.println(“Result: ” + result);
    System.out.println(“Let’s see the effect of laziness”);
    numbers.stream()
           .filter(Sample::isGT3)
           .filter(Sample::isEven)
           .map(Sample::doubleIt);

    System.out.println(“No work expended”);

  }
}
```

#### infinite series
- java 8

```java
import java.util.*;
import java.util.stream.Stream;
import static java.util.stream.Collectors.toList;

public class Sample {
  public static List<Integer> seriesOfDouble(int size) {
      return Stream.iterate(1, e -> e * 2)
                   .limit(size)
                   .collect(toList());
  }

  public static void main(String[] args) {
      System.out.println(seriesOfDouble(5));
      System.out.println(seriesOfDouble(10));
  }
}
```

#### parallel collections
- java 8

```java
import java.util.*;
import java.util.stream.Stream;

public class Sample {
  public static int doubleIt(int number) {
        try { Thread.sleep(1000); } catch(Exception ex) {} //assume a computation intensive code here
        return number * 2;
  }

  public static int doubleAndTotal(Stream<Integer> values) {
        long start = System.nanoTime();
        try {
          return values.mapToInt(Sample::doubleIt)
                       .sum();
        } finally {
          long end = System.nanoTime();
          System.out.println(“Time taken (s): ” + (end – start) / 1.0e9);
        }
  }

  public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        System.out.println(doubleAndTotal(numbers.stream()));
        System.out.println(doubleAndTotal(numbers.parallelStream()));
  }
}
```

#### invoke dynamic
- java 8

```java
import java.util.*;

public class UsingLambdas {
  public static void main(String[] args) {
        List<Integer> values = Arrays.asList(1, 2, 3);
        values.forEach(e -> System.out.println(e));
        values.forEach(e -> System.out.println(e));
        values.forEach(e -> System.out.println(e));
        values.forEach(e -> System.out.println(e));
        values.forEach(e -> System.out.println(e));
  }
}

//javac UsingLambdas
//ls (or dir)
//See the lack of inner classes.
//Do javap -c UsingLambdas
//examine the bytecode and look for invokedynamic
//now remove the .class files before procedding to the other example
```


#### new (Groovy)
- groovy

```groovy
boolean isGT3(int number) {
  println “isGT3 $number”
  number > 3
}

boolean isEven(int number) {
  println “isEven $number”
  number % 2 == 0
}

int doubleIt(int number) {
  println “doubleIt $number”
  number * 2
}

def numbers = [1, 2, 3, 5, 4, 6, 7, 8, 9, 10]
//traditional Groovy functional style is eager.
def eagerResult = numbers.findAll { isGT3(it) }.findAll { isEven(it) }.collect { doubleIt(it) }.find { it }
println “Eager Result: $eagerResult”
//We can use Java 8 Streams and be lazy – meaning efficient
def result = numbers.stream()
                        .filter { isGT3(it) }
                        .filter { isEven(it) }
                        .map { doubleIt(it) }
                        .findFirst()
                        .get()
println “Result: $result”
println “Let’s see the effect of laziness”
numbers.stream()
           .filter { isGT3(it) }
           .filter { isEven(it) }
           .map { doubleIt(it) }
println “No work expended”
```
