# NodeBR

Este é um blog comunitário brasileiro sobre [node.js][], seu propósito é ensinar como desenvolver soluções em node assim como ensinar os conceitos fundamentais necessários para escrever um código eficiente.

Este site é gerado automáticamente pela engine [jekyll][], utiliza o [twitter bootstrap][] como framework de front-end e é hospedado gratúitamente pelo [github pages][].

Todo o conteúdo deste site é armazenado neste [repositório git][] onde qualquer pessoa pode dar um fork, escrever um artigo e enviar uma requisição de pull. Se o artigo passar nos padrões de qualidade ele será publicado e ajudará a crescente comunidade brasileira de node.

O jekyll é um escrito em ruby, para rodar uma versão local do blog você precisa [instalar o jekyll][] e suas dependências. Ele foi feito para rodar no Linux, Unix ou Mac OS X mas você pode [rodar o jekyll no Windows][] também. Este blog utiliza o [pygments][] para destacar os códigos, então você terá que instalá-lo também.

Ou você pode simplesmente dar um fork neste repositório e hospedar gratúitamente a sua versão do blog no próprio github através do [github pages][].

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
    
        .
        |-- convallis.yml
        |-- elementum
        |-- urna
        |   |-- ipsum.html
        |   |-- suscipit.html
        |-- non
        |   |-- non.markdown
        |   |-- porta.markdown


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

Caso você quera fazer alguma retificação em algum post, ou até mesmo acrescentar informações relevantes, você pode dar fork no projeto e submeter uma requisição de pull com sua modificação, ou se preferir apenas adicionar nos comentários a sua opinião sobre o post.

## Licença

Este blog foi criado por Rafael Henrique Moreira sob a licença MIT. O conteúdo dos artigos é de direito autoral de seus respectivos autores.

[node.js]: http://nodejs.org/
[jekyll]: http://jekyllrb.com/
[twitter bootstrap]: http://twitter.github.io/bootstrap
[github pages]: http://pages.github.com/
[repositório git]: http://github.com/nodebr/NodeBR.com/
[instalar o jekyll]: http://jekyllrb.com/docs/installation/
[rodar o jekyll no Windows]: http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html
[pygments]: http://pygments.org/


