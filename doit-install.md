---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 파이썬 doit 설치
minutes: 10
---

> ## 학습 목표 {.objectives}
>
> *   파이썬 `doit` 을 설치한다.


## 파이썬 아나콘다 통한 `doit` 설치

파이썬 `doit`을 설치하는 가장 쉬운 방법중의 하나는 [아나콘다](http://www.continuum.io/)를 설치해서 시작하는 것이다. 이유는 
과학 컴퓨팅(Scientific Computing)에 [SciPy](http://www.scipy.org/) 스택이 필요한데, [IPython](http://ipython.org/), [NumPy](http://www.numpy.org/), 0Matplotlib](http://matplotlib.org/), [Pandas](http://pandas.pydata.org/), [Sympy](http://sympy.org/) 등이 포함된다. 따라서, 파이썬 프로그램을 개발하는데 필요한 기본적인 팩키지를 모두 내부적으로 설치해 주는 
[아나콘다](http://www.continuum.io/)를 사용한다.

### 아나콘다 설치

[아나콘다 다운로드](http://continuum.io/downloads) 사이트에서 운영체제에 맞춰 다운로드한다. 그리고 나서, `bash Anaconda-2.x.x-Linux-x86[_64].sh` 형식으로 된 파일을 찾아서 `bash` 실행한다.
~~~ {.input}
root@dev:~# wget https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1
d3.ssl.cf1.rackcdn.com/Anaconda-2.2.0-Linux-x86_64.sh
root@dev:~# bash Anaconda-2.2.0-Linux-x86_64.sh
~~~

### `doit` 설치

`doit`은 `pip install doit`으로 설치한다.

~~~ {.input}
root@dev:~# pip install doit
~~~

