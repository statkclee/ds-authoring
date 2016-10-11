---
layout: page
title: 데이터 과학
subtitle: R 노트북(Notebook)
output:
  html_notebook: 
    keep_md: yes
  pdf_document:
    latex_engine: xelatex
mainfont: NanumGothic
---



### 1. R 노트북 설치 [^r-bloggers-notebook] [^r-rstudio-notebook]

[^r-bloggers-notebook]: [Setting Up New R Notebook](https://www.r-bloggers.com/setting-up-new-r-notebook/)
[^r-rstudio-notebook]: [R Notebooks](http://rmarkdown.rstudio.com/r_notebooks.html)

R 노트북 설치는 최신 RStudio 버젼을 다운로드 받으면 설정이 되어 있기 때문에, 별도 준비가 필요없다.
다만 `rmarkdown` 의존성이 있어 설치를 확인하고 바로 `R Notebook`을 사용할 수 있다.

1. [일별 최신 RStudio](https://www.rstudio.org/download/daily/desktop/) 다운로드
1. 최신 `Rmarkdown` 팩키지 설치: `devtools::install_github("rstudio/rmarkdown")`
1. RStudio 환경설정: Tools & Global Options > Rmarkdown > **R Notebook**
1. File > New File > R Notebook
1. YAML 헤더에 `output: html_notebook` 설정 확인.






