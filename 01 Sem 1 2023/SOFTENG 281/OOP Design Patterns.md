There are 23 Main OO Design patters which can be classified into three main categories
- Creational Patterns (5)  
	- Provide ways of creating instances while hiding the creation logic
	- Instead of initiating objects directly, using the `new` operator
	- These methods give the program more flexibility in deciding which instances need to be created
- Structural Patterns (7)
	- Deal with class and object composition. 
	- The concept of inheritance is used to compose interfaces and define ways to compose objects to obtain new functionality.
- Behavioural Patterns (11)
	- Deal with communication between objects


![[Design Patterns Table.png| centre | 400]]<hr>

### Factory (Creational)
The Factory pattern is one of the most Java. It is very useful when you need to provide high flexibility for your code. 

**Problem:**
In OOP we often have to create different instances of similar classes. In such cases, we want to have highly maintainable code so that when we want to add a new class type, we just want to change the code in one place.

For example, let’s assume that you have opened a Coffee Shop and you only make espressos. You implemented an automated system to make coffee. The code looks like this:

```java
public class Main {
    public static void main(String[] args) {
        EspressoCoffee espressoCoffee = new EspressoCoffee();
        espressoCoffee.grindCoffeeBeans();
        espressoCoffee.make();
        espressoCoffee.serve();
    }

}
```

```java
public class EspressoCoffee {

	public void grindCoffeeBeans() {
		System.out.println("Use espresso roast coffee, about 9 grams for a single espresso shot");
	}

	public void make() {
		System.out.println("Place the portafilter in the espresso machine and press the button to pull the shot.");
	}

	public void serve() {
		System.out.println("Espresso can only be served in small ceramic cups");
	}
}
```

As your Coffee shop gets popular, you want to add more types of coffee: Cappuccino and Americano. However , adding a new coffee to the program is not quite that simple if the rest of the code is already coupled to the `EspressoCoffe` type.  Adding new coffees into the program would doing something like making big ugly switch statements

The solution here is to uses the *factory* pattern. 

This pattern delegates the responsibility of initializing a class by creating a type of *virtual constructor*

Firstly, you need to create a generic abstract class or interface for coffee.
```java
public interface Coffee {
	public void grindCoffeeBeans();
	public void make();
	public void serve();
}
```

We then create coffee classes that implement this class/ interface
```java
public class EspressoCoffee implements Coffee {

	@Override
	public void grindCoffeeBeans() {
		System.out.println("Use espresso roast coffee, about 9 grams ...");
	}

	@Override
	public void make() {
		System.out.println("Place the portafilter in the espresso machine ...");
	}

	@Override
	public void serve() {
		System.out.println("Espresso can only be served in small ceramic cups");
	}
}
```


We can then create a factory class method to create the coffee instance based on the type ("A", "C",  or "E"). The method `createCoffee` return a `Coffee` object with an implementation that depends on the coffee type as a parameter. 
```java
public class CoffeeFactory {
	public static Coffee createCoffee(String type) {
		switch (type) {
		case "E":
			return new EspressoCoffee();
		case "A":
			return new AmericanoCoffee();
		case "C":
			return new CappuccinoCoffee();

		default:
			System.err.println("wrong coffee type");
			System.exit(0);
		}
		return null;
	}
}
```

```java
Coffee coffee = CoffeeFactory.createCoffee("A"); // this will create an Americano
coffee.grindCoffeeBeans();
coffee.make();
coffee.serve();
```


### Builder (Creational)
**Problem:** In OOP we often have classes holding some data that we are setting and later accessing. If we have many instance fields, things can get a bit tedious!

For example, let’s consider the following `Pizza` class:

