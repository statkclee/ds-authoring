---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 정규표현식
---

> ## 학습에 들어가기 전 {.prereq}
>
> * 본 정규표현식 교재는 [Software Carpentry](http://software-carpentry.org/) 
> [Regular Expression](http://swcarpentry.github.io/v4/regexp/index.html)을 번역한 것입니다.
> * 기본적인 프로그래밍 개념을 이미 이해하고 있고, 
> 파이썬 기본구성요소에 친숙하거나 신속히 숙달할 수 있음을 가정한다.


정규표현식(Regular Expression)은 연관된 문자열 집합을 매칭하는 패턴이다.
정규표현식이 매칭에 실패하는 패턴도 있지만, 
레거시 텍스트 파일에서 정보를 추출할 때, 프로그래머 대부분이 사용하는 강력한 도구다.

> ## 주요점(key points) {.objectives}
>
> * 정규표현식은 문자열로 작성한다(따라서, 표기법이 다소 세련되지 못하다).
> * 알파벳과 숫자는 그 자체로 매칭되고, 한글도 매칭된다.
> * 반복되는 문자에 대해 `*`, `+`, `?` 특수기호를 사용한다.
> * `|`을 사용해서 또는 혹은 문자 집합을 매칭한다.
> * 괄호를 사용해서 문자열을 집단으로 묶고, 매칭되는 정보를 추출한다.
> * 정규표현식 라이브러리를 사용해서 매칭되는 모든 것을 찾고, 문자열을 바꾸고, 기타 연산작업을 수행한다.

### 학습 목차

- [정규표현식 소개](regex-intro.html)
- [간단한 패턴](regex-simple-pattern.html)
- [연산자](regex-operators.html)
- [작동원리](regex-under-the-hood.html)
- [추가 패턴](regex-more-pattern.html)
- [참고문헌 사례](regex-last-wrinkle.html)
- [요약](regex-wrapup.html)

### 소프트웨어 카펜트리 V4 동영상

| 소개 및 간단한 패턴  | 연산자 | 동작원리 |
|:------------------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------------:|
| <iframe width="250" src="https://www.youtube.com/embed/c-Ov1JUMDv4" frameborder="0" allowfullscreen></iframe> | <iframe width="250" src="https://www.youtube.com/embed/G7_HnivvnyE" frameborder="0" allowfullscreen></iframe> | <iframe width="250" src="https://www.youtube.com/embed/iixnLh55wp0" frameborder="0" allowfullscreen></iframe> |

| 추가 패턴  | 참고문헌 사례 | 
|:------------------------------------------------------------------:|:------------------------------------------------------------------:|
| <iframe width="250" src="https://www.youtube.com/embed/FgxQyukp39A" frameborder="0" allowfullscreen></iframe> | <iframe width="250" src="https://www.youtube.com/embed/RGN5tS-2Zmo" frameborder="0" allowfullscreen></iframe> |





