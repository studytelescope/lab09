## Laboratory work V

[![Build Status](https://travis-ci.org/thedraftaccount/lab05.svg?branch=master)](https://travis-ci.org/thedraftaccount/lab05)

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

![alt text](https://github.com/thedraftaccount/lab05/tree/master/artifacts)

```ShellSession
$ open https://github.com/google/googletest
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
# Setting global variables and stream editors
$ export GITHUB_USERNAME=thedraftaccount
$ alias gsed=sed # for *-nix system
```

```ShellSession
# Working directory organization
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```ShellSession
# Cloning of existing remote repo
$ git clone https://github.com/${GITHUB_USERNAME}/lab04 projects/lab05
Cloning into 'projects/lab05'...
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 24 (delta 3), reused 24 (delta 3), pack-reused 0
Unpacking objects: 100% (24/24), done.

$ cd projects/lab05
# Pushing into remote
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05
```

```ShellSession
# Create a new directory
$ mkdir third-party

# Create submodule
$ git submodule add https://github.com/google/googletest third-party/gtest
Cloning into '/home/johnsnow/thedraftaccount/workspace/projects/lab05/third-party/gtest'...
remote: Enumerating objects: 20049, done.
remote: Total 20049 (delta 0), reused 0 (delta 0), pack-reused 20049
Receiving objects: 100% (20049/20049), 7.33 MiB | 1.72 MiB/s, done.
Resolving deltas: 100% (14816/14816), done.


# Getting into new repo and checking a version
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../..

Note: checking out 'release-1.8.1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 2fe3bd99 Merge pull request #1433 from dsacre/fix-clang-warnings

So, i guess i should switch a branch!

$ git add third-party/gtest # Adding a repo and commiting changes
$ git commit -m"added gtest framework" # Add changes
[master efb2c9c] added gtest framework
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

```ShellSession
# Editing of "CMakeLists"
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check \${PROJECT_NAME} gtest_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```

```ShellSession
# Creating a repo for tests and adding test1.cpp programm
$ mkdir tests
$ cat > tests/test1.cpp <<EOF
#include <print.hpp>

#include <gtest/gtest.h>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};

  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;

  EXPECT_EQ(result, text);
}
EOF
```

```ShellSession
# Building with test included
$ cmake -H. -B_build -DBUILD_TESTS=ON
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
-- Found PythonInterp: /home/johnsnow/anaconda3/bin/python (found version "3.7.4") 
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Looking for pthread_create
-- Looking for pthread_create - not found
-- Check if compiler accepts -pthread
-- Check if compiler accepts -pthread - yes
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/johnsnow/thedraftaccount/workspace/projects/lab05/_build

# Building with output the result
$ cmake --build _build
Scanning dependencies of target gtest
[  8%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 16%] Linking CXX static library libgtest.a
[ 16%] Built target gtest
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 33%] Linking CXX static library libprint.a
[ 33%] Built target print
Scanning dependencies of target gtest_main
[ 41%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library libgtest_main.a
[ 50%] Built target gtest_main
Scanning dependencies of target check
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
Scanning dependencies of target gmock
[ 75%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 83%] Linking CXX static library libgmock.a
[ 83%] Built target gmock
Scanning dependencies of target gmock_main
[ 91%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library libgmock_main.a
[100%] Built target gmock_main

# Buliding with output of test results
$ cmake --build _build --target test
Running tests...
Test project /home/johnsnow/thedraftaccount/workspace/projects/lab05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.02 sec
```

```ShellSession
# Checking
$ _build/check
Running main() from /home/johnsnow/thedraftaccount/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from Print
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (0 ms)
[----------] 1 test from Print (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (0 ms total)
[  PASSED  ] 1 test.


# Tests launch
$ cmake --build _build --target test -- ARGS=--verbose
Running tests...
UpdateCTestConfiguration  from :/home/johnsnow/thedraftaccount/workspace/projects/lab05/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/home/johnsnow/thedraftaccount/workspace/projects/lab05/_build/DartConfiguration.tcl
Test project /home/johnsnow/thedraftaccount/workspace/projects/lab05/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: check

1: Test command: /home/johnsnow/thedraftaccount/workspace/projects/lab05/_build/check
1: Test timeout computed to be: 9.99988e+06
1: Running main() from /home/johnsnow/thedraftaccount/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
1: [==========] Running 1 test from 1 test case.
1: [----------] Global test environment set-up.
1: [----------] 1 test from Print
1: [ RUN      ] Print.InFileStream
1: [       OK ] Print.InFileStream (1 ms)
1: [----------] 1 test from Print (1 ms total)
1: 
1: [----------] Global test environment tear-down
1: [==========] 1 test from 1 test case ran. (1 ms total)
1: [  PASSED  ] 1 test.
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec

```

```ShellSession
# Writing into README.md
$ gsed -i 's/lab04/lab05/g' README.md
# Tests are on
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml
$ gsed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```ShellSession
# Linter launch
$ travis lint
Hooray, .travis.yml looks valid :)
```

```ShellSession
# Commiting of all changes and pushing into remote repo
$ git add .travis.yml
$ git add tests
$ git add -p
diff --git a/CMakeLists.txt b/CMakeLists.txt
index 96a361e..aa7a323 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -4,6 +4,7 @@ set(CMAKE_CXX_STANDARD 11)
 set(CMAKE_CXX_STANDARD_REQUIRED ON)
 
 option(BUILD_EXAMPLES "Build examples" OFF)
+option(BUILD_TESTS "Build tests" OFF)
 
 project(print)
 
Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? y
@@ -34,3 +35,12 @@ install(TARGETS print
 
 install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
 install(EXPORT print-config DESTINATION cmake)
+
+if(BUILD_TESTS)
+  enable_testing()
+  add_subdirectory(third-party/gtest)
+  file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
+  add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
+  target_link_libraries(check ${PROJECT_NAME} gtest_main)
+  add_test(NAME check COMMAND check)
+endif()
Stage this hunk [y,n,q,a,d,K,g,/,e,?]? y

diff --git a/README.md b/README.md
index 58441f1..d54cc28 100644
--- a/README.md
+++ b/README.md
@@ -1,6 +1,6 @@
 ## Laboratory work IV
 
-[![Build Status](https://travis-ci.com/thedraftaccount/lab04.svg?branch=master)](https://travis-ci.com/thedraftaccount/lab04)
+[![Build Status](https://travis-ci.com/thedraftaccount/lab05.svg?branch=master)](https://travis-ci.com/thedraftaccount/lab05)
 
 Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**
 
Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? git commit -m"added tests"
Invalid number: 'it commit -m"added tests"'
@@ -1,6 +1,6 @@
 ## Laboratory work IV
 
-[![Build Status](https://travis-ci.com/thedraftaccount/lab04.svg?branch=master)](https://travis-ci.com/thedraftaccount/lab04)
+[![Build Status](https://travis-ci.com/thedraftaccount/lab05.svg?branch=master)](https://travis-ci.com/thedraftaccount/lab05)
 
 Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**
 
$ git commit -m"added tests"
[master 65cae9f] added tests
 4 files changed, 41 insertions(+), 11 deletions(-)
 create mode 100644 tests/test1.cpp

$ git push origin master
```

```ShellSession
# Travis authorization and project adding
$ travis login --auto
$ travis enable
Detected repository as thedraftaccount/lab05, is this correct? |yes| yes    
thedraftaccount/lab05: enabled :)
```

```ShellSession
# Weird repo
$ mkdir artifacts
# Sleep for 20 sec, making and saving of a sreenshot
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png
# for macOS: $ screencapture -T 20 artifacts/screenshot.png
# open https://github.com/${GITHUB_USERNAME}/lab05
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=05
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

### Задание
1. Создайте `CMakeList.txt` для библиотеки *banking*.
2. Создайте модульные тесты на классы `Transaction` и `Account`.
    * Используйте mock-объекты.
    * Покрытие кода должно составлять 100%.
3. Настройте сборочную процедуру на **TravisCI**.
4. Настройте [Coveralls.io](https://coveralls.io/).

## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2019 The ISC Authors
```