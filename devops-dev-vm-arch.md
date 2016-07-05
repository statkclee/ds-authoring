---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 정적 웹콘텐츠 개발 클라우드 가상서버 아키텍처
minutes: 10
---
> ## 학습 목표 {.objectives}
>
> *  데이터 제품을 위한 정적 웹사이트 개발 풀스택 아키텍처 설계한다.
> *  CLI, GUI, 웹 기반 개발 인터페이스를 지원한다.
> *  VM, LAMP, Jekyll, RStudio/Shiny 개발서버를 자동화한다.


## 클라우드 기반 개발 서버 아키텍처

정적 웹콘텐츠 개발을 위한 개발서버로 리눅스 우분투 운영체제 위에 세가지 개발 인터페이스를 지원한다.

- 웹 인터페이스 : 크롬, 사파리, 파이어폭스, 익스플로러 웹브라우져를 통해 개발 서버에 접속해 개발한다.
- 명령라인 인터페이스(CLI) : 보안 쉘(Secure SHell, SSH)을 통해 개발 서버에 접속해 개발한다.
- 그래픽 사용자 인터페이스 (GUI) : WIMP(Window, Icon, Menu, Pointer)를 지원하는 VNC 서비스를 원격 데스트톱을 통해 개발서버에 접속해 개발한다.

~~~ {.output}
Virtual Machine         Static Website          Desktop GUI             DevOps              
+-----------+           +-----------+           +-----------+           +---------------+ 
| Ubuntu    |           | Jekyll    |           |           |           | Jenkins       |
| LAMP      |---------->| Pandoc    |---------->| tightVNC  |---------->| GitHub Webhook|
|           |           |           |           |           |           |               | 
+-----------+           +-----------+           +-----------+           +---------------+ 
     ^                                              
     |
     |
Data Science                                  
+-----------+                                       
| Shiny     |                                       
| RStudio   |
|           |          
+-----------+          
~~~

> ## 개발 서버 준비 순서 {.prereq}
> 
> 1. 가상서버 생성할 때 LAMP를 설치하여 주문한다.  
> 2. 정적 웹사이트 개발 툴체인 설치한다.  
> 3. 데스크톱 GUI를 `tightVNC` 기준 설치한다.  
> 4. 데이터 과학 개발위한 `Shiny`와 `RStudio`를 설치한다.  
> 5. 개발 제품 배포를 위해 `젠킨스(Jenkins)`를 설치한다.
>

## 1. LAMP 설치된 가상 서버 생성

`vm-create.py` 파이썬 프로그램을 통해서 `LAMP`가 설치된 가상 컴퓨터를 주문한다.

~~~ {.input}
root@shiny:~# python vm-create.py dev-lamp
~~~

~~~ {.input}
import sys
import SoftLayer

vmname = sys.argv[1]

