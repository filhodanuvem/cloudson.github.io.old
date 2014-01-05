---
layout: post
title: "Faça um sistema de buscas com Solr/Lucene"
date: 2013-09-07 20:58
comments: true
categories: [solr]
published: false
---

É muito comum necessitarmos de um sistema de buscas em um sistema web.  
Sabemos que o mysql, o banco de dados mais popular por desenvolvedores, não foi feito para isso. Se você tem um campo fulltext em uma tabela InnoDB, não podrá criar índices para esse campo, e consequentemente não conseguirá fazer uma busca otimizada.  
Eu já vi muitas soluções de busca encima do mysql, onde a aplicação tinha um algoritmo agindo como uma camada entre o usuário e a pesquisa em si. 

{% codeblock "Pega o valor de busca e buscar em cada campo" lang:sql %}
SELECT id FROM user WHERE name like "%cloud%" OR family like "%cloud%" OR email like "%cloud%"  
{% endcodeblock %}

Nessas soluções torna-se um martírio tentar resolver vários problemas; algumas palavras (conjunções, preposições...) deveriam ser ignoradas na hora da busca, se no banco um índice está no plural uma busca pelo seu singular deveria ser eficaz, sinônimos deveriam funcionar também...

O Solr é uma plataforma acima de uma engine de busca chamada lucene que atinge todos os problemas citados acima. 

### É Java sim !

O Solr necessita de um servidor de aplicações web em java. Utilizaremos o jetty neste post, mas primeiro vamos instalar o java 7 (supondo que estamos em um linux debian como ubuntu).  
{% codeblock  lang:bash %}
$ add-apt-repository ppa:webupd8team/java # adiciona o ppa que aponta para java oficial da oracle 
$ apt-get update
$ apt-get install oracle-java7-installer 
$ apt-get install jetty # instala o servidor 
{% endcodeblock %}

Você verá que com o servidor/solr ligado, fazer com que sua aplicação converse com ele é incrivelmente fácil. Através de requests você faz as consultas. Então, largue de preconceito e preguiça! 

### Enfim, solr 

Baixaremos o solr versão 4.4.0 em [http://lucene.apache.org/solr/](http://lucene.apache.org/solr/) 
{% codeblock  lang:bash %}
wget http://ftp.unicamp.br/pub/apache/lucene/solr/4.4.0/solr-4.4.0.tgz
tar -xvf solr-4.4.0.tgz
{% endcodeblock%}

Agora vamos mover os arquivos essenciais para outra pasta, onde usaremos para subir a aplicação. 
{% codeblock  lang:bash %}
mkdir solr 
cp solr-4.4.0/example/start.jar ./solr
mkdir ./solr/webapps & cp solr-4.4.0/example/webapps/solr.war ./solr/webapps/
sudo cp -r solr-4.4.0/example/lib/ solr
{% endcodeblock %}

### Tempo de indexação e tempo de busca

### Schemas

Agora já podemos pensar na busca. 

{% codeblock lang:bash %}
cd solr 
mkdir schemas
cd schemas 
{% endcodeblock %}

O arquivo principal de configuração aponta para o que ele chama de núcleos, já que você pode ter vários schemas tipos de documentos diferentes (ex: você pode ter uma aplicação grande, usar o solr para indexar produtos e categorias desses produtos). 

{% codeblock [solr.xml] lang:xml %}
<?xml version="1.0" encoding="UTF-8" ?>
<solr>
  <cores adminPath="/admin/cores">
    <core name="product" instanceDir="product" />
  </cores>
</solr>
{% endcodeblock %}

Dentro do diretório *product/conf* ($SOLR_HOME/schemas/product/conf) teremos nosso schema xml que dita as regras da busca. 

{% codeblock [schema.xml] lang:xml%}
<?xml version="1.0" encoding="UTF-8"?>

<schema name="Product" version="1.1">
  <!-- Definindo tipos que serão usados nos campos  -->
  <types>
    <fieldtype name="sortableInt"  class="solr.SortableIntField" />
    <fieldType name="double" class="solr.TrieDoubleField"  /> 
    <fieldType name="string" class="solr.TextField" />
    <fieldType name="text_general" class="solr.TextField" >
      <analyzer type="index">
         <tokenizer class="solr.StandardTokenizerFactory"/>
         <filter class="solr.EdgeNGramFilterFactory" minGramSize="2" maxGramSize="15" side="front"/>
         <filter class="solr.LowerCaseFilterFactory"/>
         <filter class="solr.BrazilianStemFilterFactory"/>
      </analyzer>
      <analyzer type="query">
        <tokenizer class="solr.StandardTokenizerFactory"/>
        <filter class="solr.LowerCaseFilterFactory"/>
        <filter class="solr.SynonymFilterFactory" synonyms="zocsynonyms.txt"/>
        <filter class="solr.BrazilianStemFilterFactory"/>
      </analyzer>
    </fieldType>
  </types>

  <!-- 
    Valores importantes para sua busca devem estar nos documentos do solr,
    podendo ser índices de busca ou não.
   -->
  <fields>
    <!-- Basic fields -->
    <field name="id" type="sortableInt" indexed="true"  stored="true" required="true" />
    <field name="name" type="text_general" indexed="true"  stored="true" required="true"/>
    <field name="slug" type="string" stored="true" />
    <field name="price" type="double" stored="true"/>    
    <field name="category" type="string" stored="true"/>
  </fields>

  <uniqueKey>id</uniqueKey>
  <solrQueryParser defaultOperator="AND"/><!-- long tails search more specific thing  -->

</schema>
{% endcodeblock %} 

{% codeblock [solrconfig.xml] lang:xml %}
<?xml version="1.0" encoding="UTF-8" ?>
<config>
  <luceneMatchVersion>LUCENE_35</luceneMatchVersion>
  <directoryFactory name="DirectoryFactory" class="${solr.directoryFactory:solr.StandardDirectoryFactory}"/>

  <updateHandler class="solr.DirectUpdateHandler2" />
  
  <requestHandler name="/select/" class="solr.SearchHandler" />
  <requestHandler name="/update" class="solr.XmlUpdateRequestHandler" />
  <requestHandler name="/admin" class="org.apache.solr.handler.admin.AdminHandlers" />

  <dataDir>${solr.data.dir:}/product</dataDir>
</config>
{% endcodeblock %}
