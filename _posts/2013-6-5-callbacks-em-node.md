---
layout: post
title: Callbacks em Node
description: Este artigo expõe conceitos básicos para se entender o Node do livro A arte do Node.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [conceito, básico]
reference: Este artigo foi extraído do livro Art of Node escrito por <a href="http://maxogden.com/">Max Ogden</a>, traduzido por <a href="http://kaiquewdev.nodester.com/">Kaique Silva</a> e revisada por <a href="http://rafadev.com/">mim</a>. Ele está disponível no <a href="https://github.com/fth-ship/art-of-node">repositório do github</a> sobre a licença <a href="http://creativecommons.org/licenses/by/2.0/">Creative Commons Attribution</a>.
---
É um dos tópicos mais importantes para se entender se você tem que entender como utilizar o node. Quase tudo no node usa callbacks. Eles não foram inventados para o node, somente tem um uso particular com funções do JavaScript.

Callbacks são funções que serão executadas de modo assincrono, ou posteriormente. Ao passo que o código for lido de cima para baixo de modo processual, programas assincronos possivelmente executam funções em tempos diferentes baseado na ordem e velocidade em que requisições http ou o trabalho de arquivamento do sistema acontecem.

A diferença pode ser confusa sendo determinada quando uma função é assincrona ou não depende da execução de um grande contexto. Seque um simples exemplo de código assincrono:

{% highlight javascript %}
var myNumber = 1
function addOne() { myNumber++ } // define a função 
addOne() // executa a função
console.log(myNumber) // mostra na saida padrão o numero 2
{% endhighlight %}

O código acima define uma função e na linha seguinte chama essa função, sem esperar por nada. Onde ela é chamada imediatamente adicionando 1 a váriavel `myNumber`, então depois que a função foi chamada você espera que o numero seja 2 na váriavel `myNumber`.

Supondo que precisamos armazenar nosso numero em um arquivo chamado `number.txt`:

{% highlight javascript %}
var fs = require('fs') // require é uma função especial do node para requisitar os módulos 
var myNumber = undefined // não sabemos ainda qual valor esta armazenado no arquivo 

function addOne() {
  fs.readFile('./number.txt', function doneReading(err, fileContents) {
    myNumber = parseInt(fileContents)
    myNumber++
  })
}

addOne()

console.log(myNumber) // mostra na saida padrão `undefined` 
{% endhighlight %}

Porque é que temos `undefined` quando pedimos para mostrar o numero dessa vez? Nesse código usamos o método `fs.readFile`, que acontece de módo assincrono. Usualmente você tem que se comunicar com os discos rigidos ou redes bem de modo assincrono. Se for somente para acesso a memória ou fazer algo na CPU isso é feito bem de modo sincrono. A razão pela qual fazer o I/O assincrono é porque o acesso ao disco é 100,000 vezes mais devagar do que comunicar-se com a memória (RAM).

Onde você executa um programa onde as funções são imediatamente definidas, mas não executa elas imediatamente. Este é um conceito fundamental para entender sobre a programação assincrona. Onde `addOne` é chamada fora de `fs.readFile` e move-se para a proxima tarefa pronta para ser executada e não esperar o disco responder igual a `fs.readFile`. Se não tem nada para executar o node espera por pendências de operações fs/network terminarem ou pararem de executar saindo da linha de comando.

Onde `fs.readFile` esta completa para ler o arquivo (talves isso demore millisegundos ou segundos e até mesmo minutos dependendo de quanto o disco é rápipdo) executando a função `doneReading` e dando um erro (claro que se algum tipo de erro acontecer) e o conteúdo do arquivo.

A razão pela qual `undefined` foi mostrado no código acima é que ele é chamado dentro de `console.log` e fora de `fs.readFile` mostrando o valor anterior de myNumber e não o conteúdo do arquivo.

Se você tem um pedaço de código que precisa ser executado várias e várias vezes ou um tempo depois, o primeiro passo é colocar esse pedaço de código dentro de uma função. Aonde você podera chamar sem precisar escrever ele em todas as partes que for necessário e nomeando da meneira que fique claro aquilo que esta sendo feito. Isso ajuda a dar nomes descritivos para as funções. 

Callback são somente funções que são executadas de forma tardia. A chave para a compreensão de callbacks é perceber que eles são utilizados quando você não sabe **quando** alguma operação assincrona estará completa, mas você sabe **quando** se completará - a ultima linha da função assincrona! A ordem de cima-para-baixo que você declara para callbacks isso não é necessariamnete importante, somente a ordem lógica/hierárquica de assentamento do código. Primeiro divida o código em funções e use callbacks para declarar se uma depende da outra função para encerrar.

O método `fs.readFile` é fornecido pelo node no módulo `fs`, é assincrono e acontece quando se tem um tempo curto para finalizar. Considerando o que ele faz: ele vai até o sistema operacional, que por sua vez esta rodando em um disco rígido isso pode ou não estar girando a milhares de rotações por minuto. Então ele tem que usar o lazer para ler os dados e enviar atráves das camadas de comunição do sistema de volta para o seu programa em javascript. Você da ao `fs.readFile` uma função (conhecida como callback) que sera chamada depois de recuperar os dados do sistema de arquivos. Ela coloca os dados recuperados em uma variavel do javascript e passa para sua função (callback) com essa variavél, neste caso a variavel se chama `fileContents` porque ela contém o conteúdo do arquivo que foi lido. 

Pense no restaurante do exemplo do tutorial lá do inicio. Em muitos restaurantes você pega um número e coloca em sua mesa e espera por sua comida. Eles são como os callbacks. Isso deixa claro para o servidor a quem chamar quando o cheeseburger estiver pronto.

