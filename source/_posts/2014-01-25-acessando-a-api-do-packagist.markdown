---
layout: post
title: "Acessando a API do Packagist"
date: 2014-01-25 23:41
comments: true
categories: [php]
published: true
---

Essa semana precisei consumir dados de pacotes disponibilizados pelo composer. Procurei na [organization oficial](http://github.com/composer) por um client mas não encontrei. 
Percebi que nunca tinha ouvido falar se o composer/packagist disponibilizava uma API para consumo de informações sobre os pacotes. 

Depois de algumas buscas encontrei uns links úteis que responderam minha dúvida (veja as fontes no final do post) e irei escrever alguns exemplos de requests para consumir dados. 

### Listando pacotes de uma organization 

{% codeblock lang:bash %}
curl https://packagist.org/packages/list.json?vendor=respect
{% endcodeblock %}

{% codeblock lang:json %}
{ 
  "packageNames": [ 
    "respect/config", 
    "respect/data", 
    "respect/loader", 
    "respect/relational", 
    "respect/rest", 
    "respect/template", 
    "respect/test", 
    "respect/validation", 
    "respect/validation-bundle" 
  ] 
} 
{% endcodeblock %}

### Buscando pacotes por uma tag   

(atributo tag encontrado no composer.json, não confundir com git tag :P ). 

{% codeblock lang:bash %}
curl https://packagist.org/search.json?tags=respect
{% endcodeblock %}

{% codeblock lang:json %}
{ 
  "packageNames": [ 
    "respect/config", 
    "respect/data", 
    "respect/loader", 
    "respect/relational", 
    "respect/rest", 
    "respect/template", 
    "respect/test", 
    "respect/validation", 
    "respect/validation-bundle" 
  ] 
} 
{% endcodeblock %}

### Buscando pacotes pelo nome 

{% codeblock lang:bash %}
curl https://packagist.org/search.json?q=cloudson 
{% endcodeblock %}

{% codeblock lang:json %}
{
  "results": [
    {
      "name": "cloudson/gandalf",
      "description": "",
      "url": "https://packagist.org/packages/cloudson/gandalf",
      "downloads": 0,
      "favers": 1
    }
  ],
  "total": 1
}
{% endcodeblock %}

### Pegando dados de um repositório específico

{% codeblock lang:bash %}
curl https://packagist.org/packages/cloudson/gandalf.json
{% endcodeblock %}

{% codeblock lang:json %}
{
  "package": {
    "name": "cloudson/gandalf",
    "description": null,
    "time": "2014-01-26T00:26:17+00:00",
    "maintainers": [
      {
        "name": "cloudson",
        "email": "claudsonweb@gmail.com"
      }
    ],
    "versions": {
      "dev-master": {
        "name": "cloudson/gandalf",
        "description": "",
        "keywords": [],
        "homepage": "",
        "version": "dev-master",
        "version_normalized": "9999999-dev",
        "license": [],
        "authors": [
          {
            "name": "Claudson Oliveira",
            "email": "cloudson@outlook.com"
          }
        ],
        "source": {
          "type": "git",
          "url": "https://github.com/cloudson/Gandalf.git",
          "reference": "6050511663c47ba1bba37cd5e6ca0dde3eb72575"
        },
        "dist": {
          "type": "zip",
          "url": "https://api.github.com/repos/cloudson/Gandalf/zipball/6050511663c47ba1bba37cd5e6ca0dde3eb72575",
          "reference": "6050511663c47ba1bba37cd5e6ca0dde3eb72575",
          "shasum": ""
        },
        "type": "library",
        "time": "2014-01-26T00:20:55+00:00",
        "autoload": {
          "psr-0": {
            "Gandalf": "src/",
            "Gandalf/Tests": ""
          }
        }
      },
      "0.7.0": {
        "name": "cloudson/gandalf",
        "description": "",
        "keywords": [],
        "homepage": "",
        "version": "0.7.0",
        "version_normalized": "0.7.0.0",
        "license": [],
        "authors": [
          {
            "name": "Claudson Oliveira",
            "email": "cloudson@outlook.com"
          }
        ],
        "source": {
          "type": "git",
          "url": "https://github.com/cloudson/Gandalf.git",
          "reference": "119fa3309ea4a988ca44cfa63a349bf4c21cca6f"
        },
        "dist": {
          "type": "zip",
          "url": "https://api.github.com/repos/cloudson/Gandalf/zipball/119fa3309ea4a988ca44cfa63a349bf4c21cca6f",
          "reference": "119fa3309ea4a988ca44cfa63a349bf4c21cca6f",
          "shasum": ""
        },
        "type": "library",
        "time": "2014-01-26T00:12:17+00:00",
        "autoload": {
          "psr-0": {
            "Gandalf": "src/",
            "Gandalf/Tests": ""
          }
        }
      }
    },
    "type": "library",
    "repository": "https://github.com/cloudson/Gandalf",
    "downloads": {
      "total": 0,
      "monthly": 0,
      "daily": 0
    },
    "favers": 1
  }
}
{% endcodeblock %}

Você pode acabar percebendo uma certa semelhança entra as URIs citadas e aquelas acessadas no packagist (html), de fato essa semelhança existe e serve também como base pra consumir outras informações.  

Este foi um post rápido apenas para divulgar conhecimento e servir de lembrete pra mim. Até a próxima :)


fontes: 
https://groups.google.com/forum/#!topic/composer-dev/pb_Jo_cu4IA    
https://github.com/KnpLabs/packagist-api