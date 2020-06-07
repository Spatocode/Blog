---
title: "Calling C code with cgo"
date: 2019-08-22T00:59:54+01:00
categories: ["Software Engineering"]
tags: ["golang", "go", "cgo", "c", "c++"]
images: ["/cgo.jpg"]
draft: false
---

{{< figure src="/cgo.jpg" alt="cgo" >}}

C has long being the lingua-franca of programming. While programming languages, frameworks, tools e.t.c. come and go, this general-purpose, low level language developed at Bells Lab by Dennis Ritchie has been around for decades and shows no sign of fading. This is largely because of its closeness to hardware, great portability and efficiency, which made it an ideal choice for low-level development. Many programs today such as the Linux OS, Windows OS, PostgreSQL database, hardware drivers e.t.c., are largely written in C.

While writing Go programs, you may need to use a hardware driver, or even a library written in C. To achieve this you need a Foreign Function Interface(FFI). Over the years, tools like Swig have been used to achieve this. Swig provides a lot of features for integrating with C++ classes.

Asides Swig, Go provides us with a pseudo package called "C" and a tool called cgo. The former is for providing interface to C libraries while the later is for creating go bindings for C functions. It's quite notable that cgo might not be the best option at times. This is because you may loose some Go features like type safety, memory management and cross-compilation.

In this article we'll explore the C psuedo package. We'll also write some C programs and call it from Go. With much words aside let's move into the nitty-gritties.

Create a project folder in your Go path. Add a file ***hello.c*** in the folder and input the below C code.

```c
#include "stdio.h"
#include "stdlib.h"

void hello(char *name, char *buff, const size_t size) {
  snprintf(buff, size, "Hello world!!! My first %s program", name)
}
```

The above code is a simple C code that stores a series of values to a buffer which has a specific size. We achieved this using the ```snprintf()``` function found in the ```stdio``` header file. For the sake of simplicity, we're not conscious of stuffs like buffer overflows here. Next is to write some beautiful cgo code. Add the below code to your main package file.


```go
package main

// #cgo CFLAGS: -g -WALL
// #include "stdio.h
// #include "hello.c"
import "C"
import (
  "fmt"
  "unsafe"
)
```

This is where the real stuff begins. The comments after the ```package main``` declaration are not just an ordinary comment. Cgo recognizes this comment. Any comment starting with ```#cgo``` followed by a space character is a directive for cgo. These directives provides flags for the compiler and linker when building the C parts of the package. For example the directive ```// #cgo CFLAGS: -g -WALL``` tells the compiler to compiler the C code with gcc options -g(debug symbols) and ```-WALL```(all warnings) enabled. There are other flags like CPPFLAGS and FFLAGS for tweaking the C++ and Fortran code respectively. Make sure there's no new line between the Cgo comment and the ```import "C"``` statement else the compiler will throw a nasty error. The two other comments shows that we included the ```stdio.h``` header file and our ***hello.c*** file to our Go code. This tells cgo the C files to compile.

Next we import our C pseudo-package and the unsafe package. The C psuedo-package is not found in the standard Go library. It's not a package rather it's a special name interpreted by cgo as a reference to a C name space. It is used to call functions and codes in a C code.

Now let's write the code for our main function.

```go
func main() {
  size := C.uint(C.sizeof_char * 50)
  name := C.CString("cgo")
  defer C.free(unsafe.Pointer(name))

  buffer := C.malloc(size)
  defer C.free(unsafe.Pointer(buffer))

  C.hello(name, (*C.char)(buffer), size)

  greeting := C.GoString((*C.char)(buffer))
  fmt.Println(greeting)
}
```

Let's break the above code one after the other. In the first line, we create our buffer size which will be passed as an argument to our hello C function. We achieved this by calling the C ```sizeof``` operator multiplied by 50. Because this buffer size will be passed to a C function, it needs to be converted from int to uint as our hello function expects a buffer size of type uint.

The next line creates a C string. Normal Go string cannot be passed to a C function. This is because C does not have an explicit string type. C strings are represented by zero-terminated array of characters. Therefore, to create a C string, ```C.CString()``` is used to convert a Go string to a valid C string. Other functions like ```GoString()```, ```GoStringN()```, ```GoBytes()``` and ```CBytes()``` are used for similar conversion between Go and C types.

| Functions                                   | Uses                                 |
| --------------------------------------------|:-----------------------------------: |
| ```C.CString(string) *C.char```             | Converts Go string to C string       |
| ```GoString(*C.char) string```              | Converts C string to Go string       |
| ```GoStringN(*C.char, C.int) string```      | Converts from C data with explicit length to Go String       |
| ```GoBytes(unsafe.Pointer, C.int) []byte``` | Converts C data with explicit length to Go byte       |
| ```CBytes([]byte) unsafe.Pointer```         | Converts from Go byte to C array     |


It is worth noting that Go memory manager is not aware of memories allocated using a C code. This means that these memories are not managed by our garbage collector so we need to explicitly manage our memory. This is one of the price we need to pay for writing Cgo. To avoid nasty bugs and memory leaks, we need to deallocate our memory when it's not in use by calling the ```free()``` function from the C standard library.

Next, we created our buffer by allocating our memory through the use of the C's malloc function from the standard library and giving it our already created size as an argument. We also made sure this allocated memory is freed when our main function returns. Technically, C.malloc does not call the C's malloc directly, instead it calls a cgo helper function which wraps the C's malloc function.

In the next line, we called our hello function from our C code and passed the required arguments. Our buffer is of type ```unsafe.Pointer``` and needs to be type casted to a pointer to char in order to be passed as an argument to the hello function.

Finally, we print our loaded buffer to the Go's standard output. Unfortunately our buffer is of type unsafe.Pointer and once again needs to be type casted to a char and then converted to a Go string before it will be passed as an argument to Go's ```Println()``` function. After this is done, the below message is printed to the console.

```sh
Hello world!!! My first cgo program
```

A quick reminder. Make sure you use cgo when it's the last resort or when you're extremely sure of what you're doing. This is because cgo takes away some wonderful feature of Go such as memory management. Asides that, cgo is wonderful tool and makes creating go bindings for C libraries easy.

We've come to the end of this article. Hope you enjoyed it? If yes, kindly share to impact a life.
