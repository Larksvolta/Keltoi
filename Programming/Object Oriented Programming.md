# Introduction

## Classes and objects
- Classes and objects reflect real-world domain entities.
- Allows developers to think at a higher level of abstraction.
- More practical for very large programs.

## Encapsulation
- Objects contain data AND operations that work on that data.
- An extension of abstract data type (ADT)

## Information-hiding
- Certain implementation specific logic can be hidden.

## Inheritance
- New classes can be created from existing classes
- Reusability
- Polymorphic classes

# Classes and objects

## Class
- A blueprint from which objects are created.
- User-defined types
- Has attributes (data) and methods (functions or operations) which can be hidden
- Provides a public interface

## Object
- Created from a class
- Represent a specific instance of a class, but it has its own identity and can use the class methods

```
Account mark_account; // Account is the class, mark an object
```

# Declaring classes and objects

```
// Create classes:

class Player{
// attributes
std::string name; 
// from c++11 the attributes can be initialized in the class
int health;

// methods
void talk(std::string text_to say);
bool is_dead();
};

// Create objects:

Player hero;

Player *enemy = new Player(); //dynamic creation of an object
delete enemy;

// Arrays or vectors of objects can be created, with the class as as a type:

Account frank_account;
Account jim_account;

Account accounts[] {frank_account, jim_account};

std::vector<Account> accounts1 {frank_account};
accounts1.push_back(jim_account);
```

# Accessing class members

- Class attributes and methods can be accessed
- An object is needed to access instance variables
- Class members we hide in the class can't be accessed by the objects

## With an object

- We can use the dot operator:
```
Account frank_account;

frank_account.balance;
frank_account.deposit(1000.00);
```

## With a pointer to an object

- Dereference the pointer and then use the dot operator, since the latter has higher precedence:

```
Account *frank_account = new Account();

(*frank_account).balance;
(*frank_account).deposit(1000.00);
```

- Member of pointer operator (->):
```
Account *frank_account = new Account();

frank_account->balance;
frank_account->deposit(1000.00);
```

# Access Modifiers

- public - accessible everywhere
- private - accessible only by members or friends of the class
- protected - used with inheritance

If  we set  an attribute as private and set a public function with that attribute as argument, the function will be able to access the attribute. This is because private members can be accessed by other members of their class.

Hide certain parts of class declaration is a good way to control its members and pinpoint the cause of bugs. If an attribute is public it would be difficult to detect what could have modified it, but if we set it to private and only a public method can change it, it's easier too find the root of the problem.

# Member methods

Generally only the member prototypes are within the class declaration. If also the definitions are inside, the methods are declared **inline**.
In the definition though we need to add the class name as a prefix  followed by the scope resolution operator  (double colon) to the method names:

```
void Account::set_balance(double bal)
{
	balance = bal;
}
```

Even better if the class declarations are in a header file .h and the definitions in a separate .cpp file.

If the file is included by more than one cpp file, the compiler will see the declaration for the class more than once and will give an error. 

An **include guard** will be needed to solve the issue.

```
#ifndef _ACCOUNT_H_ // any name is fine, usually is the .h file name
#define _ACCOUNT_H_

// Account class declaration

#endif
```

Many compilers support the \#pragma once compiler directive.

The .cpp file would be like:

```
#include "Account.h"

// method definitions

```

The angled brackets include <> is used for **system header files** that the compiler knows where to find them. The " " is used for **project local files**.

Better not to use namespaces, like std, in the class header files.

## Exercise example
```
#ifndef __DOG_H__
#define __DOG_H__
#include <string>
using namespace std;

class Dog
{
	private:
	    string name;
	    int age;
	public:
	
	    string get_name() {return name; }
	    void set_name(string n) {name = n; }
	    int get_age() {return age; }
	    void set_age(int a) { age = a;}

// this is a trick - there's no need to call the get_age() method in order to access the private attribute "age" since we are inside the class declaration.

    int get_human_years() {
        return age * 7; 
    }
    
    string speak() {
        return string("Woof");
    }
};
#endif 
```

