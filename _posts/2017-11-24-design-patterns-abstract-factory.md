# Design Pattern - Abstract Factory

The abstract factory pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.

<br>

If you don’t know what **Factory Pattern** is, I would encourage you to go through [that](http://aatishrana.com/blog/design-patterns-factory) first.

### Done?
I will assume you know what factory pattern is from now on.

<br>

The abstract factory and factory method are similar to each other, the only difference however is

* Factory method describes a factory that creates multiple subtypes of a **single product**.
* Abstract factory describes a factory that creates multiple subtypes of **multiple products**.

The key point here is (**families of related or dependent objects**). When we create multiple objects from a factory, in order to ensure that these multiple objects are right for each other, we use the abstract factory pattern.

Continuing our example from Factory Method's [post](http://aatishrana.com/blog/design-patterns-factory). We have an abstract class Gameplay and an abstract class GameplayFactory.

```java
public abstract class Gamplay{
  // some common methods
  // some abstract methods
}
```

```java
public interface GameplayFactory{
  Gameplay getGameplay();
}
```

In the example, there were many implementations of Gameplay(*SunnyWeatherGameplay, RainyWeatherGameplay*) and GameplayFactory(*RandomGameplayFactory, StoryGameplayFactory*) however, we were only talking about the **weather**, which is just **one product**.

In GTA we have other things in our environment like DayTime, Traffic, Pedestrians, etc. These things also change depending on what the player is doing.

So these entities can also be considered a subtype of Gameplay.

```java
public class MorningGameplay extends Gameplay{
  // code related to rendering morning environment
  // sky's base color is blue, no stars
}
```
```java
public class NightGameplay extends Gameplay{
  // code related to rendering night environment
  // sky's base color is black, with stars
}
```

Since now we need multiple Gameplays, our contract for GameplayFactory is also changed to this:

```java
public interface GameplayFactory{
  Gameplay getWeatherGameplay();
  Gameplay getTimeGameplay();
}
```

Now our concrete implementations of GameplayFactory gives us **multiple products**(Weather and Time).

Let's say we want a Sunny Morning.

```java
public class SunnyMorningGameplayFactory implements GameplayFactory{

  @Override
  public Gameplay getWeatherGameplay(){
    return new SunnyWeatherGameplay();
  }
  
  @Override
  public Gameplay getTimeGameplay(){
    return new MorningGameplay();
  }
}
```

Or a Rainy Night.

```java
public class RainyNightGameplayFactory implements GameplayFactory{

  @Override
  public Gameplay getWeatherGameplay(){
    return new RainyWeatherGameplay();
  }
  
  @Override
  public Gameplay getTimeGameplay(){
    return new NightGameplay();
  }
}
```
The key point here is that we will never create a SunnyNightFactory because the 2 implementations **SunnyWeatherGameplay** and **NightGameplay** although being a subtype of Gameplay are **not related** to each other. We can not have Sun with a black sky and stars together.

```java
//	Sun in night is WRONG
public class SunnyNightGameplayFactory implements GameplayFactory{

  @Override
  public Gameplay getWeatherGameplay(){
    return new SunnyWeatherGameplay();
  }
  
  @Override
  public Gameplay getTimeGameplay(){
    return new NightGameplay();
  }
}
```

So now a random factory would be something like this:

```java
public class RandomGameplayFactory1 implements GameplayFactory {

  @Override
  public Gameplay getWeatherGameplay(){
    Random r = new Random();
    switch(r.nextInt(2)){
      case 0:
        return new SunnyWeatherGameplay();
      case 1:
        return new RainyWeatherGameplay();
    }
  }
    
  @Override
  public Gameplay getTimeGameplay(){
    Random r = new Random();
    switch(r.nextInt(2)){
      case 0:
        return new MorningGameplay();
      case 1:
        return new AfternoonGameplay();
    }
  }
}
```

Now obviously Rockstar Games would never use this type of **absurd design** for their gameplay, but the point is in Abstract Factory Pattern we create families of related objects.

<br>

<br>

Another sensible example(from [Wikipedia](https://en.wikipedia.org/wiki/Abstract_factory_pattern)) for the abstract factory would be UI rendering for different Operating Systems.

```java
public interface Widget{
  void paint();
}

public interface GUIFactory{
  public Widget createButton();
  public Widget createToolbar();
}
```
And we have different widgets for different operating systems
```java
public class WinButton implements Widget{

  @Override
  public void paint(){
    // paint button of windows design
  }
}

public class WinToolbar implements Widget{

  @Override
  public void paint(){
    // paint toolbar of windows design
  }
}

public class OSXButton implements Widget{

  @Override
  public void paint(){
    // paint button of OSX design
  }
}

public class OSXToolbar implements Widget{

  @Override
  public void paint(){
    // paint toolbar of OSX design
  }
}
```
So our factories would be

```java
public class WindowsFactory implements GUIFactory{

  @Override
  public Widget createButton(){
    return new WinButton();
  }
  
  @Override
  public Widget createToolbar(){
    return new WinToolbar();
  }
}

public class OSXFactory implements GUIFactory{

  @Override
  public Widget createButton(){
    return new OSXButton();
  }
  
  @Override
  public Widget createToolbar(){
    return new OSXToolbar();
  }
}
```

#### That is it. That’s the factory method pattern.

<br>

#### Conclusion

The abstract factory pattern provides an interface(*GameplayFactory*) for creating families of related or dependent objects(*Weather, Time*) without specifying their concrete classes.
