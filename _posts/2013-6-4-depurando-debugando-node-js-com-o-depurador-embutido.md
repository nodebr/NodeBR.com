---
layout: post
title: Depurando (Debugando) Node.js com o Depurador embutido.
description: O motor v8 vem com um extenso depurador acessível via protocolo TCP. O Node.js tem um cliente embutido para este depurador, este é o depurador do Node.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [intermediário, depurador]
---
Se a versão web do seu aplicativo está funcionando, porém a versão de linha de comando está dando erro. O que está acontecendo de errado? Pode ser difícil de descobrir. Se a aplicação simplesmente terminou sem imprimir nada. Seus `try`/`catch` e manipuladores de evento não identificaram nenhum erro e a típica abordagem por `console.log()` não imprime nada. Então o melhor a se fazer é usar o **[depurador][]** do Node.js para fazer a [depuração][] do seu código. Uns até falam **debugar** o código, debugar é uma palavra derivada da palavra em Inglês **debugging** (ou simplesmente **debug**).

O motor V8 da Google, onde o Node roda, vem com um extenso depurador acessível via protocolo TCP e o Node.js tem um cliente embutido para este depurador. Este é considerado o depurador (cliente de depuração) do Node. Este artigo ensina como usar o depurador do Node para depurar seu script expondo como colocar pontos de interrupção ([breakpoints][]), rodar o [REPL][] do Node no meio da depuração, percorrer pelo código e analizar as saídas do depurador.


## O básico

- O motor V8 da Google tem um depurador embutido e o Node tem um cliente para acessar este depurador
- O depurador pode ser iniciado usando executando o node com o argumento `debug`
- Na sua aplicação você pode definir pontos de interrupção usando a palavra reservada `debugger`

Agora vamos a cada etapa da debugação.

## Inserindo alguns pontos de interrupção

Um ponto de interrupção (breakpoint) é um lugar em seu codigo onde você deseja que sua aplicação pare momentaneamente para que você possa analisar o estado do seu programa naquele ponto do código. Se você não usar um ponto de interrupção você vai ter percorrer o código passo-a-passo até chegar onde você deseja depurar.

Então, basicamente, seu script você vai precisar colocar alguns pontos de interrupção em lugares próximos onde você acha que o seu sistema esteja se comportando de maneira indevida. Acompanhe o exemplo `depurar.js` apresentado abaixo.

{% highlight javascript %}
x = 5;
setTimeout(function () {
  debugger; // Ponto de interrupcao
  console.log("mundo");
}, 1000);
console.log("Ola");
{% endhighlight %}

O ponto de interrupção é adicionado no código usando a palavra reservada `debugger`. Este é um acessório do motor V8, não necessariamente do Node.js.

Você pode setar muitos pontos de interrupção no seu código se você precisar.

## Iniciando o depurador

Para iniciar o depurador, rode seu script usando o argumento `debug` do node, no nosso exemplo: `node debug depurar.js`. Você vai ser levado ao [REPL][] do depurador `debug >` e então você vai poder usar o comando `help` para ver a lista de comandos disponíveis e começar e debugar seu código. Quando você abre o depurador seu inicia pausado na primeira linha e você precisa usar o comando `cont` para rodar seu código até ser interrompido quando o depurador encontrar a palavra reservada `debugger` ou você usar o comando `pause`.

{% highlight javascript %}
$ node debug depurar.js
< debugger listening on port 5858
connecting... ok
break in /home/rafadev/git/depurador/depurar.js:1
  1 x = 5;
  2 setTimeout(function () {
  3   debugger;
debug> cont
< Ola
break in /home/rafadev/git/depurador/depurar.js:3
  1 x = 5;
  2 setTimeout(function () {
  3   debugger;
  4   console.log("mundo");
  5 }, 1000);
debug> next
break in /home/rafadev/git/depurador/depurar.js:4
  2 setTimeout(function () {
  3   debugger;
  4   console.log("mundo");
  5 }, 1000);
  6 console.log("Ola");
debug> repl
Press Ctrl + C to leave debug repl
> x
5
> 2+2
4
debug> next
< mundo
break in /home/rafadev/git/depurador/depurar.js:5
  3   debugger;
  4   console.log("mundo");
  5 }, 1000);
  6 console.log("Ola");
  7
debug> quit
{% endhighlight %}

O comando `next` avalia a próxima linha do seu código e o comando `repl` permite você avaliar o estado atual do seu programa remotamente através do REPL do Node. Há também outros comandos disponíveis, lembrando que você pode digitar `help` para lembrar dos comandos disponíveis.


## Listagem de código e gerando um rastreamento

A primeira coisa que você possívelmente vai fazer é se orientar. Onde, exatamente, nós estamos no código? Para fazer isso use a função `list`. Esta função imprime um segmento formatado do código de onde você está. Esta função recebe como parâmetro um número dizendo quantas linhas de contexto serão impessas. Exemplo de uso: `list(2)` vai imprimir onde você está mais as 2 linhas anteriores e as 2 posteriores.

