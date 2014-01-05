---
layout: post
title: "O que caiu na ZCPE?"
date: 2013-12-19 23:13
comments: true
categories: 
---
![](http://www.zend.com/topics/ZCE-PHP-Engineer-logo-XS.jpg)  
Meu último post tinha a intenção de ser uma série para quem está estudando para a certificação php.  O problema é que muita coisa aconteceu, a ZCE mudou, incluiu na sua grade o php 5.4 e 5.5, nomeou-se ZCPD e posteriormente ZCPE.   
Marquei minha prova para um dia antes do voucher vencer (risos), decidi fazer a mais atual e [passei!](http://zend.com/en/store/education/certification/yellow-pages.php#show-ClientCandidateID=ZEND024222).   
Abaixo listarei dez questões **"similares"** as que caíram, sintam-se a vontade para comentar sobre. 


### 1 - Qual a saída do código abaixo: 

{% codeblock lang:php %}
<?php

class A 
{
    private $foo; 
    public function __construct($foo)
    {
        $this->foo = $foo;
    }
    public function bar()
    {
        return function($x) {
            return $this->foo * $x;
        }; 
    }
}   

$a = new A(2);
$a->bar = function ($x) {
    return $x * $x;
};
$m = $a->bar();
echo $m(3);
?>
{% endcodeblock %} 

a) 6   
b) 9   
c) 0   
d) Nenhuma das acima  

Essa questão é bonita! rs. Um atributo receber uma função anônima não faz com que o mesmo posso ser chamado como um método, lembrei disso por causa de uma [brincadeira que fiz no github](https://github.com/cloudson/Gandalf).  
Mesmo assim fiquei bem confuso pois achei que o $this dentro da primeira função anônima não era um ponteiro pro objeto de A, mas é. Sendo assim, a resposta correta é (a) 6 .


### 2 - Qual palavra chave deveria ser usada na linha comentada com "HERE" para que o código funcione corretamente com a saída "Item"

{% codeblock lang:php %}
<?php

class Base
{
    public static function create()
    {
        return new self(); // HERE
    }

    public function myName()
    {
        echo __CLASS__;
    }
}

class Item extends Base 
{
    public function myName()
    {
        echo __CLASS__;
    }
}

$foo = Item::create();
$foo->myName(); 

?>
{% endcodeblock %}

Questão sobre [late static binding](http://php.net/manual/pt_BR/language.oop5.late-static-bindings.php), usando a palavra reservada static, o método utilizado é o de Item. 

### 3 - Suponha que você possui a classe MyObject já definida 

{% codeblock lang:php %}
<?php 

$foo = new MyObject;
$array = array(1,2,3);

return array_walk($array, $foo);
?>
{% endcodeblock %}

Para que o código acima funcione, MyObject precisa: 

a) Implementar a classe Callable   
d) Implementar a classe Closure  
b) Possuir o método \_\_call   
c) Possuir o método \_\_invoke  

Dentro de array_walk o segundo parâmetro será chamado como uma função, basta que a classe MyObject implemente o método \_\_invoke, (d). 

### 4 - Para conseguir usar a função count ou o construtor da linguagem sizeof em uma classe definida MyClass, essa classe precisa : 

a) Implementar a interface ArrayAccess  
b) Possuir o método \_\_count  
c) Ser convertido de um array para o objeto   
d) Nenhuma das anteriores   

Para que um objeto possa ser usado na função count, ele precisa implementar a interface Countable, que exige o método count. Letra (d)

### 5 - Qual a saída do código abaixo: 

{% codeblock lang:php %}
<?php 
    var_dump(boolval(new \StdClass));
?>
{% endcodeblock %}

a) true  
b) false  

Resposta certa é (a) true (e eu poderia jurar que um objeto standard vazio se comportaria como um array vazio mas...)

### 6 - Qual a saída do código abaixo
{% codeblock lang:php %}
<?php
function  update(&$array)
{
    foreach ($array as &$value) {
        $value = $value + 1;
    }
    $value = $value + 2;
}

$valores = array(1,2,3);
update($valores); 
?>
{% endcodeblock %}

a) 2,3,4  
b) 2,3,6  
c) 1,2,3  
d) 0,0,0  

Problema que exigiu conhecimentos em escopos, vetor passado por referência e iterado "por referência" também. Assim que o laço foreach termina a variável $value mantém o último valor do array, logo a resposta era (b).

### 7 - Qual função php inverte os valores de um array sem alterar as chaves? 
a) array_split  
b) array_reverse   
c) ksort  
d) sort   

Tipo de questão que eu odeio, apenas decoreba importa, resposta (b).

### 8 - Qual a saída do código abaixo 
{% codeblock lang:php %}
<?php
namespace MyFramework\MyNamespace; 
class MyClass
{
    public function myMethod()
    {
        return __METHOD__; 
    }
}
$foo = new MyClass;
echo $foo->myMethod();
?>
{% endcodeblock %}

a) MyFramework\MyNamespace\MyClass\myMethod  
b) MyFramework\MyNamespace\MyClass::myMethod  
c) MyFramework\MyNamespace\myMethod  
c) MyClass::myMethod  

A resposta inclui o namespace completo, letra (b).

### 9 - Qual o resultado da operação abaixo
{% codeblock lang:php %}
<?php
    return 1 ^ 2 ;
?>
{% endcodeblock %}

Utilizando o operador de ou exclusivo (^) a reposta correta é 3 pois    
  01     
  10  
  11  

A resposta é 11, seguindo o meu [post sobre conversão de base numérica](/2013/09/28/conversao-de-base-numerica/), dá 3.  

### 10 - Qual a saída para o programa abaixo ? 
{% codeblock lang:php %}
<?php
function operacao(&$x) {
    return $x + 1;
}
$a = 1;
echo operacao($a);
echo operacao($a);
?>
{% endcodeblock %}
Tentativa de pegadinha com passagem por referência, mas o resultado é 22 mesmo. :) 

### Conclusão

Outros temas que caíram na prova mas eu não consigo lembrar a questão inteira:   
* Caiu traits e resolução de conflitos com metodos com o mesmo nome.   
* Caiu uma questão simples pedindo para dizer como desabilitar os erros com uma diretiva do php.ini, a função error_reporting era uma das alternativas rsrs.   
* Como vimos acima caiu bastante sobre as interfaces da SPL.  
* Algumas questões de segurança, envolvendo roubo de sessão e como se previnir ou quais funções php podem evitar ataques vindo do usuário.   
* Caiu algumas questões sobre streams, tinha uma questão que mudou o contexto  da função [file_get_contents](http://php.net/manual/pt_BR/function.file-get-contents.php) e fazia ela usar post, por exemplo.   
* Cairam questões sobre upload (e segurança).   
* Não caiu nada sobre generators.    

Não achei a prova extremamente fácil porque não estudei por meses como gostaria de ter feito, senão poderia ter ficado mais tranquilo durante. Mas a prova não é absurdamente difícil pra quem já trabalha com a linguagem há anos. 
Um dos motivos de eu escolher a 5.5 é porque prefiro fazer questões de comportamento de código/oop do que decorar funções, e de fato o primeiro tipo caiu bastante.   
Espero que o post encoraje muita gente! :) 