# Constructors and destructors

## Constructors
- Special member method
- Invoked during object creation
- Useful for initialization
- Same name as the class
- No return type specified
- Can be overloaded

## Destructors
- Special member method
- Same name as the class preceded by a ~
- Invoked automatically when an object is destroyed
- No return type or parameters
- Only 1 destructor per class - can't be overloaded
- Useful to release memory and other resources

# Constructor initialization lists

Member values set in the constructor body are not really initialized,  when the body gets executed the attributes have already been created. Thus, they are actually assignments.

- With the lists the data members are initialized as the object is created
- The list immediately follows the parameter list
- The initialization order is the order of declaration in the class
- More efficient since it's true initialization

```
Player::Player()
	: name{"None"}, health{0}, xp {0} {
	}

Player::Player(std::string name_val)
	: name{name_val}, health{0}, xp {0} {
	}
```

# Delegating constructors

```
Player::Player(std::string name_val, int health_val, int xp_val)
	: name{name_val}, health{health_val}, xp {xp_val} {
	}

Player::Player()
	: Player {name_val, 0,0 } {
	}
```

# Default constructor parameters

- Can often simplify  our code and reduce the number of overloaded constructors
- Same rules as per non-member functions

```
class Player{
private:
	std::string name; 
	int health;
	int xp;

public:
		Player(std::string name_val = "None", int health_val = 0,
		int xp_val = 0);
		Player() {}; // Default constructor
}; // don't forget the semicolon at the end of a class declaration!

// No need to repeat the default values in the implementation, just as for the non-member functions

Player::Player(std::string name_val, int health_val, int xp_val)
	: name {name_val}, health {health_val}, xp {xp_val} {
	}

// When creating objects we can use for instance a two arguments constructor

Player emtpy; // the compiler won't know which constructor to use,
the default or the overloaded one (ambiguous constructor)
Player hero {"Hero", 100};

```

# Copy constructor

When objects are copied C++ will create a new object from an existing one.

## A copy of an object is made when
- Passing an object by value as a parameter
- returning an object from a function by value
- constructing one object based on another from the same class

C++ will provide a compiler-defined copy constructor if the user does not.
**Default member-wise copy**: it will copy the values of each data member to the new object.
**Shallow copy**: when using raw pointers, only the pointer will be copied, not the data is pointing to.

## Best practices
- Provide a copy constructor when your class has raw pointer members.
- Provide the copy constructor with a **const reference** parameter.
- Use STL classes, that already provide copy constructors, as member attributes.
- Avoid using raw pointer data members if possible, or use smart pointers.

```
// Copy Constructor declaration - it must be by reference, otherwise it will recursively make other copies. Const because we are not modifying the object.

Type::Type(const Type &source);

// Copy Constructor implementation

Type::Type (const Type &source) 
	: Type {source.x, source.y, source.z} {}

Type::Type (const Type &source) 
	: x{source.x}, y{source.y}, z{source.z} {}
```

# Shallow vs Deep copy

https://www.geeksforgeeks.org/shallow-copy-and-deep-copy-in-c/

```
// Deep copy constructor

Type::Type (const Type &source) 
	: Type {*source.x} {}
```

# Move constructor

```
int total {0};
total = 100 + 200;
```
- When the sum above gets evaluated, 300 is stored in an unnamed temp value. Since this value is not addressable it's an rvalue.
- 300 is stored in the variable *total*.
- the temp value is discarded.

Same concept for the objects, only that constantly copying temporary values can be cumbersome. **Especially when a deep copy is needed with raw pointers**.
As the name suggests, move constructors move an object rather than copying it.

## R-value references
- Used in moving semantics and perfect forwarding
- Used by move constructors and move assignment operators
- R-value reference operator (&&)

