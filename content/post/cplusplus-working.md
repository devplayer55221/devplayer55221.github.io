+++
author = "mukund"
title = "C++ Working"
date = "2025-05-11"
description = "C++ Working"
tags = [
    "c++",
]
+++

I was just learning about the C++ fundamentals from **The Cherno** C++ playlist and thought of making notes on it. Added reference at the bottom.

### What’s cmake?

CMake is an open-source, cross-platform build system generator. It doesn’t build software directly—it generates build files (like Makefiles, Ninja build files, or Visual Studio project files) based on the instructions present in <mark>CMakeLists.txt</mark>.

```cpp
cmake_minimum_required (VERSION 3.5)

project (HelloWorld)

set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Werror -std=c++14")
set (source_dir "${PROJECT_SOURCE_DIR}/src/")

file(GLOB source_files "${source_dir}/*.cpp")

add_executable (HelloWorld ${source_files})
```

This `CMakeLists.txt` file is a CMake build script that defines how to build a simple C++ project named `HelloWorld`. 

- In the above code, it specifies that minimum version of cmake should be 3.5.
- The name of the project would be ‘HelloWorld’.
- Flags are added for the compiler to show warnings and errors.
- A source directory variable is defined with the path of the subfolder inside the root directory.
- Files with `.cpp` extension will be stored into the source directory.
- An executable with the name ‘HelloWorld’ is to be created on build using the .cpp files in source directory.

A <mark>build.sh</mark> file is created that contains the cmake command to configure the build environment. It would generate Makefiles to use with the IDE (here CodeLite). 

```cpp
cmake -G "CodeLite - Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug
```

### In a C program

There is a <mark>preprocessor</mark> statement `#include <iostream>`. Any line starting with a `#` would be a preprocessor statement. The <mark>include</mark> preprocessor will copy all the contents of the mentioned file (here iostream) directly into our program file. The preprocessor statement is executed before the compilation starts. Only `.cpp` files are compiled. The `.cpp` files are compiled into `.obj` files.

<mark>**Linker**</mark> is used to join all the `.obj` files into one executable file (`.exe` in Windows)

We can just declare a function in our program, and call it in another function. The function declaration could be empty, it doesn’t need to be declared to compile successfully. The <mark>**compiler**</mark> doesn’t check if the function has been defined. Only during the runtime(build) would we get the error indicating that the function has no definition.

The `.obj` file is binary and not readable. It contains the machine code that CPU runs. We can create a `.asm` file from the `.cpp` file which contains assembly instructions.

After compilation, a process called <mark>linking</mark> takes place. Linking finds all the symbols, functions, etc. in our project and link them together. Compilation happens on individual files separately.

Let’s say that we call a function A, which in turn calls a function B and function B is declared but not defined, we will get a linking error and not a compile error. If we do not call the function A, but it still exists and call the function B, we will still get the linking error. If we add <mark>static</mark> to the declaration of function A, and do not call it, we will not get the linking error.

> If we add `static` in any function declaration, then it means that the function is only declared for that particular file. 

So, the linker understands that the function A will only be used in the file where it is defined. It will see that function A is not being called, so it doesn’t matter whether the function B (which is being called inside function A) is defined or not. If the function is not declared as static, then there is a possibility that function A might get called in some other file, and for that the function B also needs to be defined.

If we define a function inside a header file, and then include the same header file in two different `.cpp` files, we will get a linking error. It is because, including the header file means copying the contents of the header file, and here it means declaring the function in two files.

> We can use `static` keyword while declaring the function in the header file. It will mean that the linking to the static function will be internal to the file calling it and separate files will have there own separate versions of that function declared as static.

### References

- [TheCherno - Youtube](https://www.youtube.com/@TheCherno)
    - [How to setup C++ on Linux](https://www.youtube.com/watch?v=LKLuvoY6U0I&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=4&pp=iAQB)
    - [How C++ Works](https://www.youtube.com/watch?v=SfGuIVzE_Os&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=7)
    - [How the C++ Compiler Works](https://www.youtube.com/watch?v=3tIqpEmWMLI&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=6)
    - [How the C++ Linker Works](https://www.youtube.com/watch?v=H4s55GgAg0I&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=7)
