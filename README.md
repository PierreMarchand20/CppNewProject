# CppNewProject

## Developer guide

### Build

We use `CMake` to generate a build-system (`make`, `ninja`, `xcode`,...). Typically, to build your build-system in the folder `my_build`

```bash
cmake -B my_build
cmake --build my_build
```

Alternatively, in the `my_build` folder, you can call directly your generator, for example using `make`.

See [Introduction to Modern CMake](https://cliutils.gitlab.io/modern-cmake/chapters/intro/running.html#picking-a-generator) for more information.

### Sanitizers

You can build using [sanitizers](https://hpc-wiki.info/hpc/Compiler_Sanitizers). For example,

```bash 
cmake -B my_build/ -DUSE_SANITIZER=Address
```

### Test

In the folder where you generated your build-system, you can run all tests with

```bash
ctest
```
See [ctest documentation](https://cmake.org/cmake/help/latest/manual/ctest.1.html).


### Formatters

Formatters can usually be configured to be applied on save in your favorite IDE, you can also call them via the command line, or use `make format` in your build folder.

- For `C++`, we use [clang-format](https://clang.llvm.org/docs/ClangFormat.html). The format can be configured in `.clang-format`.
- For `CMake`, we use [cmake-format](https://cmake-format.readthedocs.io/en/latest/). The format can be configured in `cmake-format.py`.