```
int x {100};
int &l_ref = x; // l_value reference
l_ref = 10; // change x to 10

int &&r_ref = 200; // r-value reference
r_ref = 300; // change r-ref to 300

int &&x_ref = x; //Compiler error

// l-value reference parameters
int x {100};

void func(int &num);

func(x); //OK
func(200); //Error: cannot bind non-const lvalue reference of type 'int&' to an rvalue of type 'int'

// r-value reference parameters
void func(int &&num);

func(200); //OK
func(x); //Error: cannot bind rvalue reference of type 'int&&' to lvalue of type 'int'

These functions can be overloaded too.
```

## Move constructor syntax

```
// Declaration
Type::Type (&&source); // there's no const because we need to modify the object in order to modify its pointer

// Implementation
Type::Type (&&source)
 : data{source.data} {
 source.data = nullptr; // without this it would be a shallow copy
 }

// 'Steals' the data and nulls out the source pointer
```

# This

- Contains the address of the object - its pointer
- Can only be used in the scope of the classes
- Used for all member access. In C++ is possible to use the member names directly, but behind the scenes this will be used
- Can be dereferenced (\*this) to work with directly with the object
- 
```
// Can be used to disambiguate identifier use
// note the pointer operator ->

void Account::set_balance(double balance) {
this->balance = balance;
}

// Or to determine if two ojects are the same

in Account::compare_balance(const Account &other) {
if (this == &other)
	std:: cout << "The same objects" << std::endl;
}
```

# Using const with classes

- Const correctness - all the getters should be const.

```
string get_name() const {
return name;
}

const Player villain {"Villain", 100, 10};

```

If an object is const, even if a method does not modify the object, the compiler will give an error the same. To avoid this, a const is needed after the parameter list or right before the semicolon in a prototype.

# Static class members

- Static class data members belong only to the class, not the objects
- For class-wide information
- https://youtu.be/6G2NbfadwRA?si=SjoefHMF4nAq7jek&t=221
- https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members
- https://www.geeksforgeeks.org/c-sharp-static-class/
## From ChatGPT
- Hi Copilot, in the following C# program "Program.cs": 

```
using System;

// The namespace makes it easier to organize our application and
// avoid name collisions with other code

namespace HelloWorld

{
    // In .NET our program is contained within its own class
    class Program
    {
        // The main function is the entry point into our app
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            Console.WriteLine("What is your name?");
            // create a string variable and ask the user for some input
            string str = Console.ReadLine();
            Console.WriteLine("Why, hello there " + str);
        }
    }
}
```

I have this statement Console.WriteLine("Hello World!");, where Console is a static class from the System namespace. Now, I've been told that in the scope of the Program.cs Console would be an object and not a class, but I'm not so sure, also because if Console is static it literally means that it cannot be instantiated.
- You’re correct! In your C# program, `Console` is indeed a static class from the `System` namespace. Here’s a bit more detail to clarify:

1. **Static Class**: The `Console` class is defined as static, which means it cannot be instantiated. You cannot create an object of the `Console` class. [Instead, you access its members directly through the class itself, like `Console.WriteLine()`](https://learn.microsoft.com/en-us/dotnet/api/system.console?view=net-8.0)[1](https://learn.microsoft.com/en-us/dotnet/api/system.console?view=net-8.0).
    
2. **Class vs. Object**: A class is a blueprint for creating objects, but a static class like `Console` is a special type of class that cannot be instantiated. Therefore, in the scope of your `Program.cs`, `Console` is not an object but a static class. [This allows you to call its methods without creating an instance of the class](https://learn.microsoft.com/en-us/dotnet/api/system.console?view=net-8.0)[1](https://learn.microsoft.com/en-us/dotnet/api/system.console?view=net-8.0).
    
So, the statement `Console.WriteLine("Hello World!");` is calling the `WriteLine` method on the static `Console` class, not on an instance of `Console`.

# Struct or class

## Struct
- For passive objects with public access
- Don't declare methods in structs

## Class
- For active objects with private access
- Implement getters/setters as needed
- Implement member methods as needed