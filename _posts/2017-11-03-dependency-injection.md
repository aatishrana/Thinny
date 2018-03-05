

# Dependency Injection, What, Why and How

So you have heard of D.I everywhere. You have tried understanding it, but you need a simple explanation.

So let's understand Dependency Injection in as little time n effort as possible.

So here we go..

'**Dependency**' as it's name suggest is just telling us that something can not work unless it has that other thing. Now these 'things' can be a Class or a Method or even a Package. 

The point is if A is dependent on B, A can not work unless B is provided to it.

Let's see a code example.

```java
import Engine;
class Car{
  Engine engine;
  Car(){
   this.engine = new Engine();
  }
  void moveForward(){
    this.engine.accelerate();
    ....
  }
}
```
In the above example, in order to use class Car's moveForward method, class Engine must be created first.

<br/>

'**Dependency Injection**' means that our dependencies should be given to us from outside when we need it.

example

```java
import Engine;
class Car{
  Engine engine;
  Car(Engine engine){
   this.engine = engine;
  }
  void moveForward(){
    this.engine.accelerate();
    ....
  }
}
```

Unlike first example, in this example rather then creating class Engine's instance our self, we are given an instance through constructor.

### That's it
In essence the concept is that simple, like Hollywood Principle 

> *Don't call us, we'll call you*
> 
> *Don't call around for your dependencies, we'll give them to you when we need you*

<br/><br/><br/>

### Wanna dive deep?

so what is the point of doing this? why can't we simply create instances of our dependencies?

well their are many reasons but the biggest one is

**Loose Coupling**

*A software which is loosely coupled is considered a good software, because it is reusable,
testable and extensible.*

<br/>

So how does D.I make loose coupling.

**If we inject our dependencies, we can easily change what we are injecting. daaah!** 

So now we can easily create our classes and whether or not they are suppose to be used together, we can decide that later on.

lets see another example

```java
import Engine;
class Car{
  Engine engine;
  Car(Engine engine){
   this.engine = engine;
  }
  void moveForward(){
    this.engine.accelerate();
    ....
  }
}
```
```java
import Engine;
class Truck{
  Engine engine;
  Truck(Engine engine){
   this.engine = engine;
  }
  void moveForward(){
    this.engine.accelerate();
    ....
  }
}

```

In the above example we are reusing our Engine class for both Car and Truck. We could have very easily created new instances of engine inside our constructor, but imagine if object creation code of Engine class was changed.

like from this:

```java
	Car(){
    this.engine = new Engine();
    }
```
to this:
```java
	Car(){
    this.engine = new Engine(turboCharge, nitrousOxide);
    }
```

Not only do we have to change our code in Car and Truck class, but anywhere else where Engine class is used. We now also have to create and manage these small objects
+ turboCharge 
+ nitrousOxide

*where will they be created?*

*should they be private fields of Car?*

*what if they themselves need objects while creation?*

<br/>

Better approach is to let Car class be responsible only for it's own functionality rather than the responsibility of creating an Engine. 

Injecting would also help us test our Car class
```java
  Car(Engine fakeEngine){
  this.engine = fakeEngine;
  }
```
```java
  car.setPosition(0, 0);
  car.moveForward();
  assertEquals(10, car.getPositionX());
```
we don't need a real Engine to test our Car class, we can easily pass a fake engine and write test cases for our Car class's methods.

<br/>

#### Adding Abstraction

We can go one step further by using abstract types instead of concrete types and make our classes more extensible.

let's say instead of a concrete class Engine, we have an abstract Engine

```java
interface Engine{
	void turnOn();
    void turnOff();
    ...
}
```
and some of it's concrete implementation
```java
class PetrolEngine implements Engine{
	void turnOn(){
    	this.useSparkPlug();
        ...
    }
    void turnOff(){
    	...
    }
}
```
```java
class DieselEngine implements Engine{
	void turnOn(){
    	this.useFuelInjector();
        ...
    }
    void turnOff(){
    	...
    }
}
```

then we can use our abstract engine in our Car class

```java
import Engine;
class Car{
  Engine engine;
  Car(Engine engine){
   this.engine = engine;
  }
  void moveForward(){
    this.engine.accelerate();
    ....
  }
}
```

but which real implementation of engine will be used in Car, will be decided when we create our Car and run our program

```java
  class App{
  	public static void main(String[] args){
    	Car carOne = new Car(new PetolEngine());
        carOne.moveForward();
        
        Car carTwo = new Car(new DieselEngine());
        carTwo.moveForward();
    }
  }
```

we can do this in Truck class and all other classes which uses Engine.
In future if we create a new type of engine "SolarEngine" we can easily swap it without worrying of changes in any of our Vehicle class.

<br/>

## Conclusion
**What is Dependency Injection**
+ Don't call around for your dependencies, we'll give them to you when we need you

**Why use Dependency Injection**
+ To write loosely coupled code

**How to use Dependency Injection**
+ Pass dependencies through constructor, setter methods or directly to public fields.

<br/><br/>

## P.S.
With dependency injection our implementation code is a lot clean and easy, however we have polluted our calling code
```java
class App{
  	public static void main(String[] args){
    	NitrousOxide nitro = new NitrousOxide();
    	TurboCharge turboCharge = new TurboCharge();
    	Engine engine = new SuperEngine(turboCharge, nitro);
    	Car car = new Car(engine);
        car.moveForward();
    }
  }
```
This has created a lot of boilerplate code, which doesn't do anything but waste a lot of developer's time. This boilerplate also have to be created in right order otherwise it won't work. This can easily become very complex and hard to maintain if we are working on a large project. 

To avoid this, we can use a D.I library, which we will discuss in future posts.
