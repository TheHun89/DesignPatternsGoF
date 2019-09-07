Design Patterns

🚀 Introduction
=================

Design patterns are solutions to recurring problems; **guidelines on how to tackle certain problems**. They are not classes, packages or libraries that you can plug into your application and wait for the magic to happen. These are, rather, guidelines on how to tackle certain problems in certain situations. 

> Design patterns solutions to recurring problems; guidelines on how to tackle certain problems

> In software engineering, a software design pattern is a general reusable solution to a commonly occurring problem within a given context in software design. It is not a finished design that can be transformed directly into source or machine code. It is a description or template for how to solve a problem that can be used in many different situations.

⚠️ Be Careful
-----------------
- Design patterns are not a silver bullet to all your problems.
- Do not try to force them; bad things are supposed to happen, if done so. Keep in mind that design patterns are solutions **to** problems, not solutions **finding** problems; so don't overthink.
- If used in a correct place in a correct manner, they can prove to be a savior; or else they can result in a horrible mess of a code.

Types of Design Patterns
-----------------

* [Creational](#creational-design-patterns)
* [Structural](#structural-design-patterns)
* [Behavioral](#behavioral-design-patterns)


Creational Design Patterns
==========================

In plain words
> Creational patterns are focused towards how to instantiate an object or group of related objects.

> In software engineering, creational design patterns are design patterns that deal with object creation mechanisms, trying to create objects in a manner suitable to the situation. The basic form of object creation could result in design problems or added complexity to the design. Creational design patterns solve this problem by somehow controlling this object creation.
 
 * [Simple Factory](#-simple-factory)
 * [Factory Method](#-factory-method)
 * [Abstract Factory](#-abstract-factory)
 * [Builder](#-builder)
 * [Prototype](#-prototype)
 * [Singleton](#-singleton)
 
🏠 Simple Factory
--------------
Real world example
> Consider, you are building a house and you need doors. It would be a mess if every time you need a door, you put on your carpenter clothes and start making a door in your house. Instead you get it made from a factory.

In plain words
> Simple factory simply generates an instance for client without exposing any instantiation logic to the client

> In object-oriented programming (OOP), a factory is an object for creating other objects – formally a factory is a function or method that returns objects of a varying prototype or class from some method call, which is assumed to be "new".

**Programmatic Example**

First of all we have a door interface and the implementation
```csharp
public interface IDoor
{
    float Width { get; set; }
    float Height { get; set; }
}

public class WoodenDoor : IDoor
{
    public float Width { get; set; }
    public float Height { get; set; }

    public WoodenDoor(float width, float height)
    {
        Width = width;
        Height = height;
    }
}
```
Then we have our door factory that makes the door and returns it
```csharp
class DoorFactory
{
   public static IDoor MakeDoor(float width, float height)
   {
       return new WoodenDoor(width, height);
   }
}
```
And then it can be used as
```csharp
var door = DoorFactory.makeDoor(100, 200);
Console.WriteLine("Width: " + door.Width);
Console.WriteLine("Height: " + door.Height);
```

**When to Use?**

When creating an object is not just a few assignments and involves some logic, it makes sense to put it in a dedicated factory instead of repeating the same code everywhere. 

🏭 Factory Method
--------------

Real world example
> Consider the case of a hiring manager. It is impossible for one person to interview for each of the positions. Based on the job opening, she has to decide and delegate the interview steps to different people. 

In plain words
> It provides a way to delegate the instantiation logic to child classes. 

> In class-based programming, the factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.
 
 **Programmatic Example**
 
Taking our hiring manager example above. First of all we have an interviewer interface and some implementations for it

```csharp
interface IInterviewer
{
    void AskQuestions();
}

class Developer : IInterviewer
{
    public void AskQuestions()
    {
        Console.WriteLine("Asking about design patterns!");
    }
}

class CommunityExecutive : IInterviewer
{
    public void AskQuestions()
    {
        Console.WriteLine("Asking about community building");
    }
}
```

Now let us create our `HiringManager`

```csharp
abstract class HiringManager
{
    // Factory method
    abstract public IInterviewer MakeInterviewer();
    
    public void TakeInterview()
    {
        var interviewer = MakeInterviewer();
        interviewer.AskQuestions();
    }
}
```
Now any child can extend it and provide the required interviewer
```csharp
class DevelopmentManager : HiringManager
{
    public IInterviewer MakeInterviewer()
    {
        return new Developer();
    }
}

class MarketingManager : HiringManager
{
    public IInterviewer MakeInterviewer()
    {
        return new CommunityExecutive();
    }
}
```
and then it can be used as

```csharp
var devManager = new DevelopmentManager();
devManager.TakeInterview(); // Output: Asking about design patterns

var marketingManager = new MarketingManager();
marketingManager.TakeInterview(); // Output: Asking about community building.
```

**When to use?**

Useful when there is some generic processing in a class but the required sub-class is dynamically decided at runtime. Or putting it in other words, when the client doesn't know what exact sub-class it might need.

🔨 Abstract Factory
----------------

Real world example
> Extending our door example from Simple Factory. Based on your needs you might get a wooden door from a wooden door shop, iron door from an iron shop or a PVC door from the relevant shop. Plus you might need a guy with different kind of specialities to fit the door, for example a carpenter for wooden door, welder for iron door etc. As you can see there is a dependency between the doors now, wooden door needs carpenter, iron door needs a welder etc.

In plain words
> A factory of factories; a factory that groups the individual but related/dependent factories together without specifying their concrete classes. 
  
> The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes

**Programmatic Example**

Translating the door example above. First of all we have our `Door` interface and some implementation for it

```csharp
interface IDoor
{
    void GetDescription();
}

class WoodenDoor : IDoor
{
    public void GetDescription()
    {
        Console.WriteLine("I am a wooden door");
    }
}

class IronDoor : IDoor
{
    public void GetDescription()
    {
        Console.WriteLine("I am a iron door");
    }
}
```
Then we have some fitting experts for each door type

```csharp
interface IDoorFittingExpert
{
    void GetDescription();
}

class Welder : IDoorFittingExpert
{
    public void GetDescription()
    {
        Console.WriteLine("I can only fit iron doors");
    }
}

class Carpenter : IDoorFittingExpert
{
    public void GetDescription()
    {
        Console.WriteLine("I can only fit wooden doors");
    }
}
```

Now we have our abstract factory that would let us make family of related objects i.e. wooden door factory would create a wooden door and wooden door fitting expert and iron door factory would create an iron door and iron door fitting expert
```csharp
interface IDoorFactory
{
    IDoor MakeDoor();
    IDoorFittingExpert MakeFittingExpert();
}

// Wooden factory to return carpenter and wooden door
class WoodenDoorFactory : IDoorFactory
{
    public IDoor MakeDoor()
    {
        return new WoodenDoor();
    }

    public IDoorFittingExpert MakeFittingExpert() 
    {
        return new Carpenter();
    }
}

// Iron door factory to get iron door and the relevant fitting expert
class IronDoorFactory : IDoorFactory
{
    public IDoor MakeDoor()
    {
        return new IronDoor();
    }

    public IDoorFittingExpert MakeFittingExpert()
    {
        return new Welder();
    }
}
```
And then it can be used as
```csharp
var woodenFactory = new WoodenDoorFactory();

var door = woodenFactory.MakeDoor();
var expert = woodenFactory.MakeFittingExpert();

door.GetDescription();  // Output: I am a wooden door
expert.GetDescription(); // Output: I can only fit wooden doors

// Same for Iron Factory
var ironFactory = new IronDoorFactory();

var door = ironFactory.MakeDoor();
var expert = ironFactory.MakeFittingExpert();

door.GetDescription();  // Output: I am an iron door
expert.GetDescription(); // Output: I can only fit iron doors
```

As you can see the wooden door factory has encapsulated the `carpenter` and the `wooden door` also iron door factory has encapsulated the `iron door` and `welder`. And thus it had helped us make sure that for each of the created door, we do not get a wrong fitting expert.   

**When to use?**

When there are interrelated dependencies with not-that-simple creation logic involved

👷 Builder
--------------------------------------------
Real world example
> Imagine you are at Hardee's and you order a specific deal, lets say, "Big Hardee" and they hand it over to you without *any questions*; this is the example of simple factory. But there are cases when the creation logic might involve more steps. For example you want a customized Subway deal, you have several options in how your burger is made e.g what bread do you want? what types of sauces would you like? What cheese would you want? etc. In such cases builder pattern comes to the rescue.

In plain words
> Allows you to create different flavors of an object while avoiding constructor pollution. Useful when there could be several flavors of an object. Or when there are a lot of steps involved in creation of an object.
 
> The builder pattern is an object creation software design pattern with the intentions of finding a solution to the telescoping constructor anti-pattern.

Having said that let me add a bit about what telescoping constructor anti-pattern is. At one point or the other we have all seen a constructor like below:
 
```csharp
public Burger(size, cheese = true, pepperoni = true, tomato = false, lettuce = true)
{
}
```

As you can see; the number of constructor parameters can quickly get out of hand and it might become difficult to understand the arrangement of parameters. Plus this parameter list could keep on growing if you would want to add more options in future. This is called telescoping constructor anti-pattern.

**Programmatic Example**

The sane alternative is to use the builder pattern. First of all we have our burger that we want to make

```csharp
class Burger
{
    public int Size { get; }

    public bool Cheese { get; }
    public bool Pepperoni { get; }
    public bool Lettuce { get; }
    public bool Tomato { get; }
    
    public Burger(BurgerBuilder builder)
    {
        Size = builder.Size;
        Cheese = builder.Cheese;
        Pepperoni = builder.Pepperoni;
        Lettuce = builder.Lettuce;
        Tomato = builder.Tomato;
    }
}
```

And then we have the builder

```csharp
class BurgerBuilder
{
    public int Size { get; }

    public bool Cheese { get; private set; }
    public bool Pepperoni { get; private set; }
    public bool Lettuce { get; private set; }
    public bool Tomato { get; private set; }

    public BurgerBuilder(int size)
    {
        Size = size;
    }
    
    public BurgerBuilder AddPepperoni()
    {
        Pepperoni = true;
        return this;
    }
    
    public BurgerBuilder AddLettuce()
    {
        Lettuce = true;
        return this;
    }
    
    public BurgerBuilder AddCheese()
    {
        Cheese = true;
        return this;
    }
    
    public BurgerBuilder AddTomato()
    {
        Tomato = true;
        return this;
    }
    
    public Burger Build()
    {
        return new Burger(this);
    }
}
```
And then it can be used as:

```csharp
var burger = new BurgerBuilder(14)
		.AddPepperoni()
		.AddLettuce()
		.AddTomato()
		.Build();
```

**When to use?**

When there could be several flavors of an object and to avoid the constructor telescoping. The key difference from the factory pattern is that; factory pattern is to be used when the creation is a one step process while builder pattern is to be used when the creation is a multi step process.

🐑 Prototype
------------
Real world example
> Remember dolly? The sheep that was cloned! Lets not get into the details but the key point here is that it is all about cloning

In plain words
> Create object based on an existing object through cloning.

> The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects.

In short, it allows you to create a copy of an existing object and modify it to your needs, instead of going through the trouble of creating an object from scratch and setting it up.

**Programmatic Example**

In C#, it can be easily done using `ICloneable`
  
```csharp
class Sheep : ICloneable
{
    public string Name { get; set; }
    public string Category { get; set; }

    public Sheep(string name, string category = "Mountain Sheep")
    {
        Name = name;
        Category = category;
    }
	
    public object Clone()
    {
    	return this.MemberwiseClone();
    }
}
```
Then it can be cloned like below
```csharp
var original = new Sheep('Jolly');
Console.WriteLine(original.Name); // Jolly
Console.WriteLine(original.Category); // Mountain Sheep

// Clone and modify what is required
var cloned = original.Clone();
cloned.Name = "Dolly";
Console.WriteLine(cloned.Name); // Dolly
Console.WriteLine(cloned.Category); // Mountain Sheep
```

Also you could use the magic method `Clone` to modify the cloning behavior.

**When to use?**

When an object is required that is similar to existing object or when the creation would be expensive as compared to cloning.

💍 Singleton
------------
Real world example
> There can only be one president of a country at a time. The same president has to be brought to action, whenever duty calls. President here is singleton.

In plain words
> Ensures that only one object of a particular class is ever created.

> In software engineering, the singleton pattern is a software design pattern that restricts the instantiation of a class to one object. This is useful when exactly one object is needed to coordinate actions across the system.

Singleton pattern is actually considered an anti-pattern and overuse of it should be avoided. It is not necessarily bad and could have some valid use-cases but should be used with caution because it introduces a global state in your application and change to it in one place could affect in the other areas and it could become pretty difficult to debug. The other bad thing about them is it makes your code tightly coupled plus it mocking the singleton could be difficult.

**Programmatic Example**

To create a singleton, make the constructor private, disable cloning, disable extension and create a static variable to house the instance
```csharp
// Example taken from http://csharpindepth.com/Articles/General/Singleton.aspx
// There is a lot of caveat. So read it!
public sealed class President
{
    private static readonly President instance = new President();

    // Explicit static constructor to tell C# compiler
    // not to mark type as beforefieldinit
    static President()
    {
    }

    private President()
    {
    }

    public static President Instance
    {
        get
        {
            return instance;
        }
    }
} 
```
Then in order to use
```csharp
var president1 = President.Instance;
var president2 = President.Instance;

Console.WriteLine(President1 == President2); // true
```

Structural Design Patterns
==========================
In plain words
> Structural patterns are mostly concerned with object composition or in other words how the entities can use each other. Or yet another explanation would be, they help in answering "How to build a software component?"

> In software engineering, structural design patterns are design patterns that ease the design by identifying a simple way to realize relationships between entities.
  
 * [Adapter](#-adapter)
 * [Bridge](#-bridge)
 * [Composite](#-composite)
 * [Decorator](#-decorator)
 * [Facade](#-facade)
 * [Flyweight](#-flyweight)
 * [Proxy](#-proxy)

🔌 Adapter
-------
Real world example
> Consider that you have some pictures in your memory card and you need to transfer them to your computer. In order to transfer them you need some kind of adapter that is compatible with your computer ports so that you can attach memory card to your computer. In this case card reader is an adapter.
> Another example would be the famous power adapter; a three legged plug can't be connected to a two pronged outlet, it needs to use a power adapter that makes it compatible with the two pronged outlet.
> Yet another example would be a translator translating words spoken by one person to another

In plain words
> Adapter pattern lets you wrap an otherwise incompatible object in an adapter to make it compatible with another class.

> In software engineering, the adapter pattern is a software design pattern that allows the interface of an existing class to be used as another interface. It is often used to make existing classes work with others without modifying their source code.

**Programmatic Example**

Consider a game where there is a hunter and he hunts lions.

First we have an interface `Lion` that all types of lions have to implement

```csharp
interface ILion
{
    void Roar();
}

class AfricanLion : ILion
{
    public void Roar() {}
}

class AsianLion : ILion
{
    public void Roar() {}
}
```
And hunter expects any implementation of `ILion` interface to hunt.
```csharp
class Hunter
{
    public void Hunt(ILion lion)
    {
    }
}
```

Now let's say we have to add a `WildDog` in our game so that hunter can hunt that also. But we can't do that directly because dog has a different interface. To make it compatible for our hunter, we will have to create an adapter that is compatible
 
```csharp
// This needs to be added to the game
class WildDog
{
    public void Bark() {}
}

// Adapter around wild dog to make it compatible with our game
class WildDogAdapter : ILion
{
    private Dog { get; set; }

    public WildDogAdapter(WildDog dog)
    {
        Dog = dog;
    }
    
    public void Roar()
    {
        Dog.Bark();
    }
}
```
And now the `WildDog` can be used in our game using `WildDogAdapter`.

```csharp
var wildDog = new WildDog();
wildDogAdapter = new WildDogAdapter(wildDog);

var hunter = new Hunter();
hunter.Hunt(wildDogAdapter);
```

🚡 Bridge
------
Real world example
> Consider you have a website with different pages and you are supposed to allow the user to change the theme. What would you do? Create multiple copies of each of the pages for each of the themes or would you just create separate theme and load them based on the user's preferences? Bridge pattern allows you to do the second i.e.

![With and without the bridge pattern](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

In Plain Words
> Bridge pattern is about preferring composition over inheritance. Implementation details are pushed from a hierarchy to another object with a separate hierarchy.

> The bridge pattern is a design pattern used in software engineering that is meant to "decouple an abstraction from its implementation so that the two can vary independently"

**Programmatic Example**

Translating our WebPage example from above. Here we have the `IWebPage` hierarchy

```csharp
interface IWebPage
{
    string GetContent();
}

class About : IWebPage
{
    private ITheme Theme { get; set; }
    
    public About(ITheme theme)
    {
        Theme = theme;
    }
    
    public string GetContent()
    {
        return "About page in " + Theme.GetColor();
    }
}

class Careers : IWebPage
{
   private ITheme Theme { get; set; }
   
   public Careers(ITheme theme)
   {
       Theme = theme;
   }
   
   public string GetContent()
   {
       return "Careers page in " + Theme.GetColor();
   } 
}
```
And the separate theme hierarchy
```csharp
interface ITheme
{
    string GetColor();
}

class DarkTheme : ITheme
{
    public string GetColor()
    {
        return "Dark Black";
    }
}
class LightTheme : ITheme
{
    public string getColor()
    {
        return "Off white";
    }
}
class AquaTheme : ITheme
{
    public string GetColor()
    {
        return "Light blue";
    }
}
```
And both the hierarchies
```csharp
var darkTheme = new DarkTheme();

var about = new About(darkTheme);
var careers = new Careers(darkTheme);

Console.WriteLine(about.GetContent()); // "About page in Dark Black";
Console.WriteLine(careers.GetContent()); // "Careers page in Dark Black";
```

🌿 Composite
-----------------

Real world example
> Every organization is composed of employees. Each of the employees has same features i.e. has a salary, has some responsibilities, may or may not report to someone, may or may not have some subordinates etc.

In plain words
> Composite pattern lets clients to treat the individual objects in a uniform manner.

> In software engineering, the composite pattern is a partitioning design pattern. The composite pattern describes that a group of objects is to be treated in the same way as a single instance of an object. The intent of a composite is to "compose" objects into tree structures to represent part-whole hierarchies. Implementing the composite pattern lets clients treat individual objects and compositions uniformly.

**Programmatic Example**

Taking our employees example from above. Here we have different employee types

```csharp
interface IEmployee
{
    string Name { get; }
    float Salary { get; set; }
    IEmployee[] Roles  { get; }
}

class Developer : IEmployee
{
    public float Salary { get; set; }
    public string Name { get; }
    public IEmployee[] Roles  { get; }

    public Developer(string name, float salary)
    {
        Name = name;
        Salary = salary;
    }
}

class Designer : IEmployee
{
    public float Salary { get; set; }
    public string Name { get; }
    public IEmployee[] Roles  { get; }

    public Designer(string name, float salary)
    {
        Name = name;
        Salary = salary;
    }
}
```

Then we have an organization which consists of several different types of employees

```csharp
class Organization
{
    private IList<IEmployee> Employees { get; } = new List<IEmployee>();

    public void AddEmployee(IEmployee employee)
    {
        Employees.Add(employee);
    }

    public float GetNetSalaries() => Employees.Sum(employee => employee.Salary);
}
```

And then it can be used as

```csharp
// Prepare the employees
var john = new Developer("John Doe", 12000);
var jane = new Designer("Jane", 10000);

// Add them to organization
var organization = new Organization();
organization.AddEmployee(john);
organization-.AddEmployee(jane);

Console.WriteLine("Net salaries: " + organization.GetNetSalaries()); // Net Salaries: 22000
```

☕ Decorator
-------------

Real world example

> Imagine you run a car service shop offering multiple services. Now how do you calculate the bill to be charged? You pick one service and dynamically keep adding to it the prices for the provided services till you get the final cost. Here each type of service is a decorator.

In plain words
> Decorator pattern lets you dynamically change the behavior of an object at run time by wrapping them in an object of a decorator class.

> In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. The decorator pattern is often useful for adhering to the Single Responsibility Principle, as it allows functionality to be divided between classes with unique areas of concern.

**Programmatic Example**

Lets take coffee for example. First of all we have a simple coffee implementing the coffee interface
```csharp
interface ICoffee
{
    int Cost { get; }
    string Description { get; }
}

class SimpleCoffee : ICoffee
{
    public int Cost { get; } = 10;
    public string Description { get; } = "Simple coffee";
}
```
We want to make the code extensible to allow options to modify it if required. Lets make some add-ons (decorators)
```csharp
class MilkCoffee : ICoffee
{
    protected ICoffee Coffee { get; }
    
    public int Cost { get { return Coffee.Cost + 2; } }
	
    public string Description { get { return Coffee.Description + ", milk"; } }
	
    public MilkCoffee(ICoffee coffee)
    {
        Coffee = coffee;
    }
}

class WhipCoffee : ICoffee
{
    protected ICoffee Coffee { get; }

    public int Cost { get { return Coffee.Cost + 5; } }
	
    public string Description { get { return Coffee.Description + ", whip"; } }
	
    public WhipCoffee(ICoffee coffee)
    {
        Coffee = coffee;
    }
}

class VanillaCoffee : ICoffee
{
    protected ICoffee Coffee { get; }

    public int Cost { get { return Coffee.Cost + 3; } }
	
    public string Description { get { return Coffee.Description + ", vanilla"; } }
	
    public VanillaCoffee(ICoffee coffee)
    {
        Coffee = coffee;
    }
}
```
Lets make a coffee now
```csharp
var someCoffee = new SimpleCoffee();
Console.WriteLine(someCoffee.Cost()); // 10
Console.WriteLine(someCoffee.Description()); // Simple Coffee

someCoffee = new MilkCoffee(someCoffee);
Console.WriteLine(someCoffee.Cost()); // 12
Console.WriteLine(someCoffee.Description(); // Simple Coffee, milk

someCoffee = new WhipCoffee(someCoffee);
Console.WriteLine(someCoffee.Cost()); // 17
Console.WriteLine(someCoffee.Description()); // Simple Coffee, milk, whip

someCoffee = new VanillaCoffee(someCoffee);
Console.WriteLine(someCoffee.Cost()); // 20
Console.WriteLine(someCoffee.Description()); // Simple Coffee, milk, whip, vanilla
```

📦 Facade
----------------

Real world example
> How do you turn on the computer? "Hit the power button" you say! That is what you believe because you are using a simple interface that computer provides on the outside, internally it has to do a lot of stuff to make it happen. This simple interface to the complex subsystem is a facade.

In plain words
> Facade pattern provides a simplified interface to a complex subsystem.

> A facade is an object that provides a simplified interface to a larger body of code, such as a class library.

**Programmatic Example**
Taking our computer example from above. Here we have the computer class

```csharp
class Computer
{
    public void GetElectricShock()
    {
        Console.WriteLine("Ouch!");
    }

    public void MakeSound()
    {
        Console.WriteLine("Beep beep!");
    }

    public void ShowLoadingScreen()
    {
        Console.WriteLine("Loading..");
    }

    public void Bam()
    {
        Console.WriteLine("Ready to be used!");
    }

    public void CloseEverything()
    {
        Console.WriteLine("Bup bup bup buzzzz!");
    }

    public void Sooth()
    {
        Console.WriteLine("Zzzzz");
    }

    public void PullCurrent()
    {
        Console.WriteLine("Haaah!");
    }
}
```
Here we have the facade
```csharp
class ComputerFacade
{
    protected Computer Computer;

    public ComputerFacade(Computer computer)
    {
        Computer = computer;
    }

    public void TurnOn()
    {
        Computer.GetElectricShock();
        Computer.MakeSound();
        Computer.ShowLoadingScreen();
        Computer.Bam();
    }

    public void TurnOff()
    {
        Computer.CloseEverything();
        Computer.PullCurrent();
        Computer.Sooth();
    }
}
```
Now to use the facade
```csharp
var computer = new ComputerFacade(new Computer());
computer.TurnOn(); // Ouch! Beep beep! Loading.. Ready to be used!
computer.TurnOff(); // Bup bup buzzz! Haah! Zzzzz
```

🍃 Flyweight
---------

Real world example
> Did you ever have fresh tea from some stall? They often make more than one cup that you demanded and save the rest for any other customer so to save the resources e.g. gas etc. Flyweight pattern is all about that i.e. sharing.

In plain words
> It is used to minimize memory usage or computational expenses by sharing as much as possible with similar objects.

> In computer programming, flyweight is a software design pattern. A flyweight is an object that minimizes memory use by sharing as much data as possible with other similar objects; it is a way to use objects in large numbers when a simple repeated representation would use an unacceptable amount of memory.

**Programmatic example**
Translating our tea example from above. First of all we have tea types and tea maker

```csharp
// Anything that will be cached is flyweight. 
// Types of tea here will be flyweights.
class KarakTea
{
}

// Acts as a factory and saves the tea
class TeaMaker
{
    protected Dictionary<string, KarakTea> AvailableTea = new Dictionary<string, KarakTea>();

    public KarakTea Make(string preference)
    {
        if (!AvailableTea.Keys.Contains(preference))
	{
            AvailableTea.Add(preference, new KarakTea());
        }

        return AvailableTea[preference];
    }
}
```
Then we have the `TeaShop` which takes orders and serves them
```csharp
class TeaShop
{    
    protected Dictionary<int, KarakTea> Orders = new Dictionary<int, KarakTea>();
    protected TeaMaker TeaMaker;

    public TeaShop(TeaMaker teaMaker)
    {
        TeaMaker = teaMaker;
    }

    public void TakeOrder(string teaType, int table)
    {
        Orders[table] = TeaMaker.Make(teaType);
    }

    public void Serve()
    {
        foreach(var table in Orders.Select(order => order.Key))
        {
            Console.WriteLine("Serving tea to table# " + table);
        }
    }
}
```
And it can be used as below

```csharp
var teaMaker = new TeaMaker();
var shop = new TeaShop(teaMaker);

shop.TakeOrder("less sugar", 1);
shop.TakeOrder("more milk", 2);
shop.TakeOrder("without sugar", 5);

shop.Serve();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
```

🎱 Proxy
-------------------
Real world example
> Have you ever used an access card to go through a door? There are multiple options to open that door i.e. it can be opened either using access card or by pressing a button that bypasses the security. The door's main functionality is to open but there is a proxy added on top of it to add some functionality. Let me better explain it using the code example below.

In plain words
> Using the proxy pattern, a class represents the functionality of another class.

> A proxy, in its most general form, is a class functioning as an interface to something else. A proxy is a wrapper or agent object that is being called by the client to access the real serving object behind the scenes. Use of the proxy can simply be forwarding to the real object, or can provide additional logic. In the proxy extra functionality can be provided, for example caching when operations on the real object are resource intensive, or checking preconditions before operations on the real object are invoked.

**Programmatic Example**
Taking our security door example from above. Firstly we have the door interface and an implementation of door

```csharp
interface IDoor
{
    void Open();
    void Close();
}

class LabDoor : IDoor
{
    public void Open()
    {
        Console.WriteLine("Opening lab door");
    }

    public void Close()
    {
        Console.WriteLine("Closing the lab door");
    }
}
```
Then we have a proxy to secure any doors that we want
```csharp
class Security
{
    protected IDoor Door;

    public Security(IDoor door)
    {
        Door = door;
    }

    public void Open(string password)
    {
        if (Authenticate(password))
        {
            Door.Open();
        }
        else
        {
            Console.WriteLine("Big no! It ain't possible.");
        }
    }

    public bool Authenticate(string password)
    {
        return password == "$ecr@t";
    }

    public void Close()
    {
        Door.Close();
    }
}
```
And here is how it can be used
```csharp
var door = new Security(new LabDoor());
door.Open("invalid"); // Big no! It ain't possible.

door.Open("$ecr@t"); // Opening lab door
door.Close(); // Closing lab door
```
Yet another example would be some sort of data-mapper implementation. For example, I recently made an ODM (Object Data Mapper) for MongoDB using this pattern where I wrote a proxy around mongo classes while utilizing the magic method `__call()`. All the method calls were proxied to the original mongo class and result retrieved was returned as it is but in case of `find` or `findOne` data was mapped to the required class objects and the object was returned instead of `Cursor`.

Behavioral Design Patterns
==========================

In plain words
> It is concerned with assignment of responsibilities between the objects. What makes them different from structural patterns is they don't just specify the structure but also outline the patterns for message passing/communication between them. Or in other words, they assist in answering "How to run a behavior in software component?"

> In software engineering, behavioral design patterns are design patterns that identify common communication patterns between objects and realize these patterns. By doing so, these patterns increase flexibility in carrying out this communication.

* [Chain of Responsibility](#-chain-of-responsibility)
* [Command](#-command)
* [Iterator](#-iterator)
* [Mediator](#-mediator)
* [Memento](#-memento)
* [Observer](#-observer)
* [Visitor](#-visitor)
* [Strategy](#-strategy)
* [State](#-state)
* [Template Method](#-template-method)

🔗 Chain of Responsibility
-----------------------

Real world example
> For example, you have three payment methods (`A`, `B` and `C`) setup in your account; each having a different amount in it. `A` has 100 USD, `B` has 300 USD and `C` having 1000 USD and the preference for payments is chosen as `A` then `B` then `C`. You try to purchase something that is worth 210 USD. Using Chain of Responsibility, first of all account `A` will be checked if it can make the purchase, if yes purchase will be made and the chain will be broken. If not, request will move forward to account `B` checking for amount if yes chain will be broken otherwise the request will keep forwarding till it finds the suitable handler. Here `A`, `B` and `C` are links of the chain and the whole phenomenon is Chain of Responsibility.

In plain words
> It helps building a chain of objects. Request enters from one end and keeps going from object to object till it finds the suitable handler.

> In object-oriented design, the chain-of-responsibility pattern is a design pattern consisting of a source of command objects and a series of processing objects. Each processing object contains logic that defines the types of command objects that it can handle; the rest are passed to the next processing object in the chain.

**Programmatic Example**

Translating our account example above. First of all we have a base account having the logic for chaining the accounts together and some accounts

```c#
abstract class Account
{
    protected float _balance;
    public Account Successor { get; set; }

    public Account(float balance)
    {
        _balance = balance;
    }

    public void Pay(float amountToPay)
    {
        // Check if current acount have enough money to pay.
        if (CanPay(amountToPay))
        {
            Console.WriteLine($"Paid {amountToPay} using {GetType().Name}!");
        }
        else if (Successor != null)
        {
            Console.WriteLine($"Cannot pay using {GetType().Name}. Proceeding ..");
            // If current account have a successor account try paying using successor.
            Successor.Pay(amountToPay);
        }
        else
        {
            throw new InvalidOperationException("None of the accounts have enough balance!");
        }
    }

    protected bool CanPay(float amountRequired) => _balance >= amountRequired;
}

class Bank : Account
{
    public Bank(float balance) : base (balance)
    {
    }
}

class Paypal : Account
{
    public Paypal(float balance) : base (balance)
    {
    }
}

class Bitcoin : Account
{
    public Bitcoin(float balance) : base (balance)
    {
    }
}
```
Now let's prepare the chain using the links defined above (i.e. Bank, Paypal, Bitcoin)
```C#
// Let's prepare a chain like below
//      Bank -> Paypal -> Bitcoin
//
// First priority bank
//      If bank can't pay then paypal
//      If paypal can't pay then bit coin

var bank = new Bank(100); // Bank with balance 100
var paypal = new Paypal(200); // Paypal with balance 200
var bitcoin = new Bitcoin(300); // Bitcoin with balance 300
	
bank.Successor = paypal;
paypal.Successor = bitcoin;
	
// Let's try to pay using the first priority i.e. bank
bank.Pay(259);

// Output will be
// ==============
// Cannot pay using Bank. Proceeding ..
// Cannot pay using Paypal. Proceeding ..: 
// Paid 259 using Bitcoin!
```

👮 Command
-------

Real world example
> A generic example would be you ordering a food at restaurant. You (i.e. `Client`) ask the waiter (i.e. `Invoker`) to bring some food (i.e. `Command`) and waiter simply forwards the request to Chef (i.e. `Receiver`) who has the knowledge of what and how to cook. 
> Another example would be you (i.e. `Client`) switching on (i.e. `Command`) the television (i.e. `Receiver`) using a remote control (`Invoker`).

In plain words
> Allows you to encapsulate actions in objects. The key idea behind this pattern is to provide the means to decouple client from receiver.

> In object-oriented programming, the command pattern is a behavioral design pattern in which an object is used to encapsulate all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object that owns the method and values for the method parameters.

**Programmatic Example**

First of all we have the receiver that has the implementation of every action that could be performed
```c#
// Receiver
class Bulb
{
    public void TurnOn()
    {
        Console.WriteLine("Bulb has been lit!");
    }
    
    public void TurnOff()
    {
        Console.WriteLine("Darkness!");
    }
}
```
then we have an interface that each of the commands are going to implement and then we have a set of commands
```C#
interface ICommand
{
    void Execute();
    void Undo();
    void Redo();
}

// Command
class TurnOn : ICommand
{
    protected readonly Bulb _bulb;
    
    public TurnOn(Bulb bulb)
    {
        _bulb = bulb;
    }
    
    public void Execute()
    {
        _bulb.TurnOn();
    }
    
    public void Undo()
    {
        _bulb.TurnOff();
    }
    
    public void Redo()
    {
        Execute();
    }
}

class TurnOff : ICommand
{
    protected readonly Bulb _blub;
    
    public TurnOff(Bulb bulb)
    {
        _blub = bulb;
    }
    
    public void Execute()
    {
        _blub.TurnOff();
    }
    
    public void Undo()
    {
        _blub.TurnOn();
    }
    
    public void Redo()
    {
        Execute();
    }
}
```
Then we have an `Invoker` with whom the client will interact to process any commands
```c#
// Invoker
class RemoteControl
{
    public void Submit(ICommand command)
    {
        command.Execute();
    }
}
```
Finally let's see how we can use it in our client
```c#
var bulb = new Bulb();

var turnOn = new TurnOn(bulb);
var turnOff = new TurnOff(bulb);

var remote = new RemoteControl();
remote.Submit(turnOn); // Bulb has been lit!
remote.Submit(turnOff); // Darkness!
```

Command pattern can also be used to implement a transaction based system. Where you keep maintaining the history of commands as soon as you execute them. If the final command is successfully executed, all good otherwise just iterate through the history and keep executing the `undo` on all the executed commands. 

➿ Iterator
--------

Real world example
> An old radio set will be a good example of iterator, where user could start at some channel and then use next or previous buttons to go through the respective channels. Or take an example of MP3 player or a TV set where you could press the next and previous buttons to go through the consecutive channels or in other words they all provide an interface to iterate through the respective channels, songs or radio stations.  

In plain words
> It presents a way to access the elements of an object without exposing the underlying presentation.

> In object-oriented programming, the iterator pattern is a design pattern in which an iterator is used to traverse a container and access the container's elements. The iterator pattern decouples algorithms from containers; in some cases, algorithms are necessarily container-specific and thus cannot be decoupled.

**Programmatic example**
In PHP it is quite easy to implement using SPL (Standard PHP Library). Translating our radio stations example from above. First of all we have `RadioStation`

```php
class RadioStation {
    protected $frequency;

    public function __construct(float $frequency) {
        $this->frequency = $frequency;    
    }
    
    public function getFrequency() : float {
        return $this->frequency;
    }
}
```
Then we have our iterator

```php
use Countable;
use Iterator;

class StationList implements Countable, Iterator {
    /** @var RadioStation[] $stations */
    protected $stations = [];
    
    /** @var int $counter */
    protected $counter;
    
    public function addStation(RadioStation $station) {
        $this->stations[] = $station;
    }
    
    public function removeStation(RadioStation $toRemove) {
        $toRemoveFrequency = $toRemove->getFrequency();
        $this->stations = array_filter($this->stations, function (RadioStation $station) use ($toRemoveFrequency) {
            return $station->getFrequency() !== $toRemoveFrequency;
        });
    }
    
    public function count() : int {
        return count($this->stations);
    }
    
    public function current() : RadioStation {
        return $this->stations[$this->counter];
    }
    
    public function key() {
        return $this->counter;
    }
    
    public function next() {
        $this->counter++;
    }
    
    public function rewind() {
        $this->counter = 0;
    }
    
    public function valid(): bool
    {
        return isset($this->stations[$this->counter]);
    }
}
```
And then it can be used as
```php
$stationList = new StationList();

$stationList->addStation(new Station(89));
$stationList->addStation(new Station(101));
$stationList->addStation(new Station(102));
$stationList->addStation(new Station(103.2));

foreach($stationList as $station) {
    echo $station->getFrequency() . PHP_EOL;
}

$stationList->removeStation(new Station(89)); // Will remove station 89
```

👽 Mediator
========

Real world example
> A general example would be when you talk to someone on your mobile phone, there is a network provider sitting between you and them and your conversation goes through it instead of being directly sent. In this case network provider is mediator. 

In plain words
> Mediator pattern adds a third party object (called mediator) to control the interaction between two objects (called colleagues). It helps reduce the coupling between the classes communicating with each other. Because now they don't need to have the knowledge of each other's implementation. 

> In software engineering, the mediator pattern defines an object that encapsulates how a set of objects interact. This pattern is considered to be a behavioral pattern due to the way it can alter the program's running behavior.

**Programmatic Example**

Here is the simplest example of a chat room (i.e. mediator) with users (i.e. colleagues) sending messages to each other. 

First of all, we have the mediator i.e. the chat room 

```csharp
// Mediator
interface ChatRoomMediator 
{
    void ShowMessage(User user, string message);
}

class ChatRoom : ChatRoomMediator 
{
    public void ShowMessage (User user, string message) 
    {
        DateTime date = DateTime.Now;
	var sender = user.Name;
	Console.WriteLine($"{date: MM MMM , mm:ss} [{sender}] : { message }");  // Using C# 6.0 Interpolated Strings   
    }
}
```

Then we have our users i.e. colleagues
```csharp
class User 
{
    public string Name { get; }
    public ChatRoomMediator ChatMediator { get; }

    public User(string name, ChatRoomMediator chatMediator) {
        this.Name = name;
        this.ChatMediator = chatMediator;
    }  

    public void Send(string message) {
        this.ChatMediator.ShowMessage(this, message);
    }
}
```
And the usage
```csharp
ChatRoomMediator mediator = new ChatRoom();

User John = new User("John Doe", mediator);
User Jane = new User("Jane Doe", mediator);

John.Send("Hi there!");
Jane.Send("Hey!");

// Output will be
// 07 Jul , 21:15 [John Doe] : Hi there!
// 07 Jul , 21:15 [Jane Doe] : Hey!
```

💾 Memento
-------
Real world example
> Take the example of calculator (i.e. originator), where whenever you perform some calculation the last calculation is saved in memory (i.e. memento) so that you can get back to it and maybe get it restored using some action buttons (i.e. caretaker). 

In plain words
> Memento pattern is about capturing and storing the current state of an object in a manner that it can be restored later on in a smooth manner.

> The memento pattern is a software design pattern that provides the ability to restore an object to its previous state (undo via rollback).

Usually useful when you need to provide some sort of undo functionality.

**Programmatic Example**

Lets take an example of text editor which keeps saving the state from time to time and that you can restore if you want.

First of all we have our memento object that will be able to hold the editor state

```php
class EditorMemento {
    protected $content;
    
    public function __construct(string $content) {
        $this->content = $content;
    }
    
    public function getContent() {
        return $this->content;
    }
}
```

Then we have our editor i.e. originator that is going to use memento object

```php
class Editor {
    protected $content = '';
    
    public function type(string $words) {
        $this->content = $this->content . ' ' . $words;
    }
    
    public function getContent() {
        return $this->content;
    }
    
    public function save() {
        return new EditorMemento($this->content);
    }
    
    public function restore(EditorMemento $memento) {
        $this->content = $memento->getContent();
    }
}
```

And then it can be used as 

```php
$editor = new Editor();

// Type some stuff
$editor->type('This is the first sentence.');
$editor->type('This is second.');

// Save the state to restore to : This is the first sentence. This is second.
$saved = $editor->save();

// Type some more
$editor->type('And this is third.');

// Output: Content before Saving
echo $editor->getContent(); // This is the first sentence. This is second. And this is third.

// Restoring to last saved state
$editor->restore($saved);

$editor->getContent(); // This is the first sentence. This is second.
```

😎 Observer
--------
Real world example
> A good example would be the job seekers where they subscribe to some job posting site and they are notified whenever there is a matching job opportunity.   

In plain words
> Defines a dependency between objects so that whenever an object changes its state, all its dependents are notified.

> The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods.

**Programmatic example**

Translating our example from above. First of all we have job seekers that need to be notified for a job posting
```csharp
class JobPost {
    public string Title { get; set; }
    
    public JobPost(string title) {
        this.Title = title;
    }
}

interface Observer {
    void OnJobPosted(JobPost job);
}

class JobSeeker : Observer {
    public string Name { get; set; }
    
    public JobSeeker(string name) {
        this.Name = name;
    }
    
    public void OnJobPosted(JobPost job) {
        // Do something with the job posting
        Console.WriteLine("Hi {0}! New job posted: {1}", this.Name, job.Title);
    }
}
```
Then we have our job postings to which the job seekers will subscribe
```csharp
interface Observable {
    void Attach(Observer observer);
    void AddJob(JobPost jobPosting;
}

class JobPostings : Observable {
    protected List<Observer> observers = new List<Observer>();
    
    protected void Notify(JobPost jobPosting) {
        foreach (var observer in this.observers) {
            observer.OnJobPosted(jobPosting);
        }
    }
    
    public void Attach(Observer observer) {
        this.observers.Add(observer);
    }
    
    public void AddJob(JobPost jobPosting) {
        this.Notify(jobPosting);
    }
}
```
Then it can be used as
```csharp
// Create subscribers
var johnDoe = new JobSeeker("John Doe");
var janeDoe = new JobSeeker("Jane Doe");
var kaneDoe = new JobSeeker("Kane Doe");

// Create publisher and attach subscribers
var jobPostings = new JobPostings();
jobPostings.Attach(johnDoe);
jobPostings.Attach(janeDoe);

// Add a new job and see if subscribers get notified
jobPostings.AddJob(new JobPost("Software Engineer"));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer
```

🏃 Visitor
-------
Real world example
> Consider someone visiting Dubai. They just need a way (i.e. visa) to enter Dubai. After arrival, they can come and visit any place in Dubai on their own without having to ask for permission or to do some leg work in order to visit any place here; just let them know of a place and they can visit it. Visitor pattern lets you do just that, it helps you add places to visit so that they can visit as much as they can without having to do any legwork.

In plain words
> Visitor pattern lets you add further operations to objects without having to modify them.
    
> In object-oriented programming and software engineering, the visitor design pattern is a way of separating an algorithm from an object structure on which it operates. A practical result of this separation is the ability to add new operations to existing object structures without modifying those structures. It is one way to follow the open/closed principle.

**Programmatic example**

Let's take an example of a zoo simulation where we have several different kinds of animals and we have to make them Sound. Let's translate this using visitor pattern 

```php
// Visitee
interface Animal {
    public function accept(AnimalOperation $operation);
}

// Visitor
interface AnimalOperation {
    public function visitMonkey(Monkey $monkey);
    public function visitLion(Lion $lion);
    public function visitDolphin(Dolphin $dolphin);
}
```
Then we have our implementations for the animals
```php
class Monkey implements Animal {
    
    public function shout() {
        echo 'Ooh oo aa aa!';
    }

    public function accept(AnimalOperation $operation) {
        $operation->visitMonkey($this);
    }
}

class Lion implements Animal {
    public function roar() {
        echo 'Roaaar!';
    }
    
    public function accept(AnimalOperation $operation) {
        $operation->visitLion($this);
    }
}

class Dolphin implements Animal {
    public function speak() {
        echo 'Tuut tuttu tuutt!';
    }
    
    public function accept(AnimalOperation $operation) {
        $operation->visitDolphin($this);
    }
}
```
Let's implement our visitor
```php
class Speak implements AnimalOperation {
    public function visitMonkey(Monkey $monkey) {
        $monkey->shout();
    }
    
    public function visitLion(Lion $lion) {
        $lion->roar();
    }
    
    public function visitDolphin(Dolphin $dolphin) {
        $dolphin->speak();
    }
}
```

And then it can be used as
```php
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
```
We could have done this simply by having a inheritance hierarchy for the animals but then we would have to modify the animals whenever we would have to add new actions to animals. But now we will not have to change them. For example, let's say we are asked to add the jump behavior to the animals, we can simply add that by creating a new visitor i.e.

```php
class Jump implements AnimalOperation {
    public function visitMonkey(Monkey $monkey) {
        echo 'Jumped 20 feet high! on to the tree!';
    }
    
    public function visitLion(Lion $lion) {
        echo 'Jumped 7 feet! Back on the ground!';
    }
    
    public function visitDolphin(Dolphin $dolphin) {
        echo 'Walked on water a little and disappeared';
    }
}
```
And for the usage
```php
$jump = new Jump();

$monkey->accept($speak);   // Ooh oo aa aa!
$monkey->accept($jump);    // Jumped 20 feet high! on to the tree!

$lion->accept($speak);     // Roaaar!
$lion->accept($jump);      // Jumped 7 feet! Back on the ground! 

$dolphin->accept($speak);  // Tuut tutt tuutt! 
$dolphin->accept($jump);   // Walked on water a little and disappeared
```

💡 Strategy
--------

Real world example
> Consider the example of sorting, we implemented bubble sort but the data started to grow and bubble sort started getting very slow. In order to tackle this we implemented Quick sort. But now although the quick sort algorithm was doing better for large datasets, it was very slow for smaller datasets. In order to handle this we implemented a strategy where for small datasets, bubble sort will be used and for larger, quick sort.

In plain words
> Strategy pattern allows you to switch the algorithm or strategy based upon the situation.

> In computer programming, the strategy pattern (also known as the policy pattern) is a behavioural software design pattern that enables an algorithm's behavior to be selected at runtime.

**Programmatic example**

Translating our example from above. First of all we have our strategy interface and different strategy implementations

```csharp
interface SortStrategy {
    List<int> Sort(List<int> data) ;
}

class BubbleSortStrategy : SortStrategy {
    public List<int> Sort(List<int> data) {
        Console.WriteLine("Sorting using bubble sort");

        // Do sorting
        return data;
    }
}

class QuickSortStrategy : SortStrategy {
    public List<int> Sort(List<int> data) {
        Console.WriteLine("Sorting using quick sort");

        // Do sorting
        return data;
    }
}
```

And then we have our client that is going to use any strategy

```csharp
class Sorter {
    protected SortStrategy sortingTechnique;

    public Sorter(SortStrategy sortingTechnique) {
        this.sortingTechnique = sortingTechnique ;
    }

    public List<int> Sort(List<int> dataSet) {
        return this.sortingTechnique.Sort(dataSet);
    }
}
```
And it can be used as
```csharp
var dataSet = new List<int> { 1, 5, 4, 3, 2, 8 };

var bubbleSorter = new Sorter(new BubbleSortStrategy());
sorter.Sort(dataSet); // Output : Sorting using bubble sort

var quickSorter = new Sorter(new QuickSortStrategy());
sorter.Sort(dataSet); // Output : Sorting using quick sort
```

💢 State
-----
Real world example
> Imagine you are using some drawing application, you choose the paint brush to draw. Now the brush changes its behavior based on the selected color i.e. if you have chosen red color it will draw in red, if blue then it will be in blue etc.  

In plain words
> It lets you change the behavior of a class when the state changes.

> The state pattern is a behavioral software design pattern that implements a state machine in an object-oriented way. With the state pattern, a state machine is implemented by implementing each individual state as a derived class of the state pattern interface, and implementing state transitions by invoking methods defined by the pattern's superclass.
> The state pattern can be interpreted as a strategy pattern which is able to switch the current strategy through invocations of methods defined in the pattern's interface.

**Programmatic example**

Let's take an example of text editor, it lets you change the state of text that is typed i.e. if you have selected bold, it starts writing in bold, if italic then in italics etc.

First of all we have our state interface and some state implementations

```php
interface WritingState {
    public function write(string $words);
}

class UpperCase implements WritingState {
    public function write(string $words) {
        echo strtoupper($words); 
    }
} 

class LowerCase implements WritingState {
    public function write(string $words) {
        echo strtolower($words); 
    }
}

class Default implements WritingState {
    public function write(string $words) {
        echo $words;
    }
}
```
Then we have our editor
```php
class TextEditor {
    protected $state;
    
    public function __construct(WritingState $state) {
        $this->state = $state;
    }
    
    public function setState(WritingState $state) {
        $this->state = $state;
    }
    
    public function type(string $words) {
        $this->state->write($words);
    }
}
```
And then it can be used as
```php
$editor = new TextEditor(new Default());

$editor->type('First line');

$editor->setState(new UpperCaseState());

$editor->type('Second line');
$editor->type('Third line');

$editor->setState(new LowerCaseState());

$editor->type('Fourth line');
$editor->type('Fifth line');

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
```

📒 Template Method
---------------

Real world example
> Suppose we are getting some house built. The steps for building might look like 
> - Prepare the base of house
> - Build the walls
> - Add roof
> - Add other floors
> The order of these steps could never be changed i.e. you can't build the roof before building the walls etc but each of the steps could be modified for example walls can be made of wood or polyester or stone.
  
In plain words
> Template method defines the skeleton of how a certain algorithm could be performed, but defers the implementation of those steps to the children classes.
 
> In software engineering, the template method pattern is a behavioral design pattern that defines the program skeleton of an algorithm in an operation, deferring some steps to subclasses. It lets one redefine certain steps of an algorithm without changing the algorithm's structure.

**Programmatic Example**

Imagine we have a build tool that helps us test, lint, build, generate build reports (i.e. code coverage reports, linting report etc) and deploy our app on the test server.

First of all we have our base class that specifies the skeleton for the build algorithm
```csharp
abstract class Builder 
{    
    // Template method 
    public void Build() 
	{
        this.Test();
        this.Lint();
        this.Assemble();
        this.Deploy();
    }
    
    public abstract void Test();
    public abstract void Lint();
    public abstract void Assemble();
    public abstract void Deploy();
}
```

Then we can have our implementations

```csharp

class AndroidBuilder : Builder 
{    
	public override void Test() => Console.WriteLine("Running android tests");
    
    public override void Lint() => Console.WriteLine("Linting the android code");
    
    public override void Assemble() => Console.WriteLine("Assembling the android build");	
    
    public override void Deploy() => Console.WriteLine("Deploying android build to server");
}


class IosBuilder : Builder 
{    
	public override void Test() =>	Console.WriteLine("Running ios tests");
	
    public override void Lint() =>	Console.WriteLine("Linting the ios code");
    
	public override void Assemble() =>	Console.WriteLine("Assembling the ios build");	
    
    public override void Deploy() =>	Console.WriteLine("Deploying ios build to server");	
}
```
And then it can be used as

```csharp
var androidBuilder = new AndroidBuilder();
androidBuilder.Build();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

var iosBuilder = new IosBuilder();
iosBuilder.Build();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
```


