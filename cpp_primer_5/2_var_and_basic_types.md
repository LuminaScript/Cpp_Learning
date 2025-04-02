# C++ Primer Chapter 2: Variables and Basic Types
## 2.1 Primitive Built-in Types

### 2.1.1 Arithmetic Types

**Integral Types:**
- `bool`: true/false values
- Characters: `char` (8 bits), `wchar_t` (16+ bits), `char16_t` (16 bits), `char32_t` (32 bits)
- Integers: `short` (16+ bits), `int` (16+ bits, typically 32), `long` (32+ bits), `long long` (64+ bits)

**Floating-Point Types:**
- `float`: 6+ significant digits
- `double`: 10+ significant digits
- `long double`: 10+ significant digits

**Signedness:**
- `unsigned` types represent only non-negative values
- `signed` types represent both positive and negative values
- Most integral types default to signed with an explicit unsigned variant (unsigned int, etc.)
- `char` specifically has three distinct types:
  - `char`: Implementation-defined whether signed or unsigned
  - `signed char`: Explicitly signed (-128 to 127 on 8-bit systems)
  - `unsigned char`: Explicitly unsigned (0 to 255 on 8-bit systems)

**Range Guidelines:**
- Use `unsigned` types only when values cannot be negative
- Prefer `int` for most integer calculations
- Use `double` for floating-point calculations (more precision than `float`)
- Use character types only for character data, not arithmetic

### 2.1.2 Type Conversions
- Automatic conversions follow these rules:
  - Non-`bool` → `bool`: 0 becomes `false`, others become `true`
  - `bool` → numeric: `false` becomes 0, `true` becomes 1
  - Floating-point → integral: truncated (fractional part lost)
  - Integral → floating-point: precision may be lost
  - Out-of-range to `unsigned`: wraps around (value % range_size)
  - Out-of-range to `signed`: undefined behavior

⚠️ Be careful with `unsigned` types in loops and arithmetic 
  - `unsigned` types can't be less than zero, so `for (unsigned u = 10; u >= 0; --u)` is an infinite loop
  - When `u` is 0, decrementing gives a very large number due to wraparound

⚠️ Mixing signed and unsigned types can lead to unexpected results since signed values are converted to unsigned

### 2.1.3 Literals
- **Integer**: `42` (decimal), `052` (octal), `0x2A` (hex)
- **Floating-point**: `3.14159`, `3.14159E0`, `0.`, `.001`
- **Character**: `'a'` (char), `"hello"` (string, includes null terminator)
- **Escape sequences**: `\n`, `\t`, `\\`, `\"`, `\ooo` (octal), `\xhh` (hex)
- **Type specifiers**: `u/U` (unsigned), `l/L` (long), `f/F` (float), etc.
- **Null pointer**: `nullptr`

## 2.2 Variables

### 2.2.1 Variable Definitions

**Initialization Types:**
```cpp
// Four ways to initialize
int a = 0;      // Copy initialization
int b(0);       // Direct initialization
int c = {0};    // List initialization with equals
int d{0};       // Direct list initialization
```
**List initialization**: prevents narrowing conversions:
```cpp
int a{3.14};    // Error: narrowing conversion
int b(3.14);    // OK but truncates to 3
```

**Default initialization**:
- Global variables: zero-initialized
- Local built-in types: undefined values
- Class objects: initialized by class definition

**In-class initialization**:
```cpp
struct S {
    int count = 0;      // OK: equals sign
    double value{0.0};  // OK: braces
    char c(0);          // ERROR: parentheses not allowed
};
```
### 2.2.2 Declarations vs Definitions
- **Declaration**: Makes a name known to program
- **Definition**: Creates the associated entity
- Use `extern` for declarations without definitions:
  ```cpp
  extern int i;        // declaration only
  extern int j = 0;    // definition (initializer overrides extern)
  ```
### 2.2.3 Identifiers
- Letters, digits, underscores
- Must begin with letter or underscore
- Case-sensitive
- Cannot use C++ keywords
- Convention: variables lowercase, classes uppercase

### 2.2.4 Scope of a Name
- Region where a name is visible
- Defined by blocks (code between `{}`)
- Inner scopes can access outer scopes
- Inner names can hide outer names
- Global scope has no name (use `::name`)
## 2.3 Compound Types

### 2.3.1 References (`&`)
- An alias (alternative name) for an existing object
- Must be initialized
- Cannot be rebound to a different object
- Cannot bind to literals or temporary expressions
- Type must match (with exceptions covered later)
```cpp
int i = 42;
int &r = i;  // r refers to i
r = 10;      // changes i to 10
```

### 2.3.2 Pointers (`*`)
- An object that stores the address of another object
- Can be reassigned to point to different objects
- Can be null (`nullptr`)
- Dereference with `*` to access pointed-to value

```cpp
int i = 42;
int *p = &i;    // p holds address of i
*p = 10;        // changes i to 10
p = nullptr;    // p now points to nothing
```

#### Pointer States
- Points to valid object
- Points to position after object
- Null pointer
- Invalid/uninitialized (dangerous)

#### Compound Declarations
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

- Must be initialized at definition
- Operations that modify the object are not allowed
- By default, `const` objects have file-local scope
- Use `extern const` to share across files

### References to const
```cpp
const int ci = 42;
const int &r = ci;      // reference to const
r = 10;                 // ERROR: cannot modify through reference to const

int i = 42;
const int &r2 = i;      // reference to const can bind to non-const
r2 = 10;                // ERROR: cannot modify through r2
i = 10;                 // OK: i can still be modified directly
```

Special feature: references to `const` can bind to:
- Non-const objects
- Literals
- Expressions
- Different types (with conversion)

### Pointers and const

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

### Top-Level vs Low-Level const
- **Top-level**: The object itself is const (`const int num` or `int * const ptr`)
- **Low-level**: Pointed-to or referenced object is const (`const int *ptr`)
- When copying, top-level const is ignored
- Low-level const must match or allow conversion

### constexpr and Constant Expressions
```cpp
constexpr int mf = 20;           // compile-time constant
constexpr int limit = mf + 1;    // compile-time expression
constexpr int sz = size();       // OK only if size() is constexpr function
```

- `constexpr` ensures compile-time evaluation
- Can be used with literal types (arithmetic, pointers, references)
- `constexpr` pointer is a top-level const pointer

## 2.5 Type Management

### Type Aliases
```cpp
// Two ways to create type aliases
typedef double wages;        // traditional
using SI = Sales_item;       // modern (C++11)
```

### auto Type Specifier
```cpp
auto i = 0;                  // int
auto d = 3.14;               // double
auto s = "hello";            // const char*
```

- Compiler deduces type from initializer
- Ignores top-level const by default
- Use `const auto` to keep top-level const
- Use `auto&` for reference type

### decltype Type Specifier
```cpp
decltype(expression) var;    // var has same type as expression
```

- Deduces type without evaluating expression
- Preserves top-level const and references
- For lvalue expressions, typically yields reference type
- `decltype((var))` is always a reference type

## 2.6 User-Defined Types

```cpp
struct Sales_data {
    std::string bookNo;
    unsigned units_sold = 0;     // in-class initializer
    double revenue = 0.0;        // in-class initializer
};
```

### Header Files
```cpp
// Sales_data.h
#ifndef SALES_DATA_H
#define SALES_DATA_H

// Class definition goes here

#endif // SALES_DATA_H
```

- Use header guards to prevent multiple inclusion
- Name headers after the class they define
- Headers should only be included once in a file