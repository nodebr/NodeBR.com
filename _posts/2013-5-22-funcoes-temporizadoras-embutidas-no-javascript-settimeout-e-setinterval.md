---
layout: post
title : Funções temporizadoras embutidas no JavaScript? setTimeout e setInterval
description: Este post vai cobrir as funções setTimeout e setInterval chamadas funções temporizadoras embutidas (built-in timer functions) do JavaScript
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags : [funções]
---
Em JavaScript existem duas funções temporizadoras embutidas (built-in timer functions), <code>setTimeout</code> e <code>setInterval</code> que podem ser usadas para chamar funções de retorno (callback functions) após um determinado tempo. Veja um exemplo de uso abaixo:

{% highlight javascript %}
setTimeout(function() { console.log("setTimeout: Ja passou 1 segundo!"); }, 1000);
setInterval(function() { console.log("setInterval: Ja passou 1 segundo!"); }, 1000);
{% endhighlight %}

Este código imprime a seguinte saída:

{% highlight console %}
setTimeout: Ja passou 1 segundo!
setInterval: Ja passou 1 segundo!
setInterval: Ja passou 1 segundo!
setInterval: Ja passou 1 segundo!
setInterval: Ja passou 1 segundo!
...
{% endhighlight %}

Você pode ver que os parâmetros são os mesmos para as duas funções. O segundo parâmetro é um número que diz quanto tempo de espera, em milisegundos, vai passar antes de chamar a função passada como primeiro parâmetro. A diferença entre as duas funções é que <code>setTimeout</code> chama a função passada apenas uma vez, enquanto <code>setInterval</code> vai chamar a função passada indefinidamente sempre no intervalo de tempo passado.

Você vai poder perceber que o laço de eventos(event loop) do Node vai sempre tentar chamar sua função no tempo pré-determinado por você, porém ele nem sempre vai acertar com precisão milimétrica, pois como o Node roda em apenas uma thread e seu contexto pode estar processando outra função no momento que ele deveria chamar sua função e ele vai precisar processar esta função até o final antes de chamar a próxima função do laço de eventos (event loop). Por isso é muito importante que você sempre escreva funções não bloqueantes para sua aplicação em Node, assim você estará aproveitando todas as vantagens do Node.

Você deve ficar atento com a função <code>setInterval</code> porque ela pode causar alguns efeitos indesejados. Se, por exemplo, você precisar se certificar que seu servidor está rodando pingando ele todo segundo, você pode pensar em fazer algo assim:

{% highlight javascript %}
var recursiva = function () {
    console.log("Se passaram 1 segundo!");
    setTimeout(recursiva,1000);
}
recursiva();
{% endhighlight %}

Como você pode ver este código chama a função <code>recursiva</code> que, quando processada, faz uma chamada para <code>setTimeout(recursiva,1000);</code> que chama <code>recursiva</code> de novo após 1 segundo, tendo assim quase o mesmo efeito de <code>setInterval</code> enquanto permanece mais resistente à erros não intencionais que podem ocorrer.

Você também pode limpar os temporizadores com <code>clearTimeout</code> e <code>clearInterval</code>. O uso destas funções é bem simples:

{% highlight javascript %}
function nunca_chamada () {
  console.log("Voce nao deve executar esta funcao!");
}

var timeout1 = setTimeout(nunca_chamada,1000);
var interval1 = setInterval(nunca_chamada,1000);

clearTimeout(timeout1);
clearInterval(interval1);
{% endhighlight %}

Como pode ser visto no código, se você guardar os valores de retorno dos temporizadores, você poderá facilmente desativá-los usando <code>clearTimeout</code> e <code>clearInterval</code>. O código assima não imprime nada no console pois os temporizadores são limpos antes que possam executar a função de retorno.

O segredo final para as funções temporizadoras é que você pode passar parâmetros para a função de retorno (callback function) que será chamada apenas adicionando mais parâmetros para <code>setTiemout</code> e <code>setInterval</code>:

{% highlight javascript %}
setTimeout(console.log, 1000, "Esta funcao", "tem", 4, "parametros");
setInterval(console.log, 1000, "Esta funcao so tem um parametro");
{% endhighlight %}

O código acima imprime o seguinte retorno no console:

{% highlight shell-session %}
Esta funcao tem 4 parametros
Esta funcao so tem um parametro
Esta funcao so tem um parametro
Esta funcao so tem um parametro
Esta funcao so tem um parametro
...
{% endhighlight %}

Com estas funções temporizadores do JavaScript você pode facilmente executar tarefas de em segundo plano (background tasks) no seu servidor Node e o laço de eventos do Node vai cuidar de tudo muito bem.