```java
public class Pizza{
    private int size;           // mandatory
    private boolean onion;      // optional
    private boolean cheese;     // optional
    private boolean olives;     // optional
    private boolean tomato;     // optional
    private boolean mushroom;   // optional
    private boolean ham;        // optional
    private int sausageCount;   // optional
```
We have one mandatory attribute (`size`), and seven optional attributes. The constructor would be:
``` java 
// Bad Code
public Pizza(int size, boolean onion, boolean cheese,
            boolean olives, boolean tomato,
            boolean mushroom, boolean ham, int sausageCount){

    this.size = size;
    this.onion = onion;
    this.cheese = cheese;
    this.olives = olives;
    this.tomato = tomato;
    this.mushroom = mushroom;
    this.ham = ham;
    this.sausageCount = sausageCount;
}
```

This is very error prone: the parameters can be easily be mixed up by the developers as many of them are similar, and seven of the parameters are not even mandatory.   And if we have new ingredients in the future, this constructer wo be extended with even more parameters!

Sure, one possible solution is to use a default constructor and then use _setter_ methods to set the optional parameters. But this is bad because a developer could forget to set a mandatory, or you might want an *immutable* instance. 

**Solution:** Use the **Builder** design pattern: the main idea is to separate mandatory from optional parameters, and to move the construction logic out of the object’s class into a separate `static` _inner_ class referred to as a “builder” class:

-   The `Builder` class has a constructor only for mandatory parameters, and setter methods for all the optional parameters.
-   In addition, there is a `build()` method that glues everything together and returns a complete instance.

```java
public class Pizza {
    ...

    // Pizza's inner static Builder class:
    public static class Builder {
        private int size;                 // mandatory
        private boolean onion = false;    // optional with default value
        private boolean cheese = false;   // optional with default value
        private boolean olives = false;   // optional with default value
        private int sausageCount = 0;     // optional with default value

        
        public Builder(int size) {
            this.size = size;
        }

        public Builder addOnions() {
            this.onion = true;
            return this;
        }
        
	    /* ... Methods for other optional fields */
	    
        public Builder sausageCount(int sausageCount) {
            this.sausageCount = sausageCount;
            return this;
        }

        public Pizza build() {
            return new Pizza(this);
        }
    }
}
```

All of the builder setter methods return the builder itself. This allows for the setter invocations to be *chained*. The `Pizza` class's constructor also needs to be `private` here so that it cannot be instantiated directly by outside classes. However it will of course be instantiated through the `Builder` class

```java
public class Pizza {

    private int size;           // mandatory
    private boolean onion;      // optional
    private boolean cheese;     // optional
    private boolean ham;        // optional
    private int sausageCount;   // optional

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.onion = builder.onion;
        this.cheese = builder.cheese;
        this.ham = builder.ham;
        this.sausageCount = builder.sausageCount;
    }
    
    public static class Builder {}
}
```

Creating a pizza instance:
```java
Pizza pizza = new Pizza.Builder(10) // note that this "new" creates a "Pizza.Builder"
                .addCheese()        // add cheese to the builder instance, 
                .addOlives()        // add olives to the builder instance, 
                .sausageCount(2)    // add 2 sausages to the builder instance, 
                .addTomato()        // add tomato to the builder instance, 
                .build();           // now we create the "Pizza" instance!
```


### Prototype (Creational)
In OOP we often want to create copies of objects. Like when implementing a game with an object representing an avatar. We might find it convenient to use this object as a **Prototype** to creates thousands of copies then make minor modifications such as slightly changing the size. 

Considering the following `Avatar`

```java
public class Avatar {
    private int size;
    private int walkingSpeed;
    private int walkingDirection;

    public Avatar(int size, int walkingSpeed, int walkingDirection) {
        this.size = size;
        this.walkingSpeed = walkingSpeed;
        this.walkingDirection = walkingDirection;
    }

    public int getSize() {
        return size;
    }

    @Override
    public String toString() {
		return "Avatar [size=" + size + ", walkingSpeed=" + walkingSpeed + 
			", walkingDirection=" + walkingDirection + "]";
    }

  }
```

