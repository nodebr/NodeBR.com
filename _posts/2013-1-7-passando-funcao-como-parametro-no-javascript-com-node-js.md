---
layout: post
title : Passando função como parâmetro no JavaScript com Node.js
author: Rafael Henrique Moreira
tags : [conceito]
---
Antes de tentar entender o funcionamento do **Node.js** temos que compreender uma característica do **JavaScript** herdada da programação funcional. No **JavaScript** você pode, por exemplo, passar uma função como parâmetro. Vamos ver alguns exemplos práticos para ver como isso funciona na prática.

Podemos fazer alguma coisa como isso no JavaScript:

{% highlight javascript %}
function falar(palavra) {
  console.log(palavra);
}
function executar(funcao, valor) {
  funcao(valor);
}
executar(falar, "Oi JavaScript!");
{% endhighlight %}

Se executarmos nosso código vamos receber a saída impressa no console <code>Oi JavaScript!</code>. Você pode testar este exemplo utilizando seu prompt de comando. O Node.js é um ambiente de execução para usar o JavaScript fora do navegador também! Você pode acessar digitando o comando node no seu console e inicia o console interativo [REPL][], como no exemplo:

{% highlight javascript %}
> function falar(palavra) {
... console.log(palavra);
...}
undefined
> function executar(funcao, valor){
... funcao(valor);
... }
undefined
> executar(falar, "Oi JavaScript!");
Oi JavaScript!
undefined
{% endhighlight %}

E o nosso console imprimiu a saída esperada: <code>Oi JavaScript!</code>

Vamos entender o que está acontecendo neste contexto. O que fizemos aqui foi passar a função <code>falar</code> como o primeiro parâmetro da função <code>executar</code>. Isso é bem diferente de passar o valor retornado <code>return</code> pela função <code>falar</code>. A função <code>falar</code>, então, se tornou uma variável de nome <code>funcao</code> dentro da função <code>executar</code>, e <code>executar</code> pode chamar a função contida na variável adicionando os parênteses na variável: <code>funcao()</code>.

E o que são estes <code>undefined</code> no console? Para cada comando dado no console do Node na linha seguinte é impresso o valor retornado <code>return</code> deste comando. Vamos analisar com calma: o primeiro comando foi definir a função falar, após a definição retornou <code>undefined</code>, o segundo comando foi definir a função <code>executar</code>, que também retornou <code>undefined</code>, o terceiro comando que executou a função <code>executar</code>, que executou a função <code>falar</code> contida na variável funcao, que acabou por executar a função <code>console.log</code> que imprime no console uma <code>string</code>, mas a função <code>executar</code> em si não tem nenhum valor de retorno, então retornou <code>undefined</code>.

Nós podemos passar uma função como parâmetro para outra função usando seu nome, como foi visto no exemplo. Após entender este conceito veremos no exemplo a seguir que esta é a maneira indireta de se fazer isso, nós não precisamos definir a função para depois passar ela como parâmetro, nós podemos definir e passar a função como parâmetro para outra função no mesmo lugar.

    function executar(funcao, valor) {
      funcao(valor);
    }
    executar( function(palavra) { console.log(palavra)  }, "Oi JavaScript!");

Neste exemplo nós definimos a função que nós queremos passar para executar no lugar onde a função espera receber seu primeiro parâmetro. Aqui nós não precisamos nem dar um nome à função passada, por isso esta função é chamada de **função anônima**.

Com estes exemplos podemos observar que no **JavaScript** é possível passar uma função como parâmetro quando chamamos outra função, e podemos fazer isso atribuindo nossa função à uma variável que pode ser passada, ou simplesmente definindo nossa função bem onde estamos chamando a função que vai recebê-la como parâmetro, usando uma **função anônima**.

[REPL]: http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop