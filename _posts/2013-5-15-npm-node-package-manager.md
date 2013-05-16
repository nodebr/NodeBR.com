---
layout: post
title : NPM, The Node Package Manager
author: Caio Ribeiro Pereira
tags : [npm, módulos]
---
Olá pessoal! Este é o meu primeiro post aqui na comunidade [NodeBR][], e o assunto hoje será sobre o NPM (Node Package Manager).

Apartir da versão Node.js 0.5.x que o NPM passou a ser integrado ao instalador do Node.js e isso simplificou a vida dos desenvolvedores, pois antes disso existia diversos gerenciadores de pacotes para o Node.js. 

Ele também mantém um repositório online [NPM][] que também é mantido pela [Joyent][], atualmente ele contém mais de 30 mil módulos open-source!

O objetivo desse post é apresentar os principais comandos para você gerenciar trabalhar com ele.

## Conhecendo os principais comandos

<code>npm install nome_do_módulo</code>: instala um módulo no projeto.
<code>npm install nome_do_módulo –save</code>: instala o módulo e adiciona-o na lista de dependências do <code>package.json</code> do projeto.
<code>npm list</code>: lista todos os módulos existentes no projeto.
<code>npm list -g</code>: lista todos os módulos globais.
<code>npm remove nome_do_módulo</code>: desinstala um módulo do projeto.
<code>npm update nome_do_módulo</code>: atualiza a versão do módulo.
<code>npm -v</code>: exibe a versão atual do npm.
<code>npm adduser nome_do_usuário</code>: cria um usuário no site [NPM][] para publicar seu módulo na internet.
<code>npm whoami</code>: exibe detalhes do seu perfil público do npm (é necessário criar um usuário com o comando anterior).
<code>npm publish</code>: publica o seu módulo, é necessário ter uma conta ativa no [NPM][].

[NodeBR]: http://nodebr.com
[Joyent]: http://joyent.com
[NPM]: https://npmjs.org