If we now wanted to  create an `Avatar` with size 5, walkingSpeed 4, and walkingDirection 5. This object is created at runtime we don’t know these values when writing the code. We could do something like this:

`Avatar avatar = new Avatar(getSize(),` …. WAIT! `walkingSpeed` and `walkingDirection` are private but do not have getters. This is quite common, as not every private field necessarily needs a getter. Some fields are only internal and are not meant to be visible from outside of the object itself. Can we add the getters and do? `Avatar avatar = new Avatar(getSize(), getWalingSpeed(), getWalkingDirection());` but what if we want to clone an object implemented not by us but imported from a third-party library? We cannot change their code! What if we don’t know the type of the object, but only the one of its superclass?

**Solution**: Use the **Prototype** design pattern, which involves delegating the cloning procedure to the objects being replicated. This pattern establishes a shared interface for all objects capable of cloning, allowing you to duplicate an object without tying our code to the object’s specific class. Typically, this interface includes only one method for cloning.

```java
public interface Prototype {
    Prototype copy();
  }
```

Now, we just have to implement this interface and write the implementation of the method `copy()`. This time it’s easy because we are inside the object and have access to all fields.

```java
public class Avatar implements Prototype{
    private int size;
    private int walkingSpeed;
    private int walkingDirection;
    public Avatar(int size, int walkingSpeed, int walkingDirection) {
        this.size = size;
        this.walkingSpeed = walkingSpeed;
        this.walkingDirection = walkingDirection;
    }

    public int getSize() {
        return size;
    }

    @Override
    public String toString() {
        return "Avatar [size=" + size + ", walkingSpeed=" + walkingSpeed + 
	        ", walkingDirection=" + walkingDirection + "]";
    }

    @Override
    public Prototype copy() {
        return new Avatar(size, walkingSpeed, walkingDirection);
    }
}
```

When implementing prototype, the 
```java
Avatar avatar = new Avatar(50, 30, 6);
System.out.println(avatar);
Avatar clone = (Avatar) avatar.copy();
System.out.println(clone);
// Avatar [size=50, walkingSpeed=30, walkingDirection=6]
// Avatar [size=50, walkingSpeed=30, walkingDirection=6]
```

Furthermore, Java implements this functionality with the `Cloneable` interface. It tells the JDK that you can use the clone method of `java.lang.object` to clone an object. `clone` is anlougus to `copy` above. Let’s modify the `Avatar` class to use the `Cloneable` interface instead of our `Prototype` interface.

```java
public class Avatar implements Cloneable {
	private int size;
	private int walkingSpeed;
	private int walkingDirection;
	
	public Avatar(int size, int walkingSpeed, int walkingDirection) {
		this.size = size;
		this.walkingSpeed = walkingSpeed;
		this.walkingDirection = walkingDirection;
	}
	
	public int getSize() {
		return size;
	}
	
	@Override
	public String toString() {
		return "Avatar [size="
			+ size
			+ ", walkingSpeed="
			+ walkingSpeed
			+ ", walkingDirection="
			+ walkingDirection
			+ "]";
	}
	
	public static void main(String[] args) throws CloneNotSupportedException {
		Avatar avatar = new Avatar(50, 30, 6);
		System.out.println(avatar);
		Avatar clone = (Avatar) avatar.clone();
		System.out.println(clone);
		Object a
	}
}
```

We don't even need to implement the `clone` method as in this case we can use the standard of java that copies each of the fields. If we want to use a custom type of clone that does not copy all the fields, we can override the `clone` method and give our implementation. 

### Adapter (Structural)
An adapter structure is a class that allows for two incompatible interfaces to work with each other by translating requests from one form to another.

Consider this sorter interface that allows us to sort a list of integers in reverse order
```java
public interface Sorter {
	int[] sort(int[] numbers);
}
```

Assume we want to use an existing library that allows for the sorting of lists through its `listSorter` class.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