{% highlight javascript %}
debug> list(2)
  3
  4 setTimeout(function () {
  5   debugger;
  6   console.log("mundo");
  7 }, 1000);
{% endhighlight %}

Aqui nós podemos ver o contexto da linha corrente que o programa se encontra, então podemos saber aonde o programa encontra-se pausado. O próximo comando a ser avaliado estará marcado com a cor verde, neste exemplo a palavra `debugger` está em verde.


## Vigilantes (Watchers)

Você pode vigiar o valor de uma expressão ou de uma variável enquanto depura seu código. Em cada ponto de interrupção os vigilantes serão avaliados no contexto atual e seus valores serão impressos logo depois da origem da interrupção.

Para começar a vigiar uma expressão, insira `watch("sua_expressao")`. O comando `watchers` imprime os vigilantes ativos. Para deixar de vigiar uma expressão, insira `unwatch("sua_expressao")`.


## Movimentação com `next`, `step` e `out`

O comando `next`, como já foi dito, prossegue a execução do programa avaliando a linha a atual e pausando na primeira expressão da próxima linha. Caso a linha a ser avaliada faça referência a algum método de outro módulo, ou até mesmo do core, você também pode depurar o funcionamento interno deste método, basta utilizar a função `step` que é o abreviamento para "step into this one" que pode ser traduzido para "entrar neste" que basicamente entra para depurar o método internamente.

Quando você está depurando internamente o funcionamento deste método, você pode ir utilizando os comandos `next` e `step` para rastrear com detalhes o que está ocorrendo "por trás dos panos" depurando até o próprio core do Node, caso você deseje. Já o comando `out` serve para você voltar para o código que chamou aquele método e pausar para a próxima expressão.


## Lista de Comandos

### Movimentação

- cont, c - Continuar a execução
- next, n - Próximo passo
- step, s - Entrar
- out, o - Sair
- pause - Pausar a execução de um código (como o botão de pausar nas ferramentas de desenvolvimento)

### Pontos de interrupção

- setBreakpoint(), sb() - Setar um ponto de interrupção na linha corrente
- setBreakpoint(line), sb(line) - Setar um ponto de interrupção em uma linha específica
- setBreakpoint('fn()'), sb(...) - Setar um ponto de interrupção na primeira instrução no corpo da função
- setBreakpoint('script.js', 1), sb(...) - Setar um ponto de interrupção na primeira linha do script.js
- clearBreakpoint, cb(...) - Remover um ponto de interrupção

Também é possível inserir um ponto de interrupção em um arquivo (módulo) que ainda não foi carregado, por exemplo usando o comando `setBreakpoint('mod.js', 23)` adiciona um ponto de interrupção no módulo `mod.js` na linha `23`.

{% highlight javascript %}
$ node debug main.js
< debugger listening on port 5858
connecting to port 5858... ok
break in /home/rafadev/git/depurador/main.js:1
  1 var mod = require('./mod.js');
  2 mod.hello();
  3 mod.hello();
debug> setBreakpoint('mod.js', 23)
Warning: script 'mod.js' was not loaded yet.
  1 var mod = require('./mod.js');
  2 mod.hello();
  3 mod.hello();
debug> c
break in /home/rafadev/git/depurador/mod.js:23
 21
 22 exports.hello = function() {
 23   return 'hello from module';
 24 };
 25
debug>
{% endhighlight %}

### Informação

- backtrace, bt - Imprime um registro de chamadas do quadro atual de execução
- list(5) - Lista o código fonte do script com 5 linhas de contexto (5 linhas antes e 5 depois)
- watch(expr) - Adiciona uma expressão para a lista vigiada
- unwatch(expr) - Remove uma expressão da lista vigiada
- watchers - Lista todos os vigiados e seus valores (atumaticamente listado em cada ponto de interrupção)
- repl - Abre o REPL de debugação para avaliação no contexto de depuração do script

### Controle de execução

- run - Executa o script (é automaticamente executado quando o depurador é iniciado)
- restart - Reinicia um script
- kill - Mata um script

### Diversos

- scripts - Lista todos os scripts carregados
- version - Imprime a versão da engine V8


## Vantagens de uso

O depurador da V8 pode ser abilitado e acessado tanto iniciando o Node com o sinalizador (flag) de linha de comando `--debug` como sinalizando um processo existente em Node com `SIGUSR1`.

Uma vez que o processo foi colocado em modo de depuração dessa forma ele pode ser conectado pelo depurador do node. Tanto conectado pelo `pid` quanto pelo URI para o depurador. A sintaxe é:

- `node debug -p <pid>` - Conecta com o processo via `pid`

- `node debug` - conecta com o processo via URI tal como `localhost:5858`


[depurador]: http://pt.wikipedia.org/wiki/Depurador
[depuração]: http://pt.wikipedia.org/wiki/Depura%C3%A7%C3%A3o
[breakpoints]: http://en.wikipedia.org/wiki/Breakpoint
[REPL]: http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
