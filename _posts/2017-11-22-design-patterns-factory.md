# Design Pattern - Factory Method

The factory method pattern defines an interface for creating an object but lets subclasses decide which class to instantiate. Factory method let the class defer instantiation to subclasses.

<br>

If you don’t know what **Dependency Injection** is, I would encourage you to go through [that](http://aatishrana.com/blog/dependency-injection) first.

### Done?
I will assume you know what dependency injection is from now on.

<br>

In every software system, there are 2 type of code
 * Code which does something cool
 * Code which glue together the cool code

If we follow all good design principles, eventually we would have to write the glue code which is responsible for creating all those small dependencies, glue them together as per our configuration and run our application.

continuing our example from dependency injection's [post](http://aatishrana.com/blog/dependency-injection) 

```java
public class App{
  public static void main(String[] args){
    NitrousOxide nitro = new NitrousOxide();
    TurboCharge turboCharge = new TurboCharge();
    Engine engine = new SuperEngine(turboCharge, nitro);
    Car car = new Car(engine);
    car.moveForward();
  }
}
```
We have this boilerplate code to take care of. We need to create NitrousOxide, TurboCharge and Engine in this order for us to create a Car's instance.

This code can be written in a factory

```java
public class CarFactory{
  public Car getCar(){
    NitrousOxide nitro = new NitrousOxide();
    TurboCharge turboCharge = new TurboCharge();
    Engine engine = new SuperEngine(turboCharge, nitro);
    Car car = new Car(engine);
    return car;
  }
}
```

And can be used like this

```java
public class App{
  public static void main(String[] args){
    Car car = new CarFactory().getCar();
  }
}
```

This is called **Simple Factory**. The Simple factory is **not considered a design pattern**, because we know which class we want to instantiate. We just have to write the instantiation code in correct order and our problem is solved.

<br>

### When we don't know which class we want to instantiate and there is some logic behind its creation, that is Factory method pattern.

Let's take an example of a popular game GTA (Grand Theft Auto) San Andreas. If you haven't played that game, you should definitely try. It's an awesome game and available on every platform, even on mobile.

So, In GTA we have a character as our main player which interact with other characters and environment. Whenever it interacts with a different entity we play the game in a different way.

example:- We start a mission on a sunny morning, but when the mission starts we have a raining evening because that mission requires so.

So let's say we have a class Gameplay, which is responsible for all the changes in the game based on user's actions

```java
public class Gameplay{
  public Gameplay(){
  }
    
  // render on screen
  // do this on this button click
  //...
}
```

In order to create a different type of gameplay, we can make our Gameplay class abstract and derive different concrete implementations.

example:

```java
public abstract class Gamplay{
  // some common methods
  // some abstract methods
} 
```

```java
public class SunnyWeatherGameplay extends Gameplay{
  // code related to rendering sunny environment
  // how player should works in sunny environment
}
```

```java
public class RainyWeatherGameplay extends Gameplay{
  // code related to rendering rain
  // how player should works in rainy environment(player gets wet, etc)
}
```

Now when we know which type of Weather we want, we can easily create its instance and keep on doing our work, however when we don't know which one we want, or we have some sort of logic based on which the instance is to be created, then we use a factory method.

First we create an abstract factory.

```java
public interface GameplayFactory{
  Gameplay getGameplay();
}
```

Let's say we want the weather to be created randomly, then our implementation would be something like this.

```java
public class RandomGameplayFactory implements GameplayFactory{
  public Gameplay getGameplay(){
    Random r = new Random();
    switch(r.nextInt(2)){
      case 0:
        return this.getSunnyGameplay();
      case 1:
        return this.getRainyGameplay();
    }
  }
  
  private Gameplay getSunnyGameplay(){
    // create SunnyWeatherGameplay dependencies and pass them through constructor
    return new SunnyWeatherGameplay();
  }
  
  private Gameplay getRainyGameplay(){
    // create RainyWeatherGameplay dependencies and pass them through constructor
    return new RainyWeatherGameplay();
  }
}
```

Or let's say we want the weather to be decided based on the current mission.

```java
public class StoryGameplayFactory implements GameplayFactory{

  private Mission mission;
  
  public StoryGameplayFactory(Mission mission){
    this.mission = mission;
  }
  
  public Gameplay getGameplay(){
    // if user reached this level create rainy weather
    // if this specific mission then create this weather
    // ...
  }
  
  private Gameplay getSunnyGameplay(){
    // create SunnyWeatherGameplay dependencies and pass them through constructor
    return new SunnyWeatherGameplay();
  }
  
  private Gameplay getRainyGameplay(){
    // create RainyWeatherGameplay dependencies and pass them through constructor
    return new RainyWeatherGameplay();
  }
}
```

And now we can use different factories based on what user does.

```java
public class App{
  public static void main(String[] args){
    // mission started
    GameplayFactory factory = new StoryGameplayFactory();
    Gameplay gamePlay = factory.getGameplay();
    gamePlay.play();
    
    //..
    // user just roaming around
    factory = new RandomGameplayFactory();
    gamePlay = factory.getGameplay();
    gamePlay.play();
  }
}
```

#### That is it. That’s the factory method pattern.

<br>

#### Conclusion

The factory method pattern defines an interface(*GameplayFactory*) for creating an object(*Gameplay*) but lets subclasses(*RandomGameplayFactory, StoryGameplayFactory*) decide which class(*SunnyWeatherGameplay, RainyWeatherGameplay*) to instantiate. Factory method let the class defer instantiation to subclasses.

<br>

#### P.S.
The RandomGameplayFactory example above is a bad example because it violates OCP (Open/Close principle). A better implementation might be something like this.


```java
public class RandomGameplayFactory implements GameplayFactory{

  private Gameplay[] gameplays;
  
  public RandomGameplayFactory(Gameplay ...g){
    this.gameplays = g;
  }
  
  public Gameplay getGameplay(){
    Random r = new Random();
    int index = r.nextInt(this.gameplays.length);
    return this.gameplays[index];
  }
}
```

We will talk about OCP and other SOLID principles in future posts.
