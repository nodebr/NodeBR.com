---
layout: post
title : JavaScript no servidor com Node.js
author: Rafael Henrique Moreira
tags : [conceito, básico]
---
A primeira encarnação do [JavaScript][] viveu no navegador. Mas este é o contexto. Isso define o que você pode fazer com a linguagem, mas isso não fala muito sobre o que a linguagem em si pode fazer. JavaScript é uma linguagem completa: você pode usar isso em muitos contextos e fazer de tudo, com isso você pode alcançar o que as outras linguagens consegue.

[Node.js][] é realmente só mais um contexto: isso permite você rodar código JavaScript em backend, fora do navegador.

A fim de executar o **JavaScript** que você pretende rodar em backend, ele precisa ser interpretado e executado. Isso é o que **Node.js** faz, fazendo uso da [Google's V8 VM][], o mesmo ambiente de execução do JavaScript usado pelo Google Chrome.

Além disso, Node.js vem com muitos módulos úteis, então você não precisa escrever tudo do começo, por exemplo alguma função que escreva uma string no console.

Portanto, Node.js é de fato duas coisas: um ambiente de execução e uma biblioteca.

[Node.js]: http://nodejs.org/
[JavaScript]: http://pt.wikipedia.org/wiki/JavaScript
[Google's V8 VM]: http://code.google.com/p/v8/