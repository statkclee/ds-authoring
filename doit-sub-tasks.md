---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 파이썬 빌드 doit - 부작업을 사용해서 비슷한 유형 작업 정의하기 
---

## 부작업을 사용해서 비슷한 유형 작업 정의하기

> ## 학습목표 {.objectives}
> 
> - 부과업(sub-task) 그룹 생성하는 방법을 설명한다.
> - 부과업 정의에 어떤 추가 환경설정이 필요한지 설명한다.
> - 현재 작업을 개조해서 부과업 생성기로 변환한다.

지난번 학습에서 생성한 `doit` 파일을 다른 관점으로 살펴보자.

~~~ {.input}
    %load_ext doitmagic

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

두작업은 모두 동일한 동작을 공유하지만, 의존성과 대상만 다르다는 차이점을 알아차렸다.

매우 유사한 수많은 작업을 실행할 때, `부과업(sub-tasks)`이라는 `doit` 기능을 사용할 수 있다.

~~~ {.input|
    # sub_tasks.py
    
    data_sets = ['Tmean', 'Sunshine']
    
    def task_reformat_data():
        """Reformats all raw files for easier analysis"""
    
        for data_type in data_sets:
            yield {
                'actions': ['python reformat_weather_data.py %(dependencies)s > %(targets)s'],
                'file_dep': ['UK_{}_data.txt'.format(data_type)],
                'targets': ['UK_{}_data.reformatted.txt'.format(data_type)],
                'name': 'UK_{}_data.txt'.format(data_type),
            }
        

    -- reformat_data:UK_Sunshine_data.txt
    .  reformat_data:UK_Tmean_data.txt
~~~    

상기 예제에서, 작업을 정의하는 함수는 작업하나도 반환하지 않는다. 대신에, 파이썬 생성기 객체를 반환한다. 파이썬 생성기 객체는 많은 부과업을
반환한다. 파이썬 생성기(generator)를 들어봤는가?

만약 그렇지 않다면, 다음에 작은 데모시연이 있다.

~~~ {.input}
    def not_a_generator():
        for i in range(5):
            return i
        
    not_a_generator()
~~~

~~~ {.outpu}
    0
~~~

~~~ {.input}
    def is_a_generator():
        for i in range(5):
            yield i
            
    is_a_generator()
~~~

~~~ {.output}

    <generator object is_a_generator at 0x3052780>
~~~

~~~ {.input}
    def is_a_generator():
        for i in range(5):
            yield i
            
    g = is_a_generator()
    
    for x in g:
        print x
~~~
~~~ {.output}
    0
    1
    2
    3
    4
~~~    

이번 학습에서 생성기에 관해서 매우 자세하게 다루지를 않을 것이다. 하지만, 기억해야되는 핵심적인 사항은 함수는 `return`을 사용해서 단일
출력결과를 반환한다. 반면에 생성기는 `yield`를 사용해서 순차적으로 출력 시퀀스를 반환한다. `doit`이 작업 딕셔너리를 산출하는
생성기를 발견하면, 일련의 부과업을 생성한다.

다시 파일 출력결과를 살펴보자. 신규 생성기로 생성된 모든 작업은 동일한 명칭으로 시작한다: `reformat_data`, 이 명칭은 생성기
이름에서부터 가져왔다. 이 다음에 `basename`로 불리는데, 콜론다음에 부과업 명칭이 따라온다. 작업 딕셔너리 `name` 키를 설정해서
명시적으로 각 부과업 명칭을 부여한 것에 주목한다.

만약 부과업 명칭을 설정하지 않는다면 무슨 일이 발생할까?

~~~ {.input}
    # sub_tasks_no_name.py
    
    data_sets = ['Tmean', 'Sunshine']
    
    def task_reformat_data():
        """Reformats all raw files for easier analysis"""
    
        for data_type in data_sets:
            yield {
                'actions': ['python reformat_weather_data.py %(dependencies)s > %(targets)s'],
                'file_dep': ['UK_{}_data.txt'.format(data_type)],
                'targets': ['UK_{}_data.reformatted.txt'.format(data_type)],
            }
~~~
~~~ {.error}        
    ERROR: Task 'reformat_data' must contain field 'name' or 'basename'. {'file_dep': ['UK_Tmean_data.txt'], 'targets': ['UK_Tmean_data.reformatted.txt'], 'actions': ['python reformat_weather_data.py %(dependencies)s > %(targets)s']}
~~~    

작업 명칭을 정의해야만 한다고 `Doit`이 알려준다. 이유는 작업이 직접적으로 다른 작업에 의존할 수 있기 때문이다. 그래서 각 작업은 유일한
명칭을 가져야만하고, 그럼으로써 의존성으로 참조될 수 있다.

이제 다시 형식이 바뀐 데이터를 살펴보자.

~~~ {.input}
    !tail UK_Tmean_data.reformatted.txt
~~~
~~~ {.output}
    2012-03-01,6.4
    2012-04-01,8.3
    2012-05-01,11.3
    2012-06-01,13.7
    2012-07-01,15.7
    2012-08-01,15.7
    2012-09-01,13.3
    2012-10-01,10.5
    2012-11-01,7.0
    2012-12-01,5.3
~~~


파일에서 마지막 데이터 관측점이 2012년 12월에서 나왔다. 그래서 아마도 다시 원데이터를 다운로드해야한다. 이러한 유형의 작업이 많이
하기보다는 아마도 결국 그만두어야되는 작업이다. 그래서 `doit`이 처리하도록 한다:

~~~ {.input} 
    # download_temp_data.py
    
    import datetime
    from doit.tools import timeout 
    
    data_sets = ['Tmean', 'Sunshine']
    
    def task_get_temp_data():
        """Downloads the raw temperature data from the Met Office"""
    
        return {
            'actions': ['wget -O %(targets)s http://www.metoffice.gov.uk/climate/uk/datasets/Tmean/ranked/UK.txt'],
            'targets': ['UK_Tmean_data.txt'],
        }
    
    def task_reformat_data():
        """Reformats all raw files for easier analysis"""
    
        for data_type in data_sets:
            yield {
                'actions': ['python reformat_weather_data.py %(dependencies)s > %(targets)s'],
                'file_dep': ['UK_{}_data.txt'.format(data_type)],
                'targets': ['UK_{}_data.reformatted.txt'.format(data_type)],
                'name': 'UK_{}_data.txt'.format(data_type),
            }
~~~
~~~ {.output}
    .  get_temp_data
    -- reformat_data:UK_Sunshine_data.txt
    .  reformat_data:UK_Tmean_data.txt

    --2014-04-05 12:08:16--  http://www.metoffice.gov.uk/climate/uk/datasets/Tmean/ranked/UK.txt
    Resolving www.metoffice.gov.uk (www.metoffice.gov.uk)... 23.63.99.234, 23.63.99.216
    Connecting to www.metoffice.gov.uk (www.metoffice.gov.uk)|23.63.99.234|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 25576 (25K) [text/plain]
    Saving to: ‘UK_Tmean_data.txt’
    
         0K .......... .......... ....                            100% 2.15M=0.01s
    
    2014-04-05 12:08:16 (2.15 MB/s) - ‘UK_Tmean_data.txt’ saved [25576/25576]
~~~    
    

영국 기상청 웹사이트에서 기온 데이터 최신 버젼을 다운로드하는 새로운 작업을 추가해서, `doit`이 지시작업을 따라서 파일을 다운로드한다.
그리고나서 데이터 형식을 바꾸는 작업으로 진행된다.
일조시간 데이터는 변경된 것이 없어서, 형식이 다시 바뀌지 않는다. 하지만, 평균 기온 파일 새버젼이 나와서,
`doit`이 자동으로 `UK_Tmean_data.reformatted.txt` 파일을 다시 생성했다.

~~~ {.input}
    !tail UK_Tmean_data.reformatted.txt
~~~
~~~ {.output}
    2013-03-01,5.1
    2013-04-01,7.0
    2013-05-01,10.0
    2013-06-01,12.8
    2013-07-01,14.5
    2013-08-01,14.4
    2013-09-01,12.4
    2013-10-01,9.2
    2013-11-01,5.7
    2013-12-01,3.9
~~~


이제 데이터에 2013년부터 모든 정보가 담겨졌다.

### 도전과제:

기온과 일조시간 데이터 모두를 다운로드하는데 부과업을 사용해서 `download_temp_data.py` 파일을 편집하라.


    
