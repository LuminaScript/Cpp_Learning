# Inheritance
## Basics
- Derived class inherit all Based class members
- Derived class can have self-defined members
    ```cpp
    #include <iostream>
    using namespace std;

    // Base class Dog
    class Dog {
    public:
        Dog() {}

        void Bark() {
            cout << "bark!" << endl;
        }

        void Walk() {
            x += 1;
            y += 1;
        }

        float x, y;
    };

    // Derived class Golden, inheriting from Dog
    class Golden : public Dog {
    public:
        Golden() {
            m_sticksRetrieved = 0;
        }

        void Retrieve() {
            cout << "retrieving a stick\n";
            m_sticksRetrieved++;
        }

        float m_sticksRetrieved;
    };

    int main() {
        Golden goldenDog;
        goldenDog.Bark();       // Call inherited method from Dog
        goldenDog.Retrieve();   // Call derived class method

        return 0;
    }
    // sizeof(Dog) : 8
    // sizeof(Golden): 12
    ```
    
    
## Access Modifier
### Member scope specifer
```cpp 
private: 
 int private_member;
public:
 int public_member;
protected:
 int protected_member:
```
- public: anywhere in the program
- private: only within the base class
- protected: only within the base & derived class

### Inheritance:
```cpp 
class DerivedA : public Base {};
class DerivedB : protected Base {};
class DerivedC : private Base {};
```
| **Inheritance Type** | **Public Members** (Base) | **Protected Members** (Base) | **Private Members** (Base) |
|----------------------|--------------------------|-----------------------------|----------------------------|
| **Public Inheritance**   | Public                     | Protected                   | Inaccessible                |
| **Protected Inheritance**| Protected                  | Protected                   | Inaccessible                |
| **Private Inheritance**  | Private                    | Private                     | Inaccessible                |


## Calling Parent Constructor
Reference: https://hatchjs.com/call-parent-constructor-c/

### Ways to call a parent constructor in C++
- By default, derived class calls base class constructor
- List initialization
```cpp 
class Animal {
public:
    Animal(int age) { cout << "Animal of age: " << age << endl; }
};

class Dog : public Animal {
public:
    Dog(int age) : Animal(age) { // list initialization here
        cout << "Dog created!" << endl; 
    }
};

int main() {
    Dog d(5);
    return 0;
}

```

### The constructor chaining order
 Derived class constructor -> Base class constructor -> Base class destructor -> Derived class destructor


## Call member function from derived class

### Example without `virtual` (Incorrect Behavior):
- **Issue:** Without `virtual`, the base class function is called, even though the object is of type `Derived`.
```cpp
#include <iostream>
using namespace std;

class Base {
public:
    void MemberFunc() {
        cout << "Base::MemberFunc()\n";
    }
};

class Derived : public Base {
public:
    void MemberFunc() {
        cout << "Derived::MemberFunc()\n";
    }
};

int main() {
    Base* instance = new Derived();
    instance->MemberFunc();  // Calls Base::MemberFunc()
    delete instance;
    return 0;
}
```


### Keyword `virtual` and dynamic dispatch:
- Dynamic Dispatch ensures that the correct function is called at runtime based on the actual object type, not the pointer type.
- Use the virtual keyword in the base class to enable function overriding in the derived class.
- Use override in the derived class to indicate that you intend to override a base class method.
```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void MemberFunc() {
        cout << "Base::MemberFunc()\n";
    }
};

class Derived : public Base {
public:
    void MemberFunc() override { // can do without override
        cout << "Derived::MemberFunc()\n";
    }
};

int main() {
    Base* instance = new Derived(); // create a runtime Derived class
    instance->MemberFunc();  // Calls Derived::MemberFunc()
    instance->Base::Memberfun(); // explicitly calling base base
    
    Base* base_instance = new Based(); // use virtual 
    base_instance->MemberFunc(); // base::memberfunc()
    
    delete instance;
    delete base_instance;
    return 0;
}
```

### virtual base class destructor

-  the destructor is not virtual, only the base class destructor will be called, potentially causing memory leaks or resource leaks by skipping the derived class destructor.
```cpp 
#include <iostream>
using namespace std;

class Base {
public:
    Base() { cout << "Base Constructor\n"; }
    virtual ~Base() { cout << "Base Destructor\n"; }  // Virtual destructor
};

class Derived : public Base {
public:
    Derived() { cout << "Derived Constructor\n"; }
    ~Derived() override { cout << "Derived Destructor\n"; }
};

int main() {
    Base* instance = new Derived();
    delete instance;  // Calls both Derived and Base destructors
    return 0;
}

```

#### Illustration of vtable Usage
The given code demonstrates dynamic dispatch with polymorphism through the use of a vtable. Below is a detailed breakdown of how the vtable works at runtime.
```cpp 
int main() {
    // Base pointer pointing to a Derived object
    Base* instance = new Derived();
    instance->MemberFunc();  // Calls Derived::MemberFunc() via vtable
    delete instance;  // Properly calls both Derived and Base destructors

    // Base pointer pointing to a Base object
    instance = new Base();
    instance->MemberFunc();  // Calls Base::MemberFunc() directly
    delete instance;

    return 0;
}
```
| **Class** | **vtable** (function pointers) |
|-----------|--------------------------------|
| **Base**  | `Base::MemberFunc()`, `Base::~Base()` |
| **Derived** | `Derived::MemberFunc()`, `Derived::~Derived()` |




## Abstract Class
An **abstract class** in C++ defines an abstract type that **cannot be instantiated** directly but can serve as a **base class** for other derived classes.
### Syntax for Pure Virtual Function

```cpp
struct Abstract {
    virtual void f() = 0;  // pure virtual function
};
```
### Key Points about Abstract Classes in C++
- Must contain at least **one pure virtual function**.
- **Cannot instantiate** objects directly, only as part of derived classes.
- Can **use pointers or references** to interact with derived objects.
- **Virtual destructors** can be pure virtual but must be defined if used.
- **Derived classes** must implement all pure virtual functions to be **instantiable**.
- **Avoid calling virtual functions** in constructors or destructors to prevent **undefined behavior**. 
    ```cpp
    struct Shape {
        virtual void draw() = 0;  // pure virtual function
    };

    struct Circle : Shape {
        void draw() override {
            std::cout << "Drawing Circle" << std::endl;
        }
    };

    int main() {
        // Shape shape;       // Error: abstract class cannot be instantiated
        Circle c;             // OK
        Shape& s = c;         // OK to reference abstract class
        s.draw();             // Virtual dispatch to Circle::draw()
        return 0;
    }
    ```