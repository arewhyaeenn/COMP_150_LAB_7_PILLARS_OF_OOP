# Object-Oriented Programming - Pillars of OOP 1

In this lab:

* Polymorphism again
* Abstraction and inheritance via `interface` implementations
* Overriding
* Encapsulation
* Inheritance via extending interfaces
* `default` methods and multiple inheritance

The **Pillars of Object Oriented Programming** are **polymorphism**, **inheritance**, **abstraction** and **encapsulation**. We will be talking about each of these conceptually and as they appear in Java.

The primary motivations underlying the pillars (and the goals to keep in mind throughout this lab) are increased code reusability, decreased code dublication, and increased ease of code maintenance. In other words, [**don't repeat yourself** (DRY)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

Quoting the wiki linked above:

*Violations of DRY are typically referred to as WET solutions, which is commonly taken to stand for "write every time", "write everything twice", "we enjoy typing" or "waste everyone's time".*

## Polymorphism Revisited

We have discussed **polymorphism** briefly with respect to the `+` operator in a previous lab. The `+` operator is used to denote a variety of operations depending on its surroundings; it performs concatenation on `String`s and addition on numerical primitives. In other words, the `+` operator has **multiple forms** or **multiple definitions**. The word "polymorphism" broken into its roots means "many forms".

We have also discussed polymorphism in the context of overloading methods by creating multiple methods sharing an identifier to deal with different argument configurations.

Finally, we have touched on polymorphism in the context of **class inheritance**; every class **inherits from** (or **extends**) the `Object` class, meaning:

* Every class is the `Object` class, but with **extra stuff**™ (whatever is defined in the class body).
* Every instance of every class literally **is an `Object`** (that is, it is an instance of the `Object` class), likely with some extra capabilities defined by said **extra stuff**™.

We will talk significantly more about this last form of polymorphism in the next lab. 

## Abstraction, Polymorphism and Inheritance with `interface`

Here we will discuss another example of polymorphism.

Occasionally you will need to implement a category of classes which perform the same (or similar) tasks in different ways and which possibly store their data in different forms. An `interface` can be used to organize these classes, allowing them to be tested by the same client, declared with the same type, and otherwise used interchangeably.

This is done through **abstraction**. `interface`s consist of **abstract methods**. These are essentially method declarations without bodies, declaring a method which must be defined by any implementation of the `interface`. An abstract method specifies some functionality that implementations must have, but does not fill out how that functionality is achieved.

Classes can then implement these `interface`s by "filling in" their abstract methods' bodies. Implementations of an `interface` take the abstracted or generalized outline provided in the `interface` and create a tangible, fully defined version of it.

For example, the `Polygon` `interface` below declares some abstract methods which must be filled out by any implementing class (and defines a default number of sides for implementing classes).

```java
public interface Polygon
{
    public final static int DEFAULT_N_SIDES = 0;

    public abstract int getNumberOfSides();
    public abstract double getPerimeter();
    public abstract double getArea();
    public abstract String toString();
}
```

The methods in the `interface` above (and in any `interface`) **must** be `public` and either `abstract` or `static` but not both. In fact, the "`public abstract`" on the start of each method declaration above can be omitted; methods in interfaces are assumed to be `public`, and if they aren't specified to be `static` then they are assumed to be `abstract`. Most IDEs will grey-out these modifiers and inform (when moused over) that they are redundant for `interface` methods.

The class data above (`DEFAULT_N_SIDES`) is `public`, `final`, and `static`. These three modifiers, like `public` and `abstract` for the methods, are redundant and can be omitted. 

Thus, the `interface` above is identical to this one:

```java
public interface Polygon
{
    int DEFAULT_N_SIDES = 0;

    int getNumberOfSides();
    double getPerimeter();
    double getArea();
    String toString();
}
```

We will create four implementations of the `Polygon` interface: `Rectangle`, `Triangle`, `Square`, and `Circle`. `Rectangle` is done below, and you'll create the others in your lab.

<a name="rectangle"></a>

```java
public class Rectangle implements Polygon
{
    final private double DEFAULT_LENGTH = 0;

    private double width;
    private double height;

    Rectangle (double width, double height)
    {
        setHeight(height);
        setWidth(width);
    }

    private void setHeight(double height)
    {
        if (height >= 0)
        {
            this.height = height;
        }
        else
        {
            this.height = DEFAULT_LENGTH;
        }
    }

    private void setWidth(double width)
    {
        if (width >= 0)
        {
            this.width = width;
        }
        else
        {
            this.width = DEFAULT_LENGTH;
        }
    }

    @Override
    public int getNumberOfSides()
    {
        return 4;
    }

    @Override
    public double getPerimeter()
    {
        return 2 * width + 2 * height;
    }

    @Override
    public double getArea()
    {
        return width * height;
    }
}
```

**<a name="q1"></a>[EXERCISE 1](#a1)** Notice that the `setWidth` and `setHeight` methods are very similar. One could even say they are repetitive. If we were to decide to change our validation procedure for new lengths, we would have to update these two methods individually. This is error-prone in terms of maintenance: we could easily change one and forget to change the other. How might one change the WET solution above into a DRY solution?

We haven't seen the `@Override` tags before; let's discuss them. To override a method is to replace the an inherited method with a new one. These `@Override` tags specify that the subsequent methods are taking the place of the abstract ones in the `Polygon` `interface`, i.e. overriding the `Polygon` methods with new implementations. While these tags are not mandatory when overriding methods, they are beneficial for the following reasons:

* They will cause a compile-time error if an overridden method declaration has a typo (e.g. an incorrect identifier or incorrect sequence of arguments), which makes such errors easier to identify and fix; otherwise, a new method can accidentally be created where overriding was intended.
* They improve readability. In fact, many IDEs will display extra information detailing the location of the method being overridden if the `@Override` annotation is present before a method.

The implementing class is said to **inherit from** the interface. Above, `Rectangle` inherits from `Polygon`. This is one example of **inheritance** in Java. We'll cover one more example in this reading, and two more in the next reading.

## Interfaces and Encapsulation

You might have noticed already how well `interface`s mix with the principle of encapsulation. The `interface` defines how clients will interact with implementations, but leaves the specifics of what goes on "under the hood" in the implementations up to them.

Many different implementations of a single interface can be made. Moreover, members of these different interfaces can all be stored in variables of the same type. We could declare and instantiate a `Rectangle` instance like this:

```java
Rectangle myRectangle = new Rectangle (3, 4);
```

But we could also do it like this:

```java
Polygon myPoly = new Rectangle (3, 4);
```

Moreover, the `myPoly` variable in the second example is much more flexible than `myRectangle` is; `myPoly` isn't limited to referencing `Rectangle` instances, and can instead reference an instance of any implementation of `Polygon`. So, if you made a `Triangle` class which implemented `Polygon`, then the following would be completely valid:

```java
Polygon myPoly = new Rectangle(3, 4);
myPoly = new Triangle(3, 4, 5);
```

This does come with a tradeoff, however: `myPoly` "doesn't know", at any given time, which implementation it references an instance of, so it cannot be used to access members of `Rectangle` or `Triangle` which are not declared in `Polygon`. So, the two implementations can be used interchangeably in `Polygon` variables, but these variables are limited to the functionality defined in the `Polygon` class. It is imperative, then, to declare **all** necessary methods for client interaction with `Polygon`s in the interface itself.

**<a name="q2"></a>[EXERCISE 2](#a2)** In the [`Rectangle`](#rectangle) implementation above, why do you think the `setWidth` and `setHeight` methods are private?

## `default`

An interface can specify a `default` implementation for its `abstract` methods.

Consider the `Person` interface below:

```java
public interface Person
{
    String getName();
    int getAge();
    void incrementAge();
}
```

We can specify a `default` implementation of `getName` by editing it as follows:

```java
public interface Person
{
    default String getName()
    {
        return "Joanne Buck";
    };
    
    int getAge();
    void incrementAge();
}
```

With this `default` implementation provided, implementing classes do not need to implement that particular method; any implementations that don't implement the method will use the implementation provided by the interface.

For instance, with `getName` implemented above, the following class is completely valid:

```java
public class JoanneBuck implements Person
{
    private int age;
    
    public JoanneBuck(int age)
    {
        this.age = age;
    }

    @Override
    public int getAge()
    {
        return age;
    }

    @Override
    public void incrementAge()
    {
        age++;
    }
}
```

Of course, implementing classes can instead choose to override the `default` implementation:

```java
public class JoanneBuck implements Person
{
    private int age;
    final private static String NAME = "Joanne Buck";

    public JoanneBuck(int age)
    {
        this.age = age;
    }
    
    @Override
    public String getName()
    {
        return NAME;
    }

    @Override
    public int getAge()
    {
        return age;
    }

    @Override
    public void incrementAge()
    {
        age++;
    }
}
```

**<a name="q3"></a>[EXERCISE 3](#a3)** Imagine you've made many implementations of an old interface, and then you update the interface by adding a single new method to it. Imagine moreover that many of the implementations should implement this particular new method identically. What should you do?

## Extending Interfaces and Inheritance

Consider this version of `Person`:

```java
public interface Person
{
    String getName();
    int getAge();
    void incrementAge();
}
```

Imagine you need to create a `Student` interface. Any `Student` is also a `Person`, and should therefore have all of the functionality of a `Person`. We could simply copy-paste the contents of `Person` into `Student`, then add some **extra stuff**™:

```java
public interface Student
{
    String getName();
    int getAge();
    void incrementAge();
    String getStudentID();
    
    default String getInstitution()
    {
        return "CSUCI";  // go dolphins
    };
}
```

Of course, this is a clear violation of DRY programming. Any time we update `Person`, we will also have to update `Student` in a similar way.

We can instead use the `extends` keyword, to create a `Student` interface containing everything from `Person`, but with some **extra stuff**™:

```java
public interface Student extends Person
{
    String getStudentID();
    default String getInstitution()
    {
        return "CSUCI";  // go dolphins
    };
}
```

This way, `Student` is shorter, and edits to `Person` will also update `Student` without extra work.

This is an example of **inheritance**. The `Student` class **inherits from** the `Person` class. If a class implements the `Student` interface, it will inherently implement the `Person` interface as well, so any `Student` instance is also a `Person` instance.

Note that the extended interface outlines a smaller or more specific category of objects. Every `Student` is a `Person`, but not every `Person` is a `Student`, so if we know someone is a `Student` then we can attribute more specificity to our understanding of their characteristics. The **extra stuff**™ in `Student` doesn't apply to all people, because it is too specific, so it must outline a smaller subset of people.

## Multiple Inheritance

A single class can implement multiple interfaces. Consider, for example, two extensions of `Person`, called `Student` and `Faculty`:

```java
public interface Person
{
    String getName();
    int getAge();
    void incrementAge();
}
```

```java
public interface Student extends Person
{
    String getStudentID();
    default String getInstitution()
    {
        return "CSUCI";  // go dolphins
    };
}
```

```java
public interface Faculty extends Person
{
    String getFacultyID();
    default String getInstitution()
    {
        return "CSUCI";  // go dolphins
    }
}
```

Then, imagine you want to implement a `TA` (**T**eaching **A**ssistant) class. Most TA's are graduate students who run some sessions in undergraduate courses, so a `TA` is both a `Student` and `Faculty`. As such, the `TA` class must implement both interfaces.

At first glance, you might want to implement `TA` like this:

```java
public class TA implements Student, Faculty
{
    private int age;
    private String name;
    private String ID;
    
    public TA(String name, int age, String ID)
    {
        this.name = name;
        this.age = age;
        this.ID = ID;
    }

    @Override
    public String getFacultyID()
    {
        return ID;
    }

    @Override
    public String getStudentID()
    {
        return ID;
    }

    @Override
    public String getName()
    {
        return name;
    }

    @Override
    public int getAge()
    {
        return age;
    }

    @Override
    public void incrementAge()
    {
        age++;
    }
}
```

The implementation above is *mostly* correct. It is missing an implementation of `getInstitution`... kind of. It would be more accurate to say, it has **too many** implementations of `getInstitution` (the two provided by default implementations in `Student` and `Faculty`) and it doesn't know which one to use. Thus `getInstitution` must be overwritten. One option is to simply call one of the two defaults:

```java
@Override
public String getInstitution()
{
    return Student.super.getInstitution();
}
```

Of course, the method can be overwritten with in the normal sense as well:

```java
@Override
public String getInstitution()
{
    return this.institution;
}
```

This second overwritten `getInstitution` would require the addition of a `String institution` to the `TA` class, of course.

`TA` could also be made an `interface`, and could extend both `Student` and `Faculty`:

```java
public interface TA extends Student, Faculty
{
    @Override
    default String getInstitution()
    {
        return "CSUCI";
    }
}
```

This extension requires the same clarification as the `TA` class: it must either decide which default implementation of `getInstitution` to use or create a new default implementation.

**<a name="q4"></a>[EXERCISE 4](#a4)** Decide which of the following should extend which others. If you don't know what all of these are (like I didn't while writing this) you should google! Don't stress too much about getting this answer "right". It is subjective, and these could be organized in different ways using different types of inheritance.

* `Vehicle`
* `AerialVehicle`
* `Propeller`
* `JetEngine`
* `Boat`
* `Sedan`
* `Truck`
* `ATV`
* `FordF150`
* `ToyotaTundra`
* `WheeledVehicle`
* `HondaAccord`
* `ToyotaCamry`
* `JetPlane`
* `Helicopter`
* `FighterJet`
* `SeaPlane`
* `Airliner`
* `Jetpack`
* `MotorBoat`
* `Canoe`

## Answers to Selected Exercises

### **<a name="a1"></a>[EXERCISE 1](#q1)** 

We've repeated code in the `setWidth` and `setHeight` methods almost exactly. This could be replaced with a call to a new method "`validateLength`" method, in which we can put the repeated code.

### **<a name="a2"></a>[EXERCISE 2](#q2)**

`Rectangle` is an implementation of `Polygon`; presumably its intended purpose, then, is to be referenced through a `Polygon` variable, otherwise it would just be implemented without the interface. `Polygon` doesn't have `setWidth` and `setHeight` methods, so there is no reason to make them public in `Rectangle`, as they would be inaccessible from `Polygon` variables anyway. Nothing would break if these extra methods were made public, but they are not part of the `Polygon` functionality, so they've been categorized as "stuff happening under the hood", not part of the interface through which `Polygons` interact, and therefore made inaccessible to clients. Nothing would break if these methods were made public.

### **<a name="a3"></a>[EXERCISE 3](#q3)**

The new method should be given a default implementation. This way, you'll only need to update implementing classes whose needs stray from this default.

### **<a name="a4"></a>[EXERCISE 4](#q4)**

Some of the following are kind of a stretch, as the formal definitions of a lot of these vary based on context, and some of them should be inheritance through [composition](https://en.wikipedia.org/wiki/Composition_over_inheritance). For instance, a `JetPlane` here is represented as "A `JetEngine` with **extra stuff**™", but it would be more accurate to say that a `JetPlane` contains a `JetEngine`, so a `JetPlane` would contain a field variable of type `JetEngine` instead of extending it. It is also very arguable that many of these would be concrete classes, not interfaces, so they would use `implements` instead of `extends`. My point is, don't worry too much about the specifics of my "solution" below, as it is very subjective and/or context-dependent.

* `Vehicle`
* `Propeller`
* `JetEngine` (does this count as a vehicle? you could ride one for a short time...)
* `AerialVehicle extends Vehicle`
* `Boat extends Vehicle`
* `WheeledVehicle extends Vehicle`
* `Sedan extends WheeledVehicle`
* `Truck extends WheeledVehicle`
* `ATV extends WheeledVehicle`
* `FordF150 extends Truck`
* `ToyotaTundra extends Truck`
* `HondaAccord extends Sedan`
* `ToyotaCamry extends Sedan`
* `JetPlane extends JetEngine, AerialVehicle`
* `Helicopter extends Propeller, AerialVehicle`
* `FighterJet extends JetPlane`
* `SeaPlane extends AerialVehicle, Boat?` (is it a boat? if you turn on the engine but don't take off...)
* `Airliner extends JetPlane, WheeledVehicle`
* `Jetpack extends JetEngine, AerialVehicle`
* `MotorBoat extends Boat, Propeller`
* `Canoe extends Boat`

# Lab Assignment

## Task 1

Create a client to test the `Rectangle` class above. Start with the client below, and test every `public` element in the `Rectangle` class. Note that `myRectangle` is declared as a `Polygon` but uses the `Rectangle` constructor; because `Rectangle implements Polygon`, any `Rectangle` **is** a `Polygon`. Because `myRectangle` is declared as a `Polygon` and not as a `Rectangle`, it can only be used to access implemented members of the `Polygon` class, and not to access other members of the `Rectangle` class.

```java
public class PolygonClient
{
    public static void main(String[] args)
    {
        Polygon myRectangle = new Rectangle(3, 4);
        // TODO test the Rectangle methods
    }
}
```

## Task 2

Create the `Square`, `Triangle`, and `Circle` classes. In each class, you will need to decide what instance data to declare, create at least 1 constructor, and implement all of the `Polygon` methods.`Circle`s have 0 sides.

## Task 3

Create a new `PolygonClient` to test all four implementations of the `Polygon` class. 

Your client should continuously construct polygons for the user (storing them in an `ArrayList<Polygon>`). It should start by prompting the user for the polygon they want to create next (of the four options), and then follow up by prompting the user for whatever data is necessary to construct the desired polygon. Finally, it should ask the user if they want to continue. If they enter "yes" it should let them enter another polygon; if "no", it should print out all polygons that have been gathered thus far, as well the outputs of all of the methods from the `Polygon` interface.

You may use the `InputHandler` class below to validate user inputs, and do not need to deal with user-input issues not handled by this class. You may read through it, or simply trust that its static `getDoubleFromUser`, `getPolygonFromUser`, and `getYesNoFromUser` methods return a `double`, `String`, or `boolean` respectively, from `System.in`. In the case of `getPolygonFromUser`, the output is "circle", "rectangle", "square", or "triangle".

```java
import java.util.Scanner;
import java.util.Random;
public class InputHandler
{
    final private static Scanner user = new Scanner(System.in);
    final private static Random generator = new Random();

    final private static String doublePrompt = "\nPlease enter a double literal, you perfect, sweet, wonderful, kind soul.\n:>  ";
    final private static String doubleReprompt = "\nI SAID a DOUBLE LITERAL you ABSOLUTE BUFFOON!!\n:>  ";
    final private static String doubleRegex = "[+-]?[0-9]+(\\.[0-9]*)?";

    final private static String polyPrompt = "\nThe smell of freshly baked blueberry pie overwhelms you.\n" +
            "You know it can be yours if you supply a polygon (circle rectangle square triangle)\n:>  ";
    final private static String polyReprompt = "\nYour pie diminishes! But there is still some left!\n" +
            "You know it still can be yours if you but supply a polygon (circle rectangle square triangle)\n:>  ";
    final private static String polyRegex = "(circle)|(rectangle)|(square)|(triangle)";
    private static double remainingPie = generator.nextInt(10);

    final private static String yesNoPrompt = "\nContinue? (yes no)\n:>  ";
    final private static String yesNoReprompt = "\nYou can't delay forever!\n Continue? (yes no)\n:>  ";
    final private static String yesNoRegex = "(yes)|(no)";

    public static double getDoubleFromUser()
    {
        String userInput = getInputFromUser(doublePrompt, doubleReprompt, doubleRegex);
        return Double.parseDouble(userInput);
    }

    public static String getPolygonFromUser()
    {

        String response = getInputFromUser(polyPrompt, polyReprompt, polyRegex);
        System.out.println("You know, with an odd sense of certainty, that at some point in the near future,\n" +
                "you will receive your " + remainingPie + " pie(s) without warning.");
        remainingPie = generator.nextInt(10);
        return response;
    }

    public static boolean getYesNoFromUser()
    {
        String userInput = getInputFromUser(yesNoPrompt, yesNoReprompt, yesNoRegex);
        return userInput.equals("yes");
    }

    private static String getInputFromUser(String prompt, String reprompt, String regex)
    {
        System.out.print(prompt);
        String userInput = user.nextLine();

        while (!userInput.matches(regex))
        {
            System.out.print(reprompt);
            remainingPie = remainingPie / 2;
            userInput = user.nextLine();
        }

        return userInput;
    }
}
```

## Task 4

Write me a nice note. It might be about that goofy `InputHandler` class above. It might be about my outstanding hairdo. If you are in one of my classes, submit it with your lab. If not, write it on a napkin and burn it in a small metal or glass container, and then howl at the sky for precisely ten seconds and your message will be delivered to me by the owl or butterfly nearest you at its earliest convenience. Note that constructive criticism does not qualify as "nice" and will not receive points.

(not graded)

## Task 5

If you're not very familiar with cartesian and polar coordinates, check out [this video](https://www.youtube.com/watch?v=L4v98ZZft68) covering the two coordinate systems before starting this task.

Download [point.zip](./point.zip). Read the `Point` `interface` defined in `Point.java`. You will refer to it throughout this task, as it contains descriptions of all methods. You must implement these methods in the provided `PolarPoint` and `CartesianPoint` classes.

If you mouse over the underlined immediate syntax errors in `CartesianPoint.java` and `PolarPoint.java`, your IDE will likely instruct you on a shortcut to create (empty) method definitions for all missing abstract methods from the `Point` `interface`. This isn't true for all IDEs, so you might need to type them out.

Create a client class to test these methods.

Note that in both `PolarPoint` and `CartesianPoint`, the instance data is `private`, but all of the methods defined in `Point` are `public`. This is an example of **encapsulation**; the data and interface are separated, so changes can be made to `PolarPoint` or `CartesianPoint` individually and as long as these two classes adhere to the `Point` interface, uses of `Point`s in other classes will not need to change.

## Task 6 (Optional)

This is a mathy bonus task that has nothing to do with polymorphism. It is not worth extra credit.

Recall that the area of a circle is pi times the radius squared. As such, a circle with radius 1 should have area equal to pi.

Consider the circle with radius 1 centered on the origin. It is contained completely within the 2x2 square with corners *(-1,-1)*, *(-1,1)*, *(1,-1)* and *(1,1)* (in cartesian coordinates). This square has area equal to 4 square units.

Generate 10000 random cartesian points in this 2x2 square around the origin. Count how many are inside the unit circle centered on the origin (by checking if the distance to the origin is less than 1). Calculate the proportion of generated random points which are in the circle. Multiply this proportion by 4 (the area of the entire square) to approximate the the area of the circle (and thereby approximate pi).

Discuss the results.




