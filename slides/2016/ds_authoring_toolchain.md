<style>
.reveal h1, .reveal h2, .reveal h3 {
  word-wrap: normal;
  -moz-hyphens: none;
}
.small-code pre code {
  font-size: 1em;
}
.midcenter {
    position: fixed;
    top: 50%;
    left: 50%;
}
.footer {
    color: black; background: #E8E8E8;
    position: fixed; top: 90%;
    text-align:center; width:100%;
}
.pinky .reveal .state-background {
  background: #FF69B4;
} 
.pinky .reveal h1,
.pinky .reveal h2,
.pinky .reveal p {
  color: black;
}
</style>

�ѱ� 
========================================================
autosize: true
font-import: http://fonts.googleapis.com/css?family=jejugothic
font-family: 'jejugothic'


<div class="footer" style="margin-top:-50px;background-color:transparent;"><SPAN STYLE="font-size:120%;font-weight:bold;"><a href="http://www.webzen.com/">���� ������ ������</a><br>2016�� 10��</SPAN></div>



���� ����
========================================================

## ������õ�Ϻ�('15.08��)
- [���� ���� ���� ����Ʈ���� �����̴�!](https://goo.gl/RiXTIb)
- [��ǻ�� ���°� ����Ʈ���� ����](https://goo.gl/IXIW4n)

## �����Ͱ����ڸ� ���� ���� ��ü��
- [������ ������ ���� ���۵���](https://statkclee.github.io/ds-authoring/)
- [Modern Scientific Authoring](http://swcarpentry.github.io/modern-scientific-authoring/)

## [R Presentation](https://support.rstudio.com/hc/en-us/articles/200486468-Authoring-R-Presentations)
- [beamer_presentation, PDF](http://rmarkdown.rstudio.com/beamer_presentation_format.html)
- [ioslides_presentation, HTML](http://rmarkdown.rstudio.com/ioslides_presentation_format.html)
- [slidy_presentation, HTML](http://rmarkdown.rstudio.com/slidy_presentation_format.html)
- [revealjs::revealjs_presentation, HTML](http://rmarkdown.rstudio.com/revealjs_presentation_format.html)


������ �ٸ� ��� �з�����
========================================================

<img src="../img/data-conference-three-languages.png" alt="������" width="120%"/>


���� ��Ȳ ���
========================================================

- **�Ʒ��ѱ�, ���극���ǽ�, ����ũ�μ���Ʈ ���� ���� ����ũ�� ������ ����** :���ݱ��� �������� �ܼ��� ���۹��� �����ϴ� ���� ���� ���������, ���뼺�� ����, �Ҹ�Ȯ�ϰ� ������ ��ġ�ϴ� ����� ��������� �̾��ϰ�, �������� �ý��۰� ������ ���� �ʴ´�.
- **���� ���� ���� ����� ������ ����**:  ���峪 �ѱ�, ���극���ǽ��� �żӼ��� ���߰�, ���Ҿ� ������ ����(�ֳ��ϸ� ����̰� ���� �纻 �ϳ��� �����ϱ� ����)�ϰ� �Ѵ�. ������, ����� ������ ������ ������ ���뼺�� ���� �Ҹ�Ȯ�ϸ�, å���� �� �� ���� ����ȸ�� �ٱ��Ͽ� ��� �ް��� ���� �Ϳ� ���� ������� �������ϰ� �ִ�.
- **����ũ�� LaTeX**: ������ ���Ǿ��� ���İ� ����������� ���� �Ǹ��� ����� �����Ѵ�. �������� �ý��۰� ��ȭ�� �� �Ǵµ�, �Ϲ� �ؽ�Ʈ�� ������ �����ϱ� �����̴�. ������, ���ݱ��� �н��ϱ� ���� �����ϰ�, �ؽ�Ʈ�� �׸��� ���ϴ� ���� ��ġ��Ű�� �۾��� ��������Ե� ���ð� �ҿ�� �� �ִ�.
- **Authorea, Overleaf ���� ����� ����** : ������ ���� �������̽��� ���ڿ��� ���������� ������ LaTeX���� ����ǰ�, ��������� Ÿ�����ؼ� ���� ������ �ǽð����� ȭ�鿡 �ٽ� ����ؼ� �����ش�.
- **HTML** :  ���� ����Ƽ�� ���� LaTeX ���� �ξ� (�ξ�) �� �ܼ�������, �ξ� �� ���� ����� �����Ѵ�: �ּ�, ���������, ������ ��ȣ�ű�� ���� �ܼ��� ��ɵ� ���������� �������� �ʴ´�. ����� �������ϰ� ���� ���� �ְ�, CSS2�� ���������� �����ε� �����ϴ�.
- **��ũ�ٿ�** : HTML�� ���� �ܼ�ȭ ������� ���ߵǾ���. ��ũ�ٿ��� �Ϲ�-�ؽ�Ʈ ���ڿ��� ���ʸ� ����Ѵ�: ������ ������ �����ϰ�, ���Ÿ�ü�� ����µ� `*��ǥ*`�� ���δ� ���. HTML���� �� ���� �۾��� ����������, Ÿ���� ���� �ξ� �� ����. ������, �����ϰԵ� ���� ��� ��ũ�ٿ� ����������� ��ü���� ����� �߰��Ǿ ����ũ�ٿ� ǥ�ء��� �������� �ش�ȴ�.

������ ������ ���� ��ũ�ٿ�� LaTeX
======================================================
<br/>
### GUI ���� �ý���
- ����ũ�� ������ ���۽ý���: �Ʒ��ѱ�, MS ����
- Ŭ���� ������ ���۽ý���: ���۵���

### ��ũ�ٿ� : ������Ʈ�� ��α�
- ����: ��κ� ����� HTML�� ���ϴ� ��� ���� �״��� ���� Ÿ�������� �ʰ� ����
- ����: 
    - ���� ��κ��� ������������ �޾����� �ʱ� �����̴�.
    - ���� ���������ڰ� �̸� �޾��� ���ɼ��� ����. (����, ���� ���������ڰ� LaTeX�� ������� �ʾҴٸ�, LaTeX���� ��ȯ������ ���� ���̴١�).
    - ���п������� ���ϴ� ��� ������ ��ũ�ٿ��� �����ϴ� �ʴ´�. (���� ���, ������ ��������)


### LaTeX : �������� �� ����
- ����
    - PDF�� �ٸ� ǥ���������� ������ �۾��� �����Ѵ�.
    - �׸��� ǥ�� ��ġ�ϴµ� Ź���� ������ �����ش�.
    - ��������� �� ���� �۾��� �� �ִ�.
    - �������� ����Ʈ���� ������ ȣȯ�ȴ�.
    - ���� ���ο��� �޾��ִ� �����̴�. (������, �й� �о߸��� ����� �پ缺�� �����Ѵ�.)
- ���� 

�ѱ����̱� ������ �˾ƾ� �Ǵ� ��
=======================================================

![����� ��ȣ �׸��� ��ǻ��](../fig/auth-unicode-utf-8.png)

�����ڷ�: [R �����ڵ�, ���ڵ�](https://statkclee.github.io/ds-authoring/regex-encoding.html)

�ؽ�Ʈ ������ ó�� ���� : ���ڵ� 
=======================================================
<br/>
![`readr` �ΰ��� Ȯ��](../fig/auth-encoding-readr.png)

������ �۾� �帧 
=======================================================
<br/>
![���۹��� ����, ����, �� �Һ���� �̸��� ���޸�](../fig/latex-writing-supply-chain.png)

- �����ڿ� ���� ���̿� �ټ� �߰��ܰ� ����
    - ���� �� ������
    - ����
    - ���
    - ���� : ���� å��, �¶��� ����, ����å ��
- �� �ܰ躰 �η�, ����Ʈ����, ������ �� �ʿ�

������ ���� ���� �۾��帧 
=======================================================
<br/>
![�����ڿ� ���� �߰����� �ڵ�ȭ�� ���� ���۹� ���� ����](../fig/latex-writing_is_coding.png)


���� �帧 �� 
=======================================================
<br/>
![�����ڿ� ���� �߰����� �ڵ�ȭ�� ���� ���۹� ���� ����](../fig/latex-writing_is_coding.png)
***
<br/><br/><br/>
![���۹��� ����, ����, �� �Һ���� �̸��� ���޸�](../fig/latex-writing-supply-chain.png)


������ �ڵ�ȭ�ϴ� ���� ����
=======================================================
<br/>
![������ �ڵ�ȭ�ϴ� ���� ����](../fig/latex-how-it-works.png)

���̽� ����ũ��(Python Sphinx)
=======================================================
<br/>
![���̽� ����ũ�� ���� �帧](../fig/latex-sphinx.png)

- [Welcome to Reeborg's world!](http://reeborg.ca/index_en.html)
- [������ �ѱ��� ����](http://reeborg.ca/docs/ko/index.html)
- [���α׷��ְ� �����ذ�: ���̽�, ������, ����](http://reeborg.xwmooc.org/)

�ϴٿ�(Bookdown)
=======================================================
<br/>
## ��ġ���

1. <https://github.com/rstudio/bookdown-demo> GitHub���� ��ũ�� �߰�, git clone �Ͽ� ���� ��ǻ�Ϳ� �����Ѵ�.
2. RStudio ������� ������ �ٿ�ε� �ؼ� ��ġ�Ѵ�.
3. Ŭ���� bookdown-demo ����ҿ��� bookdown-demo.Rproj ������ Ŭ���ؼ� ����.
4. bookdown ��Ű���� ��ġ�Ѵ�. `devtools::install_github("rstudio/bookdown")`
5. index.Rmd R ��ũ�ٿ� ������ ����, RStudio build �ǿ� Build Book�� Ŭ���Ѵ�.

<br/>
## [���� ���� �����(OpenIntro Statistics)](https://statkclee.github.io/openIntro-statistics-bookdown/)

��θ� ���� ���̽�
=======================================================
<br/>

<img src="../fig/chuck_and_victor.jpg" alt="������� �����԰� �Բ�" width="77%" />

[��θ� ���� ���̽�(Programming for Everybody)](https://statkclee.github.io/pythonlearn-kr/)


R ��Ʈ��(Notebook)
=======================================================
<br/>
## ��ġ���

1. [�Ϻ� �ֽ� RStudio �ٿ�ε�](https://www.rstudio.org/download/daily/desktop/)
2. �ֽ� Rmarkdown ��Ű�� ��ġ: devtools::install_github("rstudio/rmarkdown")
3. RStudio ȯ�漳��: Tools & Global Options > Rmarkdown > R Notebook
4. File > New File > R Notebook
5.YAML ����� output: html_notebook ���� Ȯ��.

����: [R Notebooks from RStudio](http://rmarkdown.rstudio.com/r_notebooks.html)

���: ������ķ��(DataCamp)
=======================================================

<iframe src="https://player.vimeo.com/video/153250442?title=0&byline=0&portrait=0" width="640" height="375" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

[DataCamp ������ ����](https://www.datacamp.com/teach/documentation)
<p><a href="https://vimeo.com/153250442">DataCamp ������ ���� ������</a>, <a href="https://vimeo.com">Vimeo</a>.</p>

## [�ѱ��� R �Թ�](https://www.datacamp.com/courses/1709)

�� ��ǥ�ڷ� �ۼ�
=======================================================
<br/>
## R Presentations
<br/>
[Authoring R Presentations](https://support.rstudio.com/hc/en-us/articles/200486468-Authoring-R-Presentations)


�׸���, ������ ī��Ʈ��
=======================================================
<br/>

## A Data Carpentry Workshop
<br/>
### ���: �������б�
### �Ͻ�: Nov 06-07, 2016, 9:30 am - 5:30 pm
### ����: Kari Jordan, �̱���
<br/>
<https://statkclee.github.io/2016-11-06-sogang/>

�����ڷ�
========================================================

* [Top Five CSS Customizations for R Presentations, Andy Lyons, September, 2014](http://rstudio-pubs-static.s3.amazonaws.com/27777_55697c3a476640caa0ad2099fe914ae5.html#/)
* [Geospatial Data Processing and Analysis in R, Andy Lyons, Stanford University](https://github.com/ajlyons/rspatialdata/blob/master/Spatial-data-in-R.Rpres)
