---
layout: post
title: Primeiros passos com Passport e Express em Node.js
description: Passport é um middleware para Node.js que faz a implementação de autenticação rápida e fácil.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [primeiros passos, módulos, express]
---
[Passport][] é um middleware para Node.js que faz a implementação de autenticação em um aplicativo de maneira rápida e fácil. Em aplicações web modernas podem-se ter várias formas de autenticação. Tradicionalmente usuários se logam fornecendo um usuário e uma senha. Com o crescimento das redes socieais, logar-se com um provedor [OAuth][] como o Facebook ou o Twitter tem se tornado métodos populares de autenticação.

Passport reconhece que cada aplicação tem requisítos únicos de autenticação. Mecanismos de autenticação, conhecidos como estratégias, são empacotados como módulos individuais. As aplicações podem escolher qual estratégia empregar sem criar dependências desnecessárias.

Sabendo que muitos usuários preferem se logar utilizando uma já conta existente em uma rede social, como o Facebook ou Twitter. Neste exemplo vai ser implementado o suporte para o usuário se logar com a sua conta do Twitter.


## Instalando as dependências

{% highlight console %}
$ npm install express
$ npm install passport
$ npm install passport-twitter
$ npm install connect-ensure-login
{% endhighlight %}

O exemplo consiste em uma aplicação [Express][] simples, baseada na [aplicação desenvolvida passo a passo][] do artigo anterior, então para melhor acompanhamento se certifique de ter compreendido os conceitos básicos já apresentados.

Passport tem uma arquitetura modular que quebra o mecanismo de autenticação em estratégias (neste caso do Twitter) que são distribuídas separadamente, mantendo o núcleo leve. Também será usado o [connect-ensure-login][] para proteger rotas exclusivas para usuários que logados.


## Checklist da autenticação

São necessários verificar três itens para implementar a autenticação.

- Configurar o middleware de sessão
- Configurar as estratégias de autenticação
- Adicionar as rotas de autenticação

Estes tópicos serão detalhados a seguir, começando por: rotas para o middleware.


## Rotas para autenticação

Vamos adicionar rotas para `/account`, que mostra para a pessoa detalhes da conta.

{% highlight javascript %}
app.get('/account',
  ensureLoggedIn('/login'),
  function(req, res) {
    res.send('<html><body>Ola '+ req.user.username+'.<br/><a href="/logout">Logout</a></body></html> ');
  });
{% endhighlight %}

Para acessar esta página o usuário terá que estar logado, `ensureLoggedIn` vai verificar isso, e caso ele não esteja logado, redireciona o usuário para a página de login. Que é implementada a seguir.

{% highlight javascript %}
app.get('/login',
  function(req, res) {
    res.send('<html><body><a href="/auth/twitter">Login com Twitter</a></body></html>');
  });
{% endhighlight %}

Simples assim. O usuário pode clicar no link e se logar com sua conta do Twitter. O que nos leva à próxima etapa da nossa implementação:


## Configurar a autenticação do Twitter

A autenticação do Twitter usa o padrão de autorização [OAuth], que significa que você vai precisar obter uma chave (`key`) e uma chave secreta (`secret`) do Twitter. Se você não tem uma ainda, você vai precisar [registrar sua aplicação no Twitter][]. Para nosso que nosso exemplo funcione em seu servidor local, você pode completar o campo `Website` com `http://127.0.0.1:3000/` e o campo `Callback URL` com `http://127.0.0.1:3000/auth/twitter/callback`.

Uma vez que você tenha as chaves, configure a estratégia de autenticação do Twitter assim:

{% highlight javascript %}
var TWITTER_CONSUMER_KEY = "INSIRA_SUA_KEY_AQUI";
var TWITTER_CONSUMER_SECRET = "INSIRA_SUA_SECRET_AQUI";
 
passport.use(new TwitterStrategy({
    consumerKey: TWITTER_CONSUMER_KEY,
    consumerSecret: TWITTER_CONSUMER_SECRET,
    callbackURL: "http://127.0.0.1:3000/auth/twitter/callback"
  },
  function(token, tokenSecret, profile, done) {
    // NOTA: Voce tera, provavelmente, que associar o usuario do Twitter
    //       com um registro do usuario no banco de dados da aplicacao.
    var user = profile;
    return done(null, user);
  }
));
{% endhighlight %}

Esta função fornecida para a estratégia é conhecida como "callback de verificação". Callbacks de verificação recebem credenciais como argumentos (neste caso o `token`, `tokenSecret` e `profile`), que são usados para localizar e retornar registros do usuário. A instància `user` retornada vai ser logada e e definida no `request` em `req.user`.

Na maioria das aplicações, você vai precisar associar a conta do Twitter com um registro do usuário no banco de dados da aplicação. Isso permite, também, você associar outras contas (como as do Facebook) no mesmo usuário, permitindo eles se logarem usando ambos os serviços. Para manter este exemplo simples vai ser utilizado os dados do `profile` diretamente, dispensando o uso de associações com o banco de dados.

O protocolo OAuth usado pelo Twitter envolve um processo de dois passos usando redirecionamentos para a troca e verificação dos tokens. Isto é bastante complicado, mas o middleware Passport faz esta tarefa fácil. Apenas adicione as seguintes rotas:

{% highlight javascript %}
app.get('/auth/twitter', passport.authenticate('twitter'));
app.get('/auth/twitter/callback', passport.authenticate('twitter', { successReturnToOrRedirect: '/account', failureRedirect: '/login' }));
{% endhighlight %}

