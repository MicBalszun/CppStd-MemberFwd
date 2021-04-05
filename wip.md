# Summary

In short, the goal is to allow forward declarations of c++ member functions without having to give the whole class definition (in particular without the member variables).

This would alow the following code (The `__FWD__` "keyword is just pseudipseudo syntax at this point):

```C
// Foo.h
#include <string_view>

class __FWD__ Foo  {
    int bar(std::string_view view);
    char baz(std::size_t i) const;
};

// Foo.cpp
#include <Foo.h>
#include <string>

class Foo {
public:
    void bar(std::string_view view ) {
        s = std::string(view);
    }
    char baz(std::size_t i) const {
        return s[i];
    }
private:
    std::string s;
};
```


# Motivation

Generally, forward declaration is about physically separating the interface from implementation. C++ already allows (probably even encourages) to separate the declaration of member functions from their definition. However, their declaration is part of the class definition, which also includes all (private) member variables. Consequently the compiler has to have processed their type definitions (as well as any default member initializer expressions), even in cases where those members are completely irrelevant for the code generation on the user side.

There foward declarations in general can have different goals:
- Source code organization
- Circular dependencies
- Hiding implementation details from the user **and the compiler**

The motivation behind this paper is mainly related to the last point: Hiding implementation details from the compiler
- Simplifies ensuring ABI stability
- Flattens dependency trees
- Improves compile and link times


**TBD**

# Further Design Questions:

- Does the Interfacee have to forward declare all member functions? Only public ones? Whatever the user wants?
- Do we allow forward declaration for types with virtual memberfunctions?
- What about inheritance
- **TBD**

# Syntax Considerations
- **TBD**

# Implementation Concerns
- **TBD**



# NOTES /Questions/TODO
- How to ensure that forward declared functions aren't inline and thus potentially not emitted into the object file?
