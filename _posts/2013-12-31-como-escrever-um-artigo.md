---
layout: post
title: Como escrever um artigo para contribuir com a comunidade
description: Descrição do post para ser visualizado pelas redes sociais e otimizado nos mecanismo de buscas.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [conceito, básico]
published: false
reference: Este artigo foi extraído do endereço <a href="http://site.com/">Site</a>.
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec sit amet nisi metus. 

# Lorem ipsum dolor sit amet

## Donec mauris orci, condimentum ac accumsan vel

Lorem ipsum dolor sit amet, <code>consectetur adipiscing elit</code>. Donec sit amet nisi metus.

###  Nullam ante augue, tempus vitae sagittis ac

Vivamus aliquam fringilla tellus, tempor vehicula lacus ultrices sed.

**Integer at laoreet nunc...**

- convallis elementum ipsum mi sed ligula
- In elementum mauris <code>euismod lacus varius non</code> pulvinar mauris porta
- Duis id urna in ipsum suscipit vulputate. Sed placerat risus vitae turpis posuere porta

## Sed porttitor congue dolor quis consequat.

Fusce pellentesque eros sit amet leo blandit vitae euismod leo mattis. Nullam placerat euismod eros id pellentesque.

{% highlight console %}
.
|-- convallis.yml
|-- elementum
|-- urna
|   |-- ipsum.html
|   |-- suscipit.html
|-- non
|   |-- non.markdown
|   |-- porta.markdown
{% endhighlight %}


- **\convallis.yml**  
  Fusce pellentesque

- **\non**  
  it amet leo blandit

{% highlight javascript %}
var net = require('net');

var server = net.createServer(function (socket) {
  socket.write('Echo server\r\n');
  socket.pipe(socket);
});

server.listen(1337, '127.0.0.1');
{% endhighlight %}
