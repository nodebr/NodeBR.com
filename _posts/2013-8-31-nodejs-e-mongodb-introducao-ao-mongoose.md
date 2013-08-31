---
layout: post
title: Nodejs e MongoDB - Introdução ao Mongoose
description: Neste artigo é dada uma introdução para a utilização do banco de dados não relacional MongoDB no Nodejs utilizando o Mongoose
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [dica]
---
[Mongoose][] é uma biblioteca do Nodejs que proporciona uma solução baseada em esquemas para modelar os dados da sua aplicação. Ele possui sistema de conversão de tipos, validação, criação de consultas e hooks para lógica de negócios.

Mongoose fornece um mapeamento de objetos do MongoDB similar ao ORM (Object Relational Mapping), ou ODM (Object Data Mapping) no caso do Mongoose. Isso significa que o Mongoose traduz os dados do banco de dados para objetos JavaScript para que possam ser utilizados por sua aplicação.

**Obs**: Este artigo vai assumir que você tem o [Nodejs][] e o [MongoDB][] instalados. Você precisa estar com o MongoDB rodando em seu computador para poder fazer os seguintes exemplos.

## Instalando o pacote Mongoose

Utilizando o [NPM][] via linha de comando é muito simples fazer a instalação dos pacotes do Nodejs. Para fazer a instalação do Mongoose, execute a seguinte linha de comando que o NPM cuidará de instalar a versão estável mais recente do pacote requerido:

{% highlight javascript %}
npm install Mongoose
{% endhighlight %}

## Conectando com o MongoDB

Neste artigo vamos [conectar o Mongoose][] com o banco de dados de testes que o MongoDB define quando você o inicializa pelo console e vamos garantir que qualquer erro de conexão seja impresso no console.

{% highlight javascript %}
var Mongoose = require('Mongoose');

var db = Mongoose.connection;

db.on('error', console.error);
db.once('open', function() {
  console.log('Conectado ao MongoDB.')
  // Vamos adicionar nossos Esquemas, Modelos e consultas aqui
});

Mongoose.connect('mongodb://localhost/test');
{% endhighlight %}

Ao executar nossa aplicação de exemplo podemos observar no console do MongoDB que foram abertas 5 conexões simultâneas. Isto ocorre porque o Mongoose usa um conjunto de 5 conexões simultâneas por padrão que são compartilhadas por toda sua aplicação. Para melhorar o desempenho das nossas aplicações vamos deixá-las abertas, porém você pode alterar o comportamento padrão adicionando parâmetros opcionais ao `Mongoose.connect()` - o parâmetro `poolSize` define a quantidade de conexões simultâneas. Veja abaixo a saída do meu MongoDB exibindo que foram abertas 5 conexões simultâneas:

{% highlight javascript %}
Sat Aug 31 11:12:21.827 [initandlisten] connection accepted from 127.0.0.1:64413 #2 (1 connection now open)
Sat Aug 31 11:12:21.830 [initandlisten] connection accepted from 127.0.0.1:64414 #3 (2 connections now open)
Sat Aug 31 11:12:21.831 [initandlisten] connection accepted from 127.0.0.1:64415 #4 (3 connections now open)
Sat Aug 31 11:12:21.831 [initandlisten] connection accepted from 127.0.0.1:64416 #5 (4 connections now open)
Sat Aug 31 11:12:21.832 [initandlisten] connection accepted from 127.0.0.1:64417 #6 (5 connections now open)
{% endhighlight %}


## Esquemas e Modelos

Para começar vamos precisar de um [esquema][] e um [modelo][] para que possamos trabalhar com os dados que serão persistidos em nosso banco de dados MongoDB. Esquemas definem a estrutura dos documentos dentro de uma coleção e modelos são usados para criar instâncias de dados que serão armazenados em documentos. Nós vamos fazer um banco de dados de filmes para acompanhar os filmes que tem cenas extras após os créditos finais (também chamado de 'credit cookies'). O código abaixo mostra nosso esquema `movieSchema` e nosso modelo `Movie` criado.

{% highlight javascript %}
var movieSchema = new Mongoose.Schema({
  title: { type: String },
  rating: String,
  releaseYear: Number,
  hasCreditCookie: Boolean
});

var Movie = Mongoose.model('Movie', movieSchema);
{% endhighlight %}

A última linha deste código compila o modelo `Movie` utilizando o esquema `movieSchema` como estrutura. O Mongoose também cria uma coleção no MongoDB chamada `Movies` para estes documentos.

Você pode notar que o modelo `Movie` está em letra maiúscula, isto porque quando um modelo é compilado é retornado uma função construtora que será usada para criar as instâncias do modelo. As instâncias criadas pelo construtor do modelo são documentos que serão persistidos pelo MongoDB ao utilizar a função `save`.

## Criar, Recuperar, Atualizar e Deletar (CRUD)

Criar um novo documento `Movie` é fácil agora que já definimos o modelo, basta instancializar o modelo `Movie` e salvar esta instância na base. Atualizar é igualmente fácil, faça suas modificações e então chame a função `save` do seu documento.

{% highlight javascript %}
var thor = new Movie({
  title: 'Thor',
  rating: 'PG-13',
  releaseYear: '2011',  // Note o uso de String ao inves de Number
  hasCreditCookie: true
});

thor.save(function(err, thor) {
  if (err) return console.error(err);
  console.dir(thor);
});
{% endhighlight %}

Observe que utilizamos uma String ao invés de um Número no campo `releaseYear` e o Mongoose vai se encarregar de converter o dado para tipo especificado no esquema. 

Quando adicionamos estes dois códigos dados à nossa aplicação e executamos vemos que a função `save` vai fornecer um documento recém criado, observado pelo que foi impresso no console de nossa aplicação.

{% highlight javascript %}
{ __v: 0,
  title: 'Thor',
  rating: 'PG-13',
  releaseYear: 2011,
  hasCreditCookie: true,
  _id: 5222012cb65eddf003000001 }
{% endhighlight %}

Coleções no MongoDB possuem **esquemas flexíveis**, isto quer dizer que coleções não impõem a estrutura dos documentos. Na prática isto significa que documentos da mesma coleção não precisam ter o mesmo conjunto de campos ou estrutura, e campos comuns em documentos de uma coleção podem carregar diferentes tipos de dados. Como foi visto em nosso exemplo, utilizando o Mongoose para mapear nossa base, ele padroniza os documentos de um mesmo esquema a fim garantir que instâncias do modelo que compilou aquele esquema sempre tenham o mesmo tipo de dados nos atributos especificados pelo esquema.

MongoDB também possui uma estrutura de dados chamada [índice][] que permite você localizar rápidamente documentos baseado nos valores armazenados em certos campos específicos. Fundamentalmente, índices no MongoDB é similar à índices em outros sistemas de banco de dados. Em nosso exemplo o índice do modelo criado é `_id`.


## Recuperando um documento da base

Diferentes maneiras podem ser utilizadas para recuperar um documento existente na base de dados. Você pode buscar documentos baseado em qualquer propriedade do esquema e você pode buscar qualquer quantidade de documentos. Utilize [findOne][] para limitar os resultados a um único documento.

{% highlight javascript %}
// Buscando um unico filme pelo nome
Movie.findOne({ title: 'Thor' }, function(err, thor) {
  if (err) return console.error(err);
  console.dir(thor);
});

// Buscando todos os filmes
Movie.find(function(err, movies) {
  if (err) return console.error(err);
  console.dir(movies);
});

// Buscando todos os filmes que possuem 'credit cookie'.
Movie.find({ hasCreditCookie: true }, function(err, movies) {
  if (err) return console.error(err);
  console.dir(movies);
});
{% endhighlight %}

Mongoose também permite você criar [funções auxiliares estáticas][] para buscar seus dados. Para isso você deve atribuir sua função estática ao esquema antes de ser feita compilação para o modelo.

{% highlight javascript %}
movieSchema.statics.findAllWithCreditCookies = function(callback) {
  return this.find({ hasCreditCookie: true }, callback);
};

var Movie = Mongoose.model('Movie', movieSchema);

// Utilizadno a funcao auxiliar estatica do modelo 'Movie' compilado
Movie.findAllWithCreditCookies(function(err, movies) {
  if (err) return console.error(err);
  console.dir(movies);
});
{% endhighlight %}

Isso é tudo que precisa fazer para manipular os dados em MongoDB. Mongoose faz esta tarefa muito simples então você pode desenvolver seus serviços rapidamente. Usando isso junto com suas habilidades com Express, você pode desenvolver um aplicativo web muito bom e funcional.

Abaixo está um código utilizando os conceitos aqui apresentados que implementa a função auxiliar estática, para buscar todos filmes com 'credit cookies', salva 3 filmes do Thor com 'credit cookies' e em seguida cria um `Timeout` para buscar os filmes utilizando a função auxiliar 1 segundo depois - Isto é feito pois como estamos trabalhando com **IO não bloqueante** o Node não aguarda o tempo de salvar os filmes antes de prosseguir, por isso damos um tempo para se certificar que eles foram salvos antes de buscá-los.

{% highlight javascript %}
var Mongoose = require('Mongoose');

var db = Mongoose.connection;

db.on('error', console.error);
db.once('open', function() {
  // Create your schemas and models here.
  console.log('Conectado.')
  
  
  
  var movieSchema = new Mongoose.Schema({
    title: { type: String },
    rating: String,
    releaseYear: Number,
    hasCreditCookie: Boolean
  });
  
  
  movieSchema.statics.findAllWithCreditCookies = function(callback) {
    return this.find({ hasCreditCookie: true }, callback);
  };

  var Movie = Mongoose.model('Movie', movieSchema);

  
  for (var i =1; i<=3; i++){
    var thor = new Movie({
      title: 'Thor ' + i,
      rating: 'PG-13',
      releaseYear: '2011',  // Note o uso de String ao inves de Number
      hasCreditCookie: true
    });

    thor.save(function(err, thor) {
      if (err) return console.error(err);
      console.log('Salvo: ')
      console.dir(thor);
    });
  }
  
  setTimeout(function(){
    // Utilizadno a funcao auxiliar estatica do modelo 'Movie' compilado
    Movie.findAllWithCreditCookies(function(err, movies) {
      if (err) return console.error(err);
      console.log('Buscado: ')
      console.dir(movies);
    });
  }, 1000);
});

Mongoose.connect('mongodb://localhost/test');
{% endhighlight %}


[Mongoose]: http://Mongoosejs.com/
[Nodejs]: http://nodejs.org/
[MongoDB]: http://www.mongodb.org/
[conectar o Mongoose]: http://Mongoosejs.com/docs/api.html#connection_Connection
[esquema]: http://Mongoosejs.com/docs/guide.html
[modelo]: http://Mongoosejs.com/docs/models.html
[índice]: http://docs.mongodb.org/manual/core/indexes/
[findOne]: http://Mongoosejs.com/docs/api.html#model_Model.findOne
[funções auxiliares estáticas]: http://Mongoosejs.com/docs/api.html#schema_Schema-static
