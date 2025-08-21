 - **A variable whose value is an address of another variable or function**.
- Same as normal variables it has its own type and value, and a memory location where it's bound to.
- Can be used inside functions to access data defined outside. These variables may not be in scope and cannot be accessed by their name.

# Declaring pointers

- variable_type \*pointer_name
The asterisk can be placed also next to the type.

- To avoid having garbage data, representing in this case an address (or "pointing anywhere"), they need to be initialized. They can be initialized to "point nowhere" {nullptr}, address zero, or to variables and functions.

# Accessing variables/pointers addresses

- Unary address operator & to the left of an operand.
- The operand must have an l value, so can't be a constant or an expression evaluating to temporary values.
- All the types of pointers have the same size, since their value is actually an address.
- Typed pointers - the compiler makes sure the address stored in a pointer is of the correct type.
- **What they are pointing to is different to what they are**. Pointer variables are primitive types. In fact, they could be also untyped, not pointing to any specific type of variable.
# Dereferencing a pointer or accessing the data of the pointed variable

- If a pointer has a valid address, the dereferencing operator  \* can be used to access the data.

```
int score {100};
int *score_ptr{&score};
	
*score_ptr = 200;
```

When we dereference a pointer on the left hand of an assignment statement (l-value), this results in the address of the variable the pointer is pointing to (score), storing the value in the right side of the assignment (r-value) to that address.
- **l-value of an assignment statement is an address or location where data is stored.**
- **the \* basically follows the pointer to the variable.**

The . operator has the precedence over the \*, thus parenthesis should be needed when dereferencing pointers.

```
(*vector_ptr).at(0);
```

# Dynamic memory allocation

- *new* keyword to allocate **heap** storage at **runtime**:

```
int *int_ptr {nullptr};

int_ptr = new int; //allocate an integer on the heap, notice it has no name
cout << int_ptr << endl; // the address where int is stored
cout << *int_ptr << endl; // this will be garbage as int wasn't initialized
*int_ptr = 100;
cout << *int_ptr << endl; //100
```

Note that the only way to get to the storage in the heap is via the pointer.

- *delete* to deallocate storage, followed by the pointer's name.
- To deallocate an array, we should use:
```
delete [] array_ptr
```

- A memory leak happens when the pointer to a variable in the heap is lost, thus making that variable inaccessible.
- https://www.geeksforgeeks.org/heap-overflow-stack-overflow/

# Arrays and pointers

- The value of an array name is the address of the first element.
- So if a pointer points to the same data type as the array element, then the pointer and array name are interchangeable. The only difference is that the array name is not a variable and can't be changed.
- Arrays can be dereferenced as the pointers, and will give the value of the first element.
- **C++ doesn't have true arrays, and they are just the address of the first element in a chunk of memory.**
- We can offset a pointer to access different elements of an array, like:

```
(score_ptr + 1) // 4 bytes away (n*sizeof (type))
(score_ptr + 2) // 8 bytes away
```

The address value will increase by 4 (or a different number depending on the machines), because we are adding the size of an integer to the pointer value - the address of a new integer that is 4 bytes away.

```
int array_name[] {1,2,3,4,5};
int *pointer_name {array_name};
```

https://www.digitalocean.com/community/tutorials/find-array-length-in-c-plus-plus
https://faculty.cs.niu.edu/~mcmahon/CS241/Notes/arrays_and_pointers.html#:~:text=Like%20all%20other%20variables%2C%20each,picture%20can%20help%20illustrate%20this.

| Subscript notation  | Offset notation          |
| ------------------- | ------------------------ |
| array_name[index]   | \*(array_name + index)   |
| pointer_name[index] | \*(pointer_name + index) |
Associativity when operator have the same precedence, so right to left:

```
*score_ptr++ //first increment and then dereference

(*score_ptr)++ //first dereference then increment
```
https://www.geeksforgeeks.org/operator-precedence-and-associativity-in-c/?ref=lbp

# Constant

- Pointers to constants:
```
const int *int_ptr; //the pointer can change and point somewhere else, but the data is pointing to no.
```

- Constant pointers:
```
int const *int_ptr; //the pointer cannot change and point somewhere else, but the data is pointing to yes.
```

- Constant pointers to constants:
```
const int const *int_ptr; //the pointer itself and the data cannot change.
```

# Passing pointers to functions

- *Indirection* or de-reference operator: \*