// library class that we cannot change
public class ListSorter {
    public List<Integer> sortList(List<Integer> numbers)   {
        //copy the list number into another list
        List<Integer> copyList = new ArrayList<>(numbers);
        Collections.sort(copyList,  Collections.reverseOrder());
        return copyList; // return the sorted list
    }
}

```

Here we have a problem, The `ListSorter` Class does not sort arrays. It is impractical to directly modify the `ListSorter` Class as it is not our library and we may unintentionally introduce bugs in the process amongst other reasons. The **Adapter** Design pattern tells us to create a class called `SortListAdapter` that implements the interface `Sorter`. Its `Sort` method coverts `int[]` to `List<Integer>` calls the `sortList()` method and then converts the list back into an array. 

```java
public class SortListAdapter implements Sorter {
    @Override
    public int[] sort(int[] numbers) {
        List<Integer> numberList = convertArray2List(numbers);

        ListSorter sorter = new ListSorter();
        List<Integer> listSorted = sorter.sortList(numberList);
        int[] arraySorted = convertList2Array(listSorted);

        return arraySorted;
    }
}
```

This is done with the use of two helper methods in the `SortListAdapter` Class
```java
private List<Integer> convertArray2List(int[] numbers) {
    List<Integer> list = new ArrayList<>();
    for (int number : numbers) {
        list.add(number);
    }
    return list;
}

private int[] convertList2Array(List<Integer> numbers) {
    int[] array = new int[numbers.size()];
    for (int i = 0; i < numbers.size(); i++) {
        array[i] = numbers.get(i);
    }
    return array;
}
```

Now in order to sort integers we can just call the adapters `sort` method;

```java
public static void main(String[] args) {
    int[] numbers = new int[] { 5, 34, 2, -1, 3, 444, 89 };
    System.out.println("array in input: " + Arrays.toString(numbers));

    Sorter sorter = new SortListAdapter();

    int[] sortedNumbers = sorter.sort(numbers);
	    System.out.println("ordered array (reverse order): " + 
						    Arrays.toString(sortedNumbers));
}
```

### Composite (Structural)
Sometimes a program will have entities that can be organised into a tree like structure.  For instance a file system would have two classes `Folder` and `File`. A `Folder` can contain other folders and files. This is a classic example of a tree-like structure with files as the leaves and folders as the nodes.

Both `Folder` and `File` however will share several similar properties like name and size. 

One way of implementing this would be to create regular classes for both `File` and `Folder`
Where file has `name` and `size` attributes and `Folder` has a list for files and for folders as well as methods for adding and removing files and folders separately. 

This approach requires a-lot of duplicate code which is *obviously* bad.

``` java
public int totalSize() {
	int size = 0;
	for (File file : files) {
		size = size + file.getSize();
	}
	for (Folder folder : folders) {
		size = size + folder.totalSize();
	}
	return size;
}
```

**Solution:**
The design pattern can help! It lets you work with tree-like structures by treating individual objects and groups of objects (called composites) uniformly (i.e, in the same way).

To implement the Composite pattern, you’ll need to create three things:

- **Element** – a base interface or abstract class with common methods for managing child composites. It should be either an interface or an abstract class.
- **Leaf** – implements the default behaviour of the base component.
- **Composite** – implements the base component methods.

**Element** Create a common interface (or abstract class) to treat files and folders uniformly.
```java
interface FileSystemElement {
    void display();
    int  getSize();
}
```

**Leaf** The leaf `File` will be:
```java
class File implements FileSystemElement {
    private String name;
    private int size;

    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public void display() {
        System.out.println("File -> " + name);
    }

    @Override
    public int getSize() {
        return size;
    }
}
```

The **Composite** class.
``` java
class Folder implements FileSystemElement {
    private String name;
    private List<FileSystemElement> components = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void addComponent(FileSystemElement component) {
        components.add(component);
    }

    public void removeComponent(FileSystemElement component) {
        components.remove(component);
    }

