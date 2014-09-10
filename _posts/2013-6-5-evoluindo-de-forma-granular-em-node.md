---
layout: post
title: Evoluindo de forma granular em Node
description: Este artigo expõe conceitos básicos para se entender o Node do livro A arte do Node.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [conceito, básico]
reference: Este artigo foi extraído do livro Art of Node escrito por <a href="http://maxogden.com/">Max Ogden</a>, traduzido por <a href="http://kaiquewdev.nodester.com/">Kaique Silva</a> e revisada por <a href="http://rafadev.com/">mim</a>. Ele está disponível no <a href="https://github.com/fth-ship/art-of-node">repositório do github</a> sobre a licença <a href="http://creativecommons.org/licenses/by/2.0/">Creative Commons Attribution</a>.
---
Como todo boa ferramenta, o node é bem adequado para certos casos de uso. Por exemplo: Rails, o popular web framework, é ótimo para modelar complexas [lógicas de negócio](http://en.wikipedia.org/wiki/Business_logic), ex. usando código para representar a vida em um plano objetivado que vivemos físicamente como contas, empréstimos, itinerários e inventários. Embora tecnicamente seja possivel fazer o mesmo utilizando o node, haveria desvantagens claras sabendo que o node é projetado para resolver problemas de I/O e não saber sobre a pate da 'lógica de negócio'. Cada ferramente tem seu foco voltado a resolver diferentes problemas. Esperamos que este guia ajude-o a ganhar uma compreensão intuitiva dos pontos fortes do node e você saberá quando ele será útil.

### O que esta fora do escopo do node?

Fundamentalmente o node é somente usado como ferramenta para gerenciar I/O ao redor do sitema de arquivos e redes, ele deixa outras funcionalidades mais bonitas com módulos de terceiros. Aqui são algumas das coisas fora do escopo do node:

#### Web frameworks

Existem uma boa quantidade de web frameworks construidos em cima do node (framework é um pacote que tenta resolver um problema de alto nível e problemas similares ao modelar a lógica de negócio), mas o node não é um framework para web. Frameworks web são escritos para serem utilizado no node e nem sempre tomam o mesmo tipo de decisões sobre a adição de complexidade, abstração e compreensão que o node faz e podem ter outras prioridades. 

#### Sintaxe da linguagem 

O node usa JavaScript e não muda nada sobre isso. Felix Geisendörfer tem uma belo conteúdo escrito sobre o 'estilo de escrita do node' [aqui](https://github.com/felixge/node-style-guide).

#### Abstração da linguagem 

Quando possivel o node vai usar a maneira mais simples de escrever algo. Código mais 'bonito' faz do seu JavaScript mais complexo e compromissado com vantagens e desvantagens. Programar é difícil, especialmente em JS onde você tem 1000 soluções para o mesmo problema! Essa é a principal razão para o node optar pela simplicidade sempre que possivel e que pode ser uma opção universal. Se você esta resolvendo um problema complexo e você esta insatisfeito com o modo que o node implementa as coisas com soluções originais do JS, sinta-se livre para resolver isso dentro do seu app ou módulo usando qualquer abstrações que você preferir.

Um grande exemplo de como o node usa os callbacks. Logo no inicio foi experimentado a caracteristica chamada 'promessas' que adiciona algumas funcionalidades para fazer o código assincrono parecer mais linear. Ele foi levado para o fora do núcleo do node por algumas razões:

- eles são mais complexos que callbacks
- ele podem ser implementados na userland (distriuído no npm como módulo de terceiros)

Considerando uma das mais universais e básicas ideias que o node faz: ler um arquivo. Onde você lê um arquivo e precisa saber onde os erros acontecem, como exemplo quando o disco rigido morre no meio da sua leitura. Se você tem promessas tudo que você terá é um código como esse:

{% highlight javascript %}
fs.readFile('movie.mp4')
  .then(function(data) {
    // faça algo com os dados 
  })
  .error(function(error) {
    // lide com o erro 
  })
{% endhighlight %}

Isso adiciona uma complexidade, desnecessária. No lugar de duas funções separadas o node somente usa uma única função de callback. Aqui temos as régras:

- Quando não existir erros passe null como primeiro argumento
- Quando o existir erro, passar ele como primeiro argumento
- O restante dos argumentos são usados para qualquer coisa (usualmente dados ou respostas já que na maior parte do tempo o node esta lendo ou escrevendo coisas)

Por isso, o node usa o estilo de callback:

{% highlight javascript %}
fs.readFile('movie.mp4', function(err, data) {
  // lide com o erro, e faça algo com os dados 
})
{% endhighlight %}
