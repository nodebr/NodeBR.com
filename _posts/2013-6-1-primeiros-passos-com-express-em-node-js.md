---
layout: post
title: Primeiros passos com Express em Node.js
description: Express é um framework para Node.js minimalista e flexível com um robusto conjunto de recursos para construir aplicações web híbridas de uma ou múltiplas páginas.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [primeiros passos, módulos, express]
---
[Express][] é um framework para Node.js inspirado no [Sinatra][]. Ele é minimalista, flexível e contém um robusto conjunto de recursos para desenvolver aplicações web, como um sistema de Views intuitivo ([MVC][]), um robusto sistema de roteamento, um executável para geração de aplicações e muito mais.

## Instalação

Para iniciar o desenvolvimento com Express primeiro é preciso instalá-lo, há muitas formas de fazer isso, porém a mais fácil é através da [npm][].

{% highlight console %}
$ npm install express@3.x
{% endhighlight %}

## Primeira aplicação com o Express

Para criar a primeira aplicação poderia ser utilizado o [express(1)][] e ele geraria automaticamente a aplicação e seu esqueleto, de qualquer forma uma aplicação em Express pode ser um único arquivo contendo em JavaScript e no nosso caso, de uma simples aplicação de "Ola Mundo", isso é exatamente o que precisamos.

A primeira coisa que é preciso fazer é importar o módulo `express` e criar um `app`.

{% highlight javascript %}
var express = require('express'),
    app = express();
{% endhighlight %}

Com uma nova instância de aplicação a próxima tarefa é estabelecer uma ou mais rotas. Uma rota consiste em um método `HTTP`, um caminho (uma string ou uma expressão regular) e uma função callback (que será chamada quando o evento ocorrer). Este exemplo "Ola Mundo" chama `app.get()` que representa o método `GET` do `HTTP`, e o caminho `/` representando nossa página raiz, seguido da função de callback.

Os objetos `req` e `res` são exatamente os mesmos objetos que o node fornece à você, assim você pode chamar `res.pipe()`, `req.on('data', callback)` ou qualquer outra coisa que você quiser fazer sem involver o Express.

{% highlight javascript %}
app.get('/', function(req, res){
    res.send('Ola Mundo');
  });
{% endhighlight %}

Em seguida temos que informar o servidor para escutar uma dada porta. Abaixo foi chamado o método `listen(3000)` do nosso objeto `app` que tenta criar um servidor e vinculá-lo à porta 3000. A porta pode ser qualquer uma que você escolher, por exemplo a porta 80 padrão usando `listen(80)`.

{% highlight javascript %}
var server = app.listen(3000);
console.log('Servidor Express iniciado na porta %s', server.address().port);
{% endhighlight %}

## Rodando a aplicação

Para rodar o servidor basta salvar esta aplicação em um arquivo, neste exemplo `app.js`, e simplesmente executá-la passando o nome do arquivo como parâmetro para o node:

{% highlight console %}
$ node app.js
Servidor Express iniciado na porta 3000
{% endhighlight %}

Finalmente para confirmar que tudo está funcionando como o esperado basta acessar o caminho `http://localhost:3000` em seu navegador ou simplesmente usar o comando `curl` do linux:

{% highlight console %}
$ curl http://localhost:3000
Ola Mundo
{% endhighlight %}

## Todo o código da aplicação

Abaixo está as 12 linhas de código usadas para criar nossa primeira aplicação em Express:

{% highlight javascript %}
var express = require('express'),
    app = express();

app.use(express.logger());

app.get('/', function(req, res){
    res.send('Ola Mundo');
});

var server = app.listen(3000);
console.log('Servidor Express iniciado na porta %s', server.address().port);
{% endhighlight %}

[Express]: http://expressjs.com/
[Sinatra]: http://www.sinatrarb.com/
[MVC]: http://pt.wikipedia.org/wiki/MVC
[express(1)]: http://expressjs.com/guide.html#executable
[npm]: http://npmjs.org/