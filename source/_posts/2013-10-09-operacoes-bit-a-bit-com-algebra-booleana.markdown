---
layout: post
title: "Operações bit a bit com álgebra booleana"
date: 2013-10-09 22:39
comments: true
categories: 
published: false
---

Hoje vamos falar um pouco sobre álgebra booleana ou álgebra de boole. Recebeu esse nome em homenagem a George Boole, um matemático inglês que criou o sistema de lógica no século XIX [1](http://pt.wikipedia.org/wiki/%C3%81lgebra_booleana).  
Em cursos de conputação, essa disciplina se encontra no primeiro período, ela serve de base até mesmo para algoritmos, outra cadeira base desse tipo de curso. Neste post vamos apresentar a  álgebra e relacioná-la com linguagens de programação, utilizando o php como exemplo.  

### Domínio e operação de adição
Como já sabemos de [outros carnavais](http://cloudson.github.io/2013/09/28/conversao-de-base-numerica/) a álgebra booleana trabalha com valores binários, verdadeiro ou falso, ligado ou desligado, corrente elétrica ou ausência dela, ou por fim, 1 0u 0. Baseado nisso podemos realizar operações sob esses valores.  
A adição representa o mesmo que o OR lógico. Então o resultado entre 1 + 0 é 1, já que podemos ler "verdadeiro ou falso resulta em verdadeiro". A partir disso temos a velha piada de que 1 + 1 na computação é 1 porque "verdadeiro ou verdadeiro resulta em verdadeiro". 
Com duas variáveis lógicas ($a e $b) e o operador de adição, podemos montar todos os valores resultantes possíveis, no que chamamos de *tabela verdade* : 

$a|$b|$a*b
--|---|---
1|1|1
1|0|1
0|1|1
0|0|0
