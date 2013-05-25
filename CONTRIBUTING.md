## Contribuindo

A melhor maneira de contribuir é dar um fork neste repositório, adicionar seu artigo e submeter um requisição de pull.

O seu artigo precisa estar dentro da pasta _posts e o nome do arquivo deve seguir o padrão YYYY-MM-DD-nome-do-artigo-sem-acentos.md, exemplo:

2013-01-07-como-escrever-um-artigo.md

### Formato do artigo

Todo artigo é um arquivo markdown com alguns meta-dados no topo, seguindo o padrão abaixo.

    ---
    layout: post
    title : Como escrever um artigo para contribuir com a comunidade
    author: Rafael Henrique Moreira
    tags : [tutorial, iniciante]
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

Note que é utilizado tags <code>{% highlight javascript %}</code> e <code>{% endhighlight %}</code> do pygments para destacar o código. O pygments tem uma grande [lista de linguagens suportadas][].

Caso você quera fazer alguma retificação em algum post, ou até mesmo acrescentar informações relevantes, você pode dar fork no projeto e submeter uma requisição de pull com sua modificação, ou se preferir apenas adicionar nos comentários a sua opinião sobre o post.
