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

## Memeber initializer Lists

## Strutc in C++

## Resource Aquisition in Initialization RAII

#3 RUl of Five - redcucing Memory allocation

## Friend functions

## explicit cnstructors and list initizliation to avoid conversions

# Inheritances

##