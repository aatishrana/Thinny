# Design Patterns - Strategy

In the strategy pattern,  method of a class is defined as a separate interface and concrete implementation of that interface is used depending on our need. This allows better decoupling between the method and the class that uses the method.

If you don't know what Polymorphism is I would encourage you to go through that first.

<br>

### Done?
I will assume you know what polymorphism is from now on.

So in polymorphism or more specifically (subtype / inclusion) polymorphism our compiler allow us to write a method that takes a class T in its argument, but will also work correctly if passed a subclass of T

example:

```java
public class Application{
  public static void main(String[] args){
    describeAnimal(new Lion());
    describeAnimal(new Duck());
  }
  
  public static void describeAnimal(Animal animal){
    animal.walk();
    animal.eat();
    animal.fly();
  }
}
```
The method describeAnimal takes 1 argument of type Animal but it will work correctly even when passed an argument of Animal's sub type (Lion, Duck, etc).

```java
public interface Animal{
  void walk();
  void eat();
  void fly();
}
```

```java
public class Lion implements Animal{
 
  @Override
  public void walk(){
    // code of animal walking on 4 legs
  }
  
  @Override
  public void eat(){
    // code of animal eating and processing meat
  }

  @Override
  public void fly(){
    // this animal can't fly, handle that
  }
}
```

```java
public class Duck implements Animal{
 
  @Override
  public void walk(){
    // code of animal walking on 2 legs
  }
  
  @Override
  public void eat(){
    // code of animal eating bread crumbs
  }

  @Override
  public void fly(){
    // code of animal flying at low altitude
  }
}
```

Now this polymorphic behavior of animal looks good but in large applications where we add more and more types of animals, because they have different types of behaviors(strategies) on how to walk, eat, fly and so on, things can go out of hands very quickly.

Currently these 2 types have completely different strategies of all 3 methods but if we add more types of animals, many of them would have all or some similarities.

example:

```java
public class Monkey implements Animal{
 
  @Override
  public void walk(){
    // code of animal walking on 2 legs
  }
  
  @Override
  public void eat(){
    // code of animal eating banana
  }

  @Override
  public void fly(){
    // can not fly
  }
}
```
now monkey walks on 2 legs just like duck however it doesn't fly like lion and at the same time it is also vegetarian like the duck. 

we can add more animals, like Dragon which can eat meat, fly high and walks on 2 legs.
or add Chimpanzee which eats banana, can not fly and walk on 2 legs exactly same as Monkey.

Moreover as we add more behavior to our animal class(swim, makeSound, etc), there are going be more combinations of behaviors.

