---
layout: post
title : Utilizando módulos em sua aplicação Node.js
author: Onezino Gabriel Moreira
tags : [módulos]
---
Pessoal, vamos trabalhar com módulos em nossa aplicação **Node**. Vou começar com o código abaixo.

{% highlight javascript %}
http = require('http');
{% endhighlight %}

Essa instrução carrega o módulo <code>http</code>, um módulo do sistema <code>core module</code>.
O carregamento de módulos em Node é feito pela função <code>require</code>. Para quem vem do Python é como executar o código:

{% highlight python %}
import os as real_os
{% endhighlight %}

Módulos são sub programas que executão tarefas bem definidas. Nós esperamos com nosso exemplo que o módulo <code>http</code> permita que utilizemos os protocolo <code>http</code>. Os módulos do sistema do **Node** têm suas funções documentadas na página da [API do Node][].

## Core Module

São módulos embutidos no Node. Eles são carregados pela declaração sem prefixo do nome do módulo. Por exemplo:

{% highlight javascript %}
require('buffer');
{% endhighlight %}

Módulos declarados dessa forma dão preferência aos módulos do sistema, mesmo tendo um arquivo <code>buffer.js</code> na raiz de nossa aplicação.

## Módulos da aplicação.

É comum uma grande aplicação dividir suas tarefas em módulos para atender aos requisitos do sistema, tomando como exemplo uma aplicação com o arquivo <code>circle.js</code> (expõe funções matemáticas aplicadas a círculos) em sua raiz. Esse arquivo corresponde a um módulo da aplicação e pode ser chamado da seguinte forma:

{% highlight javascript %}
circle = require('./circle');
circle = require('circle');
circle = require('/path/to/app/circle');
{% endhighlight %}

todas são formas válidas de carregar módulos na aplicação.

## Prefixos <code>./</code> <code>/</code>

Os prefixos determinam a localização do módulo. O prefíxo <code>/</code> utiliza o caminho absoluto do módulo, já o prefíxo <code>./</code> utiliza o caminho relativo a partir do caminho atual.

dica: Quando o módulo não vem prefixado e há um conflito de nomes com módulos do sistema, os módulos do sistema ganham preferência no carregamento.

## Extensão dos módulos

Quando os módulos não tem a extensão explícita o Node procura pelas seguintes extensões em sua devida ordem :

- Primeiro <code>.js</code>: o arquivo é iterpretado como um arquivo de código fonte javascript;
- Depois <code>.json</code>: é utilizado um analisador JSON no arquivo;
- Por último <code>.node</code>: interpretado como um addon através do dlopen.

## Módulos do node_module

Quando o arquivo não é prefixado e não é nativo do Node <code>core modules</code>, o Node busca o módulo na pasta <code>node_module</code>. A busca percorre toda o caminho de diretórios do código executado até a raíz.

Fica mais fácil de entender com o exemplo abaixo. (vamos supor que código é executado em <code>/path/to/app/code.js</code>)

{% highlight javascript %}
require('mymodule');
{% endhighlight %}

Como <code>mymodule</code> não é um módulo do sistema ele então pesquisa na pastas <code>node_module</code>. Essa pesquisa é recursiva por todo o caminho iniciando no diretório atual até atingir a raiz.

- 1º <code>/path/to/app/node_modules/mymodule.js</code>
- 2º <code>/path/to/node_modules/mymodule.js</code>
- 3º <code>/path/node_modules/mymodule.js</code>
- 4º <code>/node_modules/mymodule.js</code>

## Diretórios como módulos

Quando um diretório da aplicação contém um arquivo <code>package.json</code> ele é também é considerado um módulo e pode ser carregado como outros módulos.

{% highlight javascript %}
draw = require('./draw');
{% endhighlight %}

Ele busca pelo arquivo: <code>./draw/package.json</code> que contém informações do módulo e qual arquivo carregar. Caso não exista esse arquivo mas exista um <code>index.js</code> ele é utilizado como arquivo **source** do módulo.

## Outras informações que você deve conhecer sobre os módulos

- Os módulos são carregados apenas uma vez.
- Node trata as chamadas cíclicas de módulos, então mesmo que não seja muito bom tê-las você não precisa se preocupar.
- Somente variáveis exportadas estão disponíveis no módulo, as demais variáveis e funções são privadas do módulo.
- Você pode carregar somente atributos específicos do módulo. Ex: <code>var EventEmitter = require('events').EventEmitter;</code>

[API do Node]: http://nodejs.org/api/