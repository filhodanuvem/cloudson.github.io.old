--- 
layout: post
title: "Conversão de base numérica"
date: 2013-09-28 18:32
comments: true
categories: [computação]
---

Não sei se esse é um tópico desnecessário mas vejo muitas questões de prova da ZCE (php) que se relacionam com conversão de base numérica. Se ajudar uma pessoa no mundo, já fico satisfeito. 

### Começando do começo 
Com nossos algarismos indo-arábicos usamos a base 10. Ou seja, há 10 símbolos que representam qualquer número. Para um sistema computacional, a base utilizada é a 2, como você deve imaginar. Isso quer dizer que todas as representações utilizam 2 símbolos, o 0 e o 1. A seguir vamos ver como transformamos um número em base 10 para base 2: 
Para descrobrirmos qual a representação do número 9 (base 10) para base 2, divimos o mesmo por 2 sucessivamente: 

9 / 2 = 4 (resta 1)   
4 / 2 = 2 (resta 0)   
2 / 2 = 1 (resta 0)   

Quando o resultado da divisão inteira for 1, o método para e você já tem a resposta da conversão. 
O primeiro dígito binário é resultado da última divisão; 1. A seguir aparecem os restos das divisões de baixo para cima; 001. Ou seja, 9 em base 2 é 1001.   
Vamos repetir o método para alguns números 

8 / 2 = 4 (resta 0)   
4 / 2 = 2 (resta 0)  
2 / 2 = 1 (resta 0)   
8 em base 2 é 1000 

21 / 2 = 10 (resta 1)  
10 / 2 = 5  (resta 0)  
5  / 2 = 2  (resta 1)
2  / 2 = 1  (resta 0)
21 em base 2 é 10101

### E o caso contrário ? 

Eu tenho o número 1101 na base binária e quero saber qual sua representação na base decimal. Multiplicaremos cada dígito desse numero por 2 elevado a uma potência incremental (da direita pra esquerda). Ok, ficou mais confuso do que parece, veja abaixo: 

1101 = 1\*2^3 + 1\*2^2 + 0\*2^1 + 1\*2^0 = 8 + 4 + 0 + 1 = 13 

Para tirar a prova, vamos utilizar um número que convertemos na seção anterior, o 1001 (que deve dar 9):

1001 = 1\*2^3 + 0\*2^2 + 0\*2^1 + 1\*2^0 = 8 + 0 + 0 + 1 = 9 

Satisfeito? :) Não?   
Vamos pegar um número maior então, 101010:  

101010 = 1\*2^5 + 0 + 1\*2^3 + 0 + 1\*2^1 + 0 = 32 + 8 + 2 = 42 

### E se eu estiver em outra base? 

Quando citei o php lá no começo do post estava me referindo aos números octais nele, veja o código abaixo: 

{% codeblock lang:php %}
<?php
    $foo = 013; 
    $bar = $foo + 5; 
{% endcodeblock %}

Qual o valor de $bar no fim do script? 18? Não. O valor é 16.  
Se você nunca trabalhou com php, eu te perdôo. Números na linguagem que começam com 0 (da esquerda pra direita) são automaticamente interpretados como base 8. Questões relacionadas a octais na ZCE (certificação da linguagem) são comuns, então vamos a seguir fazer a conversão do octal 013 para decimal, para que assim tenhamos a prova da "hipótese" de que o resultado é 16.  
A verdade é que não há nenhuma regra nova, a conversão de binário para decimal e de octal para decimal seguem o mesmo método, porém a base muda de 2 para 8 então ... 

013 = 0\*8^2 + 1\*8^1 + 3\*8^0 = 0 + 8 + 3 = 11 

Viram a diferença? Multiplicamos cada dígito por 8 elevado a um expoente incremental (incremental da direita pra esquerda rs).  
11 + 5 = 16, como foi dito anteriormente.  

### Já que estamos aqui, vamos falar também de ... 

Hexadecimais! 16 símbolos para representar qualquer número. Os símbolos são : 

| Simbolo  |  Valor  | 
| -------- | --------| 
|   0      |   0    | 
| 1 | 1 |
| 2 | 2 |
| 3 | 3 | 
| 4 | 4 | 
| 5 | 5 | 
| 6 | 6 | 
| 7 | 7 | 
| 8 | 8 | 
| 9 | 9 | 
| a | 10 | 
| b | 11 | 
| c | 12 | 
| d | 13 |
| e | 14 | 
| f | 15 | 

Sendo redundante: A regra pra conversão de hexadecimal para decimal é a mesma, vamos converter o número #f00 para decimal: 

f00 = f\*16^2 + 0\*16^1 + 0\*16^0 = 15\*16^2 + 0\*16^1 + 0\*16^0 = 15*256 + 0 + 0 = 3840 .  

Perceba que usamos a tabela para converter as letras por valores numéricos, nada além disso. 

### Conclusão 
Com essas regrinhas, não há o que temer :). Dúvidas? Mande um comentário ali aí.  
Pretendo fazer mais posts de conhecimento menos específico como este, então nos veremos em breve. Até mais. 
