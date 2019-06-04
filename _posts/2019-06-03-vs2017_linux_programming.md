---
layout: post
title: 'VS 2017 linux 프로그래밍 1'
author: jack.jeong
date: 2019-06-03 15:00
tags: [Linux,CentOS,c,c++]
---

Visual Studio 2017 의 강력한 IDE 기능을 이용한 linux 프로그래밍하기 1

가상OS ( 리눅스 CentOS 7 ) 설치
-----------

- Windows 10 pro Hyper-V 설치<br>
제어판 > 프로그램 제거 > windows 기능 켜기/끄기 > Hyper-V 모두체크<br>
Hyper-V 설치 가능여부 확인

- CentOS 7 설치<br>
Hyper-V 관리자에서 가상컴퓨터 새로 만들기<br>
1세대 또는 2세대 만들기 후 보안 > 보안부팅사용체크해제

```
# yum install openssh-server g++ gdb gdbserver zip unzip gcc gcc-c++
# systemctl restart network
# vi /etc/ssh/sshd-config
... 생략 ...
  port 22 // 주석해제
  permitRootLogin yes

# systemctl start sshd.service
```

```
// 방화벽 설정
# firewall-cmd --zone=public --add-port=22/tcp --permanent
# firewall-cmd --reload
# systemctl restart firewalld.service
```

* Hyper-V CentOS 7 에 고정 ip 설정하기<br>
고정 ip로 설정하면 VS 2017 에서 재접속마다 원격 시스템을 바꿔주지 않아도 되므로 편리하다

  * 새 가상 네트워크 스위치 만들기<br>
Hyper-V 관리자 > 가상스위치 관리자 > 새 가상 네트워크 스위치 > 내부 클릭
  * Host OS의 제어판 > 네트워크 및 인터넷 > 네트워크 및 공유센터 > 위에서 만든 가상 스위치 선택 >
인터넷 프로코콜 버전 4(TCP/IP V4) > 속성 클릭<br>
   ```
      ip 주소 192.168.120.1
      서브넷마스크 255.255.255.0
   ```
  * 네트워크 및 공유센터 > 이더넷 > 속성 > 공유 > 인터넷 연결공유 체크 및 위에서 만든 가상스위치 선택
  * CentOS 7 설정
    ```
    # vi /etc/sysconfig/network-scripts/ifcfg-eth0
      BOOTPROTO="none"
      ONBOOT="yes"
      IPADDR=192.168.120.20
      NETMASK=255.255.255.0
      GATEWAY=192.168.120.1

    # vi /etc/sysconfig/network
      NETWORKING=yes
      NETWORKING_IPV6=no

    # vi /etc/resolv.conf
      nameserver 168.126.63.1
    ```
> Hyper-V 가상OS 포트 포워딩<br>
> 제어판 > 네트워크 및 인터넷 > 네트워크 및 공유 센터 > 이더넷 > 속성 > 공유 > 설정<br>
> 고급설정 서비스에서 1706 체크(또는 새로 만들기) >  편집 클릭 <br>
> ip주소 입력 > 확인

- C++ 14 컴파일러 사용하기<br>
   gcc/g++ 7.3 upgrade

```
# curl https://ftp.gnu.org/gnu/gcc/gcc-7.3.0/gcc-7.3.0.tar.gz -O
# tar xvfz gcc-7.3.0.tar.gz
# yum install gmp-devel mpfr-devel libmpc-devel
# mkdir gcc-7.3.0-build
# cd gcc-7.3.0-build
# ../gcc-7.3.0/configure --enable-languages=c,c++ --disable-multilib
# make -j7
# sudo make install
# sudo mv /usr/bin/gcc /usr/bin/gcc-4.8
# sudo mv /usr/bin/g++ /usr/bin/g++-4.8
# sudo mv /usr/local/bin/gcc /usr/local/bin/gcc-7.3 
# sudo mv /usr/local/bin/g++ /usr/local/bin/g++-7.3 
# sudo update-alternatives --install /usr/bin/gcc gcc /usr/local/bin/gcc-7.3 73 --slave /usr/bin/g++ g++ /usr/local/bin/g++-7.3 
# sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 48 --slave /usr/bin/g++ g++ /usr/bin/g++-4.8
# sudo update-alternatives --config gcc

// 확인 코드 작성

# vi test.cpp

#include <iostream>

int main()
{
    auto lambda = [](auto x){
        return x;
        };
    std::cout << lambda("Hello lambda!\n");
    return 0;
}


# g++ -o test.out test.cpp
# ./test.out
```

Visual Studio 2017 설정
-----------

- 원격 시스템 추가<br>

<pre><code>
메뉴 > 도구 > 옵션 > 플랫폼간 연결 관리자 > 원격시스템 추가

  호스트이름: 192.168.120.20
  이름: root
  암호: root 암호

연결관리자 > 원격 헤더 intellisense 관리자 업데이트 클릭
</code></pre>

> - VS 2017 cmake 빌드하기
> 1. CMake > 생성
> 2. CMake > 모두빌드하기
> 3. 빌드된 경로 복사해서 putty에서 연 후<br>
>    # make install
> 4. install된 경로에서 파일 실행
> 5. 디버깅시 시작항목 선택에서 target 선택<br>
>   접속이 안되면 CentOS 7 방화벽 포트 열기
> 
> ```
> # firewall-cmd --permanent --zone=public --add-port=10000/tcp
> # sudo firewall-cmd --reload
> ```
