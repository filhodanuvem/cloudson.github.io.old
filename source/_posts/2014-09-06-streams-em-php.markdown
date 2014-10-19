---
layout: post
title: "Streams em PHP"
date: 2014-09-06 20:26
comments: true
categories: [php]
---

Estudantes da Certificação PHP já leram, temeram ou irão em algum momento ouvir falar de streams em php.  
Neste post vamos tentar desmistificar a dificuldade ao redor do assunto. 
A motivação pra escrever sobre, surgiu de uma conversa que tivemos no [Valor](http://valor.com.br), um amigo apresentou o tema, e achei interessante replicá-lo aqui.  

### O que são streams
Provavelmente você já manipulou arquivos em php, operações como abrir ou escrever num arquivo se tornam simples com as funções herdadas do C. 

{% codeblock lang:php %}
<?php

// abre um arquivo gerando um recurso manipulável
$handle = fopen("meuarquivodeteste", "a+");  
// escreve no arquivo associado ao recurso 
fwrite($handle, "Isso é um teste");  
// fecha o arquivo 
fclose($handle);  

{% endcodeblock %}  

Mas se pararmos pra pensar, essas operações de manipulação de recursos (abrir, ler, escrever, buscar...) podem ser usadas não só em arquivos locais, mas em arquivos que podem ser acessados via http, por exemplo: 

{% codeblock lang:php %}
<?php

// lendo um arquivo servido via http(s) 
$pathGist = "https://gist.githubusercontent.com/cloudson/6372b6907e298abb4c26/raw/fce2fb435326ca1d43b0ce0b1fe51599abf2ca9f/exemplo.txt";
$handle = fopen($pathGist, "r");
$string = fread($handle, 200);
var_dump($string);
fclose($handle);

{% endcodeblock %}

Tendo em vista essa semelhança, qualquer recurso que possa ter essas operações pode ser chamada de um(a) **stream** em php. Assim como fizemos via http, podemos utilizar essas mesmas funções em outros *contextos*. 

### Contextos
A linguagem já possui suporte para trabalhar com streams em certos contextos, alguns deles:  

* file:// - Acesso local  
* http:// - Acesso via http(s)   
* ftp:// - Acesso via ftp(s)  
* php:// - Acesso I/O da linguagem 
* ssh2:// - Acesso via ssh2 

Perceba que o reconhecimento de contexto é feito automaticamente pela função que gera o recurso stream. A omissão do contexto indica o uso de file:// . 

{% codeblock lang:php  %}
<?php

// abrindo um arquivo local
$handle = fopen("file://meuarquivo") 

{% endcodeblock %}

#### Contexto php:// 
Também como herança do C, afirmar que imprimir algo na tela é o mesmo que escrever no arquivo STDOUT (Standard output) é verídico em php. Esse "arquivo" é acessado via protocolo php:// 

{% codeblock lang:php %}
<?php 
// o mesmo que dar um echo na tela
$handle = fopen("php://stdout", "w"); 
fwrite($handle, "olá mundo".PHP_EOL);
fclose($handle);

{% endcodeblock %} 
Alguns arquivos disponíveis por esse protocolo são:   

* stdin - Usado para entrada de dados (read-only). 
* stdout - Usado para saída de dados (write-only). 
* stderr - Usado para saída de erros (por padrão a saída de erros é o mesmo que o stdout (write-only). 
* input - Utilizado para entrada de dados, via request, por exemplo se um post é enviado com um arquivo. É possível recuperá-lo via esse arquivo. 
* memory - Escrita e leitura de dados temporários em memória. 
* temp - Escrita e leitura em arquivo temporário físico no S.O (arquivo salvo em sys_get_temp_dir() ) 
 
#### Contexto compress.zlib://

Há uma leve diferença entre os termos compactação e compressão de dados. Compactar quer dizer unir vários arquivos num só, como é possível usando o formato tar. Comprimir quer dizer que um algoritmo foi aplicado num arquivo de tal forma que seu tamanho foi minimizado. O gzip é um famoso algoritmo de compressão de dados. 
Com esse contexto podemos ler arquivos comprimidos com gzip, e é o que faremos a seguir. 

{% codeblock lang:bash %}
# criando um arquivo comprimido
echo "olá" >> test
gzip test
{% endcodeblock %}

{% codeblock lang:php %}
<?php
// lê o arquivo comprimido 
$h = fopen("compress.zlib://test.gz", "r");
$content = fread($h, 32);
echo $content.PHP_EOL;
fclose($h);

{% endcodeblock %}  

#### Contexto zip://

Como o zip é um algortimo de compressão que pode servir como compactador de arquivos, pode existir a necessidade de ler um arquivo dentro de um zip. É o que veremos abaixo: 

{% codeblock lang:php %}
<?php
// lê um arquivo dentro do gzip
$h = fopen("zip://test.gz#file1.txt", "r");
$content = fread($h, 32);
echo $content.PHP_EOL;
fclose($h);

{% endcodeblock %}  

### Criando um contexto 

A função *stream_context_create* cria um stream context em php. É questão de prova utiliza-la para mudar o comportamento padrão de alguma função. 
{% codeblock lang:php %}
// Um post será enviado para a url, ao invés do padrão get
$context = stream_context_create([
   "http" => ["method" => "post"]
]);

// perceba que o quarto parâmetro do fopen é um contexto
$handle = fopen('http://gist.githubusercontent.com/cloudson/6372b6907e298abb4c26/raw/fce2fb435326ca1d43b0ce0b1fe51599abf2ca9f/exemplo.txt"', 'r', false, $context);
$content = fread($handle, 200);
echo $content.PHP_EOL;
fclose($handle);

{% endcodeblock %}

### Wrappers 

A mágica não para por ae. Você pode encapsular sua própria forma de lidar com arquivos sob um protocolo, criando um **wrapper** . 
Você deve escrever uma classe Wrapper que segue uma certa interface/sinópse: 

{% codeblock lang:php %}
<?php

// declarando uma classe que diz como manipular arquivos. 
class YourStreamWrapper
{
   public function stream_open($path, $mode, $options, &$opened_path);
   
   public function stream_read($count); 

   public function stream_write($data); 

   public function stream_tell();  
   
   public function stream_eof(); 

   function stream_seek($offset, $whence); 
}
// usando a classe num novo protocolo declarado cloud:// 
stream_wrapper_register("cloud", "YourStreamWrapper);

// você poderá usar fopen("cloud://file",  "r") ... :) 

{% endcodeblock %}

Imagina que lindo poder ler arquivos do dropbox ou google drive utilizando a função fopen. 
Criando wrappers específicos isso seria possível. 

### Outras possibilidades

Existem diversas funções sobre stream que facilitam a vida na hora de querer um certo comportamento. 
É possível usar a [stream_context_set_default](http://php.net/manual/en/function.stream-context-set-default.php) para declarar o protocolo padrão de stream, sobescrevedo o file:// . Definir o timeout de "conexão" com [stream_set_timeout](http://php.net/manual/en/function.stream-set-timeout.php). 
Ou uma das coisas mais legais, na minha opinião, copiar conteúdo de arquivos entre streams com [stream_copy_to_stream](http://php.net/manual/en/function.stream-copy-to-stream.php).  

### Conclusão
Espero que depois do post não haja o que temer no assunto. Nele vimos o que são e a facilidade de trabalharmos com recursos stream, e como os utilizamos o tempo todo sem nem perceber. 

#### Fontes 
* [Protocolo e Wrappers de streams](http://php.net/manual/en/wrappers.php)   
* [Class Wrapper como sinópse](http://php.net/manual/en/class.streamwrapper.php)  
* [Zlib](http://pt.wikipedia.org/wiki/Zlib) 
* [Arquivos tar](http://en.wikipedia.org/wiki/Tar_%28computing%29)
