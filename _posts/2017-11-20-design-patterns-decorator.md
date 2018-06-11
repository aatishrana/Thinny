# Design Pattern - Decorator

The decorator pattern attaches additional responsibility to an object dynamically.


Let's say we need to create a restaurant menu, where we are going to prepare and serve different types of dishes.
We also need to bill the customer as per the cost of each item.

```java
public class Pizza{
  void prepare(){
    // preparation code
  }
  
  String description(){
    return "pizza";
  }
  
  int cost(){
    return 50;
  }
}
```

```java
public class Burger{
  void prepare(){
    // preparation code
  }
  
  String desc(){
    return "burger";
  }
  
  int price(){
    return 47;
  }
}
```

Just like the class Pizza and Burger, we would have to create a lot of other food item classes and then we can possibly use them like this

```java
public class App{
  public static void main(String[] args){
    Pizza pizza = new Pizza();
    pizza.prepare();
    
    Burger burger = new Burger();
    burger.prepare();
    
    Pasta pasta = new Pasta();
    pasta.prepare();
    
    int totalCost = 0;
    totalCost += pizza.cost();
    totalCost += burger.price();
    totalCost += pasta.cost();
    
    // ....
    // bill the customer
  }
}
```

Now, this is very basic code, this type of implementation would never be used in a real-world project. To improve this we can make an abstract FoodItem and extend different dishes from it which would be like this.

```java
public interface FoodItem{
  void prepare();
  String description();
  int cost();
}
```

```java
public class Pizza implements FoodItem{
  @Override
  void prepare(){
    // prepration code
  }
  
  @Override
  String description(){
    return "pizza";
  }
  
  @Override
  int cost(){
    return 50;
  }
}
```

We would still have to create a lot of food item classes but now it is easier to add a new type of food and calculate the total cost.

```java
public class App{
  public static void main(String[] args){
    List<FoodItem> items = new ArrayList<>();
    items.add(new Pizza());
    items.add(new Burger());
    items.add(new Pasta());
    
    int totalCost = items.stream()
                         .map(item -> item.cost())
                         .reduce(0, (c1, c2) -> c1 + c2);
    
    // ....
    // bill the customer
  }
}
```

This is a better than before. There is no inconsistency in method names for every food item, all the necessary methods and data are present and we can use the abstract FoodItem interface for grouping and sending of data as well.

#### But in the real world, things ain't that simple.

Just a Pizza class is not sufficient enough, real pizza has subcomponents like cheese, veg toppings, non-veg toppings, tomato sauce, bread base etc. So does the Burger, Pasta, so on and so forth.

These subcomponents are usually common in components, you can use cheese in Pizza as well as in Pasta.

If you add cost, quantity, preparation etc for subcomponents as well, things can get very complex.

example

```java
public class Pizza implements FoodItem{
  private boolean hasCheese;
  private VegTopping vegTopping;
  private NonVegTopping nonVegTopping;
  private int sauceQuantity;
  //...
  
  @Override
  void prepare(){
    // prepare base
    if(hasCheese)
      // add cheese
    vegTopping.prepare();
    nonVegTopping.prepare();
    // add toppings
    // handle cases of only veg/ only non-veg/ both allowed
  }
  
  @Override
  String description(){
    String desc = "pizza";
    if(hasCheese)
      desc = desc+"with chesse";
    // handle cases of only veg/ only non-veg/ both allowed
    return desc;
  }
  
  @Override
  int cost(){
    int basePrice = 50;
    vegTopping.cost();
    nonVegTopping.cost();
    if(hasCheese)
      // add more cost
    return total;
  }
  
}
```

As you can see our Pizza class is now very complicated. It has multiple if conditions on almost every method, it is hard to read and understand, in order to change or add a new variety of pizza I would have to change a lot in this class which violates Open Close principle, because of so many things going on it is prone to errors.

And this type of complexity is repeated on every FoodItem (Burger, Pasta, etc).
So we have to maintain all this complexity in all those classes.

<br>

If we go further improving the design, many new developers would come up with the idea of creating a separate class for every combination of subcomponents.

example

```java
public class CheesePizza implements FoodItem{
  @Override
  void prepare(){
    // prepration code for cheese pizza
  }
  
  @Override
  String description(){
    return "Cheese pizza";
  }
  
  @Override
  int cost(){
    return 60;
  }
}
```

