---
layout: post
title: "Iniciando com testes unitários usando PHPUnit"
date: 2014-01-04 22:15
comments: true
categories: [php, tdd, testes]
published: false
---

Olá pessoal! Primeiro post do ano e vamos falar de testes unitários em php com a abordagem mais simples possível. Gostaria que primeiramente você pensasse num cenário e respondesse uma pergunta; Supomos que você está desenvolvendo uma aplicação para a minha locadora de animes (se você não sabe o que é um anime, substitua por filmes rs). Na entrega eu te pergunto "Como você me prova que sua aplicação realmente funciona?", você provavelmente me responde que fez baterias de testes simulando o que um usuário irá fazer com o sistema. Até aí nada de estranho. 

Uma semana depois eu faço um requerimento de uma mudança na *feature* de login de usuários. Você implementa a mudança e me entrega, novamente eu te pergunto "Como você me prova que sua aplicação realmente funciona?", você dará a mesma justificativa, mas um furo já pode ser percebido; Provavelmente quando você mudou a feature, testou ela isoladamente e não o sistema todo, algo pode ter quebrado longe de seus olhos.  
É aí que surge a seguinte motivação; "E se colocássemos robôs para testar nossas aplicações? Por que não escrever códigos que simulem pessoas testando partes do sistema?".  
Entre esses testes automatizados, podemos classificá-los em vários tipos, robôs que testam sua interface, robôs que testam performance da sua aplicação ... robôs que testam um pedacinho do seu código. Vamos nos concentrar nesse último tipo, os testes unitários. 

### Ambiente 

Para escrever esse post eu usei: 

* Ubuntu 12.10
* php 5.4 (você pode usar 5.3)
* PHPUnit 3.7 (Baixado o [phar](https://phar.phpunit.de/phpunit.phar) do site)

### O problema

Nosso problema será bem simples, vamos implementar um programa que encontre o fatorial de um número. 
