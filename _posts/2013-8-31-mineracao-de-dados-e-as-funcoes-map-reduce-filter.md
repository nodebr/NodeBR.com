---
layout: post
title: Mineração de dados e as funções map, reduce e filter
description: Neste artigo exibiremos como fazer a extração de informação em uma base de dados da Internet utilizando o Nodejs e demonstrando como utilizar as funções do JavaScript map, reduce e filter
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [funções, javascript, exemplo, intermediário]
---
Este artigo vai mostrar como é simples fazer uma **mineração de dados na Internet** com o **Nodejs** e demonstrando como utilizar funções muito úteis do JavaScript: `map`, `reduce` e `filter`.

Bancos de dados não-relacionais ([NoSQL][]), como o [CouchDB][] e o [mongoDB][], utilizam o [MapReduce][], que é uma combinação das funções `map` e `reduce` apresentadas aqui, para efetuarem suas consultas na base de dados. Eles implementam funções de maneira muito similares às do JavaScript e este é o melhor motivo para compreender o funcionamento desta operação.

O texto também serve de base para quem deseja fazer um **web crawler**, também chamado de **web spider**, que são programas que acessam páginas da web e extraem informações relevantes de seu conteúdo. Os motores de busca, como a Google e o Bing, utilizam esta técnica para manterem suas bases de dados atualizadas. Os crawlers também são utilizados para varrer a Internet a fim de minerar endereços de email e dados pessoais.


## Buscando o conteúdo na Web

A fonte de dados utilizada será o [Feed RSS do HackerNews][], como o RSS está em XML podemos tratá-lo da mesma forma que um documento HTML.

Para dar início ao nosso projeto temos que criar nosso aplicativo e fazer com que ele busque todo o Feed para que seja possível fazer a extração dos dados. Para isso será utilizado a função `get` do módulo `https`. Observe como o aplicativo exemplo `app.js` ficou.


{% highlight javascript %}
// app.js

var https = require('https');

function getCallback(response){
  var body = '';
  console.log("Temos uma resposta: " + response.statusCode);
  response.on('data', function (chunk) {
    body += chunk;
  });
  response.on('end', function(){
    console.log("Corpo da mensagem: " + body.slice(0,200) + '...');
  });
}

https.get('https://news.ycombinator.com/bigrss', getCallback)
  .on('error', function(e){
    console.log("Ocorreu um erro: " + e.message);
  });
{% endhighlight %}

Vamos acompanhar o que é feito neste código passo a passo. Primeiro importamos o módulo `https` que buscará o endereço da web que queremos. Depois definimos a função `getCallback` que, como o nome sugere, será a função callback do nosso `https.get()`, ela recebe o objeto `response` como argumento e é adicionado um `event listner` para o evento `data`, que concatena a resposta na variável `body`, e um `event listner` para o evento `end`, que chama a função `console.log()` quando a resposta de nossa requisição está completa. Por último executamos o método `https.get()` passando como parâmetro o endereço do Feed que vamos buscar e a função callback que tratará a resposta, adicionamos também um `event listner` para o evento `error` que nos indicará se ocorreu algum erro durante nossa requisição.

É interessante ressaltar que o Node trabalha com eventos, então cada pacote recebido como resposta é um evento que deve ser tratado no nosso objeto `response`, por isso temos que concatenar toda a resposta a fim de trabalhar com o corpo completo da página requisitada.


## Minerando os dados

Agora que temos o corpo da página temos que fazer a extração dos dados que queremos e do jeito que queremos. Neste exemplo vamos demonstrar as habilidades das funções `map`, `reduce` e `filter` desenvolvendo um contador de palavras dos títulos dos posts. Ele contará o número de ocorrências de cada palavrá e retornará uma lista de túplas `[palavra, nro_ocorrências]` das palavras que ocorreram mais de 10 vezes nos títulos.

Como foi dito, a base de dados que vamos minerar está em RSS então ela pode ser tratada da mesma forma que uma página HTML. Para isso podemos utilizar o [jQuery][] para extrair a informação formatada.

