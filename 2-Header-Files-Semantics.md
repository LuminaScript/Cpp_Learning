# Classes

## Basics
- **User-Defined Type**: Consists of data members, member functions, and nested types.
- **Interface Hiding**:Controls access to members using access specifiers:
  - **`public`**: Accessible from anywhere, including external functions or classes.
  - **`private`**: Only accessible within the class, not by derived classes.
  - **`protected`**: Accessible within the class and its derived classes, but not from outside.

    
- **scope operator**: extend class definition access outside class
  ```cpp
    // Student.h
    class Student {
    private:
        Student(); // Declaration of the constructor
    };
  ```
   ```cpp
    // Student.cpp
    #include "Student.h"

    Student::Student() {
        // Constructor implementation
    }
   ```
   

## Constructor, Destructor, Copy Constructor

- Default constructor , default deconstructor, default copy constructor
```cpp
// Student.hpp ------------------------------------------------------------------
#ifndef STUDENT_HPP
#define STUDENT_HPP

#include <string>

class Student {
public:
    // Constructor without parameters
    Student();
    // Constructor with a string parameter
    Student(std::string name);
    // Destructor
    ~Student();

    // Print the student's name
    void printName();

public:
    std::string m_name;
};

#endif

// student.cpp ----------------------------------------------------------------
#include "Student.hpp"
#include <iostream>

// Constructor without parameters
Student::Student() {
    m_name = "no name";
    std::cout << "Constructor:" << std::endl;
    std::cout << "m_name is: " << m_name << std::endl;
}

// Constructor with a string parameter
Student::Student(std::string name) {
    m_name = name;
    std::cout << "Constructor:" << std::endl;
    std::cout << "m_name is: " << m_name << std::endl;
}

// Destructor
Student::~Student() {
    std::cout << "Destructor: " << m_name << std::endl;
}

// main.cpp -------------------------------------------------------------------
#include "Student.hpp"

int main() {
    Student mike; // initialize m_name to empty string if no construtcor is provided
    Student mike("mike"); // list initizliation
    Student mike2 = mike; // copy constructor invoked 
    mike.m_name = "mike s";
    mike.printName();

    return 0;
}
```

### User-defined Copy Constructor and Copy Assignment Operator
- Default construtco and copy assignmnet is shallow copy:
- trivial copy (copy constructor);
if a class member is int* data, default construtco is a shallow construtoc which will just copy the pointer address to new  data, which will cause double free detetced during deconstrutcor. 

- deep copy: dynamically allocated memory
```cpp 
#include <iostream>
using namespace std;

class Array {
public:
    // (1) Constructor
    Array() {
        cout << "Constructor" << endl;
        data = new int[10];
        for (int i = 0; i < 10; i++) {
            data[i] = i * i;
        }
    }

    // (2) Destructor
    ~Array() {
        cout << "Deconstructor" << endl;
        delete[] data;
    }

    // (3.1) Copy constructor: deep copy
    // const: does not want to change original value
    // &: pass by reference
    Array(const Array& rhs) {
        cout << "Copy Constructor" << endl;
        data = new int[10];
        for(int i = 0; i < 10; i++) {
            data[i] = rhs.data[i];
        }

    }
    
    // (3.2) Copy assignment: object is already constructed, we are just making a copy later
    Array& operator=(const Array& rhs) {
        cout << "Copy assignment" << endl;
        if (&rhs == this) { // avoid making copy of itself
            return *this; // return the object itself, this is a pointer
        }
        delete[] data; // deallocate lhs memory
        data = new int[10];// allocate rhs memory
        for (int i = 0; i < 10; i++) {
            data[i] = rhs.data[i];
        }
        return *this;
        
    }

    void PrintingData() {
        for (int i = 0; i < 10; i++) {
            cout << data[i] << endl;
        }
    }

    void SetData(int index, int value) {
        data[index] = value;
    }

private:
    int* data;
};

int main() {
    Array arr;
    arr.SetData(5, 42);
    Array arr2; // copy constructor
    arr2 = arr; // copy assignment

    arr.PrintingData();
    arr2.PrintingData();

    return 0;
}
```


## C++ Rule of Three  
cppreference: https://en.cppreference.com/w/cpp/language/rule_of_three

