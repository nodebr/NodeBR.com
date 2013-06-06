---
layout: post
title: Entendendo o Node
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
Node.js é um projeto de código aberto projetado para auxiliar na escrita de programas em JavaScript que conversem com redes, sistema de arquivos ou outro I/O (entrada/saida, leitura/escrita).
É isso o que ele é. Ele é uma simples e estável plataforma de I/O que encoraja você a escrever módulos para funcionar nele.

Quais são alguns exemplos de I/O? Bom... aqui tem um diagrama de uma aplicação que eu fiz com node e ela mostra várias fontes de I/O:

![diagrama do servidor]({{ site.url }}/img/posts/diagrama-servidor.png)

Se você não entende todas as diferentes coisas que estão nesse diagrama, fique tranquilo. O ponto é, mostrar um unico nó de processo (o hexagono no meio) pode agir como o corredor entre todos os diferentes pontos finais de I/O (laranja e roxo representam I/O).

Normalmente na construção desses sistemas temas as seguintes situações:

- Dificuldade de programar, mas tem um super rendimento com resultados rápidos (igual escrever servidores web do zero em C)
- Facil de programar mas não é o ideal em relação a velocidade/robustez (semelhando ao caso de tentar o upload de um arquivo de 5GB e o seu servidor quebrar)

A meta do Node é acertar o balanço entre estes dois: relativamente simples de entender e usar rápido o suficiente para mais casos de uso.

Node não é o tanto dos seguintes itens:

- Um web framework (semelhante ao Rails ou Django, que são utilizadas para certos tipos de coisas)
- Uma linguagem de programação (ele usa JavaScript mas o node não é uma linguagem)
  
Em vez disso, node é em algum lugar no meio. Assim:

- Projetado para ser simples e portanto relativamente simples de entender o uso
- Normalmente para programas baseados em I/O que precisam ser rápidos e/ou lidar com muitas conexões
  
No baixo nivel, node é descrito como uma ferramente para escrever os seguintes dois maiores tipos de programas:

- Programas de reder ustilzando protocolos como: HTTP, TCP, UDP, DNS e SSL
- Programas de leitura e escrita de dados para o arqivos do sistema ou processes/memôria local

O que é um programa baseado em "I/O"? Aqui estão os pontos comuns do I/O:

- Bancos de dados (ex. MySQL, PostgreSQL, MongoDB, Redis, CouchDB)
- APIs (ex. Twitter, Facebook, Apple Push Notifications)
- Conexões HTTP/WebSocket (de usuários de um aplicativo web)
- Arquivos (redimensionamento de imagem, editor de videos, rádio online)

Node faz o I/O de forma assíncrona [asynchronous](http://en.wikipedia.org/wiki/Asynchronous_I/O) para lidar com diferentes situações simultaneas. Por exemplo, se você vai até um fast food e faz o pedido de um cheesebuger você tem de imadiato o pedido feito mas não o lanche, então você espara ele ficar pronto para comer. Neste meio tempo outros pedidos estão sendo feitos na lanchonete para outras pessoas. Imagine que você tenha que esperar o registro do seu cheeseburger, bloqueando outras pessoas porque o seu pedido tem que ser feito para o de outra começar enquanto preparam o seu. Isso é chamado de **I/O bloqueante** porque todo o I/O (cozinhar cheeseburgers) acontece um a um enfileirando tudo. O Node, por sua vez é **não-bloqueante**, significando que os pedidos serão feitos e entregues quando estiverem prontos.  

Aqui tem uma lista de coisas divertidas que podem serem feitas com graças ao node por sua natureza não bloqueante:
  
- Controle [quadicopteros voadores](http://nodecopter.com)
- Escrever bots par ao chat IRC
- Criar [robôs bipedes que andam](http://www.youtube.com/watch?v=jf-cEB3U2UQ)