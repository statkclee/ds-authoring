---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 파이썬 빌드 doit - `doit`이 작업을 실행할 필요가 있는지 판단하기 
---

## `doit`이 작업을 실행할 필요가 있는지 판단하기

> ### 학습목표 {.objectives}
> 
> - 작업이 다른 파일에 의존성을 가질 때, 작업을 갱신할지 `doit`이 어떻게 결정하는지 설명한다.
> - 만약 작업이 어떤 의존성도 갖지 못한다면 어떻게 결정되는지를 설명한다.
> - 작업 환경설정을 변경해서 `doit`이 갱신여부를 결정하도록 하는 방법을 설명한다.
> - `doit`이 어느 함수는 작업이고 어는 것은 아닌지를 어떻게 결정하는지 설명한다.

다음에 스크립트가 있는데, 원데이터 파일 모두 다운로드했다. 데이터 다운로드 작업은 다소 시간이 걸린다. 그래서, 모든 올바른 파일명을 새로운
파이썬 함수로 계산하는 부분을 리팩토링했다.

~~~ {.input}

    
    # download_all_data.py
    
    data_sets = ['Tmean', 'Sunshine']
    
    def get_data_file_parameters(data_type):
        """Takes a string describing the type of climate data, returns url and file name for that data"""
        
        base_url = 'http://www.metoffice.gov.uk/climate/uk/datasets/{0}/ranked/UK.txt'
        data_url = base_url.format(data_type)
        data_target = 'UK_{0}_data.txt'.format(data_type)
        return data_url, data_target
    
    def task_download_data():
        """Downloads all raw data files from the Met Office website"""
    
        for data_type in data_sets:
            data_url, data_target = get_data_file_parameters(data_type)
            yield {
                'actions': ['wget -O %(targets)s {0}'.format(data_url)],
                'targets': [ data_target ],
                'name' : data_type,
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
    .  download_data:Tmean
    .  download_data:Sunshine
    -- reformat_data:UK_Sunshine_data.txt
    -- reformat_data:UK_Tmean_data.txt
    

    --2014-04-05 12:08:57--  http://www.metoffice.gov.uk/climate/uk/datasets/Tmean/ranked/UK.txt
    Resolving www.metoffice.gov.uk (www.metoffice.gov.uk)... 23.63.99.234, 23.63.99.216
    Connecting to www.metoffice.gov.uk (www.metoffice.gov.uk)|23.63.99.234|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 25576 (25K) [text/plain]
    Saving to: ‘UK_Tmean_data.txt’
    
         0K .......... .......... ....                            100% 1.51M=0.02s
    
    2014-04-05 12:08:57 (1.51 MB/s) - ‘UK_Tmean_data.txt’ saved [25576/25576]
    
    --2014-04-05 12:08:57--  http://www.metoffice.gov.uk/climate/uk/datasets/Sunshine/ranked/UK.txt
    Resolving www.metoffice.gov.uk (www.metoffice.gov.uk)... 23.63.99.216, 23.63.99.234
    Connecting to www.metoffice.gov.uk (www.metoffice.gov.uk)|23.63.99.216|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 20986 (20K) [text/plain]
    Saving to: ‘UK_Sunshine_data.txt’
    
         0K .......... ..........                                 100% 6.48M=0.003s
    
    2014-04-05 12:08:57 (6.48 MB/s) - ‘UK_Sunshine_data.txt’ saved [20986/20986]
~~~    
    

여기서 눈여겨볼 점은 두가지다. 첫째로, 새로운 파이썬 함수, `get_data_file_parameters`는 `task_`로 시작되지
않는다. 그래서 `doit`이 작업으로 실행할 필요는 없다.

두번째로, 얼마나 많이 상기 스크립트를 실행하는지 관계없이, `doit`은 항상 데이터 파일을 다시 다운로드한다. 파일들이 어디에도 의존성을
갖지 않기 때문에, `doit`은 파일들의 갱신여부를 확인할 수 없다. 이런 연유로 인해서 항상 다시 다운로드한다.

만약 사람이 스스로 이 모든 작업을 수행한다면, 아마도 매달 월별 기온 데이터만 갱신할 것이다.
`Doit`을 가지고 작업 환경설정 딕셔너리에 `uptodate`라는 또다른 매개변수를 정의할 수 있다.
파이썬 함수를 정의해서 `doit`이 작업을 다시 실행할지 여부를 결정하게한다.

이것이 `doit`을 사용하는 가장 큰 이점 중 하나다: 작성할 수 있는 파이썬 코드는 모두 타당하다. 사실 부과업을 생성하는데 작업 생성기를
사용하는 것 조차도 관례에 불과하다. 작업환경설정을 딕셔너리로 작성된 임의 파이썬 스크립트도 `doit`과 함께 동작한다.

원데이터 파일을 최신 상태로 유지하는 문제로 돌아가서, 본인이 스스로 함수를 작성해서 원데이터 파일이 얼마나 오래되었는지 점검할 수 있다.
하지만, `doit`에는 고맙게도 번거로운 작업을 수행해주는 유틸리티 함수가 따라온다.
4주 후에 데이터 파일 기한을 만료시키자:

~~~ {.input}
    
    # monthly_raw_data_update.py
    
    import datetime
    from doit.tools import timeout 
    
    data_sets = ['Tmean', 'Sunshine']
    
    def get_data_file_parameters(data_type):
        """Takes a string describing the type of climate data, returns url and file name for that data"""
    
        base_url = 'http://www.metoffice.gov.uk/climate/uk/datasets/{0}/ranked/UK.txt'
        data_url = base_url.format(data_type)
        data_target = 'UK_{0}_data.txt'.format(data_type)
        return data_url, data_target
    
    def task_download_data():
        """Downloads all raw data files from the Met Office website"""
    
        for data_type in data_sets:
            data_url, data_target = get_data_file_parameters(data_type)
            yield {
                'actions': ['wget -O %(targets)s {0}'.format(data_url)],
                'targets': [ data_target ],
                'name' : data_type,
                'uptodate': [timeout(datetime.timedelta(weeks=4))],
    
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
    .  download_data:Tmean
    .  download_data:Sunshine
    -- reformat_data:UK_Sunshine_data.txt
    -- reformat_data:UK_Tmean_data.txt
    

    --2014-04-05 12:08:57--  http://www.metoffice.gov.uk/climate/uk/datasets/Tmean/ranked/UK.txt
    Resolving www.metoffice.gov.uk (www.metoffice.gov.uk)... 23.63.99.234, 23.63.99.216
    Connecting to www.metoffice.gov.uk (www.metoffice.gov.uk)|23.63.99.234|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 25576 (25K) [text/plain]
    Saving to: ‘UK_Tmean_data.txt’
    
         0K .......... .......... ....                            100% 1.87M=0.01s
    
    2014-04-05 12:08:57 (1.87 MB/s) - ‘UK_Tmean_data.txt’ saved [25576/25576]
    
    --2014-04-05 12:08:57--  http://www.metoffice.gov.uk/climate/uk/datasets/Sunshine/ranked/UK.txt
    Resolving www.metoffice.gov.uk (www.metoffice.gov.uk)... 23.63.99.216, 23.63.99.234
    Connecting to www.metoffice.gov.uk (www.metoffice.gov.uk)|23.63.99.216|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 20986 (20K) [text/plain]
    Saving to: ‘UK_Sunshine_data.txt’
    
         0K .......... ..........                                 100% 6.86M=0.003s
    
    2014-04-05 12:08:57 (6.86 MB/s) - ‘UK_Sunshine_data.txt’ saved [20986/20986]
~~~    
    

두번째로 상기 스크립트를 실행하면, `doit`이 원데이터파일이 4주 경과되지 않았다는 것을 알고서 다시 다운로드하지 않는다.

모든 작업을 이와 같은 방식으로 정의하는 가장 큰 이점은 신규 데이터셋을 추가하는 것이 훨씬 더 쉽다는 것이다.
데이터셋 목록에 `Rainfall`을 추가해서 강수량 데이터를 다운로드하자:

~~~ {.input} 
    # rainfall_data.py
    
    import datetime
    from doit.tools import timeout 
    
    data_sets = ['Tmean', 'Sunshine', 'Rainfall']
    
    def get_data_file_parameters(data_type):
        """Takes a string describing the type of climate data, returns url and file name for that data"""
    
        base_url = 'http://www.metoffice.gov.uk/climate/uk/datasets/{0}/ranked/UK.txt'
        data_url = base_url.format(data_type)
        data_target = 'UK_{0}_data.txt'.format(data_type)
        return data_url, data_target
    
    def task_download_data():
        """Downloads all raw data files from the Met Office website"""
    
        for data_type in data_sets:
            data_url, data_target = get_data_file_parameters(data_type)
            yield {
                'actions': ['wget -O %(targets)s {0}'.format(data_url)],
                'targets': [ data_target ],
                'name' : data_type,
                'uptodate': [timeout(datetime.timedelta(weeks=4))],
    
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
    .  download_data:Rainfall
    -- download_data:Tmean
    -- download_data:Sunshine
    .  reformat_data:UK_Rainfall_data.txt
    -- reformat_data:UK_Sunshine_data.txt
    -- reformat_data:UK_Tmean_data.txt
    

    --2014-04-05 12:08:58--  http://www.metoffice.gov.uk/climate/uk/datasets/Rainfall/ranked/UK.txt
    Resolving www.metoffice.gov.uk (www.metoffice.gov.uk)... 23.63.99.234, 23.63.99.216
    Connecting to www.metoffice.gov.uk (www.metoffice.gov.uk)|23.63.99.234|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 25518 (25K) [text/plain]
    Saving to: ‘UK_Rainfall_data.txt’
    
         0K .......... .......... ....                            100% 1.68M=0.01s
    
    2014-04-05 12:08:58 (1.68 MB/s) - ‘UK_Rainfall_data.txt’ saved [25518/25518]
~~~    
    

이제 반짝반짝 빛나는 다음 단계 분석작업을 위해서 올바른 형식으로 맞춰진 강수량 데이터가 준비되었다:

~~~ {.input}
    !head UK_Rainfall_data.reformatted.txt
~~~
~~~ {.output}
    month,value
    1910-01-01,111.4
    1910-02-01,79.5
    1910-03-01,75.5
    1910-04-01,69.1
    1910-05-01,66.4
    1910-06-01,65.3
    1910-07-01,81.6
    1910-08-01,90.3
    1910-09-01,92.0
~~~


### 도전과제:

- `doit`을 사용해서 만약 20초이상 경과되면 원데이터를 다운로드 하도록, `rainfall_data.py` 파일을 편집한다.
- `uptodate` 환경설정 행을 download_data 작업에서 reformat_data task 작업으로 이동하면 무슨 일이 발생할까?
- 시도해보고, 맞는지 살펴보라!
