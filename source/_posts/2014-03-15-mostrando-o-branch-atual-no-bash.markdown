---
layout: post
title: "Mostrando o branch atual no bash"
date: 2014-03-15 22:19
comments: true
categories: [bash, console, linux]
---

Quando estamos num console unix vemos uma área fixa que informa - geralmente - o usuário, a máquina e o diretório em que estamos no momento. 

{% codeblock lang:bash %}
cloudson@Matrix:/var/www$
{% endcodeblock %}

Mas eu demorei um bocado para saber que dava para editar o padrão daquele área.  
Existe uma váriavel PS1 relacionado a ele. Poderíamos setar 

{% codeblock lang:bash %}
export PS1="\u:\w$";
{% endcodeblock %}

Isso irá exibir `cloudson:/var/www$` pois \u ser refere ao usuário e \w ap diretório atual. Podemos usar várias outras varáveis pra compor a informação:

Caractere  |  Uso
 -------- | -------- 
\d  |  Data, no formato "DiaDaSemana Mês Dia" (exe; "Tue May 26").   
\h  |  O nome do host. (exe; Matrix)   
\j  |  O número de jobs atualmente rodando via shell. 
\t  |  Tempo, no formato de 24 horas HH:MM:SS . 
\T  |  Tempo,  no formato de 12 horas HH:MM:SS . 
\@  |  Tempo, no formato 12 horas am/pm. 
\u  |  O nome do usuário atual. 
\v  |  A versão do Bash (ex; 2.00) 
\w  |  A atual diretório. 
\W  |  O nome base do $PWD. 
\!  |  O número histórico desse comando. 
\$  | Se você não for root, insere um "$"; se for root, você terá um "#" .
\n  | Uma quebra de linha.  
\e  | An escape character.   

Um exemplo útil seria exibir em qual branch estamos, caso possível.

{% codeblock lang:bash %}
export PS1="\u@\h:\w$(git branch 2>/dev/null | grep '^*')$ ";
{% endcodeblock %}

E é isso! Agora não há perigo de estar num branch errado e só perceber tarde :)

Referências:  

* http://ss64.com/bash/syntax-prompt.html
* http://tldp.org/HOWTO/Bash-Prompt-HOWTO/x329.html  


