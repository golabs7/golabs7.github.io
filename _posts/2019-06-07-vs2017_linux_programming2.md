---
layout: post
title: 'VS 2017 linux 프로그래밍 2'
author: jack.jeong
date: 2019-06-07 16:30
tags: [Linux,CentOS,C,C++]
---

Visual Studio 2017 의 강력한 IDE 기능을 이용한 linux 프로그래밍하기 2

Open Source 설치하기
-----------

- OpenSSL<br>
[OpenSSL Downloads](https://www.openssl.org/source/)<br>

```
1. 설치파일 다운로드 하기
# wget https://www.openssl.org/source/old/1.1.1/openssl-1.1.1.tar.gz
# tar xvfz openssl-1.1.1.tar.gz
# cd openssl-1.1.1
# yum install perl
# ./config shared
# make
# make install

openssl의 실행파일 : /usr/local/bin
openssl의 include파일 : /usr/local/include
openssl의 lib파일 : /usr/local/lib

위 경로로 openssl 폴더를 만들고 복사해서 사용하면 VS 2017 intellsense를 자동 감지한다.
인증서비스를 위한 파일 : /usr/local/ssl 에 설치된다.
config를 실행할 때 prefix를 주지 않으면 /usr/local/bin 에 설치된다.
다른 디렉토리에 설치를 하고 싶으면
# ./config --prefix=/usr/local --openssldir=/usr/local/openssl

2. 환경변수 등록하기
# cd
# vi ~/.bash_profile
# export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# source ~/.bash_profile
# cp /usr/local/bin/openssl /usr/bin

3. 버전 확인
# openssl version
```

- Boost C++ Libraries <br>
[Boost Downloads](https://www.boost.org/users/download/)

```
# curl -O https://dl.bintray.com/boostorg/release/1.69.0/source/boost_1_69_0.tar.gz
# tar xvfz boost_1_69_0.tar.gz
# ./bootstrap.sh
# ./b2

아래 경로로 파일들을 복사한다
Boost의 include파일 : /usr/local/include/boost
Boost의 lib파일 : /usr/local/lib/boost

```
> VS 2017 CMake error 발생시
> Microsoft CMake binary를 다운로드 받는다<br>
> [microsoft/CMake Downloads](https://github.com/Microsoft/CMake/releases)<br>
> 
> ```
> // 스크립트 실행
> # chmod +x cmake-3.14.2542820-MSVC_2-Linux-x64.sh
> # sh cmake-3.14.2542820-MSVC_2-Linux-x64.sh
>
> // cmake cpack ctest 파일을 /usr/local/bin 로 복사한다
> # cp -f ./bin/cmake ./bin/cpack ./bin/ctest /usr/local/bin
>
> // 나머지 파일들은 /usr/local/share/cmake-3.14 로 복사한다
> ```



