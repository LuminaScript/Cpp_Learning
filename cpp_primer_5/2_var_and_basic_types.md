# C++ Primer: Variables and Basic Types

## 2.1 Primitive Built-in Types

### 2.1.1 Arithmetic Types

#### Integral Types
1. `bool`: true/false values
2. **Characters**:
   1. `char` (8 bits)
   2. `wchar_t` (16+ bits)
   3. `char16_t` (16 bits)
   4. `char32_t` (32 bits)
3. **Integers**:
   1. `short` (16+ bits)
   2. `int` (16+ bits, typically 32)
   3. `long` (32+ bits)
   4. `long long` (64+ bits)

#### Floating-Point Types
1. `float`: 6+ significant digits
2. `double`: 10+ significant digits
3. `long double`: 10+ significant digits

#### Signedness
1. Most integral types default to `signed` (can be negative)
2. `unsigned` types represent only non-negative values
3. `char` is implementation-defined whether signed or unsigned

### 2.1.2 Type Conversions
1. Automatic conversions follow these rules:
   1. Non-`bool` → `bool`: 0 becomes `false`, others become `true`
   2. `bool` → numeric: `false` becomes 0, `true` becomes 1
   3. Floating-point → integral: truncated (fractional part lost)
   4. Integral → floating-point: precision may be lost
   5. Out-of-range to `unsigned`: wraps around (value % range_size)
   6. Out-of-range to `signed`: undefined behavior

⚠️ **Warning**: Be careful with `unsigned` types in loops and arithmetic

### 2.1.3 Literals
1. **Integer**: `42` (decimal), `052` (octal), `0x2A` (hex)
2. **Floating-point**: `3.14159`, `3.14159E0`, `0.`, `.001`
3. **Character**: `'a'` (char), `"hello"` (string, includes null terminator)
4. **Escape sequences**: `\n`, `\t`, `\\`, `\"`, `\ooo` (octal), `\xhh` (hex)
5. **Type specifiers**: `u/U` (unsigned), `l/L` (long), `f/F` (float), etc.
6. **Null pointer**: `nullptr`

## 2.2 Variables

### 2.2.1 Definitions and Initialization

```cpp
// Four ways to initialize
int a = 0;      // Copy initialization
int b(0);       // Direct initialization
int c = {0};    // List initialization with equals
int d{0};       // Direct list initialization
```

1. **List initialization** prevents narrowing conversions:
   ```cpp
   int a{3.14};    // Error: narrowing conversion
   int b(3.14);    // OK but truncates to 3
   ```

2. **Default initialization**:
   1. Global variables: zero-initialized
   2. Local built-in types: undefined values
   3. Class objects: initialized by class definition

3. **In-class initialization**:
   ```cpp
   struct S {
       int count = 0;      // OK: equals sign
       double value{0.0};  // OK: braces
       char c(0);          // ERROR: parentheses not allowed
   };
   ```

### 2.2.2 Declarations vs Definitions
1. **Declaration**: Makes a name known to program
2. **Definition**: Creates the associated entity
3. Use `extern` for declarations without definitions:
   ```cpp
   extern int i;        // declaration only
   extern int j = 0;    // definition (initializer overrides extern)
   ```

### 2.2.3 Identifiers
1. Letters, digits, underscores
2. Must begin with letter or underscore
3. Case-sensitive
4. Cannot use C++ keywords
5. Convention: variables lowercase, classes uppercase

### 2.2.4 Scope
1. Region where a name is visible
2. Defined by blocks (code between `{}`)
3. Inner scopes can access outer scopes
4. Inner names can hide outer names
5. Global scope has no name (use `::name`)

## 2.3 Compound Types

### 2.3.1 References (`&`)
1. An alias (alternative name) for an existing object
2. Must be initialized
3. Cannot be rebound to a different object
4. Cannot bind to literals or temporary expressions
5. Type must match (with exceptions covered later)

```cpp
int i = 42;
int &r = i;  // r refers to i
r = 10;      // changes i to 10
```

