---
layout: post
title : Exemplo Hello World em Node.js
author: Rafael Henrique Moreira
tags : [primeiros passos, exemplo, básico]
---
Este é um programa rápido para garantir que tudo está instalado e funcionando corretamente:

{% highlight javascript %}
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello Node.JS!');
}).listen(8080);
console.log('Server running at http://localhost:8080/');
{% endhighlight %}

Rode o código com o comando de linha do Node em seu prompt de comando:

    > node hello_node.js
    Server running at http://localhost:8080/

Agora quando você acessar o endereço <code>http://localhost:8080/</code> com seu navegador você ler um caloroso <code>Hello Node.JS</code>!

Parabéns você instalou o **Node.js** com sucesso e está pronto para mergulhar nos nós assíncronos do **Node**.