## Move Semantics
### Basics:
`std::move`: stealing/transfering the resources from an object (avoid making copies)
- **deep copy**: copy assignment (copy resources from s1 to s2)
    ```cpp
    std::string s1 /*lvalue*/= "HelloWorld";
    std::string s2 = s1; // copy assignment: treat s1 as lvalue
    ```
- **shallow copy**: move assignment (transfer resources from s1 to s2)
    ```cpp
    std::string s1 /*lvalue*/= "HelloWorld";
    std::string s2 = std::move(s1); // move assignment: treat s1 as a rvalue
    ```
    ```cpp
    s2 = static_cast<std::string&&>(s1); // more explicit expression
    ```
    
## Smart Pointers
### Raw Pointers
- **Memory leak** (if not properly deleted)
    ```cpp
    int* array = new int[100];
    // delete array; // memory leak
    delete[] array; // correct V
    ```
- **Ownership**
     ```cpp
     int* array = new int[100];
     int* ptr = array;
     delete[] array;
     floart* newArray = ptr; // return newArray = 0 on LINUX
     ```
### Unique Pointers
- **Example**:
    ```cpp
    // initialization: create pointer and allocate memory to it on heap
    class UDT {
        public:
            UDT() {...}
            ~UDT(){...}
    }
    // create unique pointer to object
    std::unique_ptr<UDT> mike = std::unique_ptr<UDT>(new UDT);
    // no need to delete mike, automatically deleted

    // create unique pointer array
    std::unique_ptr<UDT[]> mike_array = std::unique_ptr<UDT[]>(new UDT[10]);
    // or equivalent factory pattern:
    std::unique_ptr<UDT[]> mike_array = std::make_unique<UDT[]>(10);

    ```

- **Summary**:
    - Ownership: we cannot share (no copies are allowed)
        - cannot use copy constructor:
        ```cpp
        std::unique_ptr<UDT> mike = std::unique_ptr<UDT>(new UDT); // std::unique_ptr<UDT> create pointer, new UDT allocates memory
        std::unique_ptr<UDT> joe = mike; // error, no copy constructor allowed
        std::unique_ptr<UDT> joe = std::move(mike); // compiled, allow transfer ownership
        ```
    - Memory Leak: we don't have to delete (delete within the scope)

- **Thinking**:
    - **Can unique pointer points to memory address on stack?**
        ```cpp
        int main()
        {
            int i = 1;

            std::unique_ptr<int> p1(&i);
            *p1 = 2;

            return 0;
        }
        ```
        > ISSUE: std::unique_ptr is designed for managing dynamically allocated memory, taking ownership of the pointer upon construction. However, variables like int i; are allocated on the stack, not dynamically, and their ownership cannot be transferred.
        > When the destructor of a std::unique_ptr attempts to delete the pointer it manages, it expects the pointer to have been allocated with new. Since stack-allocated variables are not created this way, using delete on them results in an error.
        > In scenarios involving stack-allocated variables, it's recommended to use raw pointers or make a copy instead, as std::unique_ptr is intended exclusively for heap-allocated resources.

- **Illustration of Execution Flow**
    ```cpp
        #include <iostream>
        #include <memory>  // For std::unique_ptr

        class UDT {
        public:
            UDT() { 
                std::cout << "UDT Constructor Called" << std::endl;
            }
            ~UDT() { 
                std::cout << "UDT Destructor Called" << std::endl;
            }
        };

        int main() {
            std::unique_ptr<UDT> mike = std::unique_ptr<UDT>(new UDT);
            return 0;
        }

    ```

    - Constructor Called
      new UDT → Memory allocated on heap → `UDT()` constructor called → "UDT Constructor Called"
    - Destructor Called
      Exiting main() → mike goes out of scope → `~UDT()` destructor called → "UDT Destructor Called"
    Memory is deallocated by unique_ptr.


### Shared Pointers
```cpp
std::shared_ptr<UDT> ptr2 = std::make_shared<UDT>();
```
- use_count: reference count is thread-safe (automatically updated)
- object (UDT) destructor is called when last pointer out of scope (use_count == 0)

    ```cpp
    #include <iostream>
    #include <memory>

    int main() {

        {
            // Creating our shared pointer
            std::shared_ptr<UDT> ptr1 = std::make_shared<UDT>();

            // Then, in a new scope, I share the resource
            std::shared_ptr<UDT> ptr2 = ptr1;

            // Our reference count is NOT updated with a weak_ptr
            std::cout << "(inside scope) use count = " << ptr2.use_count() << std::endl;
        } // Then, 'ptr2' is 'freed'

        // Then we check our updated reference count
        std::cout << "use count = " << ptr1.use_count() << std::endl;

        std::cout << "We should see the destructor call before this line\n";

        return 0;
    }

    ```
    
### Weak Pointer
- non-owning pointer, does not increase the reference count
- usage: "safter way to have dangling pointers"
- avoid break cycles 
- expired();true if the managed object (the heap allocated memory) has already been deleted, false otherwise.






