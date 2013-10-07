---
layout: post
title: "Meu metaframework em PHP"
date: 2013-09-01 12:22
comments: true
categories: [php, symfony]
---

Há algumas semanas saiu um artigo (baseado em *achismos* talvez) sobre [o php do futuro](http://www.sitepoint.com/a-php-from-the-future/), e um dos pontos do autor foi que as pessoas começariam a utilizar mais metaframeworks. Eu concordo. A verdade é que com os microframeworks instantaneamente temos metaframeworks; ferramentas próprias geradas a partir de outras menores.
Eu mesmo, apesar de trabalhar com symfony há um tempo, nunca o utilizei em projetos pessoais - que [foram ou não pro ar](http://temadehoje.gopagoda.com/).   
Neste post, vou mostrar meu skeleton ou metaframework padrão para aplicações php. 

### Estrutura 
Vamos trabalhar num sistema de diretórios análogo ao abaixo:   

--templates   
--public   
--cache   
--vendor   


### Composer 

Este cara não é um ingrediente, mas sem ele você não vai conseguir começar. O [Composer](http://getcomposer.org/) é um gerenciador de dependências pra projetos php. Se sua aplicação vai usar a ferramenta X, Y e Z, você as descreve num arquivo de configuração, roda um comando e todas estarão disponíveis. Se por acaso você queira atualizar Y ou trocar Z por W, basta reconfigurar esse arquivo. Veja um exemplo abaixo: 

{% codeblock [composer.json] lang:json %}
{
    "name" : "MeuProjeto",
    "autoload" : {
        "psr-0" : { "MeuProjeto" : "src/"}
    },
    "require": {
        "php" : ">= 5.3.8",
        "silex/silex" : "*",
        "respect/relational": "0.4.7",
        "twig/twig": "1.6.0"
    }
}
{% endcodeblock %}

Acima, o *composer.json* diz que faremos uma aplicação que precisa rodar num php pelo menos na versão 5.3.8 e que dependerá de outras ferramentas, que citaremos melhor abaixo. 
Vamos agora fazer com que essas dependências sejam baixadas.  

{% codeblock lang:bash %}
$ cd seu_diretorio_do_projeto
$ curl -s http://getcomposer.org/installer | php # baixa o composer 
$ php composer.phar install  # instala as dependencias
{% endcodeblock %}

### Configuração 
Costumo usar algumas constantes - que variam de acordo com o ambiente - no projeto. Agrupo essas informações num arquivo config.php 

{% codeblock [index.php] lang:php %}
<?php
// o composer gera um autoloader automático que já carrega as dependências
require __DIR__.'/vendor/autoload.php'; 
require __DIR__.'/config.php';
{% endcodeblock %}

{% codeblock [config.php] lang:php %}
<?php
// configuração de envio de email
// ou configuração de uma app do facebook 
// whatever

// configurações de banco de dados
if(preg_match('/127.0.0.[0-9]+/',$_SERVER['SERVER_ADDR'])){    
   define('DB_USER','foo');
   define('DB_PASS','bar');
   define('DB_PATH','127.0.0.1');
}else{
   define('DB_USER','motherfucker');
   define('DB_PASS','314159265359');
   define('DB_PATH','path_db.app.com');
}
{% endcodeblock %}

###  Silex
O [Silex](http://silex.sensiolabs.org/) é um roteador, em poucas linhas você consegue escrever um programa que roda diferentes funções para cada url (rota) de sua aplicação. 

{% codeblock [index.php] lang:php %}
<?php

require __DIR__.'/vendor/autoload.php'; 
require __DIR__.'/config.php';

$app = new Silex\Application();
$app->get('/home', function(){
    return "Estamos na home!";
});

$app->get("/contato", function(){
    return "Página de contato!"; 
});

$app->run();
{% endcodeblock %}

### Twig 
O [Twig](http://twig.sensiolabs.org/) nasceu no ecossistema symfony (como quase tudo deste post). Ele é um motor de templates, uma forma de escrever o html com inserção de informações dinâmicas vindas da sua aplicação.  
Vamos primeiro integrar o Twig ao Silex: 

{% codeblock [index.php] lang:php %}
<?php

require __DIR__.'/vendor/autoload.php';
require __DIR__.'/config.php'; 

// Apontando o twig para a pasta de cache e onde os arquivos html estarão
$twig_loader = new Twig_Loader_Filesystem(__DIR__.'/templates');
$twig        = new Twig_Environment($twig_loader, array(
    'cache' => __DIR__.'/cache',
));

$app = new Silex\Application();
// integrante do com silex
$app->register(new Silex\Provider\TwigServiceProvider(), array(
    'twig.path' => __DIR__ . '/templates',
));


$app->get('/home', function(){
    $name = 'Claudson Oliveira';
    return $app['twig']->render('home.html', array('name'=> $name));
});

$app->get("/contato", function(){
    return "Página de contato!"; 
});

$app->run();
{% endcodeblock %}

{% codeblock [home.html] language:html%}
<!DOCTYPE html>
<html>
    <head>
        <title>Blog</title>
    </head>
    <body>
        <!-- leia a documentacao do twig para inserir as variaveis -->
    </body>
</html>
{% endcodeblock %}

### Banco de dados

Para persistência em banco de dados, usaremos um cara brasileiro que eu sou fã. O [Respect\Relational](http://github.com/respect/relational), com ele não há trabalho em escrever classes que representam tabelas, ou qualquer dificuldade de configuração. 

{% codeblock [index.php] lang:php %}
<?php

require __DIR__.'/vendor/autoload.php';
require __DIR__.'/config.php'; 

// instanciando o relational com pdo 
$mapper = new Mapper(new PDO('mysql:dbname=seu_bd;host='.DB_PATH, DB_USER, DB_PASS)); 

// Apontando o twig para a pasta de cache e onde os arquivos html estarão
$twig_loader = new Twig_Loader_Filesystem(__DIR__.'/templates');
$twig        = new Twig_Environment($twig_loader, array(
    'cache' => __DIR__.'/cache',
));

$app = new Silex\Application();
// integrante do com silex
$app->register(new Silex\Provider\TwigServiceProvider(), array(
    'twig.path' => __DIR__ . '/templates',
));

// usa o mapper pra listar todos os usuarios do sistema (supondo que a tabela exista)
$app->get('/home', function() use ($mapper) {
    $usuarios = $mapper->user->fetchAll(); 
    return $app['twig']->render('home.html', array('usuarios'=> $usuarios));
});

$app->get("/contato", function() {
    return "Página de contato!"; 
});

$app->run();
{% endcodeblock %}

### Conclusão 
Vimos o básico para começar um projeto rapidamente; um controlador, uma engine de templates e uma ferramenta de persistência e consulta em banco de dados. A partir disso você pode plugar outras ferramentas, de validação, envio de email ou alguma api de rede social. 
