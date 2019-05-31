# Automate-VCPKG

While [Vcpkg](https://github.com/microsoft/vcpkg) on it's own is awesome, it does add a little bit of complexity to getting a project to build. Even more if the one trying to compile your application is not too fond of the commandline. Additionally, CMake commands tend to get rather long with the toolchain path. 

To keep things simple for new users who just want to get the project to build, this script offers a solution.

Lets assume your main `CMakelists.txt` looks something like this:

```cmake
cmake_minimum_required (VERSION 3.12.0)
project (MyProject)

add_executable(MyExecutable main.c)
```

To integrate Vcpkg into that `CMakelists.txt`, simple put the following lines before the call to `project(MyProject)`:

```cmake
include(cmake/automate-vcpkg.cmake)

vcpkg_bootstrap()
vcpkg_install_packages(libsquish physfs)
```
The call to `vcpkg_bootstrap()` will clone the official Vcpkg repository and bootstrap it. If it detected an existing environment variable defining a valid `VCPKG_ROOT`, it will update the existing installation of Vcpkg.

Arguments to `vcpkg_install_packages()` are the packages you want to install using Vcpkg.

If you want to keep the possibility for users to chose their own copy of Vcpkg, you can simply not run the code snippet mentioned above, something like this will work:

```cmake
option(SKIP_AUTOMATE_VCPKG "When ON, you will need to built the packages 
 required by MyProject on your own or supply your own vcpkg toolchain.")

if (NOT SKIP_AUTOMATE_VCPKG)
  include(cmake/automate-vcpkg.cmake)

  vcpkg_bootstrap()
  vcpkg_install_packages(libsquish physfs)
endif()
```

Then, the user has to supply the packages on their own, be it through Vcpkg or manually specifying their locations.


## Acknowledgments

Automate-VCPKG was based on https://github.com/sutambe/cpptruths/blob/vcpkg_cmake_blog/cpp0x/vcpkg_test/CMakeLists.txt.