### 2.3.2 Pointers (`*`)
1. An object that stores the address of another object
2. Can be reassigned to point to different objects
3. Can be null (`nullptr`)
4. Dereference with `*` to access pointed-to value

```cpp
int i = 42;
int *p = &i;    // p holds address of i
*p = 10;        // changes i to 10
p = nullptr;    // p now points to nothing
```

#### Pointer States
1. Points to valid object
2. Points to position after object
3. Null pointer
4. Invalid/uninitialized (dangerous)

### 2.3.3 Compound Declarations
```cpp
int* p1, p2;    // p1 is pointer to int, p2 is int
int *p1, *p2;   // better style: both are pointers
int **pptr;     // pointer to pointer
int *&rp = p1;  // reference to pointer
```

## 2.4 const Qualifier

**Basic usage**:
```cpp
const int max_size = 100;  // max_size cannot be modified
```

1. Must be initialized at definition
2. Operations that modify the object are not allowed
3. By default, `const` objects have file-local scope
4. Use `extern const` to share across files

### 2.4.1 References to const
```cpp
const int ci = 42;
const int &r = ci;      // reference to const
r = 10;                 // ERROR: cannot modify through reference to const

int i = 42;
const int &r2 = i;      // reference to const can bind to non-const
r2 = 10;                // ERROR: cannot modify through r2
i = 10;                 // OK: i can still be modified directly
```

1. Special feature: references to `const` can bind to:
   1. Non-const objects
   2. Literals
   3. Expressions
   4. Different types (with conversion)

### 2.4.2 Pointers and const

```cpp
// Pointer to const (low-level const)
const int ci = 42;
const int *p1 = &ci;    // pointer to const int
*p1 = 10;               // ERROR: cannot modify through pointer to const

// const pointer (top-level const)
int i = 42;
int * const p2 = &i;    // const pointer to int
*p2 = 10;               // OK: can modify pointed-to value
p2 = &j;                // ERROR: cannot modify pointer value

// const pointer to const
const int * const p3 = &ci;  // const pointer to const int
```

### 2.4.3 Top-Level vs Low-Level const
1. **Top-level**: The object itself is const (`const int num` or `int * const ptr`)
2. **Low-level**: Pointed-to or referenced object is const (`const int *ptr`)
3. When copying, top-level const is ignored
4. Low-level const must match or allow conversion

### 2.4.4 constexpr and Constant Expressions
```cpp
constexpr int mf = 20;           // compile-time constant
constexpr int limit = mf + 1;    // compile-time expression
constexpr int sz = size();       // OK only if size() is constexpr function
```

1. `constexpr` ensures compile-time evaluation
2. Can be used with literal types (arithmetic, pointers, references)
3. `constexpr` pointer is a top-level const pointer

## 2.5 Type Management

### 2.5.1 Type Aliases
```cpp
// Two ways to create type aliases
typedef double wages;        // traditional
using SI = Sales_item;       // modern (C++11)
```

### 2.5.2 auto Type Specifier
```cpp
auto i = 0;                  // int
auto d = 3.14;               // double
auto s = "hello";            // const char*
```

1. Compiler deduces type from initializer
2. Ignores top-level const by default
3. Use `const auto` to keep top-level const
4. Use `auto&` for reference type

### 2.5.3 decltype Type Specifier
```cpp
decltype(expression) var;    // var has same type as expression
```

1. Deduces type without evaluating expression
2. Preserves top-level const and references
3. For lvalue expressions, typically yields reference type
4. `decltype((var))` is always a reference type

## 2.6 User-Defined Types

### 2.6.1 Defining Custom Data Structures
```cpp
struct Sales_data {
    std::string bookNo;
    unsigned units_sold = 0;     // in-class initializer
    double revenue = 0.0;        // in-class initializer
};
```

### 2.6.2 Header Files
```cpp
// Sales_data.h
#ifndef SALES_DATA_H
#define SALES_DATA_H

// Class definition goes here

#endif // SALES_DATA_H
```

1. Use header guards to prevent multiple inclusion
2. Name headers after the class they define
3. Headers should only be included once in a file
