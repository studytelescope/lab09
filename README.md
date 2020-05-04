## Laboratory work IX

[![Build Status](https://travis-ci.org/thedraftaccount/lab09.svg?branch=master)](https://travis-ci.org/thedraftaccount/lab09)

Данная лабораторная работа посвещена изучению процесса создания артефактов на примере **Github Release**

```sh
$ open https://help.github.com/articles/creating-releases/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab09** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Получить токен для доступа к репозиториям сервиса **GitHub**
- [x] 4. Выполнить инструкцию учебного материала
- [x] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Set up global variables

```sh
$ export GITHUB_TOKEN=<полученный_токен>
$ export GITHUB_USERNAME=<имя_пользователя>
$ export PACKAGE_MANAGER=<пакетный менеджер>
$ export GPG_PACKAGE_NAME=<gpg2|gpg>
```

install xclip

```sh
# for *-nix system
$ $PACKAGE_MANAGER install xclip
Reading package lists... Done
Building dependency tree       
Reading state information... Done
xclip is already the newest version (0.12+svn84-4build1).

$ alias gsed=sed
$ alias pbcopy='xclip -selection clipboard'
$ alias pbpaste='xclip -selection clipboard -o'
```

Configure surrounding

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
~/thedraftaccount/workspace
$ source scripts/activate
$ go get github.com/aktau/github-release
```

Get new repo on the basis of the previous one

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
Cloning into 'projects/lab09'...
remote: Enumerating objects: 105, done.
remote: Counting objects: 100% (105/105), done.
remote: Compressing objects: 100% (58/58), done.
remote: Total 105 (delta 28), reused 105 (delta 28), pack-reused 0
Receiving objects: 100% (105/105), 336.93 KiB | 750.00 KiB/s, done.
Resolving deltas: 100% (28/28), done.

$ cd projects/lab09
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
```

Install and config gpg
Then create key and update the list of keys

```sh
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
Reading package lists... Done
Building dependency tree       
Reading state information... Done

$ gpg --list-secret-keys --keyid-format LONG #check existing secret keys
sec   rsa4096/99082BAC09789222 2020-04-16 [SC]
      28A99B939E02770C9621AD3899082BAC09789222
uid                 [ultimate] eugeny uzyanov (github release) <jonnyuz@gmail.com>
ssb   rsa4096/073A0374117F1D84 2020-04-16 [E]

sec   rsa4096/BA3D4820DE67EA33 2020-04-16 [SC]
      0AE8B80A25B9D9737EEE7EE8BA3D4820DE67EA33
uid                 [ultimate] Eugene Uzyanov (help me) <jonnyuz99@gmail.com>
ssb   rsa4096/8E37CC76AB3F67F2 2020-04-16 [E]

$ gpg --full-generate-key #generate ordinary key (because the secret one failed while creating)
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: no writable public keyring found: Not found
Key generation failed: Not found

$ gpg --list-secret-keys --keyid-format LONG #check existing secret keys
$ gpg -K ${GITHUB_USERNAME}

#Highlight public part
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')

#Highlight secret part
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')

$echo ${GPG_KEY_ID} #yay public part!
8E37CC76AB3F67F2

$ gpg --armor --export ${GPG_KEY_ID} #copy info to the system buffer
-----BEGIN PGP PUBLIC KEY BLOCK-----

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
DeJ0mUCqtoSM7Q==
=QJ8O
-----END PGP PUBLIC KEY BLOCK-----

$ open https://github.com/settings/keys
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```

Build and check

```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
cmake --build _build --target package-- Check for working C compiler: /usr/bin/cc
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
-- [hunter] HUNTER_ROOT: /home/johnsnow/.hunter
-- [hunter] [ Hunter-ID: 5659b15 | Toolchain-ID: 9b2c9d4 | Config-ID: 8a1641b ]
-- [hunter] GTEST_ROOT: /home/johnsnow/.hunter/_Base/5659b15/9b2c9d4/8a1641b/Install (ver.: 1.10.0)
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
-- Build files have been written to: /home/johnsnow/thedraftaccount/workspace/projects/lab09/_build

$ cmake --build _build --target package
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target demo
[ 75%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/johnsnow/thedraftaccount/workspace/projects/lab09/_build/print-0.1.0.0-Linux.tar.gz generated.
```

Login and configure Travis

```sh
$ travis login --auto
Successfully logged in as thedraftaccount!

$ travis enable
thedraftaccount/lab09: enabled :)
```
Enter with a digital signature

```sh
$ git tag -s v0.1.0.0
$ git tag -v v0.1.0.0
object cd02c18206c9a7d8053eba1c250c6a2678af7c9c
type commit
tag v0.1.0.0
tagger thedraftaccount <uzyanov.ed@phystech.edu> 1587845662 +0300

v0.1.0.0 updated
gpg: Signature made Сб 25 апр 2020 23:15:07 MSK
gpg:                using RSA key 0AE8B80A25B9D9737EEE7EE8BA3D4820DE67EA33
gpg: Good signature from "Eugene Uzyanov (help me) <jonnyuz99@gmail.com>" [ultimate]

$ git show v0.1.0.0
tag v0.1.0.0
Tagger: thedraftaccount <uzyanov.ed@phystech.edu>
Date:   Sat Apr 25 23:14:22 2020 +0300

v0.1.0.0 updated
-----BEGIN PGP SIGNATURE-----

iQIzBAABCgAdFiEECui4CiW52XN+7n7ouj1IIN5n6jMFAl6kmksACgkQuj1IIN5n
6jNfEw//fHkeKwScIgI5wsDLoi8BBSzwD7ukuPdK7JTOU18PzmeIT6DNudtiQkWX
AMwrtTIuIgX0c5A6w6E4KL/wiMu5gRcSy9YJwmpJbwd5NLRtA7o7UY1HcDtLeRVD
0yoxjlHo3d9tUfIvbHlU3eV27uVyY3Zaufgtpczeq7QW9eMqauUXoMHdd/rHgTL2
N680QboBd9qDjIf2NTKf8jC1DEjBXIuwPsCnEkyk2byrrcG9C6cnFJ4YysvvpYwv
DEopM1DxlOokor4eZ7zWjhzL2mHW96RDXi1ZeZQc8MiL5MJhooClpaKprsJNnLxU
hzXiQH9EU32oMk4FqCWOJxw0DQkr6seU8ybRymTaNV3oLvky8CbYQ8OhtS/cFjh9
v5Ny95D2iGlXQ5TCiA18QeXUX3BcQ75qTygNsE1WlozxGnW9hEYokDaHJksovfvh
/URjLLNYq67ZYRixzo9sBtsaBCzLk1hpuMP3kpQvzxEvGjYT4yxSSyKTTdZpuRor
anlx7udG1lbnWYEJvOuHr6GbuwaUKkspKwkX53s/EkPzlTYEaqiNkd6t+KB73BzM
K/hye08pK0qUIgYa384GocNz9m/xwlVyjvuLqDBk+Zr1lLxSvfgwoi7H37fBLVvR
Bl/N6ShSKUv370BOZMrZ+WDAlg9lHrSDfNyZq0QSngIXzF8rN68=
=l+GV
-----END PGP SIGNATURE-----

commit cd02c18206c9a7d8053eba1c250c6a2678af7c9c (HEAD -> master, tag: v0.1.0.0)

$ git push origin master --tags
```

Make release

```sh
$ github-release --version
github-release v0.7.2

$ github-release info -u ${GITHUB_USERNAME} -r lab09
tags:
- v0.1.0.0 (commit: https://api.github.com/repos/thedraftaccount/lab09/commits/cd02c18206c9a7d8053eba1c250c6a2678af7c9c)
releases:

$  
```

move tar.gz as an arthefact to release and set OS version

```sh
$ export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m` 
$ export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz
$ github-release upload \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "${PACKAGE_FILENAME}" \
    --file _build/*.tar.gz
```

```sh
$ github-release info -u ${GITHUB_USERNAME} -r lab09
tags:
- v0.1.0.0 (commit: https://api.github.com/repos/thedraftaccount/lab09/commits/cd02c18206c9a7d8053eba1c250c6a2678af7c9c)
releases:
- v0.1.0.0, name: 'libprint', description: 'my first release', id: 25884472, tagged: 25/04/2020 at 20:14, published: 25/04/2020 at 20:23, draft: ✗, prerelease: ✗

$ wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}
$ tar -ztf ${PACKAGE_FILENAME}
```

## Report

```sh
$ popd
$ export LAB_NUMBER=09
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Create Release](https://help.github.com/articles/creating-releases/)
- [Get GitHub Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
- [Signing Commits](https://help.github.com/articles/signing-commits-with-gpg/)
- [Go Setup](http://www.golangbootcamp.com/book/get_setup)
- [github-release](https://github.com/aktau/github-release)

```
Copyright (c) 2015-2020 The ISC Authors
```