Naive programmers might start copy pasting code of similar behavior in each type, or worse they might create a super class with common behavior and start extending from them until they get stuck with maximum single inheritance feature of almost every major object oriented language (Java, c# etc) 

example
```java
public class WalkingOnTwoFeet{
  void walk(){
    // code of animal walking on 2 legs
  }
}
public class Vegetarian{
  void eat(){
    // code of animal eating veg food
  }
}
public class NonFlyer{
  void fly(){
    // empty
  }
}
```
```java
public class Monkey extends WalkingOnTwoFeet implements Animal{
 
  @Override
  public void walk(){
    super.walk();
  }
  
  @Override
  public void eat(){
    // can not extend Vegetarian
  }

  @Override
  public void fly(){
    // can not extend NonFlyer
  }
}
```

<br>

### In strategy pattern we take our behavior(walking, eating, flying) and define them in separate concrete classes and use them via an abstract interface. 

first we create interfaces for each of our behavior

```java
public interface WalkingStrategy{
  void walk();
}
public interface EatingStrategy{
  void eat();
}
public interface FlyingStrategy{
  void fly();
}
```
and then their concrete implementations depending on our needs.

<br>

for walking:

```java
public class WalkingOnTwoFeet implements WalkingStrategy{
  void walk(){
    // code of animal walking on 2 legs
  }
}
```
```java
public class WalkingOnFourFeet implements WalkingStrategy{
  void walk(){
    // code of animal walking on 4 legs
  }
}
```

<br>

for eating:

```java
public class Vegetarian implements EatingStrategy{
  void eat(){
    // code of animal eating veg food
  }
}
```
```java
public class NonVegetarian implements EatingStrategy{
  void eat(){
    // code of animal eating non veg food
  }
}
```

<br>

for flying:

```java
public class NonFlyer implements FlyingStrategy{
  void fly(){
    // empty
  }
}
```
```java
public class LowFlyer implements FlyingStrategy{
  void fly(){
    // code of animal flying at low altitude
  }
}
```
```java
public class HighFlyer implements FlyingStrategy{
  void fly(){
    // code of animal flying at high altitude
  }
}
```

<br>

So now our animals would use these strategies depending on its type

```java
import WalkingOnFourFeet;
import NonVegetarian;
import NonFlyer;

public class Lion implements Animal{
 
  @Override
  public void walk(){
    new WalkingOnFourFeet().walk();
  }
  
  @Override
  public void eat(){
    new NonVegetarian().walk();
  }

  @Override
  public void fly(){
    new NonFlyer().fly();
  }
}
```

now this example of Lion is **very bad** and must not be used in production code(or anywhere for that matter). 

As you can see the concrete implementations of each of our strategies are created inside Lion class, so we have to import them. Which means every time we want to use a new strategy for lion we have to change code inside Lion then recompile and redeploy Lion along with our new strategy.

**Instead we should use our abstract interfaces of strategies and inject concrete implementations through constructor injection.**

If you don't know what Dependency Injection is and it's benefits go through it from [here](http://aatishrana.com/blog/dependency-injection)

<br>

So a better version of Lion would be

```java
import WalkingStrategy;
import EatingStrategy;
import FlyingStrategy;

public class Lion implements Animal{
 
  private WalkingStrategy walkingStrategy;
  private EatingStrategy eatingStrategy;
  private FlyingStrategy flyingStrategy;
  
  public Lion(WalkingStrategy walkingStrategy,
              EatingStrategy eatingStrategy,
              FlyingStrategy flyingStrategy){
    this.walkingStrategy = walkingStrategy;
    this.eatingStrategy = eatingStrategy;
    this.flyingStrategy = flyingStrategy;
  }
  
  @Override
  public void walk(){
    this.walkingStrategy.walk();
  }
  
  @Override
  public void eat(){
    this.eatingStrategy.walk();
  }

  @Override
  public void fly(){
    this.flyingStrategy.fly();
  }
}
```

and when we need to create a Lion

```java
public class Application{
  public static void main(String[] args){
    WalkingStrategy fourFeet = new WalkingOnFourFeet();
    EatingStrategy nonVeg = new NonVegetarian();
    FlyingStrategy noFly = new NonFlyer();
    
    Animal lion = new Lion(fourFeet, nonVeg, noFly);
  }
}
```

<br>

same for other types of animals

```java
import WalkingStrategy;
import EatingStrategy;
import FlyingStrategy;

public class Duck implements Animal{
 
  private WalkingStrategy walkingStrategy;
  private EatingStrategy eatingStrategy;
  private FlyingStrategy flyingStrategy;
  
  public Duck(WalkingStrategy walkingStrategy,
              EatingStrategy eatingStrategy,
              FlyingStrategy flyingStrategy){
    this.walkingStrategy = walkingStrategy;
    this.eatingStrategy = eatingStrategy;
    this.flyingStrategy = flyingStrategy;
  }
  
  @Override
  public void walk(){
    this.walkingStrategy.walk();
  }
  
  @Override
  public void eat(){
    this.eatingStrategy.walk();
  }

  @Override
  public void fly(){
    this.flyingStrategy.fly();
  }
}
```

```java
public class Application{
  public static void main(String[] args){
    WalkingStrategy twoFeet = new WalkingOnTwoFeet();
    EatingStrategy veg = new Vegetarian();
    FlyingStrategy lowFly = new LowFlyer();
    
    Animal duck = new Duck(twoFeet, veg, lowFly);
  }
}
```

#### That is it. That's the strategy pattern

<br>

#### Ps.
In cases where there are no other methods and properties for Lion except for these strategies which are being injected, we don't even need a separate class "Lion" to represent it, we can just create an AnimalFactory class and it can have a method which is responsible of creating the right Lion type for us using just the Animal interface.

Thats *Factory pattern*, a topic we will discuss in future.
