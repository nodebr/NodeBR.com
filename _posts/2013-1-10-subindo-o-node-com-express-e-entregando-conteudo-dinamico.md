---
layout: post
title : Subindo o node com express e entregando conteúdo dinâmico.
author: Onezino Gabriel Moreira
tags : [http, express, exemplo]
---
Este post vai ensinar como subir um aplicação [Node][] utilizando o [express][], um framework web para Node, e também vai falar um pouco do <code>package.json</code> que guarda as informações de metadados do nosso projeto.

Primeiramente crie a estrutura de diretórios, que nos será útil durante o desenvolvimento do aplicativo.

    /
    /static
    /static/css
    /static/css/style.css
    /static/javascript
    /static/javascript/app.js
    /views
    /views/index.html
    /package.json
    /myapp.js

## Definindo o projeto 'package.json'

Precisamos criar o arquivo <code>package.json</code> de metadados de informações na raiz do projeto. Esse arquivo guarda informações do projeto e suas dependencias. Com ele fica fácil fazer deploy e o controle de dependência. o ideal é deixa o arquivo mais limpo possível.

{% highlight json %}
{ "name": "server-web",
  "version": "0.4.0",
  "description": " Servidor http",
  "contributors": [ {
    "name": "Onezino Gabriel Moreira",
    "email": "heat2k4@hotmail.com" } ],
  "keywords": [ "cli", "http", "server" ],
  "dependencies" : {
    "express" : "3.x", 
    "consolidate" : "0.4.x", 
    "swig": "0.12.x" } 
}
{% endhighlight %}

As dependências que usamos são o renderizador de template e o framework web **express**. Pra instalar todas as dependências de uma vez é so digitar o comando na raiz do projeto que a ferramenta de pacote de módulos do node ([NPM][]) se encarregará de todo o trabalho:

    npm install

## Escrevendo sua aplicação

O próximo passo é escreve nossa aplicação, no <code>myapp.js</code>, a começar carregando os módulo que precissamos.

{% highlight javascript %}
var express = require('express'); 
var cons = require('consolidate');
{% endhighlight %}

De novidade temos o [consolidate][], que é um renderizador de template, tepois temos o **express** que é nosso objeto de estudo deste post. O **express** faz nosso wrapper com a biblioteca <code>http</code> então não precisamos carrega-las expressamente.

{% highlight javascript %}
app = express();
app.get('/',function(req,res){
  res.send('Hello world!')});
app.listen(3000);
console.log('app rodando na prota 3000');
{% endhighlight %}

A novidade está na forma de tratar o <code>response</code>, onde só precisamos chamar o método <code>send</code>. Mas isso não é muito útil, então vamos registrar nosso renderizador e entregar uma página mais elegante ao usuário.

{% highlight javascript %}
app.engine('html', cons.swig);
app.set('view engine','html');
app.set('views',__dirname+'/views');
{% endhighlight %}

Nesse trecho dizemos ao express quem vai renderizar nossas páginas e a localização dos tempaltes. O **express** aceita uma lista grande de templates, no nosso exemplo estou utilizando o **consolidate** mas fique livre para utilizar qualquer um de sua preferência. E editamos a linha que entrega o conteúdo na rota <code>/</code>.

{% highlight javascript %}
/* app.get('/',function(req,res){res.send();}); */
app.get('/',function(req,res){
  res.render('index')});
{% endhighlight %}

Pronto, agora só precisamos servir os arquivos estáticos (js, css, imagens). Utilizamos um middleware para tratar as chamadas que trata as chamadas get ao arquivos pesquisando de forma encadeada.

{% highlight javascript %}
app.use(express.static(__dirname + '/static'));
{% endhighlight %}

Podemos agorar utilizar os arquivos <code>/css/style.css</code>e <code>/javascript/app.js</code> em nosso <code>html</code>. Você também pode prefixar o caminho dos arquivos estáticos. vamos utilziar o prefixo <code>/js</code> para nossos arquivos **javascript**.

{% highlight javascript %}
app.use('/js', 
  express.static(__dirname+'/static/javascript'));
{% endhighlight %}

Pronto o javascript pode ser utilizado apartir do caminho <code>/js</code>, exemplo <code>/js/app.js</code>.

Conclusão

Fizemos um setup básico para uma aplicação rodando em express. Essa foi uma um post de introdução funcional, outros posts irão explorar melhor as características do **express** e vão mostrar como este framework se comporta com aplicações mais elaboradas.

[Node]: http://nodejs.org/
[express]: http://expressjs.com/
[consolidate]: https://github.com/visionmedia/consolidate.js