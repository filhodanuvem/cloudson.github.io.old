---
layout: post
title: "Gitql - Do que é feito?"
date: 2014-04-23 09:02
comments: true
categories: [golang, pet]
---

No começo do ano, um projeto bem legal apareceu no github, o [textql](https://github.com/dinedal/textql). Aquilo me fez pensar novamente "Como não tive essa ideia?"; Baseado num texto csv, poder fazer consultas sql. Simplesmente incrível.  
Depois de dar uma olhada no projeto, voltei ao trabalho; códigos, git status, git commit, git log ... "Hey! Todo mundo sabe sql, mas nem todo mundo sabe git log", e daí pensei em criar o [gitql](https://github.com/cloudson/gitql), uma forma fácil de extrair informações de repositórios git usando uma linguagem de consulta. 

### Desafios
Olhei um pouco mais de perto o que o textql fazia. Como seu objetivo era ler um arquivo aparentemente imutável, a ferramenta primeiramente criava um banco sqlite e importava todos os dados para uma tabela. "Não bom" para meu caso. Repositórios git mudam a todo momento, uma suposta tabela de commits não iria parar de crescer. Ter um banco sqlite para cada repositório e sincronizá-lo antes de cada consulta estava fora de cogitação.  
O que eu queria realmente era transformar o commando `select * from commits` em `git log`, ou seja, traduzir uma linguagem em outra. 
Além disso, percebi que era uma boa hora para estudar - na prática - uma linguagem emergente; [golang](http://golang.org/).

### Enfrentando o terceiro dragão

Para quem me conhece desde a época (não tão distante) da faculdade, sabe que sou um pouco viciado em Teoria de compiladores, linguagens e outras coisas malucas. É uma área meio conturbadora, tanto que um compilador acaba tendo um [dragão](http://www.amazon.com/Compilers-Principles-Techniques-Tools-Edition/dp/0321486811) como símbolo. Mas como eu (junto a amigos) já tinha feito um [compilador de mini-java para assembly mips](https://github.com/cloudson/Bocejo), e um tcc que tinha um [interpretador de C para javascript](http://www.gcg.ufjf.br/cloudblocks/), toda a teoria ainda estava na minha cabeça (em algum lugar).

### Definindo a linguagem 
Gitql é somente leitura, então o interpretador acabaria rodando apenas comandos select, escrevi alguns exemplos úteis, como objetivo final.

`select * from commits`  
`select author, message where (date >= '2014-04-10' and date < '2014-04-11') and (author = 'cloudson' or author = 'jeanpimentel')`
`select author, message where 'Fuck' in full_message`
...

Alguns casos eram tão específicos que eu mesmo não conseguiria gerar um git log com tal informação, o que me animava. 
Definida as palavras chaves (ficaram de fora coisas como joins, alias e  group by), era hora de implementar as fases do compilador que tinham como objetivo gerar a [AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree).

### Como a coisa funciona mais abaixo? 

**select author from commits**  
Por padrão temos um limite de 10 linhas, então o que fazemos é percorrer pelos últimos commits (utilizando [libgit2](https://github.com/libgit2/)) e quando tivermos 10 sucessos, paramos. A cada sucesso guardamos author, que é [transcrito para objeto commit.Author.Name](https://github.com/cloudson/gitql/blob/64ab944ad750a6da26456136f9c40b2a616534f8/runtime/runtime.go#L344). 

**select hash from commits where author = 'cloudson' or author = 'jeanpimentel'**    
A heurística é a mesma mas vamos agora observar o que é um **sucesso**. Percorrendo os commits procuramos aqueles que casam complementamente com o where, ou seja, um sucesso é um commit que possui um dos dois autores.

** select message from commits order by date asc limit 2 **  
Neste caso queremos os 2 primeiros commits de um repositório. Qual o problema deste exemplo? A princípio eu não tenho uma coleção de commits alocada em memória, tudo que vimos até agora é um algoritmo que vai montando uma coleção a medida que encontra sucessos. Isso nos dá uma limitação, a forma para resolver essa consulta seria   
1º) Encontrando **todas** as mensagens de commits com sucesso.  
2º) Ordenando essas mensagens (poderia ser por nome de author).  
3º) Pegando apenas as 2 primeiras linhas.  

Isso faz com que o algoritmo se torne menos eficiente (no tempo e no espaço) é o que tentei relatar na [issue #4](https://github.com/cloudson/gitql/issues/4).
Aparentemente não há outra forma, para que o order by funcione perfeitamente.


### Conclusão
Valeu a pena todos os commits de madrugada para tentar finalizar a ferramenta, e também por estudar mais go, fazia tempos que eu não achava uma linguagem tão divertida.   
Também é ~~du caralho~~ gratificante receber 500 stars por uma ideia maluca. Divulgando no [twitter](https://twitter.com/cloudson/status/455886564787519488) e no [reddit](http://www.reddit.com/r/golang/comments/2334ys/gitql_a_git_query_language_built_with_golang/) gerou-se um movimento inesperado de [pessoas](https://twitter.com/search?q=gitql&src=typd&f=realtime) [interessadas](http://www.reddit.com/r/git/comments/234e8y/gitql_a_git_query_language/) no [projeto](http://ruby5.envylabs.com/episodes/494-episode-457-april-18th-2014). Insano!  