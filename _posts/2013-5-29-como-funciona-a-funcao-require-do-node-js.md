---
layout: post
title: Como funciona a função require do Node.js
description: Seguindo a especificação CommonJS para padronização de modulos em JavaScript, a função require é fundamental para o funcionamento correto de módulos em Node.js
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [primeiros passos, módulos]
---
O Node.js segue a [CommonJS][], uma especificação de ecossistemas para o JavaScript, e a função embutida <code>require</code> é a maneira mais fácil de incluir módulos existentes em arquivos separados. O funcionamento básico do <code>require</code> é que ele lê o arquivo JavaScript, interpreta o script e em seguida retorna o conteúdo do objeto <code>exports</code>. Segue um exemplo de módulo para melhor compreensão:

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

Podemos testar nosso módulo rodando através do [REPL do node][]. Se formos na pasta onde salvamos nosso projeto <code>exemplo.js</code> com o prompt e abrirmos o <code>REPL</code> do node, para isso basta digitar <code>node</code> sem passar nenhum arquivo como parâmetro e ele abrirá em modo <code>REPL</code>, em seguida basta digitar <code>require("./exemplo.js")</code> para importar nosso módulo. Teremos a seguinte saída impressa:

{% highlight javascript %}
> require("./exemplo.js")
Avaliando o exemplo.js
{ mensagem: 'Oi', falar: [Function] }
{% endhighlight %}

Aqui é possível ver que o módulo foi importado com sucesso, e como foi dito a função <code>require</code> primeiro interpretou o script, imprimindo <code>Avaliando o exemplo.js</code> na primeira linha, e em seguida retornando o conteúdo do objeto <code>exports</code>, que contém apenas dois objetos <code>{ mensagem: 'Oi', falar: [Function] }</code>. A função <code>invisivel</code> não foi importada pois não foi atribuída ao objeto <code>exports</code>.

Porém caso quisermos utilizar a função <code>falar</code> de nosso módulo veremos que ela não estará disponível no contexto global, e a mensagem de erro dirá que <code>falar</code> não está definida.

{% highlight javascript %}
> falar()
ReferenceError: falar is not defined
...
{% endhighlight %}

Para que seja possível utilizar as funções importadas de um módulo temos que importá-lo e atribuir o retorno da função <code>require</code> à uma variável. Se rodarmos no `REPL` o código <code>var exemplo = require('./exemplo.js')</code> ocorrerá que agora nosso script <code>exemplo.js</code> será avaliado e o objeto <code>exemplo</code> receberá <code>{ mensagem: 'Oi', falar: [Function] }</code>, que é o retorno da função, assim é possível facilmente acessar sua função <code>falar</code>. Acompanhe o exemplo:

{% highlight javascript %}
> exemplo = require("./exemplo.js")
Avaliando o exemplo.js
{ mensagem: 'Oi', falar: [Function] }
> exemplo.falar()
Oi
undefined
{% endhighlight %}

Pode ser que em seu console não apareça a mensagem <code>Avaliando o exemplo.js</code> novamente, você seberá o motivo mais adiante.

Se você quiser atribuir uma função ou um novo objeto para <code>exports</code>, então você terá que usar o objeto <code>module.exports</code>. Veja o exemplo do código <code>exemplo2.js</code> para compreender melhor.

{% highlight javascript %}
module.exports = function () {
  console.log("Ola mundo")
}

require('./exemplo2.js')()
{% endhighlight %}

Neste exemplo ao importar o módulo com <code>require</code>, primeiramente ele interpreta (avalia, executa) o código o que faz o módulo importar e chamar a sí próprio e ao final <code>require</code> retorna a função atribuida ao <code>exports</code>. Ao importar o módulo temos a saída esperada:

{% highlight javascript %}
> require('./exemplo2.js')
Ola mundo
[Function]
{% endhighlight %}

A última linha <code>[Function]</code> confirma que o valor de retorno da chamada <code>require('./exemplo2.js')</code> é uma função e a linha <code>Ola mundo</code> confirma que o código foi interpretado antes de ser retornada esta função.

É importante dizer, também, que o código é interpretado apenas na primeira vez que ele é importado, se você importá-lo mais de uma vez a função <code>require</code> irá reutilziar o objeto <code>exports</code> que já está em salvo cache. Para ilustrar este ponto acompanhe o exemplo abaixo:

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

Como pode ser visto no exemplo, <code>exemplo.js</code> é interpretado apenas na primeira vez e todas as chamadas posteriores à <code>require()</code> só invoca o módulo salvo em cache, em vez de ler o arquivo novamente. No exemplo é possível ver que isso pode causar efeitos colaterais caso utilizado indevidamente.

As regras que o <code>require</code> usa para buscar os arquivos pode ser um pouco complexa, mas uma regra de ouro é que se o arquivo <strong>não</strong> inicia com <code>./</code> ou <code>/</code>, então ele é considerado um módulo do core e o endereço local do Node é verificado, ou uma dependência na pasta local <code>node_modules</code>. Se o arquivo começa com <code>./</code> então ele é considerado um arquivo relativo com o arquivo que chamou o <code>require</code>. Se o arquivo começa com <code>/</code> então ele é considerado pertencente ao endereço absoluto.

Observação:
- Você pode omitir o <code>.js</code>, o <code>require</code> vai automáticamente adicioná-lo caso for necessário.
- Se o nome do arquivo passado para <code>require</code> é o nome de um diretório, a função vai primeiramente buscar por <code>package.json</code> no diretório e então carregar o arquivo referenciado na propriedade <code>main</code>. Caso contrário a função irá procurar por um arquivo chamado <code>index.js</code> dentro da pasta.

Para informações mais detalhadas veja o [artigo detalhado sobre módulos em Node.js][].


[CommonJS]: http://pt.wikipedia.org/wiki/CommonJS
[REPL do node]: http://nodebr.com/como-usar-o-repl-do-nodejs/
[artigo detalhado sobre módulos em Node.js]: {{ site.url }}{% post_url 2013-7-29-modulos-em-nodejs %}