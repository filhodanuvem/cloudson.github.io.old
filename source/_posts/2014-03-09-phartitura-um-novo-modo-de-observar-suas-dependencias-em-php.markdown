---
layout: post
title: "Phartitura - Um novo modo de observar suas dependências em php"
date: 2014-03-09 23:30
comments: true
categories: [php, composer]
---

Hi guys! Neste post vou falar um pouco sobre o projeto que codei nas últimas semanas, [o Phartitura](http://phartitura.com). 

### Inspiração! 

O twitter virou uma grande fonte de links técnicos para mim, e por lá, vi alguém postando um site para comunidade node-js. O [David-www](http://david-dm.org/). Foi naquele momento que eu pensei "cara, não tem isso pro composer!".
O David era uma aplicação/website que mostrava todas as dependências de um projeto hospedado no npm (gerenciador de dependências node-js), citando a versão mais atual e a versão que um projeto x usaria. Dessa forma ficaria fácil ver a distância entre as versões. 

### A ilusão; "Parece simples!" 

Aparentemente parecia mamão com açúcar, eu precisava seguir um roteiro simples: 

1) Consumir metadados de um projeto A/B do packagist    
   - Com alguma pesquisa percebi que o [packagist possuia uma interface para isso](http://cloudson.github.io/2014/01/25/acessando-a-api-do-packagist/).  
2) Pegar uma dependência C/D e olhar para sua regra de versão    
   - Se C/D tem regra ~1.1, quer dizer que o composer vai baixar uma versão >=1.1.0 e <1.2.0.   
3) Definir qual a versão mais atual de C/D que casa com a regra.  
4) Definir qual a versão mais atual (de todasss) de C/D.   
5) Voltar pro passo 2 com outra dependência.