Para utilizar as funções do núcleo do jQuery no servidor podemos tanto utilizar a própria biblioteca jQuery, desenvolvida para o navegador, como utilizar a biblioteca [Cheerio][], que é uma implementação rápida, flexível e limpa do core do jQuery designada específicamente para o servidor. Matthew Mueller diz que seus benchmarks sugerem que Cheerio seria 8 vezes mais rápida que a implementação do jQuery para o navegador.

Antes de utilizá-la, vamos fazer a instalação do pacote do Cheerio para em nosso aplicativo. Para isso basta instalá-lo utilizando a [NPM][] via linha de comando:

{% highlight javascript %}
npm install cheerio
{% endhighlight %}

Vamos, então, importar o módulo para nosso aplicativo e criar uma função para minerar os dados da página requisitada. Vamos chamar nossa função de `minerarDados()` e passar para ela o corpo da resposta recebida pela função `getCallback()`. O código do nosso exemplo ficaria assim:

{% highlight javascript %}
var https = require('https'),
    cheerio = require('cheerio');

function minerarDados(body){
  var $ = cheerio.load(body);
  var titles = $('title');
  console.log(titles.slice(0,2));
}

function getCallback(response){
  var body = '';
  console.log("Got response: " + response.statusCode);
  response.on('data', function (chunk) {
    body += chunk;
  });
  response.on('end', function(){
    minerarDados(body);
  });
}

https.get('https://news.ycombinator.com/bigrss', getCallback)
  .on('error', function(e){
    console.log("Ocorreu um erro: " + e.message);
  });
{% endhighlight %}


Agora nossa função `minerarDados()` conseguiu extrair os objetos do tipo `tag` de nome `title` e imprime dois deles no console. Ok, ela extrai todos os títulos do Feed RSS, porém temos um problema, `$('title')` retorna uma lista de nós ([NodeList][]). Vamos então aplicar o **MapReduce** para extrair a informação que desejamos e por último filtrar os resultados indesejados.

Para converter-mos nossa lista de nós em um Array contendo apenas o conteúdo de texto dos nomes vamos primeiro transformar nossa **NodeList** em um Array de objetos utilizando a função `Array.prototype.slice.call()` e neste array de objetos vamos aplicar a função `map()` do JavaScript, o código ficaria assim.

{% highlight javascript %}
var titles = Array.prototype.slice.call($('title')).map(
  function(node, index, context) {
    return $(node).text();
});
{% endhighlight %}

## array.map(callback[, thisArg])

A função `map()` recebe dois parâmetros mas normalmente apenas o primeiro é especificado. O primeiro parâmetro é uma função de callback que será chamada sobre todos os elementos no Array. O segundo parâmetro é utilizado para especificar o valor para o objeto `this` durante a execução da função. O mais importante são os parâmetros passados para a função de callback, eles são: o elemento do Array em sí, o índice do Array, e todo o Array (contexto). A assinatura da função de callback se parece com essa:

{% highlight javascript %}
var callback = function(elemento, indice, contexto) { /* omitido */ }
{% endhighlight %}

Porém também podemos utilizar a função `map()` do jQuery implementada pelo Cheerio, mas para isso é preciso ter atenção pois a assinatura da função callback do jQuery é ligeiramente diferente, em seus parâmetros primeiro vem o índice do elemento seguido do elemento em sí. Sua assinatura é assim:

{% highlight javascript %}
var callback = function(indice, elemento) { /* omitido */ }
{% endhighlight %}

Para implementar utilizando a função `map()` do jQuery é preciso fazer ligeiras modificações, o código deverá se parecer com isto:

{% highlight javascript %}
var titles = $('title').map(
  function(index, node) {
    return $(node).text();
});
{% endhighlight %}


## Eliminando pontuações desnecessárias