The **Rule of Three** states: if you define **one** of these, you should define **all three**:  
1. **Destructor**: Cleans up resources owned by the class.  
2. **Copy Constructor**: Creates a copy of an object.  
3. **Copy Assignment Operator**: Assigns one object to another.

### Why Non-default Constructor Isn't Included  
 It only controls how an object is initialized and does not manage or copy resources like the other three special member functions (destructor, copy constructor, and copy assignment operator). 

For example, we could define a non-default constructor that initializes a class with a single member, such as `int a = 1;`. Since `int` is a trivial, copyable type, we wouldn’t need to define a custom copy constructor, copy assignment operator, or destructor in this case.

### Law of The Big Two 
An amendment to this rule is that if the class is designed in such a way that resource acquisition is initialization (RAII) is used for all its (nontrivial) members, the destructor may be left undefined (also known as The Law of The Big Two). A ready-to-go example of this approach is the use of smart pointers instead of plain ones
*Reference: [wiki page](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming)#:~:text=The%20term%20%22Rule%20of%20three%22%20was%20coined%20by,known%20as%20The%20Law%20of%20The%20Big%20Two%29.)*
## Avoiding copie
### 1. Delete Copy Constructor  
To prevent unintended copying, delete the copy constructor or declare it private:

```cpp
class Array {
public:
    Array() = default;
    Array(const Array& rhs) = delete;  // Delete copy constructor
```
```cpp
private:  
    Array(const Array& rhs);  // Pre-C++11: Restrict copying
};
```
### 2. Use Copy Constructor Wisely
Prefer copy constructors over copy assignments to minimize redundant operations:
```cpp
Array a(b);  // Direct copy construction (efficient)
Array c;
c = b;  // Avoid if possible; copy assignment can introduce overhead.
```

### 3. Pass by reference

## Operator overloading
cpp.conference reference: 

### binary operator overload semantic: +
```cpp 
Complex operator+(Complex const& obj) // does not return a reference
{
    Complex res;
    res.real = real + obj.real;
    res.imag = imag + obj.imag;
    return res;
}
```
### prefix / postfix increment
```cpp 
// prefix increment
X& operator++()
{
    // actual increment takes place here
    return *this; // return new value by reference
}

// postfix increment
X operator++(int)
{
    X old = *this; // copy old value
    operator++();  // prefix increment
    return old;    // return old value
}
```
### string operator (defined outside of class)
```cpp 
std::ostream& operator<<(std::ostream& os, const T& obj)
{
    // write obj to stream
    return os;
}
 
std::istream& operator>>(std::istream& is, T& obj)
{
    // read obj from stream
    if (/* T could not be constructed */)
        is.setstate(std::ios::failbit);
    return is;
}
```
### Operator ==
in class operator:
```cpp 
bool operator==(const vector3f& rhs) {
    if (x == rhs.x && y == rhs.y && z == rhs.z) return true;
    return false;
}

```
free function:
```cpp 
bool operator==(const vector3f& lhs, const Vector3f& rhs) {
    if (lhs.x == rhs.x && lhs.y == rhs.y && lhs.x == rhs.y) return true;
    return false;
    
}
```

## Memeber initializer Lists
Vs assignmengt: This approach improves performance and ensures proper initialization order.

### Example

```cpp
// Declaration
class Vector3f {
public:
    Vector3f(); // Constructor declaration

private:
    float x, y, z; // Member variables
};

// Definition
Vector3f::Vector3f() : x(1.0f), y(2.0f), z(3.0f) {
    // Rule of thumb: Initialize members in the order of their declaration
}
```
- Initialization Order Matters: Members are initialized in the order they are declared in the class, not the order in the initializer list.

## Struct in C++
struct vs class
- class all member is by default private


## Resource Aquisition in Initialization RAII

