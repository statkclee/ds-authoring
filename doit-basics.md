---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 파이썬 빌드 doit - "doit"으로 작업 자동화
---

## "doit"으로 작업 자동화

기후 데이터 분석으로 프로젝트를 시작한다. 이번 분석은 많은 단계가 필요한데, 중요한 것은 올바른 순서대로 수행되어야 한다.
또한 데이터가 항상 새로운 관측자료로 갱신된다. 그래서, 원천 데이터를 갱신할 때마다 다시 실행하기 위해서,
어떤 단계를 수행하고 어떤 단계를 수행하지 않았는지 기억해서 기록하는 것도 원치 않는다.

이를 위해서, [doit](http://pydoit.org/)으로 불리는 파이썬 라이브러리를 사용한다. 이번 학습은 doit의 기초를 다루지만
고급 사용법에 관심있는 사람을 위한 매우 훌륭한 [문서(documentation)](http://pydoit.org/contents.html)도
준비되어 있다.

> ### 학습목표 {.objectives}
> 
> - 의존성(dependency)와 대상(target) 간 차이를 설명한다.
> - 순서를 결정해서, doit이 일련의 작업을 실행하게 한다.
> - doit 작업 정의단계에서 자동변수(automatic variable)가 반복을 줄이는 방법을 설명한다.
> - 간단한 doit 작업 환경설정 파일을 작성한다.

## 기초 doit 파일

작업해야하는 원데이터(raw data)를 살펴보고 시작하자. 월별 평균 기온과 월별 총일조시간 정보를 담고 있는 파일이 두개 있다.

~~~ {.input}
    !ls *.txt
~~~

~~~ {.output}
    UK_Sunshine_data.txt
    UK_Tmean_data.txt
~~~~    

이제 `head` 명령어를 사용해서 평균 기온 파일 내부를 살짝 들여다 보자:

~~~ {.input}
    !head UK_Tmean_data.txt
~~~

~~~ {.output}
    UK Mean Temperature (Degrees C)

    Areal series, starting from 1910

    Allowances have been made for topographic, coastal and urban effects where relationships are found to exist.

    Seasons: Winter=Dec-Feb, Spring=Mar-May, Summer=June-Aug, Autumn=Sept-Nov. (Winter: Year refers to Jan/Feb).

    Monthly values are ranked and displayed to 1 dp and seasonal/annual values to 2 dp. Where values are equal, rankings are based in order of year descending.

    Data are provisional from January 2012 & Winter 2012 Last updated 01/12/2012

    

    	JAN	Year	FEB	Year	MAR	Year	APR	Year	MAY	Year	JUN	Year	JUL	Year	AUG	Year	SEP	Year	OCT	Year	NOV	Year	DEC	Year	WIN	Year	SPR	Year	SUM	Year	AUT	Year	ANN	Year

    	6.3	1916	6.8	1998	8	1938	10.7	2011	12.2	2008	15	1976	17.8	2006	17.3	1995	15.2	2006	12.2	2001	8.8	1994	6.9	1934	5.81	1989	9.15	2011	15.78	2006	11.39	2006	9.73	2006

    	5.9	2007	5.9	1990	7.9	1957	10.2	2007	12	1992	14.9	1940	17.3	1983	17.1	1997	14.6	1949	11.8	1969	8.7	2011	6.6	1988	5.56	2007	9.05	2007	15.77	2003	11.26	2011	9.64	2011
~~~


파일에 담긴 데이터는 매우 끔직한 방식으로 구성되어 있다. 파일 서두에 데이터 구조를 설명하는 7줄이 있는데 도움이 된다 (만약 `#` 같은
주석문자로 시작한다면 더 좋을 수도 있지만)

본질적으로 매월마다 두 칼럼이 있다. 첫번째 칼럼은 해당월 평균 기온정보를 담고 있고, 두번째 칼럼에는 측정년도 정보가 담겨있다. 모든 칼럼은
오름차순 온도로 정렬되어 있다.

고맙게도, 선배 대학원 졸업생이 파이썬 스크립트를 작성해두고 떠나서 데이터를 좀더 사용하기 쉬운 방식으로 마사지를 할 수 있다. 파이썬
스크립트가 어떤 작업을 수행하는지 살펴보자.

~~~ {.input}
    !python reformat_weather_data.py UK_Tmean_data.txt | head
~~~

~~~ {.output}
    Traceback (most recent call last):
      File "C:\Python34\lib\site-packages\pandas\__init__.py", line 7, in <module>
        from pandas import hashtable, tslib, lib
      File "pandas\src\numpy.pxd", line 157, in init pandas.hashtable (pandas\hashtable.c:23776)
    ImportError: No module named 'numpy'
    
    During handling of the above exception, another exception occurred:
    
    Traceback (most recent call last):
      File "reformat_weather_data.py", line 1, in <module>
        import pandas as pd
      File "C:\Python34\lib\site-packages\pandas\__init__.py", line 13, in <module>
        "extensions first.".format(module))
    ImportError: C extension: No module named 'numpy' not built. If you want to import pandas from the source directory, you may need to run 'python setup.py build_ext --inplace' to build the C extensions first.
~~~    

한결 좋다. 데이터 분석 파이프라인 첫번째 작업은 `UK_Tmean_data.txt` 파일에 파이썬 스크립트를 실행하고, 새이름
`UK_Tmean_data.reformatted.txt`으로 저장하는 것이다. "doit" 라이브러리를 사용해서 파이썬이 자동으로 원데이터가
갱신될 때마다 데이터 형식을 바꾸는 작업을 수행하게 만들고 싶다.

첫째로, doit이 설치되었는지 확실히 한다:

~~~ {.input}
    !pip install doit
~~~

~~~ {.output}
    Requirement already satisfied (use --upgrade to upgrade): doit in c:\python34\lib\site-packages
    Requirement already satisfied (use --upgrade to upgrade): six in c:\python34\lib\site-packages (from doit)
    Cleaning up...
~~~    

일반적으로, 파이썬 파일 내부에 상세 작업정보를 담고 있는 파일을 만든다. 약간의 `make` 작업 경험이 있다면, 해당 파일은
`makefile`에 상응한다.
그리고 나서, 만약 터미널에서 `doit` 명령어를 실행하면, `doit`은 현재 디렉토리에서 `dodo.py`라는 환경설정 파일을 찾아서,
파일로부터 작업내용을 일고 갱신되지 않은 작업을 실행한다. 또한 `dodo.py`가 아닌 파일을 읽어 `doit`에게 작업을 수행하게 하려면,
`doit -f <name_of_task_file.py>`같은 방식도 사용할 수 있다.

이번 학습에서 편의성을 위해서, iPython magic을 사용해서 iPython 노트북에서 `doit` 코드를 실행한다. 이 경우에,
셀(cell) 작업내용이 `dodo.py` 파일에 입력한 것에 상응한다.

~~~ {.input}
    %load_ext doitmagic
~~~

Here is our first doit file, containing just one task:

~~~ {.input}

    %%doit
    
    # one_task.py
    
    def task_reformat_temperature_data():
        """Reformats the raw temperature data file for easier analysis"""
        
        return {
            'file_dep': ['UK_Tmean_data.txt'],
            'targets': ['UK_Tmean_data.reformatted.txt'],
            'actions': ['python reformat_weather_data.py UK_Tmean_data.txt > UK_Tmean_data.reformatted.txt'],
        }
    

    .  reformat_temperature_data
~~~    

파이썬 함수에 `doit`이 수행할 단일 작업을 정의되어 있다. 함수가 수행하는 것은 작업에 대한 환경설정 정보를 담고 있는 딕셔너리를 반환하는
것이다. 좀더 구체적으로 해당 환경설정에 담긴 구성요소를 살펴보자.

작업내용에 파일 한개에 의존성이 나와 있다, `file_dep`. - `doit`에게 지시해서 해당 작업은 `UK_Tmean_data.txt`
파일에 의존성이 있어서, 만약 이 파일이 변경되면, 작업을 다시 실행할 필요가 있다.

또한, `target`도 있다 - `doit`에게 지시해서, 해당 작업은 `UK_Tmean_data.reformatted.txt` 파일을
생성한다. 만약 `UK_Tmean_data.reformatted.txt` 파일이 존재하지 않는다면, 파일 생성 작업도 실행할 필요가 있다.

마지막으로, 해당 작업에는 `action`이 한개 있다. 해당 작업이 최신으로 갱신되지 않았다고 `doit`이 판단할 때, 해당 작업 정의
`action` 부분은 실행될 명령 목록이 된다.

이제 `doit` 작업결과를 살펴보자. `Doit`은 별도 행에 각 작업 이름을 출력한다. 단지 한가지 작업만 배정했기 때문에 단지 한 줄만
출력결과로 나온다.
명시적으로 작업명칭을 부여하지 않았기 때문에, `doit`은 함수 이름으로 갈음한다. 작업명칭 앞에 점(`.`)이 의미하는 것은 `doit`이
실제로 작업 실행을 결정했다는 것이다. 다시 셀을 실행하고 무엇이 변경되었는지 살펴본다.

이제 작업명칭에 대쉬 두개(`--`)가 붙는다. 이것이 의미하는 바는 `doit`이 작업 한가지를 찾았으나,
`UK_Tmean_data.reformatted.txt` 파일이 이미 존재하고 `UK_Tmean_data.txt` 파일이 변경되지 않았기
때문에, 다시 작업을 실행하지 않았다는 것이다.

새파일을 확인해서, 작성한 작업이 원하는 작업인지 확실히 확인한다:

~~~ {.input}
    !head UK_Tmean_data.reformatted.txt
~~~

~~~ {.output}
    month,value

    1910-01-01,2.6

    1910-02-01,2.6

    1910-03-01,4.0

    1910-04-01,6.4

    1910-05-01,9.5

    1910-06-01,12.3

    1910-07-01,14.0

    1910-08-01,13.8

    1910-09-01,11.8
~~~


만약 파일마다 작업규칙 하나만 허용된다면, 수작업으로 명령어를 타이핑하거나, 작은 쉘스크립트를 작성하는 것과 다름이 없다.
운좋게도, `doit`으로 단하나의 환경설정 파일에 원하는 만큼 작업규칙을 적을 수 있다.

`two_tasks.py`으로 명명된 또다른 `doit` 파일이 있는데 두 파일 `UK_Tmean_data.txt`와
`UK_Sunshine_data.txt` 형태를 바꾸는 작업규칙을 갖고 있다.
"Tmean" 혹은 "Sunshine"에 대한 것만 제외하고 규칙은 동일하다; 나중에 규칙을 하나로 통합하는 방법을 살펴볼 것이다.

~~~ {.input}
    %%doit
    
    # two_tasks.py
    
    def task_reformat_temperature_data():
        """Reformats the raw temperature data file for easier analysis"""
            
        return {
            'file_dep': ['UK_Tmean_data.txt'],
            'targets': ['UK_Tmean_data.reformatted.txt'],
            'actions': ['python reformat_weather_data.py UK_Tmean_data.txt > UK_Tmean_data.reformatted.txt'],
    }
    
    def task_reformat_sunshine_data():
        """Reformats the raw sunshine data file for easier analysis"""
    
        return {
            'file_dep': ['UK_Sunshine_data.txt'],
            'targets': ['UK_Sunshine_data.reformatted.txt'],
            'actions': ['python reformat_weather_data.py UK_Sunshine_data.txt > UK_Sunshine_data.reformatted.txt'],
        }

    -- reformat_temperature_data
    .  reformat_sunshine_data
~~~    

이제, `doit`이 두 작업을 발견한 것을 볼 수 있다.
`doit`이 기온 데이터에 대해서 다시 형식을 변경하는 작업을 실행할 필요가 없다고 판단하지만, 일조량 데이터에 대해서는 형식을 바꾸는 작업을
실행한다.

만약 셀을 다시 실행한다면, `doit`이 어느 작업도 실행할 필요가 없다고 판단한 결과를 볼 수 있다.

주목할 한가지는 만약 작업간에 만족되어야되는 의존성이 없다면 `doit`이 정의된 순서에 맞춰 규칙을 실행한다.
또한 이용가능한 중앙처리장치가 한개 이상이라면 병렬로도 처리할 수 있다- 나중에 이런 아이디어는 다시 다룰 것이다.

상기 예제가 보여주는 다른 면은 하나만 한규칙에서 대상이 될 수 있고 다른 것에는 전제조건이된다. `dodo.py`에 언급된 파일 사이 의존성은
일방향 그래프를 구성한다. `doit`이 실행되기 위해서 해당 그래프는 어떤 사이클도 포함해서는 않된다. 예를 들어, X가 Y에 의존하고, Y가
Z에 의존하고, Z가 X에 의존한다면, 모든 것이 다른 무언가에 의존한다.
그래서 `doit`이 가장 먼저 실행할 것은 아무것도 없게된다. 만약 작업사이에 사이클이 탐지되면, `doit`은 오류 메시지를 출력하고
정지한다.

이전에 인지했듯이, 해당 파일에 상당한 중복이 있다.
먼저 파일명칭이 작업정의와 작업동작(action)에 반복된다.
운좋게도, 작업동작을 작성할 때, `doit`을 통해서 변수에 접근할 있다.

파이썬 `%` 형식자(formatter)를 사용해서 `Doit`이 동작을 정의하는 문자열에 작업 의존성과 대상을 치환한다.
다음과 같이 동작한다.

~~~ {.input}
    %%doit
    
    # automatic_variables.py
    
    def task_reformat_temperature_data():
        """Reformats the raw temperature data file for easier analysis"""
        
        return {
            'actions': ['python reformat_weather_data.py %(dependencies)s > %(targets)s'],
            'file_dep': ['UK_Tmean_data.txt'],
            'targets': ['UK_Tmean_data.reformatted.txt'],
        }
    
    def task_reformat_sunshine_data():
        """Reformats the raw sunshine data file for easier analysis"""
        
        return {
            'actions': ['python reformat_weather_data.py %(dependencies)s > %(targets)s'],
            'file_dep': ['UK_Sunshine_data.txt'],
            'targets': ['UK_Sunshine_data.reformatted.txt'],
        }

    -- reformat_temperature_data
    -- reformat_sunshine_data
~~~    

더 나아졌지만, 이제는 동작(action)이 두 작업사이에 동일하다. 의존성과 대상만 다르다.

다음절에서 나머지 중복을 제거할 것이다.

### 도전과제:

1. "Hello World!" 텍스트를 담고 있는 `hello.txt`라는 새로운 파일을 생성하는데 "echo" 유닉스 명령어를 사용하는
작업을 작성하라.
2. 다음 작업 환경설정 파일이 주어진다면, `doit`이 어떤 순서로 작업을 실행할까요?

~~~ {.input}
    def task_giraffe():
        
        return {
                'targets' : ['giraffe.txt'],
                'actions' : ['touch %(targets)s']
               }
    
    def task_zebra():
        
        return {
                'targets' : ['zebra.txt'],
                'file_dep': ['lion.txt'],
                'actions' : ['touch %(targets)s']
               }
    
    def task_lion():
        
        return {
                'targets' : ['lion.txt'],
                'actions' : ['touch %(targets)s']
               }
~~~