    @Override
    public void display() {
        System.out.println("Folder -> " + name);
        for (FileSystemElement component : components) {
            component.display();
        }
    }

    @Override
    public int getSize() {
        int size = 0;
        for (FileSystemElement component : components) {
            size = size + component.getSize();
        }
        return size;
    }
}
```

The `Composite` design pattern allows us to have a less redundant, more maintainable structure through the use of a common interface. 

### Command (Behavioural)
**Problem**
Consider the process of ordering a meal at a restaurant:
-   `Customer`, who makes an order.
-   `Waiter`, who communicates the order to the chefs.
-   `Chefs`, who cook the meal.

In object orientated programs we often have a similar situation where an `Invoker` that invokes operations on different `Receiver` objects. In the restaurant analogy, the `Waiters` are the `Invokers` and the `Receivers` are the `Chefs`. 

If the Invoker and Receiver interact with each other directly, the invoker will have a series of if statements to decide which receiver to invoke based on a given command. (A kitchen will have multiple chefs that all cook different meals).

This situation leads to it being very difficult to add new receivers (chefs) or command types (meals). It will require adding more if/else statements greatly increasing the complexity of the code. 

**Solution:**
The *Command Design Pallet* decouples an object making a request (`Invoker`), from the one that knows how to perform it (`Receiver`). It separates the object that invokes the operation from the object that actually performs the operation. It makes it easy to add new commands because existing classes remain unchanged.

>[!Analogy]-
>Following the analogy of the restaurant, we can apply the **Command Design Pattern** as follows:
>-   The `Customer` makes an order.
>-   The `Waiter` writes down the order, gives it to the kitchen and rings a bell.
>-   The right `Chef` will take the order from the table and cook the meal.
>
>All the information on what to cook is in the order itself (the piece of paper), the `Chef` does not communicate directly with neither the `Waiter` nor the `Costumer`. The `Waiter` might not know how to cook the meal or which `Chef` is going to cook the meal, the `Waiter` just delivers the order.

The components of this design pattern are:
- `Command` declares an interface for abstract commands eg. `execute()`
- `ConcreteCommand` Classes that implement the `Command` interface. Internally they have the reference of who is the receiver of the implemented command
- `Receiver` Knows how to execute a particular command
- `Invoker` holds the `ConcreteCommand` that has to be executed
- `Client` Creates a `ConcreteCommand` and gives it to the `Invoker`

In this way, the Command Design Pattern decouples the `Invoker` from `Receiver`. The `Invoker` has complete knowledge of which `ConcreteCommand` to be executed and the `ConcreteCommand` knows which `Receiver` to be invoked to execute a particular operation.

##### Example:
We want to implement a remote controller for a home automation system that controls different electrical units of a home. 

For simplicity, let’s assume we have two electrical units: a `Light` and a `GarageDoor`. Both are the `Receivers` using the terminology of this design pattern.
```java
public class Light {
	private String location;

	public Light(String location) {
		this.location = location;
	}

	public void on() {
		System.out.println(location + " light is on");
	}

	public void off() {
		System.out.println(location + " light is off");
	}
}

public class GarageDoor {

	public void up() {
		System.out.println("Garage Door is Up");
	}

	public void down() {
		System.out.println("Garage Door is Down");
	}

	public void lightOn() {
		System.out.println("Garage light is on");
	}

	public void lightOff() {
		System.out.println("Garage light is off");
	}
}

```

Next Create a `Command` interface to declare an `execute` method
```java
public interface Command {
	void execute();
}
```

Create Concrete commands for functions like turning the light on and off
```java
public class LightOnCommand implements Command {

	private Light light;

	public LightOnCommand(Light light) {
		this.light = light;
	}

	@Override
	public void execute() {
		light.on();
	}
}
```

```java
public class LightOffCommand implements Command {

	private Light light;

	public LightOffCommand(Light light) {
		this.light = light;
	}

