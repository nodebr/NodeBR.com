---
layout: post
title : Cuidado com a armadilha do escopo de variáveis em JavaScript
author: Onezino Gabriel Moreira
tags : [dica]
---
[JavaScript][] pode parecer sintaticamente parecido com linguagens c-like, mas seu comportamento com relação ao escopo das variáveis funciona bem diferente do que estamos acostumados.

Nesse post vou tentar explicar como o utiliza seu escopo.

O javascript trabalha com o conceito de contexto de execução, chamando normalmente de contexto. O contexto define o escopo das variáveis ou funções.

O contexto mais externo é conhecido como contexto global (global context) e é representado por um objeto. Ele é diferente para cada ambiente de execução do javascript. O global context de um browser é o objeto <code>window</code>, para o **node.js** ele é acessível através do <code>GLOBAL</code> ou <code>GLOBAL.window</code> ( forma mais parecida com a dos browser). Toda variável ou função global é então um atributo desse objeto. Quando um contexto é finalizado ele remove todos os objetos que pertencem a ele.

Cada função tem seu próprio contexto de execução. Quando uma função é chamada seu contexto é incluído na pilha de execução, e removido quando a função termina sua execução retornando ao contexto anterior ao da função.

### Pesquisa do indentificador

A cada execução de um contexto é criado uma “encadeamento de contexto” dos objetos na memória para permitir o acesso a todas as variáveis e funções que o contexto tem acesso. As declarações de funções e variáveis do contexto atual são os primeiros objetos que fazem parte dessa cadeia, os contextos encadeados começam dos mais próximos para os mais distantes na pilha de contextos.
A pesquisa pelo identificado percorre essa cadeia de contexto. A procura sempre ocorre do início da cadeia para o final até ele ser encontrado ou chegar ao ultimo contexto(global context).

### Trabalhando com <code>with</code> e <code>try catch</code>.

O uso dessas expressões criam uma camada a mais no encadeamento de contexto. Com o <code>with</code> é criado uma camada de objetos com todos os atributos do objeto que está se trabalhando, e quando o <code>catch</code> é executado um objeto é incluído dando acesso a exceção jogada.

{% highlight javascript %}
function() { with(location) { var url = href + “?run=1”; } return url; }
{% endhighlight %}

### Blocos sem escopo

Essa é com certeza a maior diferença entre javascript e as outras linguagens. No javascript blocos de código não criam um contexto próprio de execução permanecendo no contexto em que o o bloco foi executado.

{% highlight javascript %}
if(true) { var nome = “gabriel”; } alert(nome); // gabriel
{% endhighlight %}

ou ainda mais confuso

{% highlight javascript %}
for(i= 0;i<10;i++) { /*codigo */ } alert(i); // 10
{% endhighlight %}

Os programadores também tem que tomar cuidado quanto a declaração de variáveis. Se esquecer de utilizar o <code>var</code> a variável é incluída no contexto global e não no contexto de execução atual.

{% highlight javascript %}
function f() { var a = 10; b = 20; return a + b; }
alert(f()); //30
alert(b); //20
alert(typeof a); //undefined
{% endhighlight %}

Referências:
- [javascript - node.js global variables? - Stack Overflow][]
- [Professional Javascript for web developer][]

[JavaScript]: http://pt.wikipedia.org/wiki/JavaScript
[javascript - node.js global variables? - Stack Overflow]: http://stackoverflow.com/questions/5447771/node-js-global-variables
[Professional Javascript for web developer]: http://books.google.com.br/books?id=KW9G9rdlStIC&lpg=PP1&ots=4WVUNG_ZVw&dq=professional%20javascript%20web%20developer&hl=pt-BR&pg=PP1#v=onepage&q=professional%20javascript%20web%20developer&f=false
