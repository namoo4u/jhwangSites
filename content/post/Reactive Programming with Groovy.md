---
title : "Reactive Programming with Groovy"
date : "2016-11-09T13:07:41+09:00"
Tags :
  - "groovy"
  - "reactive"
  - "programming"
categories :
  - "Programming"
  - "Architecture"
---
### Reactive Streams, Reactive Extensions (Rx)
- The Problem :
  - Performacen : our pages should render within 1000 milliseconds
  - The Rise of microservices : free up resources with Async Operations & Non-Blocking I/O

#### What is reactive stream (Rx) ?
##### collections + time
##### Single abstration over data from many sources
##### Observer Pattern
- Push (not pull) based Iterators

##### Stream Based Funcational Programming
- Imperative vs Reactive Stream

```
// Iterative
  List numbers = 1..100
  int max = numbers.size()
  Mpa result = [count:0, sum:0]
  for (int i=0; i<max; i++) {
    // only work with even numbers
    if (numbers[i] % 2 == 0)  {
      result.count ++
      result.sum += numbers[i]
    }
  }
  println "Results were ${result}"

// Reactive Stream
  rx.Obserable.from(1..100)
    .filter({int num -> num %2 == 0})
    .reduce([count:0, sum:0], {Map data, int num -->
        data.count ++
        data.sum += num
        data
      })
      .subscribe({println "Result were ${it}"})

```

- Groovy Collections supports stream-like Operations
if this make sense to you, then you're ready for Rx

```
result = (1..100).collect { if (it % 2 == 0) {it}}
  .grep()
  .inject([count:0, sum:0]){ acc, val ->
    acc.count ++
    acc.sum += sum
    acc
  }
println "Result were: ${result}"
```

##### Streams with Extensions for Reactive Programming
##### Rx makes Async behavior easy!
##### (Reactive Pull) Backpressure

#### Rx Simplifies Complex work
- RxJava : Bring Reactive Streams to the JVM
- But Quickly switch to rxGroovy

```
rx.Obserable.from(1..1000)
  .filter(new Func<Integer, Boolean>()  {
      @Override
      Boolean call(Integer integer) {
        return integer % 2 == 0
      }
    })
  .map(new Func<integer, Integer>() {
    @Override
    Integer call(Integer integer) {
      integer * integer
    }
  })
  .subscribe(new Action<Integer>()  {
      @Override
      void call(Integer integer)  {
        System.out.println("Have even square: "+ integer)
      }
  })
```

```
@Grapes(
    @Grab('io.reactivex:rxgroovy:1.0.3')
  )
  import rx.*

rx.Observable.from(1..1000)
  .filter {it % 2 == 0}
  .map {it * it}
  .subscribe {
    println "Have even square: ${it}"
  }
```

#### Ratpack
- High performance web framework
- Non-opinionated
- Non-Blocking Network Stack
- Built on Reactive Streams, Netty, Java 8, Guice
- Fully embodies reactive
- Deterministic Asynchronous code


#### Akka
#### Reactor


출처 : [Reactive Streams and the Wide World of Groovy](http://www.slideshare.net/StevePember/reactive-streams-and-the-wide-world-of-groovy-64526341)