```java
public class VegCheesePizza implements FoodItem{
  @Override
  void prepare(){
    // preparation code for veg cheese pizza
  }
  
  @Override
  String description(){
    return "Veg Cheese pizza";
  }
  
  @Override
  int cost(){
    return 70;
  }
}
```

```java
public class NonVegPizza implements FoodItem{
  @Override
  void prepare(){
    // preparation code for non veg pizza
  }
  
  @Override
  String description(){
    return "Non Veg pizza";
  }
  
  @Override
  int cost(){
    return 97;
  }
}
```

As you can see this will lead to the creation of many many classes(n factorial to be exact). If there are n no. of options in one food item then there are going to be n! no. of classes.

#### This phenomenon has a name, its called Class Explosion.

So to solve this particular type of problem we should use decorator pattern.

<br>

<br>

## The decorator pattern attaches additional responsibility to an object dynamically.

In decorator pattern, we need an **abstract component** and an **abstract decorator**.
The component will provide us the common functionality of our base entity(example FoodItem) and the decorator will provide us the additional functionality which we can add to our component(example Cheese, Veg-Toppings, etc).

> **The magic is, our decorator is itself a component and has a component**

Let's see an example.

<br>

Our abstract component

```java
public interface FoodItem{
  String description();
  int cost();
}
```

<br>

Our abstract decorator

```java
public abstract class AddOn implements FoodItem{

  protected FoodItem item;
  
  public AddOn(FoodItem item){
    this.item = item;
  }
  
  @Override
  String description(){
    return item.description();
  }
  
  @Override
  int cost(){
    return item.cost();
  }
}
```

Class AddOn implements FoodItem and at the same time,  also has a FoodItem instance, so all its subclasses will also act as FoodItem, which allows us to pass a decorator inside another decorator. We can keep on doing that until we pass a concrete FoodItem implementation. This way we can make any combination of decorators just by injecting them into each others constructor.

<br>

Base components

```java
public class Pizza implements FoodItem{

  @Override
  String description(){
    return "pizza";
  }
  
  @Override
  int cost(){
    return 50;
  }
}
```

```java
public class Pasta implements FoodItem{

  @Override
  String description(){
    return "pasta";
  }
  
  @Override
  int cost(){
    return 30;
  }
}
```

<br>

Add ons

```java
public class Cheese extends AddOn{
  private final String addOnDesc = " with cheese";
  private final int addOnCost = 10;
  
  public Cheese(FoodItem item){
    super(item);
  }
  
  @Override
  String description(){
    return super.description() + this.addOnDesc;
  }
  
  @Override
  int cost(){
    return super.cost() + this.addOnCost;
  }
}
```

```java
public class Chicken extends AddOn{
  private final String addOnDesc = " with chicken";
  private final int addOnCost = 27;
  
  public Chicken(FoodItem item){
    super(item);
  }
  
  @Override
  String description(){
    return super.description() + this.addOnDesc;
  }
  
  @Override
  int cost(){
    return super.cost() + this.addOnCost;
  }
}
```

```java
public class Tomato extends AddOn{
  private final String addOnDesc = " with tomato";
  private final int addOnCost = 13;
  
  public Tomato(FoodItem item){
    super(item);
  }
  
  @Override
  String description(){
    return super.description() + this.addOnDesc;
  }
  
  @Override
  int cost(){
    return super.cost() + this.addOnCost;
  }
}
```

<br>

Final code

```java
public class Application{
  public static void main(String[] args){
    FoodItem cheesePizza = new Cheese(new Pizza());
    FoodItem cheeseChickenPizza = new Cheese(new Chicken(new Pizza()));
    FoodItem tomatoPasta = new Tomato(new Pasta());
    FoodItem simplePasta = new Pasta();
    
    //...
  }
}
```

As you can see creating a new combination is very simple now, all we have to do is pass our FoodItem's instance through the constructor of a newly added AddOn.

#### That is it. That’s the decorator pattern

<br>

#### Conclusion

The decorator pattern attaches additional responsibility(*hasCheese, vegTopping*) to an object(*pizza*) dynamically.
