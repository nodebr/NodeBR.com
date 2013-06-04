---
layout: post
title : NPM, The Node Package Manager
description: O objetivo desse post é apresentar os principais comandos para você gerenciar trabalhar com NPM.
author:
  name: Caio Ribeiro Pereira
  email: caio.ribeiro.pereira@gmail.com
  url: http://www.crpwebdev.com/
  github: caio-ribeiro-pereira
  twitter: crp_underground
tags : [npm, módulos]
---
Olá pessoal! Este é o meu primeiro post aqui na comunidade [NodeBR][], e o assunto hoje será sobre o NPM (Node Package Manager).

Apartir da versão Node.js 0.5.x que o NPM passou a ser integrado ao instalador do Node.js e isso simplificou a vida dos desenvolvedores, pois antes disso existia diversos gerenciadores de pacotes para o Node.js. 

Ele também mantém um repositório online [NPM][] que também é mantido pela [Joyent][], atualmente ele contém mais de 30 mil módulos open-source!

O objetivo desse post é apresentar os principais comandos para você gerenciar trabalhar com ele.

## Conhecendo os principais comandos

- `npm install nome_do_módulo` - instala um módulo no projeto.
- `npm install nome_do_módulo –save` - instala o módulo e adiciona-o na lista de dependências do <code>package.json</code> do projeto.
- `npm list` - lista todos os módulos existentes no projeto.
- `npm list -g` - lista todos os módulos globais.
- `npm remove nome_do_módulo` - desinstala um módulo do projeto.
- `npm update nome_do_módulo` - atualiza a versão do módulo.
- `npm -v` - exibe a versão atual do npm.
- `npm adduser nome_do_usuário` - cria um usuário no site [NPM][] para publicar seu módulo na internet.
- `npm whoami` - exibe detalhes do seu perfil público do npm (é necessário criar um usuário com o comando anterior).
- `npm publish` - publica o seu módulo, é necessário ter uma conta ativa no [NPM][].

[NodeBR]: http://nodebr.com
[Joyent]: http://joyent.com
[NPM]: https://npmjs.org
