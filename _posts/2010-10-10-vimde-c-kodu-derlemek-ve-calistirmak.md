---
layout: post
title: vim'de c kodunu derlemek ve çalıştırmak
excerpt:
categories: articles
comments: true
share: true
---

- SingleCompile eklentisini indir:

       wget -O SingleCompile.zip 'http://www.vim.org/scripts/download_script.php?src_id=14010'

- kur:

       sudo apt-get install unzip # unzip olmalı
       unzip -d ~/.vim SingleCompile.zip

- ~/.vimrc'ye şu ayarları ekle:

       nmap <f9> :SCCompile<cr>
       nmap <c-f9> :SCCompileRun<cr>

f9 sadece derler.  ctrl-f9 derler ve çalıştırır.

