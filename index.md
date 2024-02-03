--- 
title: "数据工程讲义：经济学分册"
author: "史冬波 续本达"
date: "2024-02-03"
colorlinks: yes
bibliography:
- book.bib
- packages.bib
description: 适用于数据工程与数据科学课程的教材。
documentclass: ctexbook
geometry:
- b5paper
- tmargin=2.5cm
- bmargin=2.5cm
- lmargin=3.5cm
- rmargin=2.5cm
github-repo: yihui/bookdown-chinese
link-citations: yes
lof: yes
lot: yes
site: bookdown::bookdown_site
biblio-style: apalike
---



# 前言 {-}

终于，我要开始写这本书了。

我写了一本书。这本书是这样的，第 \@ref(intro) 章介绍了啥啥，第 \@ref(wind) 章说了啥啥，然后是啥啥……

我用了两个 R 包编译这本书，分别是 **knitr**\index{knitr} [@xie2015] 和 **bookdown**\index{bookdown} [@R-bookdown]。以下是我的 R 进程信息：

金山找：今日北方拳输给南方拳了！
叶问：你错了，不是南北拳的问题，是你的问题！


```r
sessionInfo()
```

```
## R version 4.2.0 (2022-04-22)
## Platform: x86_64-apple-darwin17.0 (64-bit)
## Running under: macOS Big Sur/Monterey 10.16
## 
## Matrix products: default
## BLAS:   /Library/Frameworks/R.framework/Versions/4.2/Resources/lib/libRblas.0.dylib
## LAPACK: /Library/Frameworks/R.framework/Versions/4.2/Resources/lib/libRlapack.dylib
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets 
## [6] methods   base     
## 
## other attached packages:
## [1] ggplot2_3.4.3
## 
## loaded via a namespace (and not attached):
##  [1] bslib_0.5.1       compiler_4.2.0   
##  [3] pillar_1.9.0      jquerylib_0.1.4  
##  [5] tools_4.2.0       digest_0.6.33    
##  [7] jsonlite_1.8.7    evaluate_0.21    
##  [9] lifecycle_1.0.3   tibble_3.2.1     
## [11] gtable_0.3.4      pkgconfig_2.0.3  
## [13] rlang_1.1.1       cli_3.6.1        
## [15] rstudioapi_0.15.0 yaml_2.3.7       
## [17] xfun_0.40         fastmap_1.1.1    
## [19] withr_2.5.0       dplyr_1.1.3      
## [21] knitr_1.43        generics_0.1.3   
## [23] vctrs_0.6.3       sass_0.4.7       
## [25] grid_4.2.0        tidyselect_1.2.0 
## [27] glue_1.6.2        R6_2.5.1         
## [29] fansi_1.0.4       rmarkdown_2.24   
## [31] bookdown_0.30     magrittr_2.0.3   
## [33] scales_1.2.1      htmltools_0.5.6  
## [35] colorspace_2.1-0  utf8_1.2.3       
## [37] munsell_0.5.0     cachem_1.0.8
```

## 致谢 {-}

非常感谢谁谁以及谁谁对我的帮助。艾玛，要不是他们神一样的队友，我两年前就写完这本书了。

哈哈哈哈！

\BeginKnitrBlock{flushright}<p class="flushright">史冬波  
于某角落</p>\EndKnitrBlock{flushright}

