---
layout: post
title: "HTTP - Entendendo uma negociação de conteúdo via cartas"
date: 2014-08-12 22:17
comments: true
categories: [http, api, rest]
---

Atualmente é quase impossível implementar um sistema sem estabelecer conexão com outros sistemas, uma arquitetura orientada a serviços é cada vez mais valorizada.
Hoje vamos falar um pouco sobre uma das características básicas do http e/ou de serviços sob a filosofia REST; *Content Negotiation*.  

## A brincadeira
Duas pessoas poliglotas resolvem trocar cartas em quaisquer idiomas que domiman, elas só precisam informar no envelope em que idioma a resposta deve ser enviada. 
### Caso 1: Tudo ou nada 
João envia uma primeira carta para Maria perguntando como está seu novo emprego, seu envelope contém a informação 

{% codeblock lang:text %}
Accept-Language: pt-BR
{% endcodeblock %}

Recebida a carta, Maria entende que João aceita uma carta de resposta em português do Brasil, o que não é problema para uma mineira. 
Ela envia a resposta e coloca no envolope 
{% codeblock lang:text %}
Content-Language: pt-BR
{% endcodeblock %}

Ou seja, ela está confirmando que sua carta de resposta está em tal língua.
### Caso 2: Várias Opções de línguas
João resolve testar os conhecimentos de Maria e envia uma carta com o seguinte envelope
{% codeblock lang:text %}
Accept-Language: fr; q=1.0, en-US; q=0.3, pt-BR; q=0.2
{% endcodeblock %}

Nesse envelope a informação de linguagem segue um padrão maluco que informa    
fr; q=1.0 - Me responda em francês, num ranking de fluência, eu dou 100% de importância pra ela.  
en-US; q=0.3  - Se não falar francês, pode me responder em inglês americano.  
pt-BR; q=0.2 - Por fim, me responda em português brasileiro mesmo ¬¬  

Maria não fala francês e como viu que a segunda língua mais relevante pra João é o inglês, que domina, ela retorna
{% codeblock lang:text %}
Content-Language: en-US
{% endcodeblock %}

### Caso 3: Línguas desconhecidas
Num terceiro momento João envia uma carta pra maria pedindo uma resposta em chinês
{% codeblock lang:text %}
Accept-Language: zh
{% endcodeblock %}

Mas ... Maria não sabe falar essa língua e como ela não pode deixar João esperando, responde no envolope 
{% codeblock lang:text %}
Status: 406
{% endcodeblock %}

O campo de status fala sobre a resposta, nesse caso ([406](http://httpstatus.es/406)), Maria informou que não consegue responder nessa língua.


## Cliente e Servidor
Nos casos acima, João é o cliente, pode ser um navegador web, mas é mais interessante pensarmos genericamente num client consumindo uma api. Maria é o servidor que está expondo/servindo uma api.
As mensagens em questão são *requests* e *responses* HTTP, e o envelope representa na verdade um cabeçaho do protocolo.

### Negociação de conteúdo sobre mime-types

O caso mais usado sobre negociação de conteúdo em serviços fala sobre tipos. Você pode/deve desenvolver uma api que serve, transparentemente, conteúdos em mime-types diferentes, pelo menos em xml e json. A estória é a mesma, porém os cabeçalhos usado são *Accept*/*Content-type*. Veja um exemplo:   

Request:
{% codeblock lang:text %}
Accept: application/json; q=1.0, text/html; q=0.5; */*; q=0.1
{% endcodeblock %}


Response: 
{% codeblock lang:text %}
Status: 200
Content-type: application/json
{% endcodeblock %}

O Client pediu conteúdo em json ou html ou qualquer merda haha.   
A aplicação, serviu um json. 


### Conclusão
Como vimos, a negociação de conteúdo é um mecanismo do http para que uma aplicação sirva um conteúdo (ou melhor, uma representação de um recurso) de modo que o cliente entenda, pode haver negociação sobre tipos (quero que você me sirva uma imagem ao invés de um json), sobre linguagens (quero essa página em inglês), sobre encodings e charsets.  
Algumas (muitas) APIs fazem negociação de tipo via url, inserindo uma extensão (eu mesmo já [fiz](http://phartitura.com/Guzzle/Http.png) [isso](http://phartitura.com/Guzzle/Http.json)). Há bastante discussão sobre, não é a melhor maneira de se fazer, mas é a forma mais fácil e acessível via browser/humanos mais leigos.   
Espero poder voltar e falar um pouco sobre status code, que é algo simples que muitas APIs importantes não respeitam. 

### Referências
[RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec12.html)  
[Wikipedia](http://en.wikipedia.org/wiki/Content_negotiation)  
[HTTP status](http://httpstatus.es/)     