```cpp 
#include <iostream>
#include <stdexcept>

class Collection {
public:
    // Constructor
    Collection() {
        data = new int[10];  // Allocate memory for 10 integers
        try {
            throw 20;  // Simulate an exception
        } catch (int e) {
            std::cout << "Exception caught: " << e << std::endl;
        }
    }

    // Destructor
    ~Collection() {
        std::cout << "Destructor is called" << std::endl;
        delete[] data;  // Properly free allocated memory
    }

    // Overloaded operator[] for non-const access
    int& operator[](std::size_t idx) {
        return data[idx];
    }

    // Overloaded operator[] for const access
    const int& operator[](std::size_t idx) const {
        return data[idx];
    }

private:
    int* data;  // Pointer to dynamically allocated array
};

int main() {
    Collection myCollection;  // Create a Collection object
    myCollection[0] = 7;  // Modify the first element
    std::cout << myCollection[0] << std::endl;  // Output the first element

    // Program ends, destructor will be called automatically.
    return 0;
}

```
### RAII in Action:

The constructor acquires a resource (dynamic memory) by allocating an array using new int[10].
The destructor ensures the proper release of this resource by calling delete[] data when the object goes out of scope, preventing memory leaks.

### Other Common RAII Use Cases:
- File Handling: Automatically close files when the object managing them is destroyed.
- Locks/Mutexes: Acquire a lock in the constructor and release it in the destructor to ensure thread-safety.
- Memory Management: Manage dynamically allocated memory, as shown in this example, to avoid leaks and dangling pointers.
- Sockets/Connections: Ensure network connections are properly opened and closed, even in case of exceptions.

## RULE of Five - redcucing Memory allocation

c++17 modern feature

std::move called move assignment/constructor
```cpp 
// IntArray.hpp
#pragma once
#include <iostream>
#include <string>

// Class declaration
class IntArray {
public:
    // Constructor
    explicit IntArray(std::string name);

    // Destructor
    ~IntArray();

    // Copy constructor
    IntArray(const IntArray& rhs);

    // Copy assignment operator
    IntArray& operator=(const IntArray& rhs);

    // Move constructor
    IntArray(IntArray&& source) noexcept;

    // Move assignment operator
    IntArray& operator=(IntArray&& source) noexcept;

private:
    std::string m_name;
    int* m_data;
};

// IntArray.cpp
#include "IntArray.hpp"

// Constructor
IntArray::IntArray(std::string name) 
    : m_name(std::move(name)), m_data(nullptr) {}

// Destructor
IntArray::~IntArray() {
    delete m_data;
    std::cout << m_name << " destroyed" << std::endl;
}

// Copy constructor
IntArray::IntArray(const IntArray& rhs) 
    : m_name(rhs.m_name), m_data(new int(*(rhs.m_data))) {}

// Copy assignment operator
IntArray& IntArray::operator=(const IntArray& rhs) {
    if (this != &rhs) {
        delete m_data;
        m_name = rhs.m_name;
        m_data = new int(*(rhs.m_data));
    }
    return *this;
}

// Move constructor
IntArray::IntArray(IntArray&& source) noexcept 
    : m_name(std::move(source.m_name)), m_data(source.m_data) {
    source.m_data = nullptr;
    std::cout << m_name << " was move constructed" << std::endl;
}

// Move assignment operator
IntArray& IntArray::operator=(IntArray&& source) noexcept {// the function does not throw
    if (this != &source) {
        delete m_data;
        m_name = std::move(source.m_name);
        m_data = source.m_data;
        source.m_data = nullptr;
        std::cout << m_name << " used move assignment" << std::endl;
    }
    return *this;
}

// main.cpp
#include <iostream>
#include <string>
#include <vector>
#include "IntArray.hpp"

IntArray foo() {
    IntArray result("foo() created array");
    return result; // Move constructor is invoked due to return value optimization (RVO)
}

int main() {
    std::vector<IntArray> myArrays;
    myArrays.reserve(10);  // Avoids reallocation during push_back

    for (int i = 0; i < 10; ++i) {
        IntArray temp(std::to_string(i));
        myArrays.push_back(std::move(temp));  // Move constructor is called
    }

    return 0;
}
```
The move constructor is called:
- initialization: T a = std::move(b); or T a(std::move(b));, where b is of type T;
- function argument passing: f(std::move(a));, where a is of type T and f is void f(T t);
- function return: return a; inside a function such as T f(), where a is of type T which has a move constructor.

