## Laboratory work IV

[![Build Status](https://travis-ci.com/thedraftaccount/lab04.svg?branch=master)](https://travis-ci.com/thedraftaccount/lab04)

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**

```ShellSession
$ open https://travis-ci.org
```

## Tasks

- [x] 1. Авторизоваться на сервисе **Travis CI** с использованием **GitHub** аккаунта
- [x] 2. Создать публичный репозиторий с названием **lab04** на сервисе **GitHub**
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Включить интеграцию сервиса **Travis CI** с созданным репозиторием
- [x] 5. Получить токен для **Travis CLI** с правами **repo** и **user**
- [x] 6. Получить фрагмент вставки значка сервиса **Travis CI** в формате **Markdown**
- [x] 7. Выполнить инструкцию учебного материала
- [x] 8. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
# Setting environment variables
$ export GITHUB_USERNAME=<имя_пользователя>
$ export GITHUB_TOKEN=<полученный_токен>
```

```ShellSession
# Organization of a work directory
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```ShellSession
# Installing ruby version manager
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles
Upgrading the RVM installation in /home/johnsnow/.rvm/
Upgrade of RVM in /home/johnsnow/.rvm/ is complete.

# Writing some information at the end of "scripts/activate"
$ echo "source $HOME/.rvm/scripts/rvm" >> scripts/activate
$ . scripts/activate

# Configuration of RVM 
$ rvm autolibs disable
$ rvm install ruby-2.4.2
$ rvm use 2.4.2 --default
# Travis installing
$ gem install travis
Fetching faraday_middleware-0.14.0.gem
Fetching highline-1.7.10.gem
Fetching backports-3.17.1.gem
Fetching multipart-post-2.1.1.gem
Fetching faraday-0.17.3.gem
Fetching activesupport-5.2.4.2.gem
Fetching multi_json-1.14.1.gem
Fetching public_suffix-4.0.4.gem
Fetching net-http-persistent-2.9.4.gem
Fetching net-http-pipeline-1.0.1.gem
Fetching ffi-1.12.2.gem
Fetching ethon-0.12.0.gem
Fetching typhoeus-0.8.0.gem
Fetching addressable-2.7.0.gem
Fetching websocket-1.2.8.gem
Fetching pusher-client-0.6.2.gem
Fetching gh-0.16.0.gem
Fetching launchy-2.5.0.gem
Fetching travis-1.8.13.gem
Successfully installed multipart-post-2.1.1
Successfully installed faraday-0.17.3
Successfully installed faraday_middleware-0.14.0
Successfully installed highline-1.7.10
Successfully installed backports-3.17.1
Successfully installed activesupport-5.2.4.2
Successfully installed multi_json-1.14.1
Successfully installed public_suffix-4.0.4
Successfully installed addressable-2.7.0
Successfully installed net-http-persistent-2.9.4
Successfully installed net-http-pipeline-1.0.1
Successfully installed gh-0.16.0
Successfully installed launchy-2.5.0
Building native extensions. This could take a while...
Successfully installed ffi-1.12.2
Successfully installed ethon-0.12.0
Successfully installed typhoeus-0.8.0
Successfully installed websocket-1.2.8
Successfully installed pusher-client-0.6.2
Successfully installed travis-1.8.13
Parsing documentation for multipart-post-2.1.1
Installing ri documentation for multipart-post-2.1.1
Parsing documentation for faraday-0.17.3
Installing ri documentation for faraday-0.17.3
Parsing documentation for faraday_middleware-0.14.0
Installing ri documentation for faraday_middleware-0.14.0
Parsing documentation for highline-1.7.10
Installing ri documentation for highline-1.7.10
Parsing documentation for backports-3.17.1
Installing ri documentation for backports-3.17.1
Parsing documentation for activesupport-5.2.4.2
Installing ri documentation for activesupport-5.2.4.2
Parsing documentation for multi_json-1.14.1
Installing ri documentation for multi_json-1.14.1
Parsing documentation for public_suffix-4.0.4
Installing ri documentation for public_suffix-4.0.4
Parsing documentation for addressable-2.7.0
Installing ri documentation for addressable-2.7.0
Parsing documentation for net-http-persistent-2.9.4
Installing ri documentation for net-http-persistent-2.9.4
Parsing documentation for net-http-pipeline-1.0.1
Installing ri documentation for net-http-pipeline-1.0.1
Parsing documentation for gh-0.16.0
Installing ri documentation for gh-0.16.0
Parsing documentation for launchy-2.5.0
Installing ri documentation for launchy-2.5.0
Parsing documentation for ffi-1.12.2
Installing ri documentation for ffi-1.12.2
Parsing documentation for ethon-0.12.0
Installing ri documentation for ethon-0.12.0
Parsing documentation for typhoeus-0.8.0
Installing ri documentation for typhoeus-0.8.0
Parsing documentation for websocket-1.2.8
Installing ri documentation for websocket-1.2.8
Parsing documentation for pusher-client-0.6.2
Installing ri documentation for pusher-client-0.6.2
Parsing documentation for travis-1.8.13
Installing ri documentation for travis-1.8.13
Done installing documentation for multipart-post, faraday, faraday_middleware, highline, backports, activesupport, multi_json, public_suffix, addressable, net-http-persistent, net-http-pipeline, gh, launchy, ffi, ethon, typhoeus, websocket, pusher-client, travis after 26 seconds
19 gems installed
```

```ShellSession
# Linking with remote repo
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
Cloning into 'projects/lab04'...
remote: Enumerating objects: 20, done.
remote: Counting objects: 100% (20/20), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 20 (delta 2), reused 20 (delta 2), pack-reused 0
Unpacking objects: 100% (20/20), done.

$ cd projects/lab04
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04
```

```ShellSession
# Writing some stuff into travis.yml
$ cat > .travis.yml <<EOF
language: cpp
EOF
```

```ShellSession
# Writing some stuff into travis.yml
$ cat >> .travis.yml <<EOF

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install
EOF
```

```ShellSession
# Writing some stuff into travis.yml
$ cat >> .travis.yml <<EOF

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```

```ShellSession
# Logging into Travis via token
$ travis login --github-token ${GITHUB_TOKEN}
```

```ShellSession
# Linter application
$ travis lint
Hooray, .travis.yml looks valid :)

```

```ShellSession
$ ex -sc '1i|<фрагмент_вставки_значка>' -cx README.md
```

```ShellSession
# Adding of files changed into local repo
$ git add .travis.yml
$ git add README.md
$ git commit -m"added CI"
$ git push origin master
```

```ShellSession
# Application of Travis to configure project
$ travis lint
Hooray, .travis.yml looks valid :)

$ travis accounts
thedraftaccount (Thedraftaccount): subscribed, 4 repositories

$ travis sync
synchronizing: . done

$ travis repos
thedraftaccount/lab01 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

thedraftaccount/lab02 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

thedraftaccount/lab03 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

thedraftaccount/lab04 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

$ travis enable
Detected repository as thedraftaccount/lab04, is this correct? |yes| yes
thedraftaccount/lab04: enabled :)

$ travis whatsup
nothing to show

$ travis branches
$ travis history
$ travis show
no build yet for thedraftaccount/lab04

```