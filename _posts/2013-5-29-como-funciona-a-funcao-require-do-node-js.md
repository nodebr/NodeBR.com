---
layout: post
title: Como funciona a função require do Node.js
description: Seguindo a especificação CommonJS para padronização de modulos em JavaScript, a função require é fundamental para o funcionamento do módulos em Node.js
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [primeiros passos, módulos]
published: false
---
O Node.js segue a [CommonJS][], uma especificação de ecossistemas para o JavaScript, e a função embutida <code>require</code> é a maneira mais fácil de incluir módulos existentes em arquivos separados. O funcionamento básico do <code>require</code> é que ele lê o arquivo JavaScript, avalia o script e em seguida retorna o conteúdo do objeto <code>exports</code>. Segue um exemplo de módulo para melhor compreensão:

{% highlight javascript %}
console.log("Avaliando o exemplo.js");

var invisivel = function () {
  console.log("invisivel");
}

exports.mensagem = "Oi";

exports.falar = function () {
  console.log(exports.mensagem);
}
{% endhighlight %}

Podemos testar nosso módulo rodando através do [REPL do node][], na pasta onde salvamos nosso projeto <code>exemplo.js</code>, digitando <code>require("./exemplo.js")</code>. Temos a seguinte saída:

{% highlight javascript %}
> require("./exemplo.js")
Avaliando o exemplo.js
{ mensagem: 'Oi', falar: [Function] }
{% endhighlight %}

Nós vemos que o módulo foi importado com sucesso, e como foi dito a função <code>require</code> primeiro executou o script, imprimindo <code>Avaliando o exemplo.js</code> na primeira linha, e em seguida retornando o conteúdo do objeto <code>exports</code>, que contém apenas dois objetos <code>{ mensagem: 'Oi', falar: [Function] }</code>. A função <code>invisível</code>, como não foi atribuída ao <code>exports</code>, não foi importada.

Porém caso quisermos utilizar a função <code>falar</code> de nosso módulo veremos que ela não estará disponível, e a mensagem de erro dirá que <code>falar</code> não está definida neste contexto.

{% highlight javascript %}
> falar()
ReferenceError: falar is not defined
...
{% endhighlight %}

Para que seja possível utilizar as funções importadas de um módulo temos que importá-lo e atribuir o retorno à uma variável. Se rodarmos no `REPL` o código <code>var exemplo = require('./exemplo.js')</code> agora nosso código <code>exemplo.js</code> será avaliado e o objeto <code>exemplo</code> receberá <code>{ mensagem: 'Oi', falar: [Function] }</code>, assim podemos facilmente acessar sua função <code>falar</code>.

{% highlight javascript %}
> exemplo = require("./exemplo.js")
Avaliando o exemplo.js
{ mensagem: 'Oi', falar: [Function] }
> exemplo.falar()
Oi
undefined
{% endhighlight %}

Se você quiser atribuir uma função ou um novo objeto para <code>exports</code>, então você terá que usar o objeto <code>module.exports</code>. Veja o exemplo do código <code>exemplo2.js</code>.

{% highlight javascript %}
module.exports = function () {
  console.log("Ola mundo")
}

require('./exemplo2.js')()
{% endhighlight %}

Neste exemplo ao importar o módulo com <code>require</code> ele importa e chama a sí próprio e retorna a função atribuida ao <code>exports</code>. Ao importar o módulo temos a saída esperada:

{% highlight javascript %}
> require('./exemplo2.js')
hello world
[Function]
{% endhighlight %}

A última linha <code>[Function]</code> confirma que o valor de retorno da chamada <code>require('./exemplo2.js')</code> é uma função.

É importante dizer, também, que o código é avaliado apenas na primeira vez que ele é importado, se você importá-lo mais de uma vez a função <code>require</code> irá reutilziar o objeto <code>exports</code> que já está em salvo cache. Para ilustrar este ponto acompanhe o exemplo abaixo:

{% highlight javascript %}
> require("./exemplo.js")
Avaliando o exemplo.js
{ mensagem: 'Oi', falar: [Function] }
> require("./exemplo.js")
{ mensagem: 'Oi', falar: [Function] }
> require("./exemplo.js").mensagem = "Ola"
'Ola'
> require("./exemplo.js")
{ mensagem: 'Ola', falar: [Function] }
> require("./exemplo.js").falar()
Ola
undefined
{% endhighlight %}

Como pode ser visto no exemplo, <code>exemplo.js</code> é avaliado apenas na primeira vez e todas as chamadas posteriores à <code>require()</code> só invoca o módulo salvo em cache, em vez de ler o arquivo novamente. No exemplo é possível ver que isso pode causar efeitos colaterais caso utilizado indevidamente.

As regras que o <code>require</code> usa para buscar os arquivos pode ser um pouco complexa, mas uma regra de ouro é que se o arquivo <strong>não</strong> inicia com <code>./</code> ou <code>/</code>, então ele é considerado um módulo do core e o endereço local do Node é verificado, ou uma dependência na pasta local <code>node_modules</code>. Se o arquivo começa com <code>./</code> então ele é considerado um arquivo relativo com o arquivo que chamou o <code>require</code>. Se o arquivo começa com <code>/</code> então ele é considerado pertencente ao endereço absoluto.

Observação:
- Você pode omitir o <code>.js</code>, o <code>require</code> vai automáticamente adicioná-lo caso for necessário.
- Se o nome do arquivo passado para <code>require</code> é o nome de um diretório, a função vai primeiramente buscar por <code>package.json</code> no diretório e então carregar o arquivo referenciado na propriedade <code>main</code>. Caso contrário a função irá procurar por um arquivo chamado <code>index.js</code> dentro da pasta.

Para informações mais detalhadas veja a [sessão de módulos na documentação oficial do node][].


[CommonJS]: http://pt.wikipedia.org/wiki/CommonJS
[REPL do node]: http://nodebr.com/como-usar-o-repl-do-nodejs/
[sessão de módulos na documentação oficial do node]: http://nodejs.org/api/modules.html