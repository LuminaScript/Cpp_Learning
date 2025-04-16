# Chapter 3: Strings, Vectors, and Arrays - Summary

## 3.1: Namespace using Declarations
- **using declarations** simplify access to names from the `std` namespace
- Format: `using namespace::name;` (e.g., `using std::cin;`)
- Each declaration introduces a single namespace member
- Headers should not contain using declarations to avoid name conflicts

## 3.2: Library string Type
- **string** is a variable-length sequence of characters, defined in the `string` header
- **Initialization**: Various ways to initialize strings:
    - Default initialization: `string s1;` creaes an empty string
    - Copy initialization from another string: create s2 as an exact copy of s1
      `string s2 = s1;` or `string s2(s1);`
    - Initialization from string literals: create s3 containing the characters from the literal (without null terminator)
      `string s3 = "value";` or `string s3("value");` 
    - Character repetition initialization: Creates s4 with n copies of the character 'c'
      `string s4(n, 'c');`
- **Operations**:
    | Operation | Description |
    |-----------|-------------|
    | `os << s` | Writes s onto output stream os. Returns os. |
    | `is >> s` | Reads whitespace-separated string from is into s. Returns is. |
    | `getline(is, s)` | Reads a line of input from is into s. Returns is. |
    | `s.empty()` | Returns true if s is empty; otherwise returns false. |
    | `s.size()` | Returns the number of characters in s. |
    | `s[n]` | Returns a reference to the char at position n in s; positions start at 0. |
    | `s1 + s2` | Returns a string that is the concatenation of s1 and s2. |
    | `s1 = s2` | Replaces characters in s1 with a copy of s2. |
    | `s1 == s2` | The strings s1 and s2 are equal if they contain the same characters. Equality is case-sensitive. |
    | `s1 != s2` | Inequality comparison. |
    | `<, <=, >, >=` | Comparisons are case-sensitive and use dictionary ordering. |
- **cctype Functions**:
    | Function | Description |
    |----------|-------------|
    | `isalnum(c)` | True if c is a letter or a digit. |
    | `isalpha(c)` | True if c is a letter. |
    | `iscntrl(c)` | True if c is a control character. |
    | `isdigit(c)` | True if c is a digit. |
    | `isgraph(c)` | True if c is not a space but is printable. |
    | `islower(c)` | True if c is a lowercase letter. |
    | `isprint(c)` | True if c is a printable character (i.e., a space or a character that has a visible representation). |
    | `ispunct(c)` | True if c is a punctuation character (i.e., a character that is not a control character, a digit, a letter, or a printable whitespace). |
    | `isspace(c)` | True if c is whitespace (i.e., a space, tab, vertical tab, return, newline, or formfeed). |
    | `isupper(c)` | True if c is an uppercase letter. |
    | `isxdigit(c)` | True if c is a hexadecimal digit. |
    | `tolower(c)` | If c is an uppercase letter, returns its lowercase equivalent; otherwise returns c unchanged. |
    | `toupper(c)` | If c is a lowercase letter, returns its uppercase equivalent; otherwise returns c unchanged. |
## Section 3.3: Library vector Type
- **vector** is a container holding objects of the same type
- Defined as a **class template**: `vector<T>` where T is the element type
- **Initialization**:
    - **Default Initialization**: `vector<int> v1;` - Creates an empty vector with no integers
    - **Copy Initialization**: `vector<int> v2(v1);` or `vector<int> v2 = v1;` - Creates v2 with copies of all elements in v1
    - **Size and Value Initialization**: `vector<int> v3(10, 42);` - Creates v3 with 10 elements, each with value 42
    - **Size-only Initialization**: `vector<int> v4(5);` - Creates v4 with 5 elements, each initialized to 0
    - **List Initialization**: `vector<int> v5{1, 2, 3, 4};` or `vector<int> v5 = {1, 2, 3, 4};` - Creates v5 with elements 1, 2, 3, and 4
- **Adding elements**:
  - `push_back()` adds elements to the end
  - Vectors grow efficiently: Vectors grow efficiently at runtime, so start with an empty vector and add elements as needed rather than pre-sizing it, unless all elements need the same initial value.
