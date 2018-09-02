---
layout: page
title: 데이터 과학
subtitle: R 정규표현식
output:
  html_document: 
    keep_md: yes
  pdf_document:
    latex_engine: xelatex
mainfont: NanumGothic
---




### 학습 데이터 준비 [^regex-intro] [^regex-r] 

[^regex-intro]: [Regular Expression in R](https://stat545-ubc.github.io/block022_regular-expression.html)
[^regex-r]: [Regular Expressions and Character Data](https://stat545-ubc.github.io/block027_regular-expressions.html)
  
`git clone` 명령어로 정규표현식 실습을 위한 텍스트 데이터를 불러온다.
[STAT545](https://stat545-ubc.github.io/)와 연결된 [GitHub STAT545](https://github.com/STAT545-UBC/STAT545-UBC.github.io)
저장소를 클론한다.

~~~ {.shell}
$ git clone https://github.com/STAT545-UBC/STAT545-UBC.github.io.git
$ cd STAT545-UBC.github.io
~~~

GitHub 저장소에서 학습에 사용될 데이터를 가져왔으면, `list.files()` R 내부 쉘명령어로 저장소에서 로컬컴퓨터로 제대로 가져왔는지 확인한다.



~~~{.r}
install.packages("stringr")
~~~


`stringr` 팩키지에 나온 함수를 활용한다.
`stringr` 팩키지가 깔끔하고 현대적인 문자열 연산기능을 제공하고,
R에 기본으로 내장된 문자열 함수보다 사용하기 쉽고 기억하기도 좋다.
지금까지 사용한 적이 없다면, 팩키지를 설치하고 시작해본다.


~~~{.r}
library(stringr)
files <- list.files()
head(files)
~~~



~~~{.output}
[1] "_includes"                          
[2] "_layouts"                           
[3] "2018"                               
[4] "한밭대_창업과인공지능_20180503.Rmd" 
[5] "한밭대_창업과인공지능_20180529.html"
[6] "한밭대_창업과인공지능_20180529.Rmd" 

~~~



~~~{.r}
gDat <- read.delim("gapminderDataFiveYear.txt")
str(gDat)
~~~



~~~{.output}
'data.frame':	1704 obs. of  6 variables:
 $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
 $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
 $ pop      : num  8425333 9240934 10267083 11537966 13079460 ...
 $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
 $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
 $ gdpPercap: num  779 821 853 836 740 ...

~~~

문자열 함수를 사용해서 파일명을 추출한다. 예를 들어 `dplyr` 관련 문서.
`grep()` 함수를 사용해서 `dplyr` 문자열이 포함된 파일명을 식별한다.
인자값을 `value = TRUE` 으로 설정하면, `grep()` 함수가 매칭되는 것을 반환하는 반면에, `value = FALSE` 로 설정하면, 인덱스를 반환한다.
`invert` 인자를 사용하면, 지정된 패턴을 제외한 모든 것을 반환한다.
`grepl()` 함수는 유사한 함수지만, 논리벡터를 반환한다.
자세한 정보는 [여기](http://www.rdocumentation.org/packages/base/functions/grep)를 참조한다.


~~~{.r}
grep("dplyr", files, value = TRUE)
~~~



~~~{.output}
character(0)

~~~



~~~{.r}
grep("dplyr", files, value = FALSE)
~~~



~~~{.output}
integer(0)

~~~



~~~{.r}
grepl("dplyr", files)
~~~



~~~{.output}
  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [12] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [23] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [34] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [45] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [56] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [67] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [78] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [89] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[100] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
[111] FALSE FALSE FALSE FALSE FALSE FALSE FALSE

~~~

`dplyr` 팩키지와 관련된 모든 숙제 파일을 뽑아내려면 어떨까?
`hw` 문자열과 중간에 문자열 그리고 `dplyr` 문자열을 매칭하도록 명세할 필요가 있다.
이런 경우에 정규표현식이 편리하다.

### 정규표현식과 관련된 문자열 함수

정규표현식은 공통된 구조를 갖는 특정 문자열을 기술하는 패턴이다.
특정 구문에서 언어마다 약간 차이가 날 수 있지만, 모든 프로그래밍 언어에서 
공통적으로 문자열 매칭/치환에 사용되는 것이 정규표현식이다.
문자열 연산에 있어 정규표현식은 심장과 영혼이다.
`base` R과 `stringr` 팩키지 모두 문자열 함수는 정규표현식을 사용한다.
심지어 RStudio 찾기와 바꾸기 기능도 정규표현식을 허용한다.

  * 패턴과 매칭되는지 확인 : `grep(..., value = FALSE)`, `grepl()`, `stringr::str_detect()`
  * 패턴과 매칭되는 것을 추출 : `grep(..., value = TRUE)`, `stringr::str_extract()`, `stringr::str_extract_all()`
  * 문자열 내부에 패턴을 위치, 즉 매칭된 패턴의 시작점 부여 : `regexpr()`, `gregexpr()`, `stringr::str_locate()`, `string::str_locate_all()`
  * 매칭된 패턴 바꾸기 : `sub()`, `gsub()`, `stringr::str_replace()`, `stringr::str_replace_all()`
  * 패턴을 사용해서 문자열 쪼개기 : `strsplit()`, `stringr::str_split()`

### 정규표현식 구문

일반적으로 정규표현식은 문자열 내부에 반복되는 정보와 위치정보로 검색할 문자열 (혹은 문자열 클래스)를 명세한다.
특수한 의미를 갖는 메타문자를 사용해서 이런 작업을 완수한다: `$ * + . ? [ ] ^ { } | ( ) \`.
다음에 단순하고 작은 예제를 통해 정규표현식 구문과 메타문자가 어떤 의미도 함께 소개한다.

### 확장 문자열 (Escape Sequence)

문자열로 직접적으로 부호화할 수 없는 특수한 문자가 있다.
예를 들어, 단일 인용부호를 패턴으로 두고 단일 인용부호(`'`)를 갖는 국가를 검색한다고 가정하자.
패턴에서 단일 인용부호 앞에 `\` 부호를 넣어서 단일 인용부호를 "빠져나오게(escape)" 만들어야 한다.
그렇게 함으로써 문자열에서 명세하는 부분이 아님을 분명히 한다.


~~~{.r}
grep('\'', levels(gDat$country), value = TRUE)
~~~



~~~{.output}
[1] "Cote d'Ivoire"

~~~

R에는 확장 문자가 또 있다.
정규표현식을 포함해서, R에 모든 문자열함수에 동일한 규칙이 적용된다.
R 확장 문자열에 대한 전체 목록은 [여기](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Quotes.html)서 확인한다.

  * `\'`: 단일 인용부호. 
          이중 인용부호 내부에 있는 단일 인용부호를 빠져나올 필요는 없다. 그래서 앞선 예제에서 `"'"`도 사용한다.
  * `\"`: 이중 인용부호.
          마찬가지로 단일 인용부호 내부에 이중 인용부호를 사용할 수 있다. 즉, `'"'`.          
  * `\n`: 개행문자.
  * `\r`: 복귀문자.
  * `\t`: 탭문자.

> 주목: `cat()` 함수와 `print()` 함수는 확장문자열을 다르게 처리한다.
> 확장문자열을 해석된 그대로 문자열을 출력하려면, `cat()` 함수를 사용한다.


~~~{.r}
print("a\nb")
~~~



~~~{.output}
[1] "a\nb"

~~~



~~~{.r}
cat("a\nb")
~~~



~~~{.output}
a
b

~~~

### 정량자(Quantifiers)

정량자는 패턴을 얼마나 많이 반복할지 명세한다.

  * `*`: 적어도 0 번 매칭한다.
  * `+`: 적어도 1 번 매칭한다.
  * `?`: 많아아 1 번 매칭한다.
  * `{n}`: 정확히 n 번 매칭한다.
  * `{n,}`: 적어도 n 번 매칭한다.
  * `{n,m}`: n 번에서 m 번 매칭한다.


~~~{.r}
(strings <- c("a", "ab", "acb", "accb", "acccb", "accccb"))
~~~



~~~{.output}
[1] "a"      "ab"     "acb"    "accb"   "acccb"  "accccb"

~~~



~~~{.r}
grep("ac*b", strings, value = TRUE)
~~~



~~~{.output}
[1] "ab"     "acb"    "accb"   "acccb"  "accccb"

~~~



~~~{.r}
grep("ac+b", strings, value = TRUE)
~~~



~~~{.output}
[1] "acb"    "accb"   "acccb"  "accccb"

~~~



~~~{.r}
grep("ac?b", strings, value = TRUE)
~~~



~~~{.output}
[1] "ab"  "acb"

~~~



~~~{.r}
grep("ac{2}b", strings, value = TRUE)
~~~



~~~{.output}
[1] "accb"

~~~



~~~{.r}
grep("ac{2,}b", strings, value = TRUE)
~~~



~~~{.output}
[1] "accb"   "acccb"  "accccb"

~~~



~~~{.r}
grep("ac{2,3}b", strings, value = TRUE)
~~~



~~~{.output}
[1] "accb"  "acccb"

~~~

> 도전과제 {.challenges}
>
> 정량자를 사용해서 `gapminder` 데이터프레임에서 `ee` 문자열을 갖는 모든 국가를 찾아라.


~~~{.output}
[1] "Greece"

~~~

### 문자열 내부 패턴 위치

  * `^`:  문자열 시작위치를 매칭.
  * `$`: 문자열 끝위치를 매칭.
  * `\b`: *단어* 양쪽 끝에 위치한 빈문자열을 매칭. 문자열 끝을 표식하는 `^$`와 혼동하지 않는다.
  * `\B`: 단어 끝에 위치하지 않는 빈문자열을 매칭.


~~~{.r}
(strings <- c("abcd", "cdab", "cabd", "c abd"))
~~~



~~~{.output}
[1] "abcd"  "cdab"  "cabd"  "c abd"

~~~



~~~{.r}
grep("ab", strings, value = TRUE)
~~~



~~~{.output}
[1] "abcd"  "cdab"  "cabd"  "c abd"

~~~



~~~{.r}
grep("^ab", strings, value = TRUE)
~~~



~~~{.output}
[1] "abcd"

~~~



~~~{.r}
grep("ab$", strings, value = TRUE)
~~~



~~~{.output}
[1] "cdab"

~~~



~~~{.r}
grep("\\bab", strings, value = TRUE)
~~~



~~~{.output}
[1] "abcd"  "c abd"

~~~

> 도전과제 {.challenges}
>
> 해당 디렉토리에 `.txt` 확장자를 갖는 모든 파일을 찾는다.
> 
> 
> ~~~{.output}
> [1] "gapminderDataFiveYear.txt" "requirements.txt"         
> 
> ~~~

### 연산자

  * `.`: 어떤 문자 하나와 매칭. 
  * `[...]`: 문자 리스트. 꺾쇠괄호 내부에 지정된 문자중 하나와 매칭.
              문자범위를 지정하는데 꺽쇠 내부에 `-`도 사용가능. 
  * `[^...]`: 반전문자 리스트. `[...]`와 유사하지만, 꺾쇠괄호 내부에 있는 것을 **제외**한 문자 어떤 것이나 매칭 
  * `\`: 정규표현식 메타문자가 갖는 특수한 의미를 억제. 즉, `$ * + . ? [ ] ^ { } | ( ) \` 확장문자열 사용과 유사함. R에서 `\` 자체가 탈출할 필요가 있기 때문에, `\\$`처럼 역슬래쉬를 두개 사용해서 이런 메타문자를 탈출시킨다.   
  * `|`: "or" 또는 연산자. `|` 어느쪽이든 패턴을 매칭.  
  * `(...)`: 정규표현식에 있는 그룹연산자. 정규표현식으로 매칭된 부분을 나중에 불러올 수 있어서 추후 변경을 하거나 새로운 문자열을 만들어 내는데 사용할 수 있다. 각 그룹을 `\\N` 을 사용해서 참조할 수 있다. N은  `(...)` 에서 나온 번호에 해당되는 N 이다. 이것을 **역참조(backreference)**라고 부른다.


~~~{.r}
(strings <- c("^ab", "ab", "abc", "abd", "abe", "ab 12"))
~~~



~~~{.output}
[1] "^ab"   "ab"    "abc"   "abd"   "abe"   "ab 12"

~~~



~~~{.r}
grep("ab.", strings, value = TRUE)
~~~



~~~{.output}
[1] "abc"   "abd"   "abe"   "ab 12"

~~~



~~~{.r}
grep("ab[c-e]", strings, value = TRUE)
~~~



~~~{.output}
[1] "abc" "abd" "abe"

~~~



~~~{.r}
grep("ab[^c]", strings, value = TRUE)
~~~



~~~{.output}
[1] "abd"   "abe"   "ab 12"

~~~



~~~{.r}
grep("^ab", strings, value = TRUE)
~~~



~~~{.output}
[1] "ab"    "abc"   "abd"   "abe"   "ab 12"

~~~



~~~{.r}
grep("\\^ab", strings, value = TRUE)
~~~



~~~{.output}
[1] "^ab"

~~~



~~~{.r}
grep("abc|abd", strings, value = TRUE)
~~~



~~~{.output}
[1] "abc" "abd"

~~~



~~~{.r}
gsub("(ab) 12", "\\1 34", strings)
~~~



~~~{.output}
[1] "^ab"   "ab"    "abc"   "abd"   "abe"   "ab 34"

~~~

> 도전과제 {.challenges}
>
> `i` 혹은 `t` 를 포함하고, `land`로 끝나는 국가를 `gapminder` 에서 찾아 역참조를 사용해서 `land`를 `LAND`로 치환한다.
> 
> 
> ~~~{.output}
> [1] "FinLAND"     "IceLAND"     "IreLAND"     "SwaziLAND"   "SwitzerLAND"
> [6] "ThaiLAND"   
> 
> ~~~

### 문자열 클래스

문자열 클래스는 문자열 클래스 전체 예를 들어, 문자, 숫자를 명세하도록 한다. 문자열 클래스에 대한 두가지 선호방식이 존재한다. 한방식은 
꺾쇠괄호 내부 사전에 정의된 이름을 `[:`와 `:]` 사이에 사용하는 것이고, 또다른 방식은 `\` 와 특수문자를 사용하는 것이다. 두가지 방식은 때때로 상호바꿔서 사용가능하다.

  * `[:digit:]` 혹은 `\d` : 숫자, 0,1,2,3,4,5,6,7,8,9, 동등한 표현 `[0-9]`.
  * `\D` : 숫자가 아님, 동등한 표현 `[^0-9]`.
  * `[:lower:]` : 영문 소문자, 동등한 표현 `[a-z]`.
  * `[:upper:]` : 영문 대문자, 동등한 표현 `[A-Z]`.
  * `[:alpha:]` : 알파벳 대소문자, 동등한 표현 `[[:lower:][:upper:]]` 혹은 `[A-z]`
  * `[:alnum:]` : 알파벳 숫자 문자, 동등한 표현 `[[:alpha:][:digit:]]` 혹은 `[A-z0-9]`.
  * `\w` : 단어 문자, 동등한 표현 `[[:alnum:]]` 혹은 `[A-z0-9]`.
  * `\W` : 단어가 아닌 것, 동등한 표현 `[^A-z0-9]`.
  * `[:xdigit:]` : 16진수 (밑이 16), 0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f, 동등한 표현 `[0-9A-Fa-f]`.
  * `[:blank:]` : 간격 문자, 즉 스페이스와 탭.
  * `[:space:]` : 공백 문자: 탭, 개행문자, 수직탭, 공백, 복귀문자, 서식이송(form feed)
  * `\s` : 간격, ` `.
  * `\S` : 간격 아님.
  * `[:punct:]` : 구두점 문자, ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~.
  * `[:graph:]` : 그래픽 (사람이 읽을 수 있는) 문자, 동등한 표현 `[[:alnum:][:punct:]]`.
  * `[:print:]` : 출력가능한 문자, 동등한 표현 `[[:alnum:][:punct:]\\s]`.
  * `[:cntrl:]` : `\n`, `\r` 같은 제어문자, 동등한 표현 `[\x00-\x1F\x7F]`.

주의:

  * 꺾쇠 괄호 내부에 `[:...:]` 을 사용한다, 예를 들어 `[[:digit:]]`.
  * `\` 자체는 특수문자로 빼낼 필요가 있다. 예를 들어 `\\d`. 이런 정규표현식 `\t` 같은 R 확장 문자열을 혼동하지 않는다.


### 패턴에 대한 일반 모드

정규표현식에 대해서 서로 다른 [구문 표준](http://en.wikipedia.org/wiki/Regular_expression#Standards)이 존재한다. R은 두가지를 제공한다:

  * POSIX 확장 정규표현식 (기본설정)
  * 펄(Perl) 정규표현식

`grep()`, `sub()` 같은 `base` R 함수에서 `perl = FALSE/TRUE` 선택옵션 
설정을 통해 쉽게 모드를 전환할 수 있다.
`stringr` 팩키지 함수는 `perl()` 함수로 패턴을 감싼다.
두가지 표준 사이 구문에 다소간 차이가 존재하는데, 
자세한 사항은 [이곳](http://www.inside-r.org/packages/cran/stringr/docs/perl)을 참조한다.
파이썬 혹은 자바를 이전에 경험했다면, 아마 펄 모드에 더 익숙할 것이다.
하지만 이번 학습에서는 R에 기본으로 설정된 POSIX 표준만 사용한다.

마지막 정규표현식 유형이 하나 남아 있다 -- "고정(fixed)". 패턴이 글자그대로 받아들여져야 된다는 의미다.
(R `base` 함수) `fixed = TRUE` 로 명세하거나, (`stringr` 함수) `fixed()` 함수로 감싼다.
예를 들어, 정규표현식을 `"A.b"`와 같이 정의하면 "A" 다음에 임의 문자 하나 다음에 "b" 패턴을 갖는 문자열을 매칭하지만,
고정 패턴으로 정의하면, 문자그대로 "A.b"만 매칭한다.


~~~{.r}
(strings <- c("Axbc", "A.bc"))
~~~



~~~{.output}
[1] "Axbc" "A.bc"

~~~



~~~{.r}
pattern <- "A.b"
grep(pattern, strings, value = TRUE)
~~~



~~~{.output}
[1] "Axbc" "A.bc"

~~~



~~~{.r}
grep(pattern, strings, value = TRUE, fixed = TRUE)
~~~



~~~{.output}
[1] "A.bc"

~~~

기본디폴트 설정으로, 패턴매칭은 R에서 대소문자를 구별하지만, 
(R `base` 함수) `ignore.case =  TRUE` 혹은 (`stringr` 함수) `ignore.case()`로 대소문자 구별기능을 끌 수 있다.
대안으로, `tolower()` 혹은 `toupper()` 함수를 사용해서 모두 대문자, 혹은 모두 소문자로 전환할 수 있다.
위에서 사용된 예제로 실행해 본다:


~~~{.r}
pattern <- "a.b"
grep(pattern, strings, value = TRUE)
~~~



~~~{.output}
character(0)

~~~



~~~{.r}
grep(pattern, strings, value = TRUE, ignore.case = TRUE)
~~~



~~~{.output}
[1] "Axbc" "A.bc"

~~~

> 도전과제 {.challenges}
>
> `gapminder` 에서 `o` 문자가 들어간 대륙을 찾는다.
> 
> 
> ~~~{.output}
> [1] "Europe"  "Oceania"
> 
> ~~~


### 연습문제

앞에서 개별적으로 학습한 사항을 통합해보자. `dplyr` 팩키지에 대한 모든 학습 교재를 찾아,
지금까지 다룬 주제만 추출한다.
학습 교재는 다음과 같은 명명규칙을 따르고 있다:
`block` 다음에 숫자 3 자리, 그리고 나서, `_`, 그리고 마지막으로 학습주제.
[학습 인덱스](http://stat545-ubc.github.io/topics.html)를 살펴보면,
`dplyr`에 대한 학습교재가 두개 있다: [intro](http://stat545-ubc.github.io/block009_dplyr-intro.html),
[verbs for a single dataset](http://stat545-ubc.github.io/block010_dplyr-end-single-table.html).
학습교재 파일 두개에 대한 `.rmd` 파일명을 추출하자.
작업을 약간더 어렵게 만들고자, 파일명 전략과 매칭이 바로 되지 않는 몇개 가짜 파일을 GitHub 저장소에 넣었음을 밝혀둔다.

파일명에 `block`과 `dplyr` 단어가 있어야 되고 Rmd 확장자를 갖는 파일이어야 된다.
그래서 이 세가지 부분을 함께 넣으면 어떻게 될까?


~~~{.r}
pattern <- "block.*dplyr.*rmd"
grep(pattern, files, value = TRUE)
~~~



~~~{.output}
character(0)

~~~

원하는 파일을 제쳐두고, 가짜 3개 파일도 함게 딸려나왔다: block0_dplyr-fake.rmd, block000_dplyr-fake.rmd.txt, xblock000_dplyr-fake.rmd.
패턴이 충분히 엄격하지 못하다. 
어떤 파일은 `block` 다음에 숫자가 3자리도 아니다. 다른 파일은 `block`으로 시작하지도 않고,
`rmd` 다음에 `.txt` 확장자를 갖는 것도 있다.
이번에는 제대로 고쳐보자:


~~~{.r}
pattern <- "^block\\d{3}_.*dplyr.*rmd$"
(dplyr_file <- grep(pattern, files, value = TRUE))
~~~



~~~{.output}
character(0)

~~~

`dplyr_file` 변수에 파일명 두개가 저장되어 있다. 주제를 뽑아내보자.

학습주제를 뽑아내는 한방법은 `sub()`, `gsub()`, `str_sub()`같은 치환함수를 사용해서
학습주제 앞과 뒤에 오는 모든 것을 빈 문자열로 치환한다:


~~~{.r}
(dplyr_topic <- gsub("^block\\d{3}_.*dplyr-", "", dplyr_file))
~~~



~~~{.output}
character(0)

~~~



~~~{.r}
(dplyr_topic <- gsub("\\.rmd", "", dplyr_topic))
~~~



~~~{.output}
character(0)

~~~

다른 대안으로, `grep()` + `gsub()` 함수를 사용하는 대신에, `str_match()` 함수를 사용한다.
앞에서 언급했듯이, 해당 함수는 `()` 연산자로 감싼 패턴에 대한 매칭값을 전달한다.
정규표현식을 다시 구성해서 학습주제부분을 명세한다:


~~~{.r}
pattern <- "^block\\d{3}_.*dplyr-(.*)\\.rmd$"
(na.omit(str_match(files, pattern)))
~~~



~~~{.output}
     [,1] [,2]
attr(,"na.action")
  [1]   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17
 [18]  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34
 [35]  35  36  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51
 [52]  52  53  54  55  56  57  58  59  60  61  62  63  64  65  66  67  68
 [69]  69  70  71  72  73  74  75  76  77  78  79  80  81  82  83  84  85
 [86]  86  87  88  89  90  91  92  93  94  95  96  97  98  99 100 101 102
[103] 103 104 105 106 107 108 109 110 111 112 113 114 115 116 117
attr(,"class")
[1] "omit"

~~~

두번째 결과 데이터프레임 칼럼에 필요로 하는 학습주제 정보가 담겨있다.

### 고급 문자열 함수

정규표현식과 연관된 고급 문자열 함수가 더 있다: 문자열을 쪼개고, 문자열 부분집합을 뽑아내고, 문자열을 붙이는 등등.
이런 함수를 사용해서 데이터를 정제한다. 다음에 간략한 소개가 나와 있다.

앞선 연습문제에서 `dplyr` 학습주제를 두개 뽑았다. `strsplit()` 함수를 사용해서 단어로 쪼갠다.
두번째 인자 `split`은 쪼개는데 사용되는 정규표현식이고, 함수는 리스트를 반환한다.
`unlist()` 함수를 사용해서 리스트를 문자벡터로 변환한다.
혹은 `str_split_fixed()` 함수를 대신 사용해서 데이터프레임을 반환한다.


~~~{.r}
(topic_split <- unlist(strsplit(dplyr_topic[2], "-")))
~~~



~~~{.output}
[1] NA

~~~



~~~{.r}
(topic_split <- str_split_fixed(dplyr_topic[2], "-", 3)[1, ])
~~~



~~~{.output}
[1] "" "" ""

~~~

`paste()` 혹은 `paste0()` 함수를 사용해서 다시 붙여되돌린다.
`paste0()` 함수는 인자로 `sep=""`을 갖는 `paste()` 함수와 동일하다.
`collapse = "-"` 인자를 사용해서 문자열벡터를 문자열로 결합한다:


~~~{.r}
paste(topic_split, collapse = "-")
~~~



~~~{.output}
[1] "--"

~~~

또다른 유용한 함수는 `substr()` 으로 시작과 끝 위치정보를 갖는 문자열 일부를 추출하는데 사용된다.
예를 들어 `dplyr_topic`에서 처음 문자 3개만 뽑아내려면, 다음과 같이 코드를 작성한다:


~~~{.r}
substr(dplyr_topic, 1, 3)
~~~



~~~{.output}
character(0)

~~~

> 도전과제 {.challenges}
>
> 동료검토에 관련된 모든 마크다운 문서를 찾아서 해당 주제만 뽑아낸다.
>
> **힌트** : 파일명이 `peer-review`로 시작된다.
> 
> 

### 정규표현식과 쉘 글롭(glob)

유닉스 쉘 환경에서 글로빙(globbing)은 와일드카드 문자에 기반한 패턴매칭을 의미한다.
와일드카드 문자를 사용해서 문자열을 다른 문자로 치환한다.
글로빙을 파일명 혹은 경로를 매칭하는데 일반적으로 사용하지만, 훨씬 더 간결한 구문을 갖는다.
정규표현식과 다소 유사성이 있어 종종 사람들이 둘을 혼동하기도 한다.
다음에 글로빙 구문과 정규표현식에 대한 비교가 나와 있다:

  * `*` : 임의 문자를 임의 갯수만큼 매칭한다. 정규표현식 `.*` 와 동일.
  * `?` : 임의 문자를 한번만 매칭한다. 정규표현식 `.` 과 동일.
  * `\` : 정규표현식과 동일.
  * `[...]` : 정규표현식과 동일.
  * [!...] : 정규표현식 `[^...]`와 동일.


### 추가 학습교재

  * R 정규표현식 [공식 문서](https://stat.ethz.ch/R-manual/R-devel/library/base/html/regex.html)
  * 펄 정규표현식 : 펄 정규표현식 [매뉴얼](http://perldoc.perl.org/perlre.html#Regular-Expressions)
  * [`qdapRegex` 팩키지](http://trinkerrstuff.wordpress.com/2014/09/27/canned-regular-expressions-qdapregex-0-1-2-on-cran/) : 축약어, 날짜, 전자우편주소, 해쉬태그, 전화번호, 시간, 이모티콘, URL 등등 편리한 정규표현식 도구상자.
  * 최근에 가독성이 좋은 정규표현식을 개발하려는 시도가 있다. 루비 [Regularity](https://github.com/andrewberls/regularity)가
    상당한 성공을 거두었다. R 구현이 아직 베타 단계로 Regularity만큼 성능이 되지 않지만, 관심을 가지고 지켜보자.
  * 정규표현식을 학습할 수 있는 온라인 도구가 많이 있다. 데이터를 붙여놓고, 정규표현식을 작성하면, 매칭된 것을 강조하여 화면에 뿌려준다.
      - [regexpal](http://regexpal.com/)
      - [RegExr](http://www.regexr.com/)











