---
layout: post
title : JavaScript no servidor com Node.js
description: O JavaScript você pode usar em muitos contextos e fazer de tudo, com isso você pode alcançar o que as outras linguagens não conseguem.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags : [conceito, básico]
---
A primeira encarnação do [JavaScript][] viveu no navegador. Mas este é o contexto. Isso define o que você pode fazer com a linguagem, mas isso não fala muito sobre o que a linguagem em si pode fazer. JavaScript é uma linguagem completa: você pode usar isso em muitos contextos e fazer de tudo, com isso você pode alcançar o que as outras linguagens consegue.

[Node.js][] é realmente só mais um contexto: isso permite você rodar código JavaScript em backend, fora do navegador.

A fim de executar o **JavaScript** que você pretende rodar em backend, ele precisa ser interpretado e executado. Isso é o que **Node.js** faz, fazendo uso da [Máquina Virtual V8 da Google][], o mesmo ambiente de execução do JavaScript usado pelo Google Chrome.

Além disso, Node.js vem com muitos módulos úteis, então você não precisa escrever tudo do começo, por exemplo alguma função que escreva uma string no console.

Portanto, Node.js é de fato duas coisas: um ambiente de execução e uma biblioteca.

Você pode aprofundar-se cada vez mais neste mundo e descobrir como é fácil e divertido desenvolver aplicações altamente escaláveis em JavaScript com Node. O primeiro passo é instalar o Node e a maneira mais fácil de conseguir isso é [instalando o Node.js através do gerenciador de pacotes][], porém você também vai poder [instalar o Node.js através do GitHub][].

Feito isso, desenvolva sua primeira aplicação, com apenas 6 linhas de código, através do [Exemplo Hello World em Node.js][]. Você perceberá como é fácil e divertido o desenvolvimento com JavaScript no servidor e não só isso, ele também é altamente eficiênte e escalável por ser um servidor orientado a eventos não bloqueante.

Caso você queira conhecer mais sobre eventos e como desenvolver aplicações orientadas a eventos, você pode ler o [artigo explicando eventos em Node][] que poderá te ensinar muito sobre eventos.

É claro que tem muito mais a ser descoberto no Node e o objetivo deste artigo é te dar apenas o gostinho bom do prazer de desenvolver em Node, não deixe de acompanhar as [comunidades de Node.js][] e de [assinar nosso Feed de notícias][] para se aprofundar cada vez mais no assunto e ser você também um entusiásta desta tecnologia.

[Node.js]: http://nodejs.org/
[JavaScript]: http://pt.wikipedia.org/wiki/JavaScript
[Máquina Virtual V8 da Google]: http://code.google.com/p/v8/
[instalando o Node.js através do gerenciador de pacotes]: {{ site.url }}{% post_url 2013-1-23-instalando-node-js-atraves-do-gerenciador-de-pacotes %}
[instalar o Node.js através do GitHub]: {{ site.url }}{% post_url 2013-2-5-instalando-node-js-atraves-do-github %}
[Exemplo Hello World em Node.js]: {{ site.url }}{% post_url 2013-2-12-exemplo-hello-world-em-node-js %}
[artigo explicando eventos em Node]: {{ site.url }}{% post_url 2013-6-5-eventos-em-node %}
[comunidades de Node.js]: {{ site.url }}{% post_url 2013-1-10-comunidades-de-node-js %}
[assinar nosso Feed de notícias]: http://feeds.feedburner.com/NodeBR/