- **Operations**: 
    | Operation | Description |
    |-----------|-------------|
    | `v.empty()` | Returns true if v is empty; otherwise returns false. |
    | `v.size()` | Returns the number of elements in v. Returns a value of type `vector<T>::size_type`. |
    | `v.push_back(t)` | Adds an element with value t to end of v. |
    | `v[n]` | Returns a reference to the element at position n in v. |
    | `v1 = v2` | Replaces the elements in v1 with a copy of the elements in v2. |
    | `v1 = {a,b,c...}` | Replaces the elements in v1 with a copy of the elements in the comma-separated list. |
    | `v1 == v2` | v1 and v2 are equal if they have the same number of elements and each element in v1 is equal to the corresponding element in v2. |
    | `v1 != v2` | Inequality comparison. |
    | `<, <=, >, >=` | Have their normal meanings using dictionary ordering. |
## Section 3.4: Introducing Iterators
- Iterators provide a more general mechanism to access elements
- Every container has `begin()` and `end()` operations
  - `begin()` returns an iterator to the first element
  - `end()` returns an off-the-end iterator
- **Types**: `iterator` and `const_iterator`
    ```cpp 
    vector<int>::iterator it;         // Can read and write vector elements
    string::iterator it2;             // Can read and write characters in a string
    vector<int>::const_iterator it3;  // Can read but not write elements
    string::const_iterator it4;       // Can read but not write characters
    ```
- **Operations**:
    | Operation | Description |
    |-----------|-------------|
    | `*iter` | Returns a reference to the element denoted by the iterator `iter`. |
    | `iter->mem` | Dereferences `iter` and fetches the member named `mem` from the underlying element. Equivalent to `(*iter).mem`. |
    | `++iter` | Increments `iter` to refer to the next element in the container. |
    | `--iter` | Decrements `iter` to refer to the previous element in the container. |
    | `iter1 == iter2` | Compares two iterators for equality. Two iterators are equal if they denote the same element or if they are the off-the-end iterator for the same container. |
    | `iter1 != iter2` | Compares two iterators for inequality. |

## 3.5: Arrays
#### Declaration & Definition
- **Array Declaration**: 
    - Basic Declaration:`a[d]` 
        - `d` (dimension): must be a **constant expression** > zero and dimension must be known at compile time.
    - Complex Declarations
        - **Arrays Can Hold Various Types**:
          - Arrays can hold objects of most types, including pointers
          - Arrays cannot hold references. But array itself can be a reference
              ```cpp
              int *ptrs[10];            // Array of 10 pointers to int
              int &refs[10] = /* ? */;  // Error: cannot have array of references
              int (*Parray)[10] = &arr; // Pointer to an array of 10 ints
              int (&arrRef)[10] = arr;  // Reference to an array of 10 ints
              ```
        - **Reading Complex Declarations**:
          - Default: type modifiers bind right to left
          - For complex array declarations, read from inside out:
            1. Look at name and adjacent symbols (inside parentheses)
            2. Look right for array dimensions
            3. Look left for element type

        - **Example of Complex Declaration**:
          ```cpp
          int *(&arry)[10] = ptrs;  // Reference to an array of 10 pointers to int
          ```
          - Inside: `(&arry)` indicates arry is a reference
          - Right: `[10]` indicates reference to array of size 10
          - Left: `int*` indicates elements are pointers to int
- **Array initialization**: 
    - **Basic Initialization**:
      - Can use list initialization and omit the dimension
      - Compiler infers size from number of initializers
      - If dimension specified, initializers must not exceed it
      - Remaining elements are value initialized
      ```cpp
      int ia1[3] = {0, 1, 2};          // Specified size
      int a2[] = {0, 1, 2};            // Size inferred (3)
      int a3[5] = {0, 1, 2};           // Equivalent to {0, 1, 2, 0, 0}
      string a4[3] = {"hi", "bye"};    // Equivalent to {"hi", "bye", ""}
      int a5[2] = {0, 1, 2};           // Error: too many initializers
      ```
    - **Character Arrays**:
      - Can initialize from string literals
      - String literals include null terminator
      - Must account for null terminator in size
      ```cpp
      char a1[] = {'C', '+', '+'};           // Size is 3, no null
      char a2[] = {'C', '+', '+', '\0'};     // Size is 4, explicit null
      char a3[] = "C++";                     // Size is 4, automatic null
      const char a4[6] = "Daniel";           // Error: needs size 7 for null
      ```
    - **Restrictions**:
      - Cannot initialize an array as copy of another
      - Cannot assign one array to another
