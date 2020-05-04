# COMP 150 Lab 7 - Polymorphism, Inheritance, Abstraction and Encapsulation

In this lab:

* Encapsulation (here and there)
* Abstraction and polymorphism via a single `interface`
* Inheritance
* Abstract classes
* Multiple inheritance via interfacing

The **Pillars of Object Oriented Programming** are **polymorphism**, **inheritance**, **abstraction** and **encapsulation**. We will be talking about each of these conceptually and as they appear in Java.

The primary motivations underlying the pillars (and the goal to keep in mind throughout this lab) are increased code reusability and decreased code dublication. In other words, [**don't repeat yourself** (DRY)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

Quoting the wiki linked above:

 "*Violations of DRY are typically referred to as WET solutions, which is commonly taken to stand for "write every time", "write everything twice", "we enjoy typing" or "waste everyone's time". WET solutions are common in multi-tiered architectures where a developer may be tasked with, for example, adding a comment field on a form in a web application. The text string "comment" might be repeated in the label, the HTML tag, in a read function name, a private variable, database DDL, queries, and so on. A DRY approach eliminates that redundancy by using frameworks that reduce or eliminate all those editing tasks except the most important ones, leaving the extensibility of adding new knowledge variables in one place.*"

## Polymorphism Revisited

We have discussed **polymorphism** briefly with respect to the `+` operator in a previous lab. The `+` operator is used to denote a variety of operations depending on its surroundings; it performs concatenation on `String`s and addition on numerical primitives. In other words, the `+` operator has **multiple forms** or **multiple definitions**.

We have also discussed polymorphism in the context of overloaded functions by creating multiple functions sharing an identifier to deal with different argument configurations.

Finally, we have touched on polymorphism in the context of **inheritance**; every class **inherits from** (or **extends**) the `Object` class, meaning:

* Every class is the `Object` class, but with **extra stuff**™ (whatever is defined in the class body).
* Every instance of every class literally **is an `Object`** (that is, it is an instance of the `Object` class), likely with some extra capabilities defined by said **extra stuff**™.

## Abstraction and polymorphism with the `interface`

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

The methods the `interface` above (and in any `interface`) **must** be `public` and `abstract`. In fact, the `public abstract` on the start of each method declaration above can be omitted. IntelliJ will grey-out these modifiers and inform (when moused over) that they are redundant for `interface` methods. `interface` methods cannot be `static`, meaning they are instance-specific.

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

Notice the `@Override` annotation on all methods which are overridden from the `Polygon` `interface`. While these tags are not mandatory when overriding methods, the are beneficial for the following reasons:

* They will cause a compile-time error if an overridden method declaration has a type (e.g. an incorrect identifier or incorrect sequence of arguments), which makes such errors easier to identify and fix; otherwise, a new method can be created where overriding was intended.
* They improve readability. In fact, many IDEs will display extra information detailing the location of the method being overridden if the `@Override` annotation is present before a method.

**EXERCISE** Create a client to test the `Rectangle` class above. Start with the client below, and test every `public` element in the `Rectangle` class. Note that `myRectangle` is declared as a `Polygon` but uses the `Rectangle` constructor; because `Rectangle implements Polygon`, any `Rectangle` **is** a `Polygon`. Because `myRectangle` is declared as a `Polygon` and not as a `Rectangle`, it can only be used to access implemented members of the `Polygon` class, and not to access other members of the `Rectangle` class.

**EXERCISE** Create the `Square`, `Triangle`, and `Circle` classes. In each class, you will need to decide what instance data to declare, create at least 1 constructor, and implement all of the `Polygon` methods. You may assume that `Circle`s have 0 sides (although there is some philosophical argument toward inifinite sides as well).

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

**EXERCISE** Create a new `PolygonClient` to test all four implementations of the `Polygon` class. Your client shoud continuously construct polygons for the user (storing them in an `ArrayList<Polygon>`). It should start by prompting the user for the polygon they want to create next (of the four options), and then follow up by prompting the user for whatever data is necessary to construct the desired polygon. Finally, it should ask the user if they want to continue You may use the `InputHandler` class below to validate user inputs, and do not need to deal with user-input issues not handled by this class. You may read through it, or simply trust that its `static` `getDoubleFromUser`, `getPolygonFromUser`, and `getYesNoFromUser` methods `return` a `double` value or a `String` value (`"circle"`, `"rectangle"`, `"square"` or `"triangle"`), respectively, from `System.in`.

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

**EXERCISE** Write me a nice note. It might be about that goofy `InputHandler` class above. It might be about my outstanding hairdo. If you are in one of my classes, submit it with your lab. If not, write it on a napkin and burn it in a small metal or glass container, and then howl at the sky for precisely ten seconds and your message will be delivered to me by the owl or butterfly nearest you at its earliest convenience.

## Task 1

If you're not **very familiar** with cartesian and polar coordinates, check out [this video](https://www.youtube.com/watch?v=L4v98ZZft68) (or any of the HUNDREDS youtube video on the topic).

(I will record the zoom hours and add another video [here](), discussing these two coordinate schemes among other lab elements)

Download [point.zip](./point.zip). Read the `Point` `interface` defined in `Point.java`. You will refer to it throughout this task, as it contains descriptions of all methods. You must implement these methods in the provided `PolarPoint` and `CartesianPoint` classes.

Create a client class to test these methods.

Note that in both `PolarPoint` and `CartesianPoint`, the instance data is `private`, but all of the methods defined in `Point` are `public`. This is an example of **encapsulation**; the data and interface are separated, so any changes made to `PolarPoint` or `CartesianPoint` which don't require modifying `Point` itself don't create the need to then modify clients which uses `Point`s.

## Inheritance

- extends
- simple example, small inheritance hierarchy
- overwriting methods

## Abstract classes

- larger example, larger inheritance hierarchy

## Multiple Inheritance

## Extra Task

Estimate Pi with random Point generation.





