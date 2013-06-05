---
layout: post
title: Módulos do Núcleo do Node
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
Primeiro eu recomendo que uma versão do node esteja instalada no seu computador. Um modo fácil para que isso aconteça e visitando [nodejs.org](http://nodejs.org) e clique em `Install`.

Node tem um pequeno grupo de módulos que vem com ele por padrão (comumente chamados como 'núcleo do node') eles são representados por suas API's publicas e você utiliza elas para escrever os seu programas. Para trabalhar com sistema de arquivos existe o módulo `fs` e para redes os módulos são `net` (TCP), `http`, `dgram` (UDP).

Em adição ao `fs` e os módulos de rede existem outros módulos na base do núcleo do node. Lá também temos um modulo assincrono para resolver consultas de DNS chamado `dns`, um módulo para pegar informações especificas do sistema, por exemplo o tmpdir e ele chama `os`, um outro para alocação de pedaços de binários na memória chamado `buffer`, varios módulos para análise de urls e caminhos (`url`, `querystring`, `path`) entre outros. A maioria dos módulos presentes no núcleo do node suportam os principais casos de uso dele: escrever rapidamente programas que conversem com os sistema de arquivos ou rede. 

Node lida com I/O com: callbacks, eventos, streams e módulos. Se você aprende como essas quatro coisas trabalham vocês esta habil para ir em quanlquer módulo do core do node a ter um entendimento básico sobre como a interface funciona com ele.

Este artigo foi extraído do livro Art of Node escrito por [Max Ogden](http://maxogden.com/), traduzido por [Kaique Silva](http://kaiquewdev.nodester.com/) e revisada por [mim](http://rafadev.com/). Ele está disponível no [repositório do github](https://github.com/fth-ship/art-of-node) sobre a licença [Creative Commons Attribution](http://creativecommons.org/licenses/by/2.0/).