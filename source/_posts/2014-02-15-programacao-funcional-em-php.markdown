---
layout: post
title: "Programação funcional em PHP"
date: 2014-02-15 20:01
comments: true
categories: [php]
published: false
---

Hey guys! Hoje falaremos um pouco sobre as características de uma linguagem funcional encontradas no PHP. Citando um pouco as limitações da linguagem e o que ganhamos com o paradigma.

### Introdução 

O que faz de uma linguagem funcional ou não?  
A principal característica é afirmar que uma função está no mesmo "nível que um tipo de dado", ou seja, você consegue declarar uma variável do tipo função. Por quê isso? Para que você consiga encapsular blocos de código em uma variável e executá-lo em locais específicos. 
Complicado? Vamos tentar resolver o seguinte problema: 

`Você possui o array de entrada $a de strins, faça um algoritmo que retorne o mesmo array com cada elemento sem os espaços iniciais e finais (trim) e com a primeira letra em maiúscula. `

No php 5.2 (já) existiam funções para percorrer arrays aplicando (outras) funções em cada elemento, uma delas é o [array_walk](http://www.php.net/manual/pt_BR/function.array-walk.php). 

{% codeblock lang:php %}
<?php

    array_walk($a, 'trim');
    array_walk($a, 'ucfirst');

{% endcodeblock %}


Veja as linhas acima. A função array_walk recebe dois parâmetros, o primeiro é array que sofrerá as modificações, e o segundo é a função que será aplicada em cada elemento do array. Legal neh? Mas meio arcaico, isso não tem muito a ver com linguagens funcionais, foi só o jeito arcaico do php resolver isso (Opinão!!!!!! hahah). 
Vamos agora tornar o problema um pouco mais complexo: 

`Você ainda possui o array $a de entrada, e precisa aplicar uma certa operação em cada elemento do array. Se esse elemento for uma string sem espaços, você precisa deixá-la no padrão CamelCase (primeira letra maiúscula, no caso), senão no padrão hifenado (caixa baixa e espaços tornam-se hífens)`.

A primeira solução que vem a sua mente, caso você não conheça programação funcional é 

{% codeblock lang:php %}
<?php
// Esses comentários dizem exatamento o que o código faz, o que é no mínimo estranho
// mas caso alguém não tenha familiaridade com a queria api built-in do php
// eles ajudarão
for ($a as &$value) {
    // percorrer todo o vetor por referência (&)
    $value = strtolower($value);
    $value = trim($value);
    if (false !== strpos(' ', $value)) {
        // trocar espaços por hífens
        str_replace(' ', '-', $value);

        continue; 
    }
    // tornar a primeira letra maiúscula
    $value = ucfirst($value);
}

{% endcodeblock %}


Vamos resolver agora funcionalmente, passando um bloco de código para uma função:  

{% codeblock lang:php %}
<?php

$apply = function ($value) {
    $value = strtolower($value);
    $value = trim($value);
    if (false !== strpos(' ', $value)) {
        str_replace(' ', '-', $value);

        continue; 
    }
    $value = ucfirst($value);
};
array_walk($a, $apply)

{% endcodeblock %}

Observe duas coisas, na linha 3, $apply recebeu uma FUNÇÃO como valor. Essa função sem nome obviamente é chamada de função anônima. 
Se uma função é anônima, e essa variável guarda uma função, como executá-la? 

{% codeblock lang:php %}
$apply('Comunidade php de SP'); // comunidade-php-de-sp
{% endcodeblock %}

Legal neh? :) 