# Summary

In short, the goal is to allow forward declarations of c++ member functions without having to give the whole class definition (in particular without the member variables).

This would alow code similar to the following(The `__FWD__` "keyword is just pseudo syntax at this point):

```C
// Foo.h
#include <string_view>

class __FWD__ Foo  {
public:
    int bar(std::string_view view);
    char baz(std::size_t i) const;
};

// Foo.cpp
#include <Foo.h>
#include <string>

// Full definition
class Foo {
public:
    void bar(std::string_view view );
    char baz(std::size_t i) const;
private:
    std::string s;
};

void Foo::bar(std::string_view view ) {
    s = std::string(view);
}
char Foo::baz(std::size_t i) const {
    return s[i];
}
```

The main advantage being that the private member functions become a true implementation detail and consuming translation units of Foo neither need to process their type definitions nor all the headers hat have to be included directly and transitively.

# Motivation

Generally, forward declaration is about physically separating the interface from implementation. While c++ allows forward declare member functions, that forward declaration is tied to the class definition, which has to contain all member variables. While a developer can prevent access to those member variables via public/private access specifiers, their definition still needs to be visible to the compiler in any translation unit that wants to access a type's member function. This not only means that the compiler might have to repeatedly instantiate types that 

C++ already allows (probably even encourages) to separate the declaration of member functions from their definition. However, their declaration is part of the class definition, which also includes all member variables (including private ones). Consequently the compiler has to have processed their type definitions (as well as any default member initializer expressions), even in cases where those members are completely irrelevant for the code generation on the user side.

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
- What about inheritance?
- **TBD**

# Syntax Considerations
- **TBD**

# Implementation Concerns

- Assuming a TU can contain the class definition, but not the forward declaration: If member functions that are part of the forward declared interface are defined inline inside the actual class definition, the compiler might not actually emit the definition into the files object code and would thus lead to a linker error.
- Possible solutions: 
   -  Do nothing - the linker will complain loudly if this happens. However, as it depends on optimization decisions, such a problem might only manifest after completely unrelated changes in the code, compiler upgrade, or changes in the compilation flags.
   -  Require the definition to somehow reference the forward declaration
      - How can this be enforced?
      - How to make sure, there is exactly one version of the forward declared interface?
      -  
- **TBD**



# NOTES /Questions/TODO
- How to ensure that forward declared functions aren't inline and thus potentially not emitted into the object file?
