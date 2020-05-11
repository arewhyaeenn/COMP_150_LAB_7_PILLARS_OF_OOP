# COMP 150 Lab 7 - Pillars of OOP 1

In this lab:

* Polymorphism again
* Abstraction via `interface`
* Overriding again
* Encapsulation again

The **Pillars of Object Oriented Programming** are **polymorphism**, **inheritance**, **abstraction** and **encapsulation**. We will be talking about each of these conceptually and as they appear in Java.

The primary motivations underlying the pillars (and the goal to keep in mind throughout this lab) are increased code reusability and decreased code dublication. In other words, [**don't repeat yourself** (DRY)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

Quoting the wiki linked above:

*Violations of DRY are typically referred to as WET solutions, which is commonly taken to stand for "write every time", "write everything twice", "we enjoy typing" or "waste everyone's time".*

## Polymorphism Revisited

We have discussed **polymorphism** briefly with respect to the `+` operator in a previous lab. The `+` operator is used to denote a variety of operations depending on its surroundings; it performs concatenation on `String`s and addition on numerical primitives. In other words, the `+` operator has **multiple forms** or **multiple definitions**.

We have also discussed polymorphism in the context of overloaded functions by creating multiple functions sharing an identifier to deal with different argument configurations.

Finally, we have touched on polymorphism in the context of **inheritance**; every class **inherits from** (or **extends**) the `Object` class, meaning:

* Every class is the `Object` class, but with **extra stuff**™ (whatever is defined in the class body).
* Every instance of every class literally **is an `Object`** (that is, it is an instance of the `Object` class), likely with some extra capabilities defined by said **extra stuff**™.

We will talk significantly more about this last form of polymorphism in the next lab. 

## Abstraction and polymorphism with the `interface`

Here we will discuss another example of polymorphism.

Occasionally you will need to implement a category of classes which perform the same (or similar) tasks in different ways and which possible store their data in different forms. An `interface` can be used to organize these classes, allowing them to be tested by the same client (among other things).

This is done through **abstraction**. `interface`s consist of **abstract methods**. These are essentially method declarations without bodies, declaring a method which must be defined by any implementation of the `interface`.

Classes can then implement these `interface`s by "filling in" their abstract methods' bodies.

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

The methods in the `interface` above (and in any `interface`) **must** be `public` and `abstract`. In fact, the `public abstract` on the start of each method declaration above can be omitted. IntelliJ will grey-out these modifiers and inform (when moused over) that they are redundant for `interface` methods. `interface` methods cannot be `static`, meaning they are instance-specific.

The class data above (`DEFAULT_N_SIDES`) is `public`, `final`, and `static`. These three modifiers, like `public` and `abstract` for the methods, are redundant and can be omitted. In other words, the `interface` above is identical to this one:

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

Next, we will create four implementations of the `Polygon` interface: `Rectangle`, `Triangle`, `Square`, and `Circle`. `Rectangle` has been done below.

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

**<a name="q1"></a>[EXERCISE 1](#a1)** Notice that the `setWidth` and `setHeight` methods are very similar. One could even say they are repetitive. How might one change the WET solution above into a DRY solution?

**<a name="a1"></a>[SOLUTION 1](#q1)** We've repeated code in the `setWidth` and `setHeight` methods almost exactly. This could be replaced with a call to a new method "`validateLength`", but even this would be a bit redundant; as is, the `if`/`else` in each method is just using `0` if the input is negative, and the input otherwise, so it can be replaced by a call to `Math.max`.

Notice the `@Override` annotation on all methods which are overridden from the `Polygon` `interface`. While these tags are not mandatory when overriding methods, the are beneficial for the following reasons:

* They will cause a compile-time error if an overridden method declaration has a typo (e.g. an incorrect identifier or incorrect sequence of arguments), which makes such errors easier to identify and fix; otherwise, a new method can be created where overriding was intended.
* They improve readability. In fact, many IDEs will display extra information detailing the location of the method being overridden if the `@Override` annotation is present before a method.

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

Create a new `PolygonClient` to test all four implementations of the `Polygon` class. Your client shoud continuously construct polygons for the user (storing them in an `ArrayList<Polygon>`). It should start by prompting the user for the polygon they want to create next (of the four options), and then follow up by prompting the user for whatever data is necessary to construct the desired polygon. Finally, it should ask the user if they want to continue. You may use the `InputHandler` class below to validate user inputs, and do not need to deal with user-input issues not handled by this class. You may read through it, or simply trust that its static `getDoubleFromUser`, `getPolygonFromUser`, and `getYesNoFromUser` methods return a `double`, `String`, or `boolean` respectively, from `System.in`. In the case of `getPolygonFromUser`, the output is "circle", "rectangle", "square", or "triangle".

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

## Task 5

If you're not **very familiar** with cartesian and polar coordinates, check out [this video](https://www.youtube.com/watch?v=L4v98ZZft68) (or any of the literally hundreds of youtube video on the topic).

I will record the zoom hours and add another video [here]()(not recorded yet), discussing these two coordinate schemes among other lab elements.

Download [point.zip](./point.zip). Read the `Point` `interface` defined in `Point.java`. You will refer to it throughout this task, as it contains descriptions of all methods. You must implement these methods in the provided `PolarPoint` and `CartesianPoint` classes.

If you mouse over the underlined immediate syntax errors in `CartesianPoint.java` and `PolarPoint.java`, your IDE will likely instruct you on a shortcut to create (empty) method definitions for all missing abstract methods from the `Point` `interface`.

Create a client class to test these methods.

Note that in both `PolarPoint` and `CartesianPoint`, the instance data is `private`, but all of the methods defined in `Point` are `public`. This is an example of **encapsulation**; the data and interface are separated, so changes can be made to `PolarPoint` or `CartesianPoint` individually and as long as these two classes adhere to the `Point` interface, uses of `Point`s in other classes will not need to change.

## Bonus Task

This is a mathy bonus task that has nothing to do with polymorphism. It is not worth extra credit.

Recall that the area of a circle is pi times the radius squared. As such, a circle with radius 1 should have area equal to pi.

Consider the circle with radius 1 centered on the origin. It is contained completely within the 2x2 square with corners *(-1,-1)*, *(-1,1)*, *(1,-1)* and *(1,1)* (in cartesian coordinates). This square has area equal to 4 square units.

Generate 10000 random cartesian points in this 2x2 square around the origin. Count how many are inside the unit circle centered on the origin (by checking if the distance to the origin is less than 1). Calculate the proportion of generated random points which are in the circle. Multiply this proportion by 4 (the area of the entire square) to approximate the the area of the circle (and thereby approximate pi).

Discuss the results.