Reference: https://en.cppreference.com/w/cpp/language/move_constructor 
## Friend functions
The friend declaration appears in a class body and grants a function or another class access to private and protected members of the class where the friend declaration appears.
### 1) Designates a function or several functions as friends of this class:
```cpp
class Y
{
    int data; // private member
 
    // the non-member function operator<< will have access to Y's private members
    friend std::ostream& operator<<(std::ostream& out, const Y& o);
    friend char* X::foo(int); // members of other classes can be friends too
    friend X::X(char), X::~X(); // constructors and destructors can be friends
};
 
// friend declaration does not declare a member function
// this operator<< still needs to be defined, as a non-member
std::ostream& operator<<(std::ostream& out, const Y& y)
{
    return out << y.data; // can access private member Y::data
}
```

### 2) Defines & friends a non-member function
```cpp 
class X
{
    int a;
 
    friend void friend_set(X& p, int i)
    {
        p.a = i; // this is a non-member function
    }
public:
    void member_set(int i)
    {
        a = i; // this is a member function
    }
};
```

### 3) Designates class(s) as a friend of this class. 

```cpp 
class B {
friend class A; // all members in class A has acess to all members in class B
friend C; // only spefici functions (like construtcors or operators inside C are friends of B)
friend class A, C; // supported in c++ 26
};
```
## Initialization
1. Default Initialization
If an object is default-initialized, built-in types (like int) will not be initialized to any value, leaving them with indeterminate values unless it's part of a class with a constructor.
Example:
```cpp
int x;  // Default-initialized, contains garbage value
class A {
public:
    int a;
};
A obj;  // obj.a is default-initialized with garbage value
```
2. Value Initialization
Initializes the object with zero or default values. Works for both built-in and user-defined types.
Example:
```cpp
int x{};  // Value-initialized to 0
class A {
public:
    int a;
};
A obj{};  // obj.a is value-initialized to 0
```
3. Direct Initialization
Calls the constructor directly with the given arguments.
Example:
```cpp
class A {
public:
    A(int x) { std::cout << "Value: " << x << std::endl; }
};
A obj(10);  // Direct initialization
```
4. Copy Initialization
Used when an object is initialized by assigning a value or another object. In some cases, it can invoke copy or move constructors.
Example:
```cpp
class A {
public:
    A(int x) { std::cout << "Constructor called with " << x << std::endl; }
};
A obj = 5;  // Copy initialization
```
Note: This can trigger implicit type conversions unless the constructor is marked as explicit.

5. List Initialization (C++11 and later)
Uses curly braces {} to initialize an object, supporting uniform initialization.
Example:
```cpp
int x{10};  // List initialization
class A {
public:
    A(int x, int y) { std::cout << "x: " << x << ", y: " << y << std::endl; }
};
A obj{5, 6};  // List initialization
```
6. Aggregate Initialization
Direct initialization of aggregate types like structs and arrays using brace-enclosed lists.
Example:
```cpp
struct Point {
    int x, y;
};
Point p = {10, 20};  // Aggregate initialization
int arr[3] = {1, 2, 3};  // Array initialization
```
7. Reference Initialization
A reference must be initialized at the time of declaration.
Example:
```cpp
int x = 10;
int& ref = x;  // Reference initialization
```
8. Copy Elision
Copy elision is an optimization where the compiler omits the creation of temporary objects, avoiding unnecessary copies or moves.
Example:
```cpp
class A {
public:
    A() { std::cout << "A constructor\n"; }
    A(const A&) { std::cout << "A copy constructor\n"; }
};
A createA() {
    return A();  // Copy elision in effect (C++17 guarantees it)
}
int main() {
    A obj = createA();  // Copy constructor may not be called
}
```
## explicit cnstructors and list initizliation to avoid conversions
### Explicit Constructor: 
> Specifies that a constructor or conversion function(since C++11)or deduction guide(since C++17) is explicit, that is, it cannot be used for implicit conversions and copy-initialization.

```cpp 
#include <iostream>
#include <string>

class udt {
public:
    explicit udt(int);  // Explicit constructor

private:
    int m_variable;
};

// Constructor definition
udt::udt(int i) {
    m_variable = i;

    std::cout << "m_variable: " << m_variable << std::endl;
}

int main() {
    // Attempt to implicitly convert float to udt object
    udt u1 = 500;  // Error due to explicit constructor
    udt ul{500}; // okay
    udt ul{300.0f}; // not okay

    return 0;
}
```