client = SoftLayer.create_client_from_env(username="SLXXXXXX",
        api_key="7c7XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX)

shiny_object = client['Virtual_Guest'].createObject({
    'hostname': vmname,
    'domain': 'xwmooc.net',
    'startCpus': 1,
    'maxMemory': 1024,
    'hourlyBillingFlag': 'true',
    'operatingSystemReferenceCode': 'UBUNTU_LATEST',
    'localDiskFlag': 'true',
    'datacenter': { "name" : 'hkg02'},
    'local_disk': 25,
    'public': 'true'
})

for key, value in shiny_object.iteritems():
    print key, " : ", value
~~~

`service --status-all` 명령어를 통해서 `apache2` 서비스가 동작하고 있음을 확인한다.
웹브라우져를 열고 `ifconfig eth1`을 통해 *inet addr:169.XX.XXX.XX* IP주소를 확인하고 주소창에 넣어 정상적으로  
**Apache2 Ubuntu Default Page**가 뜨는지 확인하는 것도 방법이다.

~~~ {.input}
root@shiny:~# service --status-all | grep '+'
~~~

~~~ {.output}
 [ + ]  acpid
 [ + ]  apache2
 [ + ]  apparmor
 [ ? ]  apport
 [ ? ]  binfmt-support
 [ ? ]  console-setup
 [ + ]  cron
 [ + ]  cups
 [ + ]  cups-browsed
 [ ? ]  dns-clean
 [ + ]  friendly-recovery
 [ + ]  kerneloops
 [ ? ]  killprocs
 [ ? ]  kmod
 [ ? ]  lightdm
 [ ? ]  mysql
 [ ? ]  networking
 [ ? ]  nimbus
 [ ? ]  ondemand
 [ + ]  open-iscsi
 [ ? ]  pppd-dns
 [ ? ]  rc.local
 [ + ]  rsyslog
 [ + ]  saned
 [ ? ]  sendsigs
 [ ? ]  speech-dispatcher
 [ + ]  udev
 [ ? ]  umountfs
 [ ? ]  umountiscsi.sh
 [ ? ]  umountnfs.sh
 [ ? ]  umountroot
 [ + ]  xe-linux-distribution
 [ + ]  xrdp
~~~

## 2. 정적웹콘텐츠 개발 위한 제킬(Jekyll) 설치 

### 2.1 `root` 권한을 갖는 사용자 등록

루트 권한을 갖는 사용자를 준비하기 위해서 먼저 일반 사용자를 등록하고 등록된 사용자에 루트권한을 부여한다. `adduser xwmooc` 명령어를 통해서 `xwmooc` 사용자를 등록했다. 그리고 나서 `gpasswd -a xwmooc sudo` 명령어로 `sudo` 그룹에 등록해서 루트권한을 부여했다.

`su - xwmooc` 명령어를 통해서 루트가 아닌 일반사용자로 로그인했다.

~~~ {.input}
root@vnc:~# adduser xwmooc
Adding user `xwmooc' ...
Adding new group `xwmooc' (1000) ...
Adding new user `xwmooc' (1000) with group `xwmooc' ...
Creating home directory `/home/xwmooc' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for xwmooc
Enter the new value, or press ENTER for the default
        Full Name []: xwMOOC
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y
~~~

~~~ {.input}
root@vnc:~# gpasswd -a xwmooc sudo
Adding user xwmooc to group sudo
root@vnc:~# su - xwmooc
xwmooc@vnc:~$
~~~

### 2 정적 웹콘텐츠 개발 툴체인 설치

정적 웹콘텐츠 개발 툴체인 설치에 대한 자세한 사항은 [정적 웹콘텐츠 개발 가상서버 구축](03-dev-vm.html)을 참고한다.

![정적 웹콘텐츠 개발 툴체인](fig/dev-vm-software-stack.png)


## 3. 데스크톱 GUI 설치

가상 컴퓨터 데스크톱 GUI 설치에 대한 자세한 사항은 [VNC 그래픽 사용자 인터페이스(GUI)](02-xrdp-cli.html)를 참조한다.


## 4. 데이터 과학 툴체인 설치

데이터를 위한 언어인 R을 사용하여 데이터 과학 툴체인을 구축할 경우 [RStudio](http://www.rstudio.com/)와 [Shiny](http://shiny.rstudio.com/) 서버가 필요하다. [RStudio](http://www.rstudio.com/)와 [Shiny](http://shiny.rstudio.com/) 설치는 다음을 참고한다.

- [](04-docker-r.html)
- [Shiny 서버 설치](06-install-shiny.html)

**`RCurl`, `shinyapps` 라이브러리 설치 **

~~~ {.input}
apt-get -y build-dep libcurl4-gnutls-dev
apt-get -y install libcurl4-gnutls-dev
~~~


## 5. 개발운영(DevOps) 젠킨스 설치


개발운영의 대명사 [젠킨스(Jenkins)](https://jenkins-ci.org/)가 필요한데 자바 기반으로 되어 있어 자바 JDK를 깔고 젠킨스를 설치한다. 
젠킨스 설치에 대한 자세한 사항은 다음 [웹페이지](03-devops.html)를 참조한다.

## 풀스택 개발 아키텍쳐

데이터 제품 개발을 위한 정적 웹사이트 개발 풀스택 아키텍처은 다음과 같다.
`SSH`를 통해 명령라인 인터페이스(CLI) 접근, `LAMP` 설치를 통한 웹 인터페이스, `VNC`를 통한 데스크톱 GUI 인터페이스를
통해서 언제 어디서나 다양한 방식으로 개발을 진행할 수 있다.

Shiny와 RStudio를 통해 데이터 제품을 개발하고, 제킬을 통해 정적 웹사이트를 함께 젠킨스를 통해서 개발산출물을 운영 환경에
배포한다. 

![데이터 제품 개발을 위한 정적 웹사이트 개발 풀스택 아키텍처](fig/dev-vm-lamp-jekyll-vnc-jenkins.png)