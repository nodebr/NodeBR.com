---
layout: post
title : Com Node é fácil construir programas de rede escalonáveis
description: O objetivo do Node é fornecer uma maneira fácil de construir programas de rede escaláveis
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags : [conceito]
---
No exemplo do servidor web "Olá Mundo" apresentado abaixo, muitas conexões de clientes podem ser tratadas simultaneamente. Node diz ao sistema operacional (através de epoll, kqueue, /dev/poll ou select) que ele deve ser notificado quando uma nova conexão é feita, e então ele volta a dormir quando completa o processamento. Se alguém se conecta no servidor, então o node executa a função de retorno (callback function) programada e cada conexão é se torna apenas uma pequena alocação de heap.

{% highlight javascript %}
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Olá Mundo\n');
}).listen(1337, "127.0.0.1");
console.log('Servidor rodando em http://127.0.0.1:1337/');
{% endhighlight %}

Isto está em contraste com o modelo de concorrência mais comum utilizado hoje em dia, onde threads do Sistema Operacional são empregadas para tratar as conexões. Sistemas de rede baseados em thread são relativamente ineficiente e muito difíceis de usar. Para entender melhor leia os artigos (em Inglês): [The C10K problem][] e [Scalable Network Programming][]. Node vai apresentar muito mais eficiência na utilização da memória sob altas cargas do que sistemas que alocam pilhas de threads com 2mb para cada conexão. Além disso, os usuários do Node estão livres de preocupações com processos em [deadlock][] - não há bloqueios. Quase nenhuma função em Node executa diretamente E/S, então o processo nunca bloqueia. Porque nada bloqueia, programadores menos experientes são capazes de desenvolver sistemas eficiêntes.

Node é semelhante em design e foi influenciado por sistemas como a [Máquina de eventos][] do Ruby e o [Twisted][] do Python. Node leva o modelo de eventos um pouco mais a diante - ele apresenta um laço de eventos como controle de fluxo ([control flow][]) nativo, em vez de uma biblioteca. Em outros sistema sempre existe uma chamada bloqueante para iniciar o laço de eventos (event-loop), neles normalmente é definido o comprotamento através de callbacks no início do script e no final do script inicia-se o servidor através de uma chamada bloqueante como <code>EventMachine::run()</code>. No Node não há tal chamada para iniciar o laço de eventos, Node simplesmente entra no laço de eventos após executar o script de entrada e só sai do laço de eventos quando não há mais funções de retorno para serem executadas. Este comportamento é como acontece com o JavaScript do navegador - o laço de eventos é invisível ao usuário.

O protocolo HTTP é de primeira classe em Node. A biblioteca HTTP do Node nasceu das experiências do autor em desenvolvimento e trabalhando com servidores web. Por exemplo, o stream de dados para a maioria dos frameworks web é impossível. O Node tenta corrigir este problema com seu parser HTTP e sua API. Juntamente com a infra-estrutura puramente orientada a eventos de Node, ele faz uma boa base para bibliotecas web ou frameworks.

Mas o que ocorre com a concorrência em múltiplos processadores? Threads não são necessárias para escalonar programas para computadores multi-cores? No Node você pode iniciar um novo processo usando <code>[child_process.fork()][]</code> e este processo vai ser processado em paralelo e para o balanciamento de carga de novas conexões através de múltiplos processos é utilizado o [módulo cluster][].


[The C10K problem]: http://www.kegel.com/c10k.html
[Scalable Network Programming]: http://bulk.fefe.de/scalable-networking.pdf
[deadlock]: http://pt.wikipedia.org/wiki/Deadlock
[Máquina de eventos]: http://rubyeventmachine.com/
[Twisted]: http://twistedmatrix.com/
[control flow]: http://en.wikipedia.org/wiki/Control_flow
[child_process.fork()]: http://nodejs.org/api/child_process.html#child_process.fork
[módulo cluster]: http://nodejs.org/api/cluster.html