Colocando `console.log` em uma função e passando ele para um callback.

{% highlight javascript %}
var fs = require('fs')
var myNumber = undefined

function addOne(callback) {
  fs.readFile('./number.txt', function doneReading(err, fileContents) {
    myNumber = parseInt(fileContents)
    myNumber++
    callback()
  }
}

function logMyNumber() {
  console.log(myNumber)
}

addOne(logMyNumber)
{% endhighlight %}

Agora a função `logMyNumber` que é passada como argumento se torna a variavél `callback` dentro da função `addOne`. Depois que `fs.readFile` terminar a variavél `callback` é chamada (`callback()`). Somente funções podem ser chamadas, então se você passar qualquer outra coisa diferente de uma uma função, causará um erro.

Onde uma função é chamada no javascript o código dentro dessa função é imediatamente executado. Neste caso nosso log é executado onde `callback` é atualmente a função `logMyNumber`. Lembre-se, somente se você *definiu* uma função não significa que ela será executada. Você tem que *chamar* uma função para ela acontecer.

Para quebrar esse exemplo em mais pedaços, aqui tem uma linha do tempo de eventos que acontecem quando o seu programa é executado:

- 1: o código é analisado, isso significa que se existir algum erro de sintaxe o pragrama quebrará e será apontado aonde isso aconteceu.
- 2: `addOne` será chamado, onde `logMyNumber` será passado com uma função chamada `callback`, que é o que precisa ser chamado quando `addOne` estiver completa. Imediatamante disparando o método assincrono `fs.readFile`. Essa parte do programa leva um tempo para terminar.
- 3: com nada para fazer, o node espera por um tempo até o `fs.readFile` encerrar a sua execução.
- 4: `fs.readFile` termina e chama o callback, `doneReading`, que incrementa o numero e imediatamente chama a função `logMyNumber` contida na variável `callback`.

Talves a parte mais confusa de se programar com callbacks é que as funções são somente objetos armazenados em variáveis e passadas em todo o programa com diferentes nomes. Dando nomes simples e descritivos para suas variáveis faz seu código ser mais legível para outros. Geralmente falando em programas no node onde você enxerga uma variável como `callback` ou `cb` você assume ela como uma função. 

Você talves tenha escutado alguns termos como `programação evencionada` ou `ciclo de eventos`. Onde é referenciado da mesma maneira que `fs.readFile` foi implementada. Node primeiramente despacha a operação `fs.readFile` e espera por `fs.readFile` enviar um evento para concluir. Equanto a resposta é esperada o node vai buscando checar outras coisas. Dentro do node há uma lista de coisas a serem feitas mas não informaram ainda, então o ciclo do node acaba e retorna para a lista várias vezes checando se o que estava sendo processado terminou. Depois do termino ele pega o que foi 'processado', ex. callbacks que dependem desse termino são chamados. 

Aquie temos uma versão de um pseudocódigo do exemplo acima:

{% highlight javascript %}
function addOne(thenRunThisFunction) {
  waitAMinute(function waitedAMinute() {
    thenRunThisFunction()
  })
}

addOne(function thisGetsRunAfterAddOneFinishes() {})
{% endhighlight %}
Imagine que tenha 3 funções assincronas `a`, `b` e `c`. Para cada uma leva-se 1 minuto de execução e depois de terminado elas chamam um callback (que é passado como primeiro argumento). Se você tem que falar para o node 'comece executando a, depois b depois que a terminar, e executar c então b termina' isso passa a ser:

{% highlight javascript %}
a(function() {
  b(function() {
    c()
  })
})
{% endhighlight %}

Onde esse código será executado, `a` é imediatamente executado, um minuto depois que ele teminar e chama `b`, outro minuto depois que ele teminar e chamar `c` e finalmente depois que 3 minutos se passaram o node para de executar desde que não tenha mais nada para fazer. Isso é definitivamente a maneira mais elegante para escrever o código acima, mas o ponto é esse, se você tem esse código que espera por uma porção de código assincrono terminar, onde é expressado essas dependencias colocando seu código em funções, isso é passado às outras funções como callbacks.

A projeção do node requer que você pense de modo não-linear. Considerando essa lista de operações:

{% highlight console %}
ler um arquivo
processar um arquivo
{% endhighlight %}

Se você ingênuamente transforma-se isso em um pseudocódigo você teria este resultado:

{% highlight javascript %}
var file = readFile()
processFile(file)
{% endhighlight %}

Esse tipo de linearidade no código (passo-a-passo, em ordem) não é o modo como o node trabalha. Se esse código fosse executado onde `readFile` e `processFile` estão sendo chamados ao mesmo tempo. Não faria o menor sentido porque `readFile` leva um tempo para completar sua execução. Ao passo que você precisa expressar `processFile` que depende do `readFile` completo. Esta a exata finalidade dos callbacks! E por causa da forma que o JavaScript trabalha você pode escrever dependencias de diferentes maneiras: 

{% highlight javascript %}
var fs = require('fs')
fs.readFile('movie.mp4', finishedReading)

function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
}
{% endhighlight %}

Mas você támbem pode estruturar o código dessa maneira que irá funcionar:

{% highlight javascript %}
var fs = require('fs')

function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
}

fs.readFile('movie.mp4', finishedReading)
{% endhighlight %}

Ou até mesmo assim:

{% highlight javascript %}
var fs = require('fs')

fs.readFile('movie.mp4', function finishedReading(error, movieData) {
  if (error) return console.error(error)
  // faça algo com os dados em movieData 
})
{% endhighlight %}
