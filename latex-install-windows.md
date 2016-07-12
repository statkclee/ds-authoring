---
layout: page
title: xwMOOC 오픈 컴퓨터
subtitle: 한글 $LaTeX$ 윈도우 설치
minutes: 10
---

> ### 학습 목표 {.objectives}
>
>  * 윈도우 환경에 $LaTeX$을 설치한다.


### 1. 윈도우 7 환경 $LaTeX$ 설치

윈도우 환경에서 $LaTeX$ 을 설치하는 것이 경우에 따라서는 꼬일 수가 있다. [KTUG Wiki](http://wiki.ktug.org/wiki/wiki.php/설치하기Windows/tlinstall)를 참조하여 설치한다.

> ### 사전 준비 {.callout}
>
> * MikTeX 혹은 TeXLive 이전 버젼이 설치된 경우 프로그램 추가/삭제에서 지운다.
> * 윈도우즈 사용자 이름을 영문과 숫자로만 한정한다.

#### TeXLive 2015 설치

1. [http://ftp.ktug.org/tex-archive/systems/texlive/tlnet/install-tl.zip](http://ftp.ktug.org/tex-archive/systems/texlive/tlnet/install-tl.zip) 사이트에서 다운로드 한다.
1. 다운로드한 압축파일을 풀고 예를 들어 `install-tl-20160711`와 같은 폴더로 들어간다. 
1. `install-tl-advanced.bat` 파일을 실행한다.
    * **GUI** 가 뜨지 않는 경우: 시스템 변수 `Path`에 `C:\Windows\system32;` 를 추가 
    * `Directory Setup`에 `TEXDIR`이 `c:\texlive\2015` &rarr; `c:\usr\texlive\2015`으로 수정한다. 만약, 디렉토리 수정이 되지 않는 경우 원인이 `c:\usr` 디렉토리가 존재하지 않기 때문에 발생된다. 따라서 `usr` 폴더를 `c:\` 루트 디렉토리 밑에 생성시킨다.
    * `Selected scheme` 이 `scheme-full`로 되어 있는지 확인한다. 
1. **Install TeXLive** 버튼을 누르고 한참 기다린다.
    * `scheme-full`을 선택했기 때문에 시간이 조금 더 걸리지만, 장기적으로 정신건강에는 최고로 좋다.

<img src="fig/latex-install-windows.png" width="57%" alt="윈도우즈 LaTeX 설치" />


윈도우 쉘을 열고 `pdflatex --version` 명령어를 쳐서 다음과 같이 출력되면 정상이다.

~~~ {.shell}
$ pdflatex --version
~~~

~~~ {.output}
pdfTeX 3.14159265-2.6-1.40.17 (TeX Live 2016/W32TeX)
kpathsea version 6.2.2
Copyright 2016 Han The Thanh (pdfTeX) et al.
There is NO warranty.  Redistribution of this software is
covered by the terms of both the pdfTeX copyright and
the Lesser GNU General Public License.
For more information about these matters, see the file
named COPYING and the pdfTeX source.
Primary author of pdfTeX: Han The Thanh (pdfTeX) et al.
Compiled with libpng 1.6.21; using libpng 1.6.21
Compiled with zlib 1.2.8; using zlib 1.2.8
Compiled with xpdf version 3.04
~~~
