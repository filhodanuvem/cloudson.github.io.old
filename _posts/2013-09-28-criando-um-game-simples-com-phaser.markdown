---
layout: post
title: "Criando um game simples com Phaser"
date: 2013-09-28 09:58
comments: true
published: false 
categories: 
---

Phaser é um framework para criar jogos usando tecnologias web, afinal com html5+js+whatever você pode rodar a app no browser, mobile e desktop (com a ideia dos webapps). 

![Phaser logo](http://www.photonstorm.com/wp-content/uploads/2013/04/phaser-logo-small.png)

### Instalação 

Vamos baixar o Phaser (versão 1.0.6) que está no [github](http://github.com/photonstorm/phaser), você pode pegá-lo para seu folder [pelo link](https://github.com/photonstorm/phaser/archive/1.0.6.tar.gz) ou dando um git clone: 

{%codeblock lang:bash%}
git clone git@github.com:photonstorm/phaser.git
git checkout 1.0.6
{% endcodeblock %}

Apesar de criarmos um jogo com tecnlogias web do lado do cliente, precisamos de entregar a aplicação utilizando um servidor web, não faz sentido que o usuário baixe todos os recursos (imagens, sons...) do jogo de uma só vez.  
Utilizaremos o apache da forma mais simples, sem mesmo setar um virtualhost. Vamos no diretório root e criar uma pasta chamada cloudsnake: 

{% codeblock lang:bash %}
mkdir /var/www/memorycloud 
cd /var/www/memorycloud
{%endcodeblock %} 

Vamos começar a codar a partir do exemplo que a equipe do phaser disponibiliza, então copiarei um folder do phaser dentro do cloudsnake 
{% codeblock lang:bash%}
cp /diretorio_onde_esta_o_phaser/Docs/Hello\ Phaser ./
{% endcodeblock%}

Ao acessar seu [http://localhost/cloudsnake](http://localhost/cloudsnake) você verá uma imagem do Phaser. Instalação concluída! 

### Reconhecendo o campo
Por enquanto tudo que você precisa ver é o index.html do cloudsnake. Sugestivamente a função *preload* é chamada antes de rendereziar o jogo e a *create* seria o método chamado no momento inicial do jogo. Perceba que essas funções foram assinadas na instanciação do jogo, linha 13, pelo parâmetro json.   

{% codeblock  lang:javascript %}
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', { preload: preload, create: create, update: update });
{% endcodeblock %}

Na linha 25 foi adicionada a logo (setada no método preload) na área de visão do jogo. Utilizaremos a palavra **mundo** pra representar todo o universo do seu jogo, a princípio você pode pensar que mundo é a área de visão do jogo, neste momento você está certo mas logo não estará (há coisas acontecendo no jogo fora do seu campo de visão, pense em uma fase do Super Mario World, o Yoshi está correndo mesmo que você não veja rs). Assim temos uma representação do mundo em código a partir do atributo *world* em *game*. Dando um console.log na primeira linha da função *create* podemos ver (usando firebug, ou qualquer console de um browser) todos os atributos do nosso mundo, de cara você reconhecerá informações de tamanho da tela do mundo.   

{% codeblock lang:javascript%}
console.log(game.world); 
{% endcodeblock %}

### Coordenadas

Eu não sou nenhum matemático e muito menos especialista em computação gráfica, mas vamos com o mínimo. No nosso index.html definimos (naquela mesma linha 13) que nosso mundo possui largura de 800px e altura de 600px.
Na função *create* colocamos "nossa" logo na posição (x,y)=(0,0), ou seja, na origem do sistema de coordenadas. Essa origem está no canto **superior esquerdo**, por isso quando a imagem aparece na tela ela também está no canto superior esquerdo. Não devemos alterar a origem, mas podemos renderizar a imagem no centro da tela da seguinte forma: 

{% codeblock lang:javascript%}
function create() {
    // game.world possui dois atributos que representam o centro da tela. Nada mais do que 
    // game.world.centerX == game.world.width / 2 
    // game.world.centerY == game.world.height / 2 
    logo = game.add.sprite(game.world.centerX, game.world.centerY, 'logo');   
}
{%endcodeblock%}


