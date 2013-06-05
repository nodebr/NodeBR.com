---
layout: post
title: Eventos em Node
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
No node com o módulo de [eventos](http://nodejs.org/api/events.html) você pode disparar eventos, também chamando de 'emissor de evento', até mesmo o node usa em todas as suas APIs para emitir coisas.

Eventos são padrões comuns de programação, para conhecer melhor procure por ['observer pattern'](http://en.wikipedia.org/wiki/Observer_pattern) ou 'pub/sub' (publicar/assinar). Ao passo que callbacks são uma relação um-para-um entre algo que espera pelo callback e outra parte que chama o callback, eventos seguem o mesmo padrão com exceção de que eles são uma API de muitos-para-muitos.

Aqui temos casos comuns de uso dos eventos ao invés de simples callbacks:

- Uma sala de chat onde você tem um canal de mensagens com muitos ouvintes.
- Servidor de um jogo que necessita saber quando os players se ligam, desligam, movem-se, atiram ou pulam
- Conectores de bancos de dados podem precisar saber onde suas conexões estão abertar, fechadas ou enviando um erro

Se você tentar escrever um servidor de chat que se conecte usando apenas callbacks ele irá parecer com isso:

{% highlight javascript %}
var chatClient = require('my-chat-client')

function onConnect() {
  // exibe a UI quando conectar-se
}

function onConnectionError(error) {
  // exibe erros para o usuario
}

function onDisconnect() {
 // avisa ao usuario que ele foi desconectado
}

function onMessage(message) {
 // exibe a mensagem na UI da sala
}

chatClient.connect(
  'http://mychatserver.com',
  onConnect,
  onConnectionError,
  onDisconnect,
  onMessage
)
{% endhighlight %}

Como você pode ver é realmente pesado escrever porque você tem que passar todas as funções em uma ordem especifica para a função `.connect`. Escrevendo isso com eventos irá se parecer com isso:

{% highlight javascript %}
var chatClient = require('my-chat-client').connect()

chatClient.on('connect', function() {
  // exibe a UI quando conectar-se
}) 

chatClient.on('connectionError', function() {
  // exibe erros para o usuario
})

chatClient.on('disconnect', function() {
  // avisa ao usuario que ele foi desconectado
})

chatClient.on('message', function() {
  // exibe a mensagem na UI da sala
})
{% endhighlight %}

Esta abordagem é bastante similar a utilização com callbacks-puros mas essa abordagem introduz o método `.on`, onde é atrelado um callback ao evento. Isso significa que você escolhe o que tem que estar assinado para `chatClient`. Você é capaz de assinar o mesmo evento várias vezes com diferentes callbacks:

{% highlight javascript %}
var chatClient = require('my-chat-client').connect()
chatClient.on('message', logMessage)
chatClient.on('message', storeMessage)

function logMessage(message) {
  console.log(message)
}

function storeMessage(message) {
  myDatabase.save(message)
}
{% endhighlight %}

Mais conteúdo sobre eventos em Node está por ser escrito ainda no livro...

Este artigo foi extraído do livro Art of Node escrito por [Max Ogden](http://maxogden.com/), traduzido por [Kaique Silva](http://kaiquewdev.nodester.com/) e revisada por [mim](http://rafadev.com/). Ele está disponível no [repositório do github](https://github.com/fth-ship/art-of-node) sobre a licença [Creative Commons Attribution](http://creativecommons.org/licenses/by/2.0/).