Refactor using while or do-while loops in the Inventory.

Use a "for loop" when you know how many times you need to loop. It is more explicit to read and easier to understand, test, and debug.

# Pass-by-value

- When you pass data into a function, it is passed-by-value.
- A **copy** of the data is passed to the function.
- Whatever changes you make to the parameter in the function doesn't affect the argument that was passed in, because inside the function a different element will be used.

Formal vs Actual parameter
- **Formal** parameters - in the **header**
- **Actual** parameters - in the **function call,** the **arguments** that are passed to the function.

https://www.ibm.com/docs/en/zos/3.1.0?topic=calls-pass-by-value
https://byjus.com/gate/difference-between-argument-and-parameter-in-c-and-c-plus-plus/#:~:text=1-,The%20values%20that%20are%20declared%20within%20a%20function%20when%20the,declared%20are%20known%20as%20parameters.
https://blog.kotlin-academy.com/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929

# Polymorphism

Many forms for the same concept.

Return type is not considered when overloading functions.

# Passing Arrays

void print_array(int numbers[]);

- The array **elements are not copied**.
- The array **address is copied**, since the name evaluates to the location in memory of the first element.
- Therefore, the function above doesn't know how many elements are in the array, as it only knows the location of the first one (the name of the array).
- Since we are only passing the location of the array, the function can modify its values. A solution could be to set the function parameters as **const**.

# Pass by Reference

- Reference parameters - to tell the compiler to feed a function with a reference to the actual parameter  (the location of the actual parameter).
- The **formal parameter** will now be an alias for the **actual parameter**.
- Useful not only when a parameter needs to be modified by a function, but also to save resources that would be wasted passing by value. For instance when passing a large  vector (or arrays?) to a function, it can be cumbersome to make a copy of each value. And a const can be added to the parameter to avoid modifying its value inside the function (not needed in the function call).

https://www.ibm.com/docs/en/zos/3.1.0?topic=calls-pass-by-reference-c-only

```
void print (vector <int> &v)
{
 for (auto num: v)
	 cout << num << endl;
}
```

# Scope

- Scope rules to determine where an identifier can be used.
- Static or lexical scoping (instead of dynamic... Java?), the scope is determined the same way you read a program
- Local (also called Block) or Global scope
- Block statements {} create new levels of scope and they can be used alone.

## Local scope
- Identifiers declared within a block, they are visible only inside it.
- Function local variables have only the lifetime of the function and thus aren't preserved between function calls.
- In nested blocks, the inner one can see the identifiers in the outer block, but not viceversa.

### Static local variables
- Declared with static qualifier
- Has the lifetime of a program, but only visible in the statements of the function body
- The value is preserved between calls
- Only initialized the first time a function is called (if no initializer is provided they're set to 0)
- Acts like a global variable as retains its value, but it's local.

## Global scope

- Identifier declared outside any function/class
- Visible in all parts of the program following the declaration
- Global constants are OK, but not global variables (because any piece of code can change them? Yes, bugs with static variables are easier to trace)

# Function Calls

![[Function Calls]]

https://www.geeksforgeeks.org/memory-layout-of-c-program/


# Recursive functions

```
unsigned long long factorial(unsigned long long n)
if (n == 0)
	return 1;
return n * factorial(n-1);
```


# Boss/Worker 

- Write your code to the function specification
- Understand what the function does
- Understand what information the function needs
- Understand what the function returns
- Understand any errors the function may produce
- Understand any performance constraints