```
void double_data(int *int_ptr);

void double_data(int *int_ptr){
*int_ptr *=2;
}

int main() {
int value {10};
int *int_ptr {nullptr};

double_data (&value);
OR
int_ptr = &value;
double_data (int_ptr);
}
```

https://unrealcommunity.wiki/pointers-and-references-8b09d2

**Never return a pointer to a local variable!!! > Dangling or stray pointers**

# Potential pointer pitfalls

## Uninitialized pointers
```
int *ptr; // pointing anywhere (garbage) and possibly can wipe something important

*ptr = 100; // likely to crash
```

## Dangling or stray pointers

- Pointers pointing to released memory
	- 2 pointers to the same data
	- 1 pointer releases the data with delete
	- The other pointer accesses the released data
- Pointers pointing to invalid memory
	- Returning address of a function local variable that are no longer valid since the function is terminated.
## Not checking if new failed
- If *new* fails an exception is thrown and the program is terminated
- We can use exception handling to catch these issues
- Dereferencing a null pointer will cause a crash

## Memory leaks
- When allocating storage dynamically on the heap, it has no name, and the only way to access it is through the pointer. A typical situation is when a pointer is declared in function and allocating memory but, if the memory is not released with *delete* and the pointer is lost when the function is terminated, there's no way to access again that memory (orphaned or leaked).

https://www.ibm.com/docs/en/aix/7.3?topic=performance-memory-leaking-programs

# References: addendum

- An alias for a variable
- Must be initialized to a variable when declared
- Cannot be null
- Once initialized cannot refer to a different variable
- Can be considered as a constant pointer that is automatically dereferenced when used.

```
vector<string> stooges {"Larry","Moe","Curly"};
    
for (auto str : stooges)
    str = "Funny"; // won't change anything
for (auto &str : stooges)
    str = "Funny";
for (auto const &str : stooges) // const when only displaying
    cout << str << endl;

vector<string> *boogie = &stooges;
string *stooge = &stooges.at(0);
vector<string> *boogie = &stooges;
string *iggy {nullptr}; // 0
string &pop {*stooge}; //pop can be initialized only to a dereferenced pointer

/* the value of the pointed data*/  
cout << *stooge <<endl;

/* the address of the pointed data or value of the pointer*/
cout << stooge << endl;

/* the actual address of the pointer*/
cout << &stooge << endl;

/* as above*/
cout << &stooges.at(0) << endl;

/* the address of the vector*/
cout << &stooges << endl;

/* as above*/
cout << boogie << endl;

/* reference to the pointed data*/
cout << pop << endl; //Larry
```

# L-value and R-value

## L-value
- values that have names and are addressable
- modifiable if not const

## R-value
- Non-addressable and non-assignable
- A value that's not an l-value, duh!
- on the right side of an assignment expression
- a literal
- a temporary which is intended to be non-modifiable
- r-values can be assigned to l-values explicitly

```
int x{100}; // x is an l-value
int y{10}

y = 100;
100 = x; // "100" not an l-value but a r-value (literal), also a crash
x = x+y; // r-value (x+y) (temporary) assigned to l-value x

```

## L-value references

References are l-value references, because we're referencing l-values:
```
int x{100};

int &ref1 = x; //ref1 is a reference to a l-value
ref1 = 100;

int &ref2 = 100; //Error as 100 is an r-value

Pass-by-reference into functions:

int square(int &n){
	return n*n;
}

int num {10};
square(num); // OK since num it's an l-value
square(5); //Error - can't refer to r-value 5

```

# Pointers vs References

## Pass-by-value
- When the function **does not modify** the actual parameter
- The parameter is small, a simple type as int, char or double

## Pass-by-reference with a pointer
- When the function **modifies** the actual parameter
- The parameter is expensive to copy
- It's **ok** for the pointer to be **nullptr**

## Pass-by-reference with a pointer to const
- When the function **does not modify** the actual parameter
- The parameter is expensive to copy
- It's **ok** for the pointer to be **nullptr**

## Pass-by-reference with a const pointer to const
- When the function **does not modify** the actual parameter
- The parameter is expensive to copy
- It's **ok** for the pointer to be **nullptr**
- You don't want to modify the pointer itself

## Pass-by-reference with a reference
- When the function **modifies** the actual parameter
- The parameter is expensive to copy
- The parameter **will never be null**

## Pass-by-reference with a const reference
- When the function **does  not modify** the actual parameter
- The parameter is expensive to copy
- The parameter **will never be null**