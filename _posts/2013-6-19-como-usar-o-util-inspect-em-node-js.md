---
layout: post
title: Como usar o util.inspect em NodeJS
description: Este artigo aborda como utilizar o método inspect do módulo util do core do NodeJS
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [básico]
reference: Este artigo foi baseado no artigo da <a href="http://docs.nodejitsu.com/">Documentação do NodeJitsu</a>.
---
O **NodeJS** fornece uma útil função para propósitos de debugação que retorna uma representação em texto de um objeto. `util.inspect` pode ser um salva-vidas ao se trabalhar com propriedades de objetos grandes e complexos.

O [módulo util][] do NodeJS contém uma série de métodos utilizados para formatar texto, debugar, imprimir mensagens no console, checar objetos (`util.isArray(objeto)`, `util.isRegExp(objeto)`, `util.isDate(objeto)` e `util.isError(objeto)`) e um método de herança (`util.inherits(construtor, superConstrutor)`). Neste artigo veremos o método `util.inspect`.

A seguir há um exemplo simples, `util.inspect` pode ser usado com qualquer objeto e uma boa demonstração pode ser usá-lo nos objetos embutidos do Node. Tente o código abaixo no REPL do Node. Caso você não conheça o REPL, ou não saiba como acessá-lo, eu te aconselho a ler nosso [artigo explicando como usar o REPL do Node][].

{% highlight javascript %}
> var util = require('util')
undefined
>  util.inspect(console)
'{ log: [Function],\n  info: [Function],\n  warn: [Function],\n  error: [Function],\n  dir: [Function],\n  time: [Function],\n  timeEnd: [Function],\n  trace: [Function],\n  assert: [Function] }'
{% endhighlight %}

Como você pode se recordar, a cada comando passada para o REPL ele imprime o retorno do comando. O que nos interessa aqui é o retorno do comando `util.inspect(console)` que pode ser visto na linha abaixo deste comando. Podemos ver que o comando nos retornou uma string contendo todas as funções enumeráveis deste módulo.

Então nós sabemos que a função `util.inspect` retorna uma string contendo as propriedades enumeráveis de qualquer objeto e também é válido acrescentar que a função `console.dir` é um envólucro em torno de `util.inspect`, utilizando seus argumentos padrões e imprimindo seu retorno na tela. Ao se imprimir o retorno do `util.inspect` utilizando a função `console.log` o console utilizará a formatação contida na string, como a quebra de linhas e a exibição de cores.

{% highlight javascript %}
> console.log(util.inspect(console))
{ log: [Function],
  info: [Function],
  warn: [Function],
  error: [Function],
  dir: [Function],
  time: [Function],
  timeEnd: [Function],
  trace: [Function],
  assert: [Function] }
undefined
{% endhighlight %}

`util.inspect` pode também ser passada com vários argumentos opcionais, apresentados aqui com seus valores padrões:

{% highlight javascript %}
util.inspect(object, showHidden=false, depth=2, colors=false, customInspect=true);
{% endhighlight %}

Por exemplo, `util.inspect(meuObjeto, true, 7, true, false)` poderia inspecionar `meuObjeto`, mostrando todos as propriedades ocultas e não ocultas até a profundidade 7, colorir a saída e a função `inspect()` definida no objeto não será chamada.

O argumento opcional `depth` (profundidade em inglês) é o número de níveis de profundidade de um objeto que será feita a recursão, seu valor padrão é 2. Isso é útil para objetos grandes e complicados. Definindo este valor como `null` vai forçar a recursão até atingir o último nível de profundidade. Para observar seu comportamento você pode inserir os comandos a seguir no REPL do Node.

{% highlight javascript %}
var http = require('http');
console.log(util.inspect(http, false, 1));
console.log(util.inspect(http, false, 2));
{% endhighlight %}

O argumento opicional `showHidden` é um booleano que determina se vai ou não exibir as propriedades não-enumeradas de um objeto, seu valor padrão é `false` o que tende a resultar em saídas muito mais legíveis. Isso não é algo que um iniciante deve se preocupar na maior parte do tempo, mas é válido demonstrar rapidamente. Você poderá perceber a diferença executando os comandos a seguir no REPL do Node.

{% highlight javascript %}
console.log(util.inspect(console, false, 1))
console.log(util.inspect(console, true, 1))
{% endhighlight %}

O argumento opicional `color` também é um valor booleano que diz se a função deve, ou não, decorar a string de saída com códigos de cores ANSI, as cores são customizáveis. Basicamente ela faz com que, na janela do terminal, a string de retorno possa ser impressa em cores. Faça o teste utilizando o comando a seguir.

{% highlight javascript %}
var util = require('util');
console.log(util.inspect(console, false, 2, true));
{% endhighlight %}



[módulo util]: http://nodejs.org/api/util.html
[artigo falando sobre o REPL do Node]: {{ site.url }}{% post_url 2013-5-23-como-usar-o-repl-do-nodejs %}