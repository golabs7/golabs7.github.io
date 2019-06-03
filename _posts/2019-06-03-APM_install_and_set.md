---
layout: post
title: 'Apache - PHP - MariaDB (APM) 설치 및 설정 방법'
author: jason.yu
date: 2019-06-03 19:30
tags: [PHP, MariaDB, Apache, Linux, Centos]
---

본 포스팅에서는 Centos 7 환경에서 APM을 구성하는 방법에 대해 알아봅니다.

AMP 란?
-----------
AMP는 웹사이트나 서버 운영에 자주 같이 쓰이는 다음의 소프트웨어들의 이름을 합한 약자입니다:<br><br>

Apache (아파치 웹 서버);<br>
MySQL / MariaDB 데이터베이스 관리 시스템(데이터베이스 서버);<br>
PHP, Perl, 또는 Python 프로그래밍 언어.<br><br>

주로 맨 앞 글자에 운영체제의 약자까지 표기해주는데, 본 포스팅에서는 Linux(Centos 7)를 사용하므로, 최종적으로 LAMP가 됩니다.


- Apache(아파치) 설치<br>
yu 명령을 이용하여 Apcahe를 설치해줍니다. 2019년 6월 3일 현재 기준 최신 4.3.6 Stabled 버전이 설치됩니다.

```
$ yum install -y httpd

```

- Apache 시작 등록

```

$ systemctl enable httpd

```
- Apache 설치 확인

```

$  httpd -v

```


PHP 7.3 설치
----------

 <br>

- Step 1: Add PHP 7.3 Remi repository <br>
PHP 7.3 레포지토리를 추가합니다.

```
$ yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm 
$ yum -y install epel-release yum-utils

```


- Step 2: Disable repo for PHP 5.4<br>
기본적으로 PHP 5.4 레포지토리가 활성화 되어있습니다. 이것을 7.3으로 바꿉니다.

```
$ yum-config-manager --disable remi-php54
$ yum-config-manager --enable remi-php73
```


- PHP 7.3 install<br>
PHP 7.3과 Extenstion을 설치합니다.

```
$ sudo yum -y install php php-cli php-fpm php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json
```

- Check version installed

```

$ php -v

```


Apache PHP 연동
----------

- httpd.conf 수정

```

# vi /etc/httpd/conf/httpd.conf

LoadModule php7_module     modules/libphp7.so

<IfModule dir_module>

    DirectoryIndex index.html index.php

</IfModule>

AddType text/html .shtml
AddOutputFilter INCLUDES .shtml
AddType application/x-httpd-php .html .htm .php .inc
AddType application/x-httpd-php-source .phps

```

- phpinfo()로 연동 확인

```

$ vi /var/www/html/phpinfo.php

    <?php phpinfo() ?>
    :q!

$ systemctl restart httpd

```
localhost로 접속해서 PHP 정보가 출력되면 성공.


MariaDB 설치
----------

- Repository 추가

```
$ vi /etc/yum.repos.d/MariaDB.repo

    # MariaDB 10.3 CentOS repository list - created 2019-01-13 00:47 UTC
    # http://downloads.mariadb.org/mariadb/repositories/
    [mariadb]
    name = MariaDB
    baseurl = http://yum.mariadb.org/10.3/centos7-amd64
    gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
    gpgcheck=1

```

- MariaDB 설치

```
$ yum install MariaDB-server MariaDB-client
```

- 초기 보안 설정

```
$ sudo mysql_secure_installation
```
root 계정의 비밀번호 설정과 annonymous table을 삭제 할 것인지 묻는 내용임.<br>
root 계정 비밀번호를 물을 시 그냥 엔터를 입력하면 됨.<br>
root 계정 비밀번호 설정 후 몇가지 물음에 엔터를 입력하면 설정이 완료됨.<br>

- 소켓에러 발생 시 대처

```
# service mysqld stop
# chmod 755 -R /var/lib/mysql
# chown mysql:mysql -R /var/lib/mysql
# service mysqld start
```

소켓 권한 문제로 인한 에러 시 권한, 소유권 변경으로 해결 가능.

Apache, MariaDB Port 설정
----------

- SELinux 설정 확인 <br>
semanage 명령어를 사용해서 확인하고 변경할 수 있다.
명령어 실행이 안될 경우 policycoreutils-python 패키지를 설치해주면 된다.

- semanage 설치

```
$ yum install -y policycoreutils-python
```


- 포트 확인

```
$ semanage port -l | grep mysqld_port_t

$ semanage port -l | grep http_port_t

```

- SELinux에 포트 등록

```
$ semanage port -a -t mysqld_port_t -p tcp 3307

```

- 확인

```
$ semanage port -l | grep mysqld_port_t

mysqld_port_t tcp 3307, 1186, 3306, 63132-63164
```

