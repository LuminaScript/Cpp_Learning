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
    s2 = static_cast<std::string&&>(s2); // more explicit expression
    ```

