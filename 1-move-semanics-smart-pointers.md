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
        std::unique_ptr<UDT> mike = std::nuique_ptr<UDT>(new UDT);
        std::unique_ptr<UDT> joe = mike; // error
        std::unique_ptr<UDT> joe = std::move(mike); // okay
        ```
    - Memory Leak: we don't have to delete (delete within the scope)


