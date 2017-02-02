Intro to the method of knitring PDF files including Chinese:

method1
##you should install the latest ctex software on your machine, and be sure to choose the full installization package.
##the whole library 'ctex_chinese' should be copied to ~/R/library/rmarkdown/rmarkdown/templates so that you can start writing a chinese rmarkdown based on a template
##the file 'header.tex' should be copied to your working library.

method2
##you should install the latest ctex software on your machine, and be sure to choose the full installization package.
##insert the following code in the document header information:
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
##the file 'header.tex' should be copied to your working library.