## Laboratory work VIII

[![Build Status](https://travis-ci.org/thedraftaccount/lab08.svg?branch=master)](https://travis-ci.org/thedraftaccount/lab08)

Данная лабораторная работа посвещена изучению систем автоматизации развёртывания и управления приложениями на примере **Docker**

```sh
$ open https://docs.docker.com/get-started/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab08** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя> # set username
```

```sh
# set environment
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab07 lab08 # get previous lab as a template
Cloning into 'lab08'...
remote: Enumerating objects: 95, done.
remote: Counting objects: 100% (95/95), done.
remote: Compressing objects: 100% (51/51), done.
remote: Total 95 (delta 27), reused 95 (delta 27), pack-reused 0
Unpacking objects: 100% (95/95), done.

$ cd lab08
$ git submodule update --init  # update submodule recursively
Submodule 'tools/polly' (https://github.com/ruslo/polly) registered for path 'tools/polly'
Cloning into '/home/johnsnow/thedraftaccount/workspace/lab08/tools/polly'...
Submodule path 'tools/polly': checked out '0b3806e193b668fbb9b70c9aa70735b29396323d'

$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab08
```

```sh
# base image
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```

```sh
# update initial packets and install compiler
$ cat >> Dockerfile <<EOF

RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

```sh
# copy current dir to print/
$ cat >> Dockerfile <<EOF

COPY . print/
WORKDIR print
EOF
```

```sh
# make execute cmake targets
$ cat >> Dockerfile <<EOF

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

```sh
# set new env variable
$ cat >> Dockerfile <<EOF

ENV LOG_PATH /home/logs/log.txt
EOF
```

```sh
# set a place of files storing
$ cat >> Dockerfile <<EOF

VOLUME /home/logs
EOF
```

```sh
# set new work dir
$ cat >> Dockerfile <<EOF

WORKDIR _install/bin
EOF
```

```sh
# set the point of entry
$ cat >> Dockerfile <<EOF

ENTRYPOINT ./demo
EOF
```

```sh
# build all
$ docker build -t logger .
Removing intermediate container b1039005a762
 ---> 5336c0d8a3ba
Step 11/12 : WORKDIR _install/bin
 ---> Running in bd081c463be1
Removing intermediate container bd081c463be1
 ---> 4f9ed27b78ff
Step 12/12 : ENTRYPOINT ./demo
 ---> Running in d5d8d75588a0
Removing intermediate container d5d8d75588a0
 ---> 6c16d4af4878
Successfully built 6c16d4af4878
Successfully tagged logger:latest
```

```sh
# which images do we have?
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
logger              latest              db2086b06c88        4 minutes ago       344MB
ubuntu              18.04               4e5021d210f6        4 weeks ago         64.2MB
```

```sh
$ mkdir logs
# run logger
$ docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
<C-D>

$ cat logs/log.txt 
text1
text2
text3
<C-D>
```

```sh
# provide detailed information on constructs controlled by Docker
$ docker inspect logger
[sudo] password for johnsnow: 
snowSorry, try again.
[sudo] password for johnsnow: 
[
    {
        "Id": "sha256:db2086b06c8882191ee5c5a3668bf938e92c2dff5c0f744ebbe58b68545cb085",
        "RepoTags": [
            "logger:latest"
        ],
        "RepoDigests": [],
        "Parent": "sha256:dadcefa415774acce295defe9bb5d944ba6a764587029aa093f2561458ed69c3",
        "Comment": "",
        "Created": "2020-04-22T15:33:21.823607437Z",
        "Container": "15cbeeb8ef38545213c1b53b4306615954e77dd59de69c354520f8c128ecc726",
        "ContainerConfig": {
            "Hostname": "15cbeeb8ef38",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LOG_PATH=/home/logs/log.txt"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "ENTRYPOINT [\"/bin/sh\" \"-c\" \"./demo\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:dadcefa415774acce295defe9bb5d944ba6a764587029aa093f2561458ed69c3",
            "Volumes": {
                "/home/logs": {}
            },
            "WorkingDir": "/print/_install/bin",
            "Entrypoint": [
                "/bin/sh",
                "-c",
                "./demo"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "18.09.9",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LOG_PATH=/home/logs/log.txt"
            ],
            "Cmd": null,
            "ArgsEscaped": true,
            "Image": "sha256:dadcefa415774acce295defe9bb5d944ba6a764587029aa093f2561458ed69c3",
            "Volumes": {
                "/home/logs": {}
            },
            "WorkingDir": "/print/_install/bin",
            "Entrypoint": [
                "/bin/sh",
                "-c",
                "./demo"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 344488238,
        "VirtualSize": 344488238,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/snap/docker/common/var-lib-docker/overlay2/8da7ed75fbbd06ecfa2e4e34f030edab75990be171ed38af7333f552896ff5c0/diff:/var/snap/docker/common/var-lib-docker/overlay2/f6ad1eb0486da6c2e8c0c33afa7b2e2c141268f4df4974c216fb7ddf2f3a79bb/diff:/var/snap/docker/common/var-lib-docker/overlay2/9700fcb91269b55b40fdd96af3ee250414bcea9acc41f9744b797db694a38cff/diff:/var/snap/docker/common/var-lib-docker/overlay2/7870df55cb53ac2c62abe52ded78180c4f0b9af7501929b4f988ff41806736a7/diff:/var/snap/docker/common/var-lib-docker/overlay2/29fc5fc37bf245932b1cf78d734cc1c89c57a14f8db326cfaaa3939e8aafd3d4/diff:/var/snap/docker/common/var-lib-docker/overlay2/a2125b4bf75c591bbf61af868c669be4e5c3ed023501a8e769b1909a3a2000a2/diff:/var/snap/docker/common/var-lib-docker/overlay2/6437f96db9224ef2df6554bc4da4da28801e5a71fe628106072c13a5bd4589d0/diff:/var/snap/docker/common/var-lib-docker/overlay2/9faf864434ae28489997f5b734408c09171db7bc34fcc5ac5a367fd7208b8fe8/diff:/var/snap/docker/common/var-lib-docker/overlay2/39ef8fc069730bcea95b710783c95f6e2627fa80ec346b581e9ef28b2d0c59d5/diff",
                "MergedDir": "/var/snap/docker/common/var-lib-docker/overlay2/b40190fd730332622557ea76cdac2b782e2d670de55cd51717b46ebcbc1889b6/merged",
                "UpperDir": "/var/snap/docker/common/var-lib-docker/overlay2/b40190fd730332622557ea76cdac2b782e2d670de55cd51717b46ebcbc1889b6/diff",
                "WorkDir": "/var/snap/docker/common/var-lib-docker/overlay2/b40190fd730332622557ea76cdac2b782e2d670de55cd51717b46ebcbc1889b6/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:c8be1b8f4d60d99c281fc2db75e0f56df42a83ad2f0b091621ce19357e19d853",
                "sha256:977183d4e9995d9cd5ffdfc0f29e911ec9de777bcb0f507895daa1068477f76f",
                "sha256:6597da2e2e52f4d438ad49a14ca79324f130a9ea08745505aa174a8db51cb79d",
                "sha256:16542a8fc3be1bfaff6ed1daa7922e7c3b47b6c3a8d98b7fca58b9517bb99b75",
                "sha256:dbc2d6a98f6805c905100570a09398fd335cebb6a306b0a38621980694d7868d",
                "sha256:79cf218108ef187d9627e8c20b0c2116be60f0779276afa6f9ea8fd22add385a",
                "sha256:2299832b7588e0c19e871670c760cb2a92651cf01ddc6df4d22898ad3973d506",
                "sha256:4d2a201fbc33e0c341e63416a9b14fb94db56c699e2b334db4a02666177fbc58",
                "sha256:e90fdcae1b478b4ca25831e1162b9db4dfc6a73ccfabc6689e26a6cdfdfaaa3d",
                "sha256:fd21df07a1ed4372bd02cca9e8d0a761be78675ef499bf589c526de4dacf11c6"
            ]
        },
        "Metadata": {
            "LastTagTime": "2020-04-22T18:33:22.469979041+03:00"
        }
    }
]

```

```sh
$ cat logs/log.txt
text1
text2
text3
<C-D>
```

```sh
$ gsed -i 's/lab07/lab08/g' README.md
```

```sh
# create new travis instruction
$ vim .travis.yml
/lang<CR>o
services:
- docker<ESC>
jVGdo
script:
- docker build -t logger .<ESC>
:wq
```

```sh
# blablabla
$ git add Dockerfile
$ git add .travis.yml
$ git commit -m"adding Dockerfile"
[master 9632aa9] adding Dockerfile
 2 files changed, 23 insertions(+), 13 deletions(-)
 create mode 100644 Dockerfile

$ git push origin master
```

```sh
$ travis login --auto
Successfully logged in as thedraftaccount!
$ travis enable
Detected repository as thedraftaccount/lab08, is this correct? |yes| yes
thedraftaccount/lab08: enabled :)
```

## Report

```sh
$ popd
$ export LAB_NUMBER=08
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [Book](https://www.dockerbook.com)
- [Instructions](https://docs.docker.com/engine/reference/builder/)

```
Copyright (c) 2015-2019 The ISC Authors
```