	@Override
	public void execute() {
		light.off();
	}
}
```

```java
public class GarageDoorUpCommand implements Command {
	private GarageDoor door;

	public GarageDoorUpCommand(GarageDoor door) {
		this.door = door;
	}

	@Override
	public void execute() {
		door.up();
		door.lightOn();
	}

}
```

```java
public class GarageDoorDownCommand implements Command {
	private GarageDoor door;

	public GarageDoorDownCommand(GarageDoor door) {
		this.door = door;
	}

    @Override
	public void execute() {
		door.down();
		door.lightOff();
	}

}
```

Now we can implement the `RemoteController` that is our `Invoker`.

```java
//Invoker
public class RemoteController{

  private Command command;

  public void setCommand(Command command){
    this.command = command;
  }

  public void pressButton(){
    command.execute();
  }

}
```

This is an example of Client that use the classes that we have just created.
```java
public class Main {
	public static void main(String[] args) {
	    RemoteController invoker = new RemoteController();
	    Light lightBedroom = new Light("Bedroom");
	    Command lightsOn = new LightOnCommand(lightBedroom);
	    Command lightsOff = new LightOffCommand(lightBedroom);
	    Command garageUp = new GarageDoorUpCommand(new GarageDoor());
	    Command garageDown = new GarageDoorDownCommand(new GarageDoor());
	
	    // light switch on
	    invoker.setCommand(lightsOn);
	    invoker.pressButton();
	    // light switch off
	    invoker.setCommand(lightsOff);
	    invoker.pressButton();
	    // garage up
	    invoker.setCommand(garageUp);
	    invoker.pressButton();
	    // garage down
	    invoker.setCommand(garageDown);
	    invoker.pressButton();
	}
}
```


##### Extending the example
Now with the **Command Design Pattern** implemented we can do cool things like adding a queue and a history of executed commands. We just need to change the implementation of the `Invoker`.
```java
import java.util.ArrayList;
import java.util.List;

//Invoker
public class RemoteController {

	private List<Command> historyCommands;
	private List<Command> queueCommands;
	
	public RemoteController() {
		historyCommands = new ArrayList<>();
		queueCommands = new ArrayList<>();
	}
	
	public List<Command> getHistoryCommands() {
		return historyCommands;
	}
	
	public List<Command> getQueueCommands() {
		return queueCommands;
	}
	
	public void addCommand(Command command) {
		queueCommands.add(command);
	}
	
	public void executeAll() {
		for (Command command : queueCommands) {
			  command.execute();
			  historyCommands.add(command);
		}
		queueCommands.clear();
	}
}
```
Now the `Invoker` stores all executed commands and queue the ones to be executed.

We can also implement the "undo" of a command. In such a case, we need to add the `unexecute` operation in the `Command` interface and implement it in all of the `ConcreteCommands`. Intuitively, the “unexecuted” operation has to mirror the `execute` operation. For example
```java
public interface Command {
	public abstract void execute();
	public abstract void unexecute();
}


public class GarageDoorUpCommand implements Command {
	private GarageDoor door;

	public GarageDoorUpCommand(GarageDoor door) {
		this.door = door;
	}

	@Override
	public void execute() {
		door.up();
		door.lightOn();
	}

	@Override
	public void unexecute() {
		door.down();
		door.lightOff();
	}
}
```


### Strategy (Behavioural)

Let’s assume that we need to implement the payment system of a shop. Currently, the shop handles three different types of payment: by cash, by Visa card, or by MasterCard. If cards are used, a fee is to be applied.

The most obvious (but not very good) implementation could be something like this:
```java
public class Order {

    private final String payment;
    private final int amount;

    private double feeMastercard = 0.0015;
    private double feeVisaCard = 0.001;

    public Order(int amount, String payment) {
        this.amount = amount;
        this.payment = payment;
    }

    public void payCash(int amount) {
        System.out.println("Executing cash payment: Charging $" + amount);
    }