- **Accessing elements**:
  - Subscript operator `[]`
  - Range-based for loops
- **Pointers and arrays**:
    - When using an array in C++, the compiler typically converts the array to a pointer to its first element
        ```cpp
        string names[] = {"Alice", "Bob", "Charlie"};  // Array of strings
        string *p1 = &names[0];  // Explicit pointer to first element 
        string *p2 = names;      // Implicit conversion of array to pointer
        // p1 and p2 point to the same memory location
        ```
    - **Type Deduction**
        - Using `auto` with an array name results in a pointer type, not an array type
        - `decltype` preserves the array type when used with an array name
        ```cpp
            int values[] = {10, 20, 30, 40, 50};
            auto ptr = values;    // ptr is an int*, points to values[0]
            // ptr = 42;          // Error: can't assign int to a pointer

            decltype(values) valuesCopy = {1, 2, 3, 4, 5};  // valuesCopy is array of 5 ints
            // valuesCopy = ptr;  // Error: can't assign pointer to array
            ```
    - The standard library provides `begin()` and `end()`
        ```cpp
        #include <iterator>

        int data[] = {3, 1, 4, 1, 5, 9};
        int *first = begin(data);  // Points to data[0]
        int *last = end(data);     // Points one past the last element
        ```
    - Pointer Arithmetic
        - Adding an integer to a pointer advances it that many elements in the array
        - Subtracting two pointers gives the distance between them (of type `ptrdiff_t`)
        - Relational operators can compare pointers to elements in the same array
        - Result of pointer addition must point to an element in the same array or one past the end
            ```cpp
            int arr[] = {10, 20, 30, 40, 50};
            int *p1 = arr;        // Points to arr[0]
            int *p2 = p1 + 3;     // Points to arr[3] (value 40)
            int *p3 = arr + 5;    // Points one past the end (valid but can't dereference)

            ptrdiff_t elements = p2 - p1;  // elements = 3
            ptrdiff_t total = end(arr) - begin(arr);  // total = 5 (array size)

            // Compare pointers
            if (p1 < p2) {
                cout << "p1 comes before p2 in the array" << endl;
            }
            ```

    - Array Subscripting
    (`arr[i]`) is equivalent to pointer arithmetic and dereferencing (`*(arr + i)`)
        ```cpp
        int values[] = {2, 4, 6, 8, 10};
        int *middle = &values[2];  // Points to the middle element (6)

        cout << middle[0] << endl;  // Outputs 6 (same as *middle)
        cout << middle[1] << endl;  // Outputs 8 (same as *(middle + 1))
        cout << middle[-1] << endl; // Outputs 4 (same as *(middle - 1))

        // These are equivalent:
        int x = values[3];    // Subscripting the array
        int y = *(values + 3); // Pointer arithmetic and dereferencing
        ```

    - Important Cautions
        - Cannot dereference or increment an off-the-end pointer
        - Comparisons between unrelated pointers are meaningless
        - Computing a pointer more than one position past the last element is an error
        ```cpp
        int arr1[5] = {1, 2, 3, 4, 5};
        int arr2[5] = {6, 7, 8, 9, 10};
        int *p1 = arr1;
        int *p2 = arr2;

        // DANGEROUS: Undefined behavior
        if (p1 < p2) {  // Meaningless comparison of unrelated pointers
            cout << "This comparison is invalid!" << endl;
        }

        int *end = arr1 + 5;  // Points one past the last element (valid)
        // int *invalid = arr1 + 6;  // ERROR: more than one past the end (undefined behavior)

        // DANGEROUS: Undefined behavior
        // *end = 100;  // Can't dereference one-past-the-end pointer
        ```

## Section 3.6: Multidimensional Arrays
- C++ has arrays of arrays, not true multidimensional arrays
- Defined with multiple dimensions: `int ia[3][4];` (3 rows, 4 columns)
- **Subscripting**: Requires an index for each dimension
- For range-based loops, all control variables except the innermost must be references
- **Pointers**: A pointer to a multidimensional array points to the first inner array
- Type aliases can simplify pointer declarations to multidimensional arrays