---
title: Vim小技巧
reward: false
date: 2018-08-13 01:54:04
tags:
	- vim
---

# YCM配置

Python的设置在`./YouCompleteMe/third_party/ycmd/PYTHON_USED_DURING_BUILDING`中。
只允许单行，若运行`install.py`时无特殊设置则使用Python2.

YCM有默认接口文档，但有时很不方便，禁止显示的方式是在`.vimrc`文件中添加：
`` shell
Plug 'Valloric/YouCompleteMe'
set completeopt-=preview
let g:ycm_add_preview_to_completeopt = 0 
```

