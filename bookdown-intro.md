---
layout: page
title: 데이터 과학을 위한 저작도구
subtitle: 북다운(bookdown)
mainfont: NanumGothic
---


### 북다운 [^rmarkdown] [^bookdown] [^bookdown-book]

[^rmarkdown]: [R Markdown](http://rmarkdown.rstudio.com/)
[^bookdown]: [bookdown](https://github.com/rstudio/bookdown)
[^bookdown-book]: [Bookdown: Authoring Books with R Markdown](https://bookdown.org/yihui/bookdown/)

[R 마크다운](http://rmarkdown.rstudio.com/)을 활용한 다양한 저작물이 [bookdown](https://bookdown.org/) 웹사이트를 통해 공유되고 있다.

> ### 북다운이 갖는 주요 기능 {.callout}
> 
> * 다양한 출력 양식: HTML, PDF, ePub.
> * 시각적으로 미려한 기능을 갖는 책을 쉽게 만들 수 있다.
> * Gitbook (https://www.gitbook.com), Tufte CSS (http://rstudio.github.io/tufte/), Tufte-LaTeX 스타일이 포함된다.
> * 마크다운 구문을 확장해서 그림/표 번호 매기기, 상호참조 등을 지원한다.
> * 인터랙티브 HTML 위젯과 Shiny 앱을 책에서 지원한다.
> * RStudio IDE와 통합된 개발기능 지원
> * 한번 클릭으로 https://bookdown.org 사이트에 출판

### 북다운 설치

북다운을 체험하는 가장 쉬운 방법중에 하나는 이미 북다운으로 개발된 예제 책을 통해 살펴보는 것이다.

1. [https://github.com/rstudio/bookdown-demo](https://github.com/rstudio/bookdown-demo) GitHub에서 포크를 뜨고, `git clone` 하여 로컬 컴퓨터에 저장한다.
1. [RStudio 사전출시 버젼](https://www.rstudio.com/products/rstudio/download/preview/)을 다운로드 해서 설치한다.
1. 클론한 `bookdown-demo` 저장소에서 `bookdown-demo.Rproj` 파일을 클릭해서 연다.
1. **bookdown** 팩키지를 설치한다.

~~~ {.r}
devtools::install_github("rstudio/bookdown")
~~~

5. `index.Rmd` R 마크다운 파일을 열고, RStudio `build` 탭에 `Build Book`을 클릭한다.