    public void payMastercard(int amount) {
        System.out.println("Executing Mastercard payment: Charging $" + amount);
        System.out.println("fees " + feeMastercard * amount);
    }

    public void payVisaCard(int amount) {
        System.out.println("Executing Visacard payment: Charging $" + amount);
        System.out.println("fees " + feeVisaCard*amount);
    }

    public void process(){
        switch (payment){
            case "visa":
                payVisaCard(amount);
                break;
            case "master":
                payMastercard(amount);
                break;
            case "cash":
                payCash(amount);
                break;
            default:
                System.out.println("unknonw payment");
        }
    }

    public static void main(String[] args) {
        Order order1 = new Order(15, "visa");
        order1.process();

        Order order2 = new Order(100, "master");
        order2.process();

        Order order3 = new Order(100, "cash");
        order3.process();
    }

}
```

This solution has several problem
- You need to update the `process()` method every time you want to update the payment methods
- You can easily pass a wrong payment string like `visacard` instead of `visa`

This is the perfect case to apply the **Strategy** pattern

We can isolate each of the payment in different classes, called **strategies**, that define an interface common to all supported payments. Then the `Order` class uses this interface to execute the algorithm implemented by a Concrete Strategy. In this way, ordering a payment is independent from the strategy. We can add a new algorithm or update the existing one without changing the `Order` class. With this approach, our payment program becomes much more flexible.

**Problem:** We have a class that switches between algorithms to accomplish the same task. This means that we would like the option to change the algorithms at runtime.

**Solution:** The Strategy pattern takes a class that does something specific in a lot of different ways and extract all of these algorithms into separate classes called **strategies**. The Strategy pattern helps to define a family of algorithms, to encapsulate each one of them and make them interchangeable and independent from the classes that use them. Most importantly, it allows changing the strategy at runtime.

**Payment Interface**
```java
public interface Payment {
    void pay(int amount);
}
```

**“family” of the three payment algorithms:**
```java
public class CashPayment implements Payment {
    @Override
    public void pay(int amount) {
        System.out.println("Executing cash payment: Charging $" + amount);
    }
}

public class MastercardPayment implements Payment {
    double fee = 0.0015;

    @Override
    public void pay(int amount) {
        System.out.println("Executing Mastercard payment: Charging $" + amount);
        System.out.println("fees " + fee * amount);
    }
}

public class VisacardPayment implements Payment {
    double fee = 0.001;

    @Override
    public void pay(int amount) {
        System.out.println("Executing Visacard payment: Charging $" + amount);
        System.out.println("fees " + fee * amount);
    }
}
```

Now we update the `order` class to make use of the strategies
```java
public class Order {

    private final Payment payment;
    private final int amount;

    public Order(int amount, Payment payment) {
        this.amount = amount;
        this.payment = payment;
    }

    public void process() {
        payment.pay(amount);
    }
}
```

Note that now the `process()` method is no longer responsible for selecting an appropriate payment. Instead, we pass the desired strategy as an object (`Payment payment`).

The class `Order` will make the payment through the interface `Payment`. The `Order` class does not need to know the concrete payment processing. That means that in the future, we can add more strategies without changing the `Order` class at all!

Now we can use the `Order` class as follows:

```java
Order order1 = new Order(15, new VisacardPayment());
order1.process();

Order order2 = new Order(100, new MastercardPayment());
order2.process();

Order order3 = new Order(100, new CashPayment());
order3.process();
```

One of the greatest advantage of this design pattern is that it allows to easily change algorithms (strategies) at runtime. We just need to add the setter method of the strategy instance:

```java
public class Order {

    private Payment payment;
    private final int amount;

    public Order(int amount, Payment payment) {
        this.amount = amount;
        this.payment = payment;
    }

    public void setPayment(Payment payment){
        this.payment = payment;
    }

    public void process() {
        payment.pay(amount);
    }
}
```