---
layout: post
title: "Ligando extensões a linguagens no Vim"
date: 2014-10-15 23:31
comments: true
categories: ["vim", "shell"]
---

Estou engatinhando no vim, porém já venho substituído meu editor de texto principal (o sublime). Trabalhando com Drupal, muitos arquivos possuem extensões que fogem do padrão da linguagem php, então nesse pequeno post mostro como dizer pro editor que a extensão X refere-se à linguagem Y. 


Primeiramente criarei um arquivo de configuração de nome .vimrc na home do meu usuário local. 

{% codeblock lang:bash %}
" em ~/.vimrc

autocmd BufRead,BufNewFile \*.module set filetype=php

{% endcodeblock %}

Com essa linha estamos executando um comando automaticamente no vim assim que lermos ou abrirmos um arquivo que termina com **.module**, esse comando seta o editor dizendo que o tipo de arquivo é um php. 
Como executar esse arquivo a cada chamada do vim ? Vamos chamá-lo no arquivo /etc/vim/vimrc no debian

{% codeblock lang:bash %}

" final do arquivo /etc/vim/vimrc

. "~/.vimrc"
{% endcodeblock %}

Pronto! :) 
Este post é mais uma daquelas dicas rápidas que seguem de lembrete pra mim. Caso você descubra uma forma mais legal de resolver o problema, não deixe de comentar. Abraços. 
