---
layout: post
title: O que é a NPM do Node.JS
description: A NPM é um repositório online de projetos de código aberto para o Node.JS e um utilitário de linha de comando para interagir com este repositório.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [básico]
---
NPM é o nome reduzido de Node Package Manager (Gerenciador de Pacotes do Node). A NPM é duas coisas: Primeiro, e mais importante, é um repositório online para publicação de projetos de código aberto para o Node.js; segundo, ele é um utilitário de linha de comando que interage com este repositório online, que ajuda na instalação de pacotes, gerenciamento de versão e gerenciamento de dependências..

A NPM já conta com mais de 35 mil pacotes (Jul-2013), são bibliotecas e aplicações de código aberto, e muitas são adicionadas todos os dias. Estas aplicações podem ser encontradas através do [portal de busca da NPM][]. Uma vez encontrado o pacote que você deseja instalar, ele pode ser instalado com uma única linha de comando.

Digamos que você está trabalhando duro um dia para desenvolver sua Próxima Grande Aplicação. Então você se depara com um problema e decide que está na hora de usar aquela biblioteca legal que você tem ouvido a respeito - vamos usar a biblioteca assincrona de Coalan McMahon como exemplo. Felizmente a NPM é muito simples de usar, você só precisa usar o comando `npm install async`, e o módulo específico será instalado no diretório atual dentro da pasta `./node_modules/`. Uma vez instalada sua pasta `node_modules`, você será capaz de usar `require()` nela como se fossem módulos internos do seu projeto. Para compreender melhor a função `require()` eu aconselho que você leia o artigo que explica [como funciona a função require do Node.js][].

Vamos ver um exemplo de pacote instalado globalmente - vamos falar do `coffee-script`. O comando para o NPM é simples: `npm install coffee-script -g`. Para usuários do Linux este comando irá instalar o progama e colocar um link simbólico (symlink) na pasta `/usr/local/bin`. Isto permitirá você rodar o programa diretamente do console como qualquer outra ferramenta CLI (Command-Line Interface). Neste caso, rodar o comando `coffee` te permitirá usar o `REPL` do coffee script.

Outro importante uso da NPM é o gerenciamento de dependências. Quando você tem um projeto Node com um arquivo `package.json`, você pode rodar o comando `npm install` na pasta raiz do seu projeto e a NPM vai isntalar todas as dependências listadas no `package.json`. Isto faz a instalação de projetos Node de repositórios Git muito mais fáceis! Vamos usar o exemplo do `vows`, um dos frameworks de testes do Node, ele pode ser instalado através do Git, e suas dependências: `eyes`, `diff` e `glob` podem ser automáticamente instaladas utilizando uma única linha de comando da NPM.

Observe os comandos para clonar o projeto `vows` do github utilizando o git, e instalar suas dependências listadas no arquivo `package.json`:

{% highlight console %}
git clone https://github.com/cloudhead/vows.git
cd vows
npm install
{% endhighlight %}

Após rodar estes comandos, você vai ver a pasta `node_modules` contendo todas as dependências especificadas no `package.json`.

[portal de busca da NPM]: http://search.npmjs.org/
[como funciona a função require do Node.js]: {{ site.url }}{% post_url 2013-5-29-como-funciona-a-funcao-require-do-node-js %}