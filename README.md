<img src="http://numenta.org/87b23beb8a4b7dea7d88099bfb28d182.svg" alt="NuPIC Logo" width=100/>

# NuPIC C++ Core Library
[![Linux/OSX Build Status](https://travis-ci.org/htm-community/nupic.cpp.svg?branch=master)](https://travis-ci.org/htm-community/nupic.cpp)  
[![OSX CircleCI](https://circleci.com/gh/htm-community/nupic.cpp/tree/master.svg?style=svg)](https://circleci.com/gh/htm-community/nupic.cpp/tree/master) 
[![Build status](https://ci.appveyor.com/api/projects/status/59f87and1x0ugss9?svg=true)](https://ci.appveyor.com/project/htm-community/nupic-cpp)

## Community NuPIC.cpp (former nupic.core) repository

This fork is a community version of the `nupic.core` C++ repository with Python bindings. 
Our aim is to provide an actively developed successor to the nupic.core and nupic repositories by Numenta, 
which are not actively developed anymore. 

This repository contains the C++ source code for the Numenta Platform for 
Intelligent Computing ([NuPIC](http://numenta.org/nupic.html)). 
It will eventually contain all algorithms for NuPIC, but is currently in a transition period. 

The objective is to stay as close as possible to the [Nupic API Docs](http://nupic.docs.numenta.org/stable/api/index.html) 
with the aim that we don't break .py code written against the numenta's nupic.core extension library if they were to be 
ran against this extention library.

Some of the major differences between this library and Numenta's extension library are the following:

 * Support for Python 2.7 and Python 3.x (work in progress)
 * Support for Linux, OSx, and Windows MS Visual Studio 2017
 * Support for C++11, C++17 (work in progress)
 * Replaced SWIG with PyBind11 for Python interface.
 * Removed CapnProto serialization.  It was prevasive and complicated the code considerably. It was replaced 
 with simple binary streaming serialization.
 * Many code optimizations.

## Building from Source

Fork or download the HTM-Community Nupic.cpp repository from https://github.com/htm-community/nupic.cpp

### Prerequisites

- [Python](https://python.org/downloads/)
    - Version 2.7  We recommend you use the latest 2.7 version where possible. But the system version should be fine.
    - Version 3.6+   (work in progress.  The Nupic Python code will need to be upgraded as well before this will be useful.
- [CMake](http://www.cmake.org/)

### Simple Source Installation 


The easiest way to build from source is as follows. 

    python setup.py install --user -prefix=

Optionally include `--user` or other flags to determine where the package is installed.

### Testing the Installation

Regardless of how you install `nupic.bindings`, the `nupic-bindings-check` command-line script should be installed. Make sure that you include the Python `bin` installation location in your `PATH` environment variable and then execute the script:

    nupic-bindings-check

### Developer Installation

This option is for developers that would like the ability to do incremental builds of the C++ or for those that are using the C++ libraries directly.

> **Note**: The following sub-sections are related to Linux and OSX only. For Windows refer to the `external\windows64-gcc\README.md` file.

#### Configure and generate C++ build files:

    mkdir -p $NUPIC_CORE/build/scripts
    cd $NUPIC_CORE/build/scripts
    cmake $NUPIC_CORE -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../release -DPY_EXTENSIONS_DIR=$NUPIC_CORE/bindings/py/src/nupic/bindings

Notes:

- This will generate Release build files. For a debug build, change `-DCMAKE_BUILD_TYPE` to `Debug`.
- To build nupic.core for generating the nupic.bindings python extension, pass `-DNUPIC_BUILD_PYEXT_MODULES=ON`; it is the default at this time.
- To build nupic.core as a standalone static library, pass `-DNUPIC_BUILD_PYEXT_MODULES=OFF`.
- If you have dependencies precompiled but not in standard system locations then you can specify where to find them with `-DCMAKE_PREFIX_PATH` (for bin/lib) and `-DCMAKE_INCLUDE_PATH` (for header files).
- The `-DCMAKE_INSTALL_PREFIX=../release` option shown above is optional, and specifies the location where `nupic.core` should be installed. If omitted, `nupic.core` will be installed in a system location. Using this option is useful when testing versions of `nupic.core` language bindings in [`bindings`](bindings).
- Setting `-DPY_EXTENSIONS_DIR` copies the Python exension files to the specified directory. If the extensions aren't present when the Python build/installation is invoked then the setup.py file will run the cmake/make process to generate them. Make sure to include this flag if you want to do incremental builds of the Python extensions.
- On OSX with multiple Python installs (e.g. via brew) cmake might erroneously pick various pieces from different installs which will likely produce abort trap at runtime. Remove cmake cache and re-run cmake with  `-DPYTHON_LIBRARY=/path/to/lib/libpython2.7.dylib` and  `-DPYTHON_INCLUDE_DIR=/path/to/include/python2.7` options to override with desired Python install path.
- To use Include What You Use during compilation, pass `-DNUPIC-IWYU=ON`. This requires that IWYU is installed and findable by CMake, with a minimum CMake version of 3.3. IWYU can be installed from https://include-what-you-use.org/ for Windows and Linux, and on OS X using https://github.com/jasonmp85/homebrew-iwyu.
- If you would like to install all headers, libraries, and executables to the install location for C++ clients when building the nupic.binding python extensions, pass `-NUPIC_TOGGLE_INSTALL=ON`.


#### Build:

    # While still in $NUPIC_CORE/build/scripts
    make -j3

> **Note**: The `-j3` option specifies '3' as the maximum number of parallel jobs/threads that Make will use during the build in order to gain speed. However, you can increase this number depending your CPU.

#### Install:

    # While still in $NUPIC_CORE/build/scripts
    make install

#### Run the tests:

    cd $NUPIC_CORE/build/release/bin
    ./unit_tests
    ...

#### Install nupic.bindings Python library:

    cd $NUPIC_CORE
    python setup.py develop

> **Note**: If the extensions haven't been built already then this will call the cmake/make process to generate them.

If you get a gcc exit code 1, you may consider running this instead:

     python setup.py develop --user

If you are installing on Mac OS X, you must add the instruction `ARCHFLAGS="-arch x86_64"` before the python call:

    ARCHFLAGS="-arch x86_64" python setup.py develop

Alternatively, you can use the `install` command (as opposed to `develop`) to copy the installation files rather than link to the source location.

    python setup.py install

> _Note_: If you get a "permission denied" error when using the setup commands above, you may add the `--user` flag to install to a location in your home directory, which should resolve any permissions issues. Doing this, you may need to add this location to your PATH and PYTHONPATH.

Once it is installed, you can import NuPIC bindings library to your python script using:

    import nupic.bindings

You can run the nupic.bindings tests via `setup.py`:

    python setup.py test

### Using graphical interface

#### Generate the IDE solution:

 * Open CMake executable.
 * Specify the source folder (`$NUPIC_CORE/src`).
 * Specify the build system folder (`$NUPIC_CORE/build/scripts`), i.e. where IDE solution will be created.
 * Click `Generate`.
 * Choose the IDE that interest you (remember that IDE choice is limited to your OS, i.e. Visual Studio is available only on CMake for Windows).

#### Build:

 * Open `nupic_core.*proj` solution file generated on `$NUPIC_CORE/build/scripts`.
 * Run `ALL_BUILD` project from your IDE.

#### Run the tests:

 * Run any `tests_*` project from your IDE (check `output` panel to see the results).

## Build Documentation

Run doxygen, optionally specifying the version as an environment variable:

    PROJECT_VERSION=`cat VERSION` doxygen

The results will be written out to the `html` directory. 

