## Laboratory work VII

[![Build Status](https://travis-ci.org/thedraftaccount/lab07.svg?branch=master)](https://travis-ci.org/thedraftaccount/lab07)

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```sh
$ open https://github.com/ruslo/hunter
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
# Set default username and editor
$ export GITHUB_USERNAME=<имя_пользователя>
$ alias gsed=sed
```

```sh
# Get into the workspace and so on
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07 # Copy the previous lab
Cloning into 'projects/lab07'...
remote: Enumerating objects: 70, done.
remote: Counting objects: 100% (70/70), done.
remote: Compressing objects: 100% (38/38), done.
remote: Total 70 (delta 21), reused 70 (delta 21), pack-reused 0
Unpacking objects: 100% (70/70), done.

$ cd projects/lab07
$ git remote remove origin # Remove default server
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07 # Add new remote server
```

```sh
$ mkdir -p cmake #Make new folder
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake #Get a hunter package gate file
--2020-04-21 20:27:06--  https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.192.133, 151.101.128.133, 151.101.64.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.192.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17070 (17K) [text/plain]
Saving to: ‘cmake/HunterGate.cmake’

cmake/HunterGate.cmake            100%[=============================================================>]  16,67K  --.-KB/s    in 0,04s   

2020-04-21 20:27:06 (412 KB/s) - ‘cmake/HunterGate.cmake’ saved [17070/17070]

#Add the strings required by hunter
$ gsed -i '/cmake_minimum_required(VERSION 3.4)/a\ 

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```

```sh
$ git rm -rf third-party/gtest # rm subdir
$ gsed -i '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt 
$ gsed -i 's/add_subdirectory(third-party/gtest)//' CMakeLists.txt # rm subdir
$ gsed -i 's/gtest_main/GTest::gtest_main/' CMakeLists.txt # add namespace
```

```sh
$ cmake -H. -B_builds -DBUILD_TESTS=ON # init hunter workspace
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/johnsnow/.hunter
-- [hunter] [ Hunter-ID: 5659b15 | Toolchain-ID: 9b2c9d4 | Config-ID: 8a1641b ]
-- [hunter] GTEST_ROOT: /home/johnsnow/.hunter/_Base/5659b15/9b2c9d4/8a1641b/Install (ver.: 1.10.0)
-- Configuring done
-- Generating done
-- Build files have been written to: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds

$ cmake --build _builds # build the project
[ 50%] Built target print
Scanning dependencies of target check
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check

$ cmake --build _builds --target test # run tests
Running tests...
Test project /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec

$ ls -la $HOME/.hunter # show hunter dir
total 12
drwxr-xr-x  3 johnsnow johnsnow 4096 апр  4 09:58 .
drwxr-xr-x 41 johnsnow johnsnow 4096 апр 21 15:23 ..
drwxr-xr-x  6 johnsnow johnsnow 4096 апр  4 09:58 _Base
```

```sh
$ git clone https://github.com/cpp-pm/hunter $HOME/projects/hunter # Get hunter for tests
$ export HUNTER_ROOT=$HOME/projects/hunter # path to the hunter
$ rm -rf _builds # blabla
$ cmake -H. -B_builds -DBUILD_TESTS=ON # build and run for tests
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/johnsnow/projects/hunter
-- [hunter] [ Hunter-ID: xxxxxxx | Toolchain-ID: 9b2c9d4 | Config-ID: 2f6b703 ]
-- [hunter] GTEST_ROOT: /home/johnsnow/projects/hunter/_Base/xxxxxxx/9b2c9d4/2f6b703/Install (ver.: 1.10.0)
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Looking for pthread_create
-- Looking for pthread_create - not found
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds

$ cmake --build _builds #build
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target check
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check

$ cmake --build _builds --target test # run tests
Running tests...
Test project /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec
```

```sh
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest # show hunter version
 grep GTest
  hunter_default_version(GTest VERSION 1.7.0-hunter-6)
  hunter_default_version(GTest VERSION 1.10.0)

$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake # hunter.cmake file
# Copyright (c) 2013, Ruslan Baratov
# All rights reserved.

# !!! DO NOT PLACE HEADER GUARDS HERE !!!

include(hunter_add_version)
include(hunter_cacheable)
include(hunter_download)
include(hunter_pick_scheme)
include(hunter_cmake_args)

...

hunter_pick_scheme(DEFAULT url_sha1_cmake)
hunter_cacheable(GTest)
hunter_download(PACKAGE_NAME GTest PACKAGE_INTERNAL_DEPS_ID 1)

$ mkdir cmake/Hunter
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
# add LOCAL in HunterGate function
```

```sh
$ mkdir demo # create test
$ cat > demo/main.cpp <<EOF
#include <print.hpp>

#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF

$ gsed -i '/endif()/a\ # add target demo

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
' CMakeLists.txt
```

```sh
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly #Add submodule
Cloning into '/home/johnsnow/thedraftaccount/workspace/projects/lab07/tools/polly'...
remote: Enumerating objects: 29, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 6423 (delta 10), reused 24 (delta 10), pack-reused 6394
Receiving objects: 100% (6423/6423), 1.64 MiB | 60.00 KiB/s, done.
Resolving deltas: 100% (4418/4418), done.

$ tools/polly/bin/polly.py --test
Python version: 3.7
Build dir: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.10.2

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default`
  `-DCMAKE_TOOLCHAIN_FILE=/home/johnsnow/thedraftaccount/workspace/projects/lab07/tools/polly/default.cmake`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "cmake" "-H." "-B/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/home/johnsnow/thedraftaccount/workspace/projects/lab07/tools/polly/default.cmake"

-- [polly] Used toolchain: Default
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/johnsnow/projects/hunter
-- [hunter] [ Hunter-ID: xxxxxxx | Toolchain-ID: 9b2c9d4 | Config-ID: 2f6b703 ]
-- [hunter] GTEST_ROOT: /home/johnsnow/projects/hunter/_Base/xxxxxxx/9b2c9d4/2f6b703/Install (ver.: 1.10.0)
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Looking for pthread_create
-- Looking for pthread_create - not found
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE
-- Configuring done
-- Generating done
-- Build files have been written to: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default
Execute command: [
  `cmake`
  `--build`
  `/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default`
  `--`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "cmake" "--build" "/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default" "--"

Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target demo
[ 75%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Run tests
Execute command: [
  `ctest`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default]> "ctest"

*********************************
No test configuration file found!
*********************************
Usage

  ctest [options]

-
Log saved: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:05.158626s
Build: 0:00:01.856485s
Test: 0:00:00.043737s
-
Total: 0:00:07.059205s
-
SUCCESS

$ tools/polly/bin/polly.py --install
Python version: 3.7
Build dir: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.10.2

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `--build`
  `/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default`
  `--`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07]> "cmake" "--build" "/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default" "--"

[ 50%] Built target print
[100%] Built target demo
Run tests
Execute command: [
  `ctest`
]

[/home/johnsnow/thedraftaccount/workspace/projects/lab07/_builds/default]> "ctest"

*********************************
No test configuration file found!
*********************************
Usage

  ctest [options]

-
Log saved: /home/johnsnow/thedraftaccount/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:00.000307s
Build: 0:00:01.114617s
Test: 0:00:00.024501s
-
Total: 0:00:01.139709s
-
SUCCESS
```

## Report

```sh
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

### Задание
1. Создайте cвой hunter-пакет.

## Links

- [Create Hunter package](https://docs.hunter.sh/en/latest/creating-new/create.html)
- [Custom Hunter config](https://github.com/ruslo/hunter/wiki/example.custom.config.id)
- [Polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2020 The ISC Authors
```