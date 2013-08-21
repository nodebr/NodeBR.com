---
layout: post
title: Como evitar o inferno de callbacks
description: Este artigo mostra como evitar códigos poucos legíveis e expõe as boas práticas da utilização de callbacks
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [dica]
reference: Este artigo foi extraído do <a href="http://callbackhell.com/" rel="nofollow">artigo Callback Hell</a>, escrito por <a href="http://maxogden.com/" rel="nofollow">Max Ogden</a>, disponível no <a href="https://github.com/maxogden/callback-hell" rel="nofollow">repositório do GitHub</a>, traduzido e adaptado por <a href="http://rafadev.com/">mim RafaDev</a>.
---
Este artigo é um guia para se escrever programas assíncronos em JavaScript que expõe dicas para se evitar o 'inferno de callbacks'.

O bom entendimento de callbacks é essencial para a programação orientada a eventos do Node, este é o nome dado a funções que serão executadas de modo assíncrono, ou posteriormente. Caso tenha dúvidas do que é uma callback, você pode ler o [artigo explicando callbacks em Node][].

## O que é o 'inferno de callbacks'

JavaScript assíncrono, ou JavaScript que usa callbacks é difícil de se entender intuitivamente. Muitos códigos acabam se parecendo como este:

{% highlight javascript %}
fs.readdir(source, function(err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function(filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function(err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function(width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(destination + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})
{% endhighlight %}

Você consegue ver quantas `functions` e `})` têm neste código? Isto é carinhosamente conhecido como **o inferno de callbacks**.

Escrever códigos melhores não é difícil, você só precisa saber algumas coisas.

## Nomeie suas funções

Aqui esta um confuso código em JavaScript que roda no navegador que utiliza [browser-request][] para fazer uma requisição AJAX para o servidor.

{% highlight javascript %}
var form = document.querySelector('form')
form.onsubmit = function(submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, function(err, response, body) {
    var statusMessage = document.querySelector('.status')
    if (err) return statusMessage.value = err
    statusMessage.value = body
  })
}
{% endhighlight %}

O código possui duas funções anônimas, observe como fica o código quando damos nomes a estas funções.

{% highlight javascript %}
var form = document.querySelector('form')
form.onsubmit = function formSubmit(submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, function postResponse(err, response, body) {
    var statusMessage = document.querySelector('.status')
    if (err) return statusMessage.value = err
    statusMessage.value = body
  })
}
{% endhighlight %}

Como você pode ver nomear as funções é muito fácil e traz algumas coisas boas para seu código:

-- Facilita a leitura do código
-- Quando acontecem exceções você pega rastreamento da pilha (stacktraces) que referencia o nome atual da função ao invés de "anonymous"
-- Permite você manter seu código mais raso, ou não aninhado profundamente

## Mantenha seu código raso

Aproveitando o exemplo apresentado, vamos um pouco mais longe e se livrar do nível triplo de aninhamento que há no código.

{% highlight javascript %}
function formSubmit(submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse(err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
}

document.querySelector('form').onsubmit = formSubmit
{% endhighlight %}

Códigos como este facilitam a leitura e a manutenção posterior.

## Modularize seu código

Esta é a parte mais importante: **Qualquer pessoa é capaz de criar módulos** (biblioteca AKA). Citando [Isaac Schlueter][] (do projeto do Node): "Escreva módulos pequenos onde cada um faça uma coisa, e monte-os em outros módulos que fazem coisas maiores. Você não entra no inferno de callback se você não ir para lá."

Vamos pegar o exemplo apresentado e transformá-lo em um módulo dividindo-o em um par de arquivos. Aqui será apresentado um método simples que funciona tanto no navegador como no servidor.

Vamos criar um arquivo chamado `formuploader.js` que contém nossas duas funções apresentadas anteriormente.

{% highlight javascript %}
function formSubmit(submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse(err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
}

exports.submit = formSubmit
{% endhighlight %}

A variável `exports` da última linha é um exemplo do sistema de módulos CommonJS. O bom deste sistema de módulos é que ele é muito simples - você só precisa definir o que deve ser compartilhado quando o módulo for importado, para isto é utilizado a variável local `exports` do módulo. Para compreender melhor como funciona a importação de módulos você pode ler o artigo que explica [como funciona a função require do Node.js][];

O Node tem um sistema simples de carregamento de módulos que utiliza o padrão CommonJS e de fato a maior parte das funções do núcleo do Node é implementada utilizando módulos escritos em JavaScript. Compreender profundamente o sistema de módulos do Node é crucial para implementação de códigos legíveis e de fácil manutenção, fundamental para a maior parte das aplicações. Para entender melhor este assunto acesse o artigo que aborda detalhadamente os [módulos em Node.js][].

Para usar módulos no padrão CommonJS no navegador, você pode utilizar a biblioteca [browserify][]. Esta biblioteca basicamente permite você utilizar a função `require` para carregar módulos no seu programa.

Agora que temos o `formuploader.js` e ele foi carregado na página utilizando [a tag HTML script][], nós só precisamos importá-lo e usá-lo. Veja como ficou o código da nossa aplicação agora:

{% highlight javascript %}
var formUploader = require('formuploader')
document.querySelector('form').onsubmit = formUploader.submit
{% endhighlight %}

Agora nossa aplicação exemplo apresentada só possui duas linhas e tem os seguintes benefícios:

-- Facilita o entendimento de novos desenvolvedores - Eles não precisam empacar tentando ler todas as funções `formuploader` para entender o que está acontecendo aqui
-- As funções do `formuploader` agora podem ser usadas em outros lugares sem a duplicação de código e pode facilmente ser compartilhaada no GitHub
-- O código em sí é agradável, simples e fácil de ler

Há muitos padrões de módulos [para o navegador][] e [para o servidor][]. Alguns deles são muito complicados. Os mostrados aqui são os considerados mais simples de se entender.

## E o Promises?

[Promises][] é o padrão mais abstrato para trabalhar com código assíncrono em JavaScript.

O escopo deste artigo é mostrar como escrever um código agradável em JavaScript. Se você utiliza uma biblioteca de terceiros que adiciona abstração para seu JavaScript, então certifique-se de estar disposto a forçar todos que contribuem para a sua biblioteca a também terem os mesmos pontos de vistas sobre o JavaScript que você.

[Max Ogden][] expõe que utiliza callbacks em 90% do código assíncrono que escreve e quando as coisas se complicam ele utiliza algo como a biblioteca [async][], que é um módulo que fornece funções poderosas para trabalhar com código JavaScript assíncrono, ela foi projetada para utilizar como o Node.js mas também pode ser utilizada diretamente no navegador.

## Leitura adicional

-- [Um padrão para desacoplar eventos DOM (em Inglês)][]






[artigo explicando callbacks em Node]: {{ site.url }}{% post_url 2013-6-5-callbacks-em-node %}
[browser-request]: https://github.com/iriscouch/browser-request
[Isaac Schlueter]: http://twitter.com/izs
[como funciona a função require do Node.js]: {{ site.url }}{% post_url 2013-5-29-como-funciona-a-funcao-require-do-node-js %}
[módulos em Node.js]: {{ site.url }}{% post_url 2013-7-29-modulos-em-nodejs %}
[browserify]: https://github.com/substack/node-browserify
[a tag HTML script]: http://www.w3schools.com/tags/att_script_src.asp
[para o navegador]: http://www.adequatelygood.com/2010/3/JavaScript-Module-Pattern-In-Depth
[para o servidor]: {{ site.url }}{% post_url 2013-7-29-modulos-em-nodejs %}
[Promises]: http://domenic.me/2012/10/14/youre-missing-the-point-of-promises/
[Max Ogden]: http://maxogden.com/
[async]: https://github.com/caolan/async
[Um padrão para desacoplar eventos DOM (em Inglês)]: https://gist.github.com/3392235
