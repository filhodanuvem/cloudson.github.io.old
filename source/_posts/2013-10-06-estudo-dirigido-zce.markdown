---
layout: post
title: "Estudo dirigido ZCE"
date: 2013-10-06 21:43
comments: true
categories: [php, zce]
---

A ZCE é a prova de cerficação oficial para programadores php. Essa semana a Zend anunciou uma [nova prova baseada nas versões mais atuais do php](http://shop.zend.com/en/zend-php5-certification-voucher.html). Na phpconference do ano passado, consegui um voucher pra cerficação, e até hoje não fui fazer a prova. 
O tempo está passando e eu acabarei indo pra guerra por livre e espontânea pressão.    
Mas como dizem que a melhor forma de aprender é ensinando, então a proposta é gerar posts com questões para (eu mesmo) estudar pra prova. Dessa vez vamos abordar questões bem simples, muitas sobre coerção de tipos e conversão de base numérica.  
As respostas para as questões estão no final do post, se encontrar algum erro, é só deixar um comentário. 

### 1- Qual dos seguintes comentários é inválido em php? :
a) // foo   
b) /\* bar \*/   
c) # baz  
d) None above  

### 2- Qual a saída do código abaixo:

{% codeblock lang:php %}
<?php
    $foo = 017; 
    echo $foo + 1;   
{% endcodeblock %}
a) Fatal error  
b) 18    
c) 16  
d) 25  

### 3- Selecioee uma ou mais alternativas corretas: 

a) 3.14 == 3.142  
b) 314e-2 == 3.14   
c) 3.14 == 314e2   
d) 3 == 3.14    

### 4- Selecione uma ou mais alternativas incorretas:   

a) 0xF < 015   
b) 0x0a0 == 160    
c) 0xf < 15   
d) 0xf == 0x0f   

### 5- Veja o código abaixo e assinale a alternativa correta: 
{% codeblock lang:php %}
<?php
    $cloud = 23;
    $zce   = true;   
    return $cloud == true;
{% endcodeblock %}

a) true  
b) false  
c) 23   
d) None above  

### 6- Selecione um ou mais respostas corretas:
a) "" == false  
b) "00" == false  
c) "0" == false  
d) " " == false  
e) "false" == true  

### 7- Veja o código abaixo e responda :

{% codeblock lang:php%}
<?php 

    $foo = "Zend Compiler";
    $2foo = "Hip Hop";
    return $foo > $2foo; 
{% endcodeblock %}
a) true  
b) false  
c) Error   
d) "Zend Compiler Hip Hop"  

### 8 - Qual a saída do código abaixo:
{% codeblock lang:php %}
<?php

function menu()
{
	return "====ZCE day!====";
}

$foo = "menu"; 
echo $foo();
{% endcodeblock %}

a) Fatal error  
b) "menu"  
c) "====ZCE day!===="  
d) None above  

### 9 - Qual a saída do código abaixo: 
{% codeblock lang:php %}

<?php 
    $foo = null;
    $bar = new stdClass; 
    var_dump(isset($foo), isset($bar), isset($baz));
{% endcodeblock %}

a) Fatal Error  
b) false, true, false  
c) false, false, true   
d) true, true, false   

### 10 - Selecione uma ou mais respostas incorretas:

a) 1 | 2 == 1  
b) 1 ^ 3 == 2   
c) 2 ^ 3 == 8   
d) 2 & 3 == 2   

### Respostas
Update 02/02/14: Agradecimentos ao [@rogeriopradoj](http://github.com/rogeriopradoj) que revisou o gabarito com várias alternatias incorretas! :)


#### 1d
Os três tipos de comentários apresentados são válidos, para minha surpresa, já que eu nunca tinha usado # .   
#### 2c
Fiz um [post sobre conversão de base numérica aqui](http://cloudson.github.io/2013/09/28/conversao-de-base-numerica/), se você não soube responder essa, o texto pode te ajudar. Mas a conta realizada foi a seguinte: 
017 = 1 x 8^1 + 7 x 8^0 = 7 + 8 = 15   
#### 3b   
314e-2 é 3.14 em notação cientifica, o mesmo que 314 x 10^-2  
#### 4a,c  
0xf é 15 em hexadecimal e 015 é 15 em octal, o que afirma que a) e c) são incorretas. 0x0a0 == a x 16^1 == 10 x 16^1 == 160 e 0xf == 0x0f == 15 
#### 5a 
0 convertido para um boolean é false, outro numérico qualquer é considerado true
#### 6a,d,e
Mais sobre coerção de tipos [http://www.php.net/manual/en/language.types.boolean.php#language.types.boolean.casting](http://www.php.net/manual/en/language.types.boolean.php#language.types.boolean.casting) 
#### 7c  
A variável $2foo não possui um nome válido, pois começa com um número [http://www.php.net/manual/en/language.variables.basics.php](http://www.php.net/manual/en/language.variables.basics.php)
#### 8c 
Um novo tipo foi definido no php 5.4 (?). Um callable é como o nome diz um valor que pode invocar uma função. Esses callables podem ser funções anônimas, ou até mesmo strings contendo o nome de uma função, como é o nosso caso. 
#### 9b
Como o próprio nome diz, a função isset testa se uma variável foi declarada (ou se uma região da memória associada ao identificador, existe)[http://php.net/manual/pt_BR/function.isset.php](http://php.net/manual/pt_BR/function.isset.php)  
#### 10a,c
Essa questão tem a ver com álgebra de booleanos. Um gancho para um próximo post :)    
1 | 2 == 01 + 10 == 11 == 3  
1 ^ 3 == 2  
2 ^ 3 == 1    
2 & 3 == 2 