Implementar esse *Versioning Resolver* não era um dos casos mais simples porque o composer trabalha com muitos tipos de regras de versão (muitos um pouco distante do [semver](http://semver.org) comum). Exemplos abaixo:

* ~1.0
* 2.0.3-alpha
* 3.2.2-beta8
* \>2.0,<=4.0.2
* <3.0.0 | > 5.0.3a
* 6.6.*
* dev-master  
...


Eu poderia (e pesquisei sobre) usar uma biblioteca pronta que me respondesse a questão "Eu tenho 3 versões, qual deleas é a mais nova que casa com a regra x?". O Mais perto disso que eu conseguiria, era usar o próprio composer/composer. Mas seria uma *library* muito grande para ter como dependência do phartitura, então decidi escrever eu mesmo esse *Resolver*! #facaNaCaveira. 

### Desafios, desafios...

Além disso, existiam outros "problemas": 
#### Gargalo no curl/tempo de request 
O exemplo mais claro e querido é o [symfony/symfony](http://phartitura.com/symfony/symfony).
Quando você tenta ver uma phartitura do projeto, a aplicação vai pegar os metadados disponíveis pelo packagist deste projeto (ou seja, vai realizar aquele passo 1). Esses metadados vêm como json, vamos dar uma olhada na seção que nos interessa, as dependências:

{% codeblock lang:json %}
{
    "require":{
       "php":">=5.3.3",
       "symfony\/icu":"~1.0",
       "doctrine\/common":"~2.2",
       "twig\/twig":"~1.11",
       "psr\/log":"~1.0"
    },
    "require-dev":{
       "doctrine\/data-fixtures":"1.0.*",
       "doctrine\/dbal":"~2.2",
       "doctrine\/orm":"~2.2,>=2.2.3",
       "monolog\/monolog":"~1.3",
       "propel\/propel1":"1.6.*",
       "ircmaxell\/password-compat":"1.0.*",
       "ocramius\/proxy-manager":">=0.3.1,<0.4-dev"
    },
    "replace":{
       "symfony\/browser-kit":"self.version",
       "symfony\/class-loader":"self.version",
       "symfony\/config":"self.version",
       "symfony\/console":"self.version",
       "symfony\/css-selector":"self.version",
       "symfony\/dependency-injection":"self.version",
       "symfony\/debug":"self.version",
       "symfony\/doctrine-bridge":"self.version",
       "symfony\/dom-crawler":"self.version",
       "symfony\/event-dispatcher":"self.version",
       "symfony\/filesystem":"self.version",
       "symfony\/finder":"self.version",
       "symfony\/form":"self.version",
       "symfony\/framework-bundle":"self.version",
       "symfony\/http-foundation":"self.version",
       "symfony\/http-kernel":"self.version",
       "symfony\/intl":"self.version",
       "symfony\/locale":"self.version",
       "symfony\/monolog-bridge":"self.version",
       "symfony\/options-resolver":"self.version",
       "symfony\/process":"self.version",
       "symfony\/propel1-bridge":"self.version",
       "symfony\/property-access":"self.version",
       "symfony\/proxy-manager-bridge":"self.version",
       "symfony\/routing":"self.version",
       "symfony\/security":"self.version",
       "symfony\/security-bundle":"self.version",
       "symfony\/serializer":"self.version",
       "symfony\/stopwatch":"self.version",
       "symfony\/swiftmailer-bridge":"self.version",
       "symfony\/templating":"self.version",
       "symfony\/translation":"self.version",
       "symfony\/twig-bridge":"self.version",
       "symfony\/twig-bundle":"self.version",
       "symfony\/validator":"self.version",
       "symfony\/web-profiler-bundle":"self.version",
       "symfony\/yaml":"self.version"
    }
 },
{% endcodeblock %}

As informações são bem parecidas com um composer.json, então para descobrir dados de cada dependência, é preciso fazer várias novas requests pro packagist perguntando sobre cada uma dessas. Entendeu o problema com o Symfony? São MUITAS dependências.   
Como seria insano forçar todos os usuários a esperar essas trocentas requests com CURL, serializei a informação no Redis com TTL de algumas horas, isso torna um segundo pedido para symfony/symfony incrivelmente rápido mas acaba não dando uma visão exata do pacote. Por exemplo, se neste exato momento o symfony está cacheado no Phartitura, e no github sair um novo release, levará horas para que a informação mude. Abri uma [issue](https://github.com/cloudson/Phartitura/issues/4) para criar um console job (provavelmente com [symfony/console](http://symfony.com/doc/current/components/console/introduction.html)) que atualiza esses caras automaticamente.

#### Priorizando as versões

Outro problema divertido foi/é responder a pergunta "Como eu sei qual a versão mais atual entre X,Y e Z?". Você pode pensar rapidamente, o timestamp de release da tag/versão responde isso mas ...   
* A versão dev-master sempre é encontrada (e não pode ser descartada, já que alguns projetos apontam pra ela - tsc tsc tsc)   
* A versão dev-develop também é sempre encontrada e pode estar à frente de tags  
* Alguns projetos (Como o Symfony) mantêm duas ou mais versões   simultaneamente, então um release para a 2.3.4 pode vir depois de um release para a 2.4.1 :(    
* Algumas pessoas versionam com umas tags %$@#!% 1.0.0-alpha8 (o composer por exemplo ¬¬). Cara, o mundo inteiro está usando algo alpha há mais de dois anos? Para mim o composer está na versão 1.0.x   

Implementei uma [SplMaxHeap](http://www.php.net/manual/en/class.splmaxheap.php) para que a medida que eu fosse inserindo versões nessa pilha, ele ordenasse deixando a mais prioritária no topo. A classe atual está com problemas (como aquele citado sobre o symfony) e é encontrada [aqui](https://github.com/cloudson/Phartitura/blob/master/src/Cloudson/Phartitura/Project/VersionHeap.php). 


#### Negociação de conteúdo com Respect/Rest

Foi um bom momento para estudar um pouco sobre HTTP/REST. Com a ferramenta brasileira, [foi muito simples dar o primeiro passo](https://github.com/respect/rest#content-negotiation) para a tarefa 
`Se alguém quiser consumir os dados de um projeto como json, retorne como json`.

Mas depois a situação foi ficando um pouco mais complicada. Geralmente as pessoas não usam cabeçalho http para negociar contéudo com serviços, elas usam a própria url. Então para a url http://phartitura.com/cloudson/gandalf eu criei uma rota http://phartitura.com/cloudson/gandalf.json . Acho que encontrei um problema na priorização das rotas do Respect/Rest: 

{% codeblock lang:php %}
<?php
$app = new Router;
$app->get('/*/*.json', $jsonCallback);
$app->get('/*/*', $htmlCallback); 

{% endcodeblock %}

Setando essas duas rotas, a ferramenta não sabe qual é a mais prioritária, escolhendo a segunda :( Provavelmente eu vá abrir uma issue daqui a uns dias, ou você mesmo pode fazer isso.   
As prioridades são dadas pelo número de *wild cards*, então resolvi da seguinte forma : 
{% codeblock lang:php %}
<?php
$app = new Router;
$app->get('/*/*.json', $jsonCallback);
$app->get('/*/**', $htmlCallback); // três *

{% endcodeblock %}

### Conclusão

Foi um projeto bem legal e desafiador. Prova que qualquer "escritor de códigos" pode lançar algo a nível mundial, pois apesar do maior número de acessos vir do brasil, Phartitura teve acessos de EUA, Inglaterra, Alemanha, Canada, Índia, Austrália, Japão... são 39 países nesse momento.  
Sobre o futuro, há muito a se implementar, quero fazer análise para projetos privados (subindo o composer.json), refatorar código, aumentar a cobertura de testes, criar jobs que atualizem informações no phartitura automaticamente, envio de notificação sobre novos releases para usuários que assim quiserem... 
Enfim, o céu é o limite! Fiquem com a música tema do projeto 

<iframe width="420" height="315" src="//www.youtube.com/embed/k1-TrAvp_xs" frameborder="0" allowfullscreen></iframe>