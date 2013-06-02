---
layout: post
title: Gerando seu app automaticamente com Express(1) em Node.js
description: Express é um framework para Node.js minimalista e flexível e vem com um executável para gerar o seu app automaticamente com todo o esqueleto pronto
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [primeiros passos, módulos, express]
---
[Express][] é um framework para Node.js minimalista, flexível e conta com um robusto conjunto de recursos para desenvolver aplicações web. Ele vem com um executável embutido apropriadamente chamado [express(1)][]. Se você instalar express globalmente com a [npm][] você vai tê-lo disponível em qualquer lugar do seu computador.


## Instalação

Instalar o Express globalmente através da `npm` é muito fácil, basta utilizar o seguinte comando e a versão mais recente será instalada disponível.

{% highlight console %}
$ npm install -g express
{% endhighlight %}


Esta ferramenta fornece uma maneira fácil de ter o esqueleto da sua aplicação montado e funcionando, mas tem um escopo limitado, por exemplo ele só suporta poucas engines para o template, enquanto que o Express por sí próprio suporta virtualmente qualquer engine de templete desenvolvida para o node. Certifique-se de confirir o `--help`:

{% highlight console %}
$ express --help

  Usage: express [options]

  Options:

    -h, --help          output usage information
    -V, --version       output the version number
    -s, --sessions      add session support
    -e, --ejs           add ejs engine support (defaults to jade)
    -J, --jshtml        add jshtml engine support (defaults to jade)
    -H, --hogan         add hogan.js engine support
    -c, --css   add stylesheet  support (less|stylus) (defaults to plain css)
    -f, --force         force on non-empty directory
{% endhighlight %}


## Gerando o esqueleto de sua aplicação

Caso você precise gerar uma aplicação com [EJS][], [Stylus][] e o suporte a sesão, você só precisa executar o seguinte comando:

{% highlight console %}
$ express --sessions --css stylus --ejs myapp

  create : myapp
  create : myapp/package.json
  create : myapp/app.js
  create : myapp/public
  create : myapp/public/javascripts
  create : myapp/public/images
  create : myapp/public/stylesheets
  create : myapp/public/stylesheets/style.styl
  create : myapp/routes
  create : myapp/routes/index.js
  create : myapp/views
  create : myapp/views/index.ejs

  install dependencies:
    $ cd myapp && npm install
    
  run the app:
    $ node app  
{% endhighlight %}

E como qualquer outra aplicação em node, você precisa instalar as dependências:

{% highlight console %}
$ cd myapp
$ npm install 
{% endhighlight %}

Neste ponto o esqueleto da sua aplicação já está construído contendo todas as configurações iniciais para ela já poder rodar corretamente. Então, vamos executá-la:

{% highlight console %}
$ node app
{% endhighlight %}

Para se certificar que tudo está funcionando corretamente, basta acessar `http://localhost:3000/` e ver a calorosa página de boas vindas do Express.


## Concluindo

Isto é tudo que você precisa para ter uma aplicação simples e rodando em Express. Lembre-se que o Express não está limitado a nenhuma estrutura de diretórios específica, este é simplesmente um patamar de partida para você trabalhar em cima. Para estruturas de aplicações alternativas se certifique de ver os [exemplos no repositório do github][] do Express.

[Express]: http://expressjs.com/
[express(1)]: http://expressjs.com/guide.html#executable
[npm]: http://npmjs.org/
[EJS]: https://github.com/visionmedia/ejs
[Stylus]: http://learnboost.github.io/stylus/
[exemplos no repositório do github]: https://github.com/visionmedia/express/tree/master/examples