Agora que já temos um Array de strings contento o texto de todos os títulos da nossa base de dados que estamos explorando. Você já deve ter notado que os títulos não contém apenas letras, eles também contém números e pontuações. Estes são desnecessários em nosso contador de palavras, então vamos tratar de removê-los. Isto pode ser feito nesta mesma chamada `map()` utilizando expressão regular.

{% highlight javascript %}
var words = $('title')
  .map(function(index, node) {
    return $(node).text().toLowerCase().match(/([a-z]+)/g);
  });
{% endhighlight %}

Se você já utilizou RegEx (expressões regulares) no JavaScript você sabe que isso gera um efeito colateral: Esta função retorna Arrays com todas as palavras que passaram na RegEx individualmente. Contudo esse é um efeito colateral útil para nós pois só precisamos das palavras, no entanto temos mais um problema: o Array `words` retornado é de duas dimensões, isso que significa que temos que achatá-lo para apenas uma dimensão. Este é um bom momento para fazer uso da função `reduce()` do JavaScript.

{% highlight javascript %}
var words = $('title')
  .map(function(index, node) {
    return $(node).text().toLowerCase().match(/([a-z]+)/g);
  })
  .reduce(function(last, now){
    return last.concat(now)
  }, []);
{% endhighlight %}


## array.reduce(callback[, initialValue])

Assim como `map`, `reduce` recebe dois argumentos. O primeiro é novamente a função de callback, que será chamada para cada elemento no Array. O segundo parâmetro é o `initialValue` que será de fácil entendimento quando você ver a assinatura da função de callback:

{% highlight javascript %}
var callback = function(valorAnterior, valorAtual, indice, contexto) { /* omitted */ }
{% endhighlight %}

Como você pôde ver, o primeiro parâmetro passado para a função de callback é o valor anterior. Se você precisar somar um Array de números, isso não será problema. Mas em nosso caso nós precisamos retornar um Array, então nós especificamos um valor inicial para o `valorAnterior`, neste caso um Array vazio `[]` onde serão adicionados os elementos de nossa antiga Array bi-dimensional.


## Contando as palavras

Para esta tarefa podemos utilziar `reduce` novamente, mas antes de fazê-lo, vou mostrar como o resultado vai se parecer:

{% highlight javascript %}
[['the', 'on', 'news', 'hacker', ...], [50, 66, 20, 19, ...]]
{% endhighlight %}

Isso será uma Array de duas dimensões novamente. São dois Arrays dentro de um Array, e o índice da palavra no primeiro Array corresponderá com o índice do número de vezes que ela ocorre nos títulos no segundo Array. Para fazer isso vamos utilizar `reduce` e vamos especificar uma Array 2d vazia como valor inicial: `[[], []]`.

{% highlight javascript %}
var scores = $('title')
  .map(function(index, node) {
    return $(node).text().toLowerCase().match(/([a-z]+)/g);
  })
  .reduce(function(last, now){
    return last.concat(now)
  }, [])
  .reduce(function(last, now){
    var index = last[0].indexOf(now);
    if (index === -1) {
      last[0].push(now);
      last[1].push(1);
    } else {
      last[1][index] += 1;
    }
    return last;
  }, [[], []]);
{% endhighlight %}


## Comprimindo os Arrays

Estamos próximos de concluirmos nossa coleção de dados. Tudo que devemos fazer agora é combinar as duas Arrays em uma. O formato final do Array será este:

{% highlight javascript %}
[['the', 50], ['on', 66], ['news', 20], ['hacker', 19], ...]
{% endhighlight %}

O JavaScript não implementa a função `zip()` nativamente, nós poderíamos utilziar esta função do pacote [Underscore][] mas como esta função é muito simples de ser implementada vamos implementar nós mesmos a mesclagem dos valores destas listas.

{% highlight javascript %}
var zip = [];
scores[0].forEach(function(word, i) {
  zip.push([word, scores[1][i]])
});
{% endhighlight %}


## Filtrando os dados

