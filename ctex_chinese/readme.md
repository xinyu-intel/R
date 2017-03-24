Intro to the method of knitring PDF files including Chinese:

## method1

1.you should install the latest ctex software on your machine, and be sure to choose the full installization package.

2.the whole library 'ctex_chinese' should be copied to ~/R/library/rmarkdown/rmarkdown/templates so that you can start writing a chinese rmarkdown based on a template.

3.the file 'header.tex' should be copied to your working library.

## method2

1.you should install the latest ctex software on your machine, and be sure to choose the full installization package.

2.insert the following code in the document header information:

```
---
title: "标题"
author: "你的名字"
output:
  pdf_document:
	includes:
	  in_header: header.tex
	keep_tex: yes   
	latex_engine: xelatex
  word_document: default
  html_document: default
---
```
	
3.the file 'header.tex' should be copied to your working library.

## Attention

The methods above can only work on a windows machine, if you are using a MacBook or a Linux machine, you can modify header.tex to include Tex live packages, or you can first use a GitHub template to knitr a HTML page and then print it to PDF. It works well, too.
