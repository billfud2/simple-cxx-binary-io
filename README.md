# Simple C++ binary I/O

`binary_io.hpp` is a header providing sane low-level binary I/O functions in C++. Download and `#include` it to get access to `[try_]read_bytes` and `[try_]write_bytes`. **The C++20 standard is required**.

`read_bytes` and `try_read_bytes` read binary data from `std::istream`s, `write_bytes` and `try_write_bytes` write binary data to `std::ostream`s.

The `try_*` methods throw `std::ios_base::failure` if reading or writing fails.



## Methods

```c++
bool [try_]read_bytes( [std::istream &s, ]      auto *const data[, const size_t size])
bool [try_]write_bytes([std::ostream &s, ]const auto *const data[, const size_t size])

bool [try_]read_bytes( [std::istream &s, ]      auto *const data...)
bool [try_]write_bytes([std::ostream &s, ]const auto *const data...)

T [try_]read_bytes<T>([istream &s])
```

## Arguments

`s`: the `std::istream` from which to read, or the `std::ostream` to which to write (optional, default `std::cin` for reading, `std::cout` for writing)

`data`: a pointer (or pointers, if `size` isn't specified) to the element(s) to store the data to be read in, or from which to take data to be written out

`size`: the number of elements (*not* bytes) to read in or write out (optional, default 1)

## Return Value

Methods returning `bool` return `true` if the read was successful, `false` otherwise. The template version taking a type returns a value of type `T`.

## Examples

Open the file `vals.dat` and read two values, an `int` and a `float`, from it:

```c++
int i; float f;
std::ifstream is("vals.dat");
bool read_success = read_bytes(is, &i, &f);
```

Read a 3x6 array of `float`s from stdin:
```c++
float a[3][6];
bool read_success = read_bytes(&a, 3*6);
```

Read a `size_t` from stdin, then read that many `float`s into a `std::vector`:
```c++
auto n = read_bytes<size_t>();
std::vector<float> v(n);
bool read_success = read_bytes(v.data(), n);
```

A `struct` with constructor that reads an `int` then that number of `floats` into a vector, throwing if the read fails:
```c++
struct MyStruct {
    const int n; // members MUST be arranged in the order that they are read in
    std::vector<float> v;
    MyStruct(std::istream &s): n{try_read_bytes<int>(s)}, v(n) {
        try_read_bytes(s, v.data(), n);
    }
};
```

Write the contents of `std::vector` `v` to the file `v.dat`, throwing if the write fails:

```c++
std::ofstream f("v.dat");
try_write_bytes(f, v.data(), v.size());
```

Generate 3 random `int`s and write them to stdout:

```c++
int x = rand(), y = rand(), z = rand();
bool write_success = write_bytes(&x, &y, &z);
```