Lembrando que nossa aplicação só se interessa por palavras que ocorreram mais de 10 vezes, a fim de filtrar as palavras menos importantes da lista, então vamos implementar esta última funiconalidade. Para isto podemos utilizar a função nativa do JavaScript `filter` da seguinte maneira:

{% highlight javascript %}
var filtered = zip.filter(function (element){
  return element[1] >= 10
});
{% endhighlight %}


## array.filter(callback[, thisObject])

Os parâmetros da função `filter` são exatamente os mesmos da função `map` já apresentada, ela possui dois parâmetros porém normalmente só o primeiro é especificado. O primeiro parâmetro é uma função de callback que será chamada sobre todos os elementos no Array. O segundo parâmetro é utilizado para especificar o valor para o objeto `this` durante a execução da função. O importante é a função de callback, seus parâmetros são: o elemento do Array em sí, o índice do Array e todo o Array (contexto). A assinatura da função de callback se parece com essa:

{% highlight javascript %}
var callback = function(elemento, indice, contexto) { /* omitido */ }
{% endhighlight %}

Esta função retorna verdadeiro ou falso para filtrar os elementos que continuam no Array e os elementos que são excluídos do Array. Quando fizemos `element[1] >= 10` utilizamos o operador lógico de comparação que retorna verdadeiro para todos os elementos cujo valor do índice 1 seja maior ou igual a 10 e falso caso contrário - o índice 1 guarada a quantidade de vezes que a palavra ocorreu nos títulos.


## Conclusão

Agora nosso exemplo está concluido e conseguímos fazer a mineração dos dados que queríamos dos Feeds do HackerNews. O código final ficou assim:

{% highlight javascript %}
var https = require('https'),
    cheerio = require('cheerio');

function minerarDados(body){
  var $ = cheerio.load(body);
  var scores = $('title')
    .map(function(index, node) {
      return $(node).text().toLowerCase().match(/([a-z]+)/g);
    })
    .reduce(function(last, now){
      return last.concat(now)
    }, [])
    .reduce(function(last, now){
      var index = last[0].indexOf(now);
      if (index === -1) {
        last[0].push(now);
        last[1].push(1);
      } else {
        last[1][index] += 1;
      }
      return last;
    }, [[], []]);
  var zip = [];
  scores[0].forEach(function(word, i) {
    zip.push([word, scores[1][i]])
  });
  var filtered = zip.filter(function (element){
    return element[1] >= 10
  });
  console.log(filtered.slice(0,20));
}

function getCallback(response){
  var body = '';
  console.log("Got response: " + response.statusCode);
  response.on('data', function (chunk) {
    body += chunk;
  });
  response.on('end', function(){
    minerarDados(body);
  });
}

https.get('https://news.ycombinator.com/bigrss', getCallback)
  .on('error', function(e){
    console.log("Ocorreu um erro: " + e.message);
  });
{% endhighlight %}

O exemplo apresentado funcionou corretamente e conseguiu extrair a informação da base da Internet escolhida e imprimir os 20 primeiros elementos do nosso Array no console `console.log(filtered.slice(0,20));`.

O artigo teve como finalidade ensinar os leitores como é fácil fazer tal operação utilizando o Nodejs e as funções nativas do JavaScript, podendo ser utilizado como base para desenvolvimento de aplicações mais complexas como robôs de web crawling entre outros.



[Feed RSS do HackerNews]: https://news.ycombinator.com/bigrss
[jQuery]: http://jquery.com/
[Cheerio]: http://matthewmueller.github.io/cheerio/
[NPM]: {{ site.url }}{% post_url 2013-7-19-o-que-e-a-npm-do-nodejs %}
[NodeList]: http://www.w3schools.com/dom/dom_nodelist.asp
[Underscore]: http://documentcloud.github.io/underscore/
[NoSQL]: http://pt.wikipedia.org/wiki/NoSQL
[CouchDB]: http://couchdb.apache.org/
[mongoDB]: http://www.mongodb.org/
[MapReduce]: http://en.wikipedia.org/wiki/MapReduce