A primeira rota vai começar a transação OAuth e redirecionar o usuário para o Twitter. Uma vez logado, o Twitter vai redirecionar o usuário de volta para nossa aplicação e Passaport vai trazê-lo de volta para a página original requisitada (ou `/`).

Fácil de mais, mas ainda há mais uma coisa a se fazer.


## Configurando as sessões

A fim de manter o controle do fato do usuário estar logado, uma aplicação precisa implementar suporte para sessões. Faça isso usando o cookie parser e o middleware de sessões embutidos no Express, e inicializando o Passport.

{% highlight javascript %}
app.use(express.static(__dirname + '/public'));
app.use(express.cookieParser());
app.use(express.session({ secret: 'usado para calcular o hash' }));
app.use(passport.initialize());
app.use(passport.session());
{% endhighlight %}

Quando o usuário se logar o registro do usuário é armazenado na sessão a fim de manter o estado de logado enquanto ele nevega em seu site. Funções de serialização e deserialização são fornecidas para controlar este processo.

{% highlight javascript %}
passport.serializeUser(function(user, done) {
  done(null, user);
});

passport.deserializeUser(function(obj, done) {
  done(null, obj);
});
{% endhighlight %}

Como foi notado acima, se você está criando registros do usuário em seu próprio banco de dados, você pode serializar somente o `ID` do usuário para minimizar a quantidade de dados armazenados na sessão. Para manter este exemplo simples, todo o registro do usuário foi serializado.


## Rodando a aplicação

Para rodar nossa aplicação basta savá-la em um arquivo, neste exemplo chamado `app.js` e executá-la passando o nome do arquivo como parâmetro para o node:

{% highlight console %}
$ node app.js
Seridor Express iniciado na porta 3000
{% endhighlight %}

E para confirmar que tudo está funcionando você pode acessar o sua aplicação através de seu navegador pelo endereço `http://localhost:3000`.


## Concluindo

Se você seguiu o passo-a-passo agora os usuários podem se logar usando suas contas do Twitter. Se você quiser se aprofundar mais nesta ferramenta consulte o [guia][] e acesse o [repositório][] para mais detalhes de como ela funciona. Agora se você precisa implementar uma API de autenticação, dê uma olhada nos projetos irmãos do Passport: [OAuthorize][] e [OAuth2orize][]


## O código completo da aplicação

Abaixo segue o código completo da aplicação, basta copiar e colar em um arquivo JavaScript, alterar as strings `INSIRA_SUA_KEY_AQUI` e `INSIRA_SUA_SECRET_AQUI` com os dados do seu registro no Twitter e tudo funcionará corretamente.

{% highlight javascript %}
var express = require('express'),
    passport = require('passport'),
    TwitterStrategy = require('passport-twitter').Strategy,
    ensureLoggedIn = require('connect-ensure-login').ensureLoggedIn,
    app = express();
 
app.use(express.static(__dirname + '/public'));
app.use(express.cookieParser());
app.use(express.session({ secret: 'usado para calcular o hash' }));
app.use(passport.initialize());
app.use(passport.session());
 
passport.serializeUser(function(user, done) {
  done(null, user);
});
 
passport.deserializeUser(function(obj, done) {
  done(null, obj);
});
 
 
var TWITTER_CONSUMER_KEY = "INSIRA_SUA_KEY_AQUI";
var TWITTER_CONSUMER_SECRET = "INSIRA_SUA_SECRET_AQUI";
 
passport.use(new TwitterStrategy({
    consumerKey: TWITTER_CONSUMER_KEY,
    consumerSecret: TWITTER_CONSUMER_SECRET,
    callbackURL: "http://127.0.0.1:3000/auth/twitter/callback"
  },
  function(token, tokenSecret, profile, done) {
    // NOTA: Voce tera, provavelmente, que associar o usuario do Twitter
    //       com um registro do usuario no seu banco de dados.
    var user = profile;
    return done(null, user);
  }
));
 
 
app.get('/', function(req, res){
    res.send('<html><body>Ola mundo<br/><a href="/login">Login</a></body></html>');
  });
 
app.get('/account',
  ensureLoggedIn('/login'),
  function(req, res) {
    res.send('<html><body>Ola '+ req.user.username+'.<br/><a href="/logout">Logout</a></body></html> ');
  });
 
app.get('/login',
  function(req, res) {
    res.send('<html><body><a href="/auth/twitter">Login com Twitter</a></body></html>');
  });
  
app.get('/logout',
  function(req, res) {
    req.logout();
    res.redirect('/');
  });
 
app.get('/auth/twitter', passport.authenticate('twitter'));
app.get('/auth/twitter/callback', passport.authenticate('twitter', { successReturnToOrRedirect: '/account', failureRedirect: '/login' }));
 
 
var server = app.listen(3000);
console.log('Seridor express iniciado na porta %s', server.address().port);
{% endhighlight %}


[Passport]: http://passportjs.org/
[OAuth]: http://oauth.net/
[Express]: http://expressjs.com/
[aplicação desenvolvida passo a passo]: {% post_url 2013-6-1-primeiros-passos-com-express-em-node-js %}
[connect-ensure-login]: https://github.com/jaredhanson/connect-ensure-login
[OAuth]: http://en.wikipedia.org/wiki/OAuth
[registrar sua aplicação no Twitter]: https://dev.twitter.com/apps
[guia]: http://passportjs.org/guide/
[repositório]: https://github.com/jaredhanson/passport
[OAuthorize]: https://github.com/jaredhanson/oauthorize
[OAuth2orize]: https://github.com/jaredhanson/oauth2orize