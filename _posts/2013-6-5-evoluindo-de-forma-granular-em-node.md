---
layout: post
title: Evoluindo de Forma Granular em Node
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
Como todo boa ferramenta, o Node é bem adequado para certos casos de uso. Por exemplo: Rails, o popular web framework, é ótimo para modelar complexas [lógicas de negócio](http://en.wikipedia.org/wiki/Business_logic), ex. usando código para representar a vida em um plano objetivado que vivemos físicamente como contas, empréstimos, itinerários e inventários. Embora técnicamente seja possível fazer o mesmo utilizando o Node, haveria desvantagens claras sabendo que o Node é projetado para resolver problemas de I/O e não saber sobre a parte da 'lógica de negócio'. Cada ferramenta tem seu foco voltado a resolver diferentes problemas. Esperamos que este guia ajude-o a ganhar uma compreensão intuitiva dos pontos fortes do Node e você saberá quando ele será útil.

### O que está fora do escopo do Node?

Fundamentalmente o Node é somente usado como ferramenta para gerenciar I/O ao redor do sitema de arquivos e redes, ele deixa outras funcionalidades mais bonitas com módulos de terceiros. Estas são algumas das coisas fora do escopo do Node:

#### Web frameworks

Existem uma boa quantidade de web frameworks construidos em cima do Node (framework é um pacote que tenta resolver um problema de alto nível e problemas similares ao modelar a lógica de negócio), mas o Node não é um framework para web. Frameworks web são escritos para serem utilizado no Node e nem sempre tomam o mesmo tipo de decisões sobre a adição de complexidade, abstração e compreensão que o Node faz e podem ter outras prioridades. 

#### Sintaxe da linguagem 

O Node usa JavaScript e não muda nada sobre isso. Felix Geisendörfer tem um belo conteúdo escrito sobre o 'estilo de escrita do Node' [aqui](https://github.com/felixge/Node-style-guide).

#### Abstração da linguagem 

Quando possível o Node vai usar a maneira mais simples de escrever algo. Código mais 'bonito' faz do seu JavaScript mais complexo e compromissado com vantagens e desvantagens. Programar é difícil, especialmente em JS onde você tem 1000 soluções para o mesmo problema! Essa é a principal razão para o Node optar pela simplicidade sempre que possível e que pode ser uma opção universal. Se você está resolvendo um problema complexo e você está insatisfeito com o modo que o Node implementa as coisas com soluções originais do JS, sinta-se livre para resolver isso dentro do seu app ou módulo usando quaisquer abstrações que você preferir.

Um grande exemplo de como o Node usa os callbacks. Logo no início foi experimentado a caracteristica chamada 'promessas' que adiciona algumas funcionalidades para fazer o código assíncrono parecer mais linear. Ele foi levado para o fora do núcleo do Node por algumas razões:

- eles são mais complexos que callbacks
- ele podem ser implementados na userland (distriuído no npm como módulo de terceiros)

Considerando uma das mais universais e básicas ideias que o Node faz: ler um arquivo. Quando você lê um arquivo e precisa saber onde os erros acontecem, como exemplo quando o disco rígido morre no meio da sua leitura. Se você tem promessas tudo que você terá é um código como esse:

{% highlight javascript %}
fs.readFile('movie.mp4')
  .then(function(data) {
    // faça algo com os dados 
  })
  .error(function(error) {
    // lide com o erro 
  })
{% endhighlight %}

Isso adiciona uma complexidade, desnecessária. No lugar de duas funções separadas o Node somente usa uma única função de callback. Aqui temos as regras:

- Quando não existir erros passe null como primeiro argumento
- Quando o existir erro, passar ele como primeiro argumento
- O restante dos argumentos são usados para qualquer coisa (usualmente dados ou respostas já que na maior parte do tempo o Node está lendo ou escrevendo coisas)

Por isso, o Node usa o estilo de callback:

{% highlight javascript %}
fs.readFile('movie.mp4', function(err, data) {
  // lide com o erro, e faça algo com os dados 
})
{% endhighlight %}