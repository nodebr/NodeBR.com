---
layout: post
title : Instalando o Node.js através do gerenciador de pacotes
description: Este post é um guia prático para a instalação do Node.js através do gerenciador de pacotes.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags : [primeiros passos, básico]
---
Este post é um guia prático para a **instalação do Node.js** através do gerenciador de pacotes.

## Debian

Para o **Debian Squeeze**, sua melhor aposta é em compilar o **Node** você mesmo (como root):

{% highlight console %}
apt-get install make python g++
mkdir ~/nodejs && cd $_
wget -N http://nodejs.org/dist/node-latest.tar.gz
tar xzvf node-latest.tar.gz && cd `ls -rd node-v*`
./configure
make install
{% endhighlight %}

## Ubuntu

Para instalar o Node no **Ubuntu**, digite as seguintes linhas de comando para instalar a versão estável atual do Node na versão estável atual do Ubuntu:

{% highlight console %}
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:chris-lea/node.js
sudo apt-get update
sudo apt-get install nodejs npm
{% endhighlight %}


Caso você precise compilar os módulos Node C++:

{% highlight console %}
sudo apt-get install nodejs-dev
{% endhighlight %}


## openSUSE

Primeiro adicione o repositório:

{% highlight console %}
sudo zypper ar http://download.opensuse.org/repositories/devel:/languages:/nodejs/openSUSE_11.4/ NodeJSBuildService
{% endhighlight %}

Então instale os pacotes:

{% highlight console %}
sudo zypper in nodejs nodejs-devel
{% endhighlight %}

Atente para a versão do seu openSUSE, caso queira você pode ver as [versões disponíveis no repositório do openSUSE][]. Você pode verificar qual sua versão do openSUSE com o comando:

{% highlight console %}
more /etc/SuSE-release
SuSE Linux 9.1 (i586)
VERSION = 9.1
{% endhighlight %}

## Amazon Linux

Primeiro instale o repositório:

{% highlight console %}
sudo yum localinstall --nogpgcheck http://nodejs.tchol.org/repocfg/amzn1/nodejs-stable-release.noarch.rpm 
{% endhighlight %}

Então instale os pacotes:

{% highlight console %}
sudo yum install nodejs-compat-symlinks npm
{% endhighlight %}

## Windows

Para usuários **Windows**, você pode simplesmente fazer o [download do instalador do node.js][] e seguir os passos para a instalação.

Você também pode utilizar o [chocolatey][] para instalar Node no Windows usando o comando:

{% highlight console %}
cinst nodejs
{% endhighlight %}

Ou para fazer uma instalação completa com o gerenciador de pacotes [NPM][] do Node:

{% highlight console %}
cinst nodejs.install
{% endhighlight %}


Você pode verificar se a instalação foi bem concluída com um simples [exemplo Hello World em Node.js][]!

[versões disponíveis no repositório do openSUSE]: http://download.opensuse.org/repositories/devel:/languages:/nodejs/
[download do instalador do node.js]: http://nodejs.org/download/
[chocolatey]: http://chocolatey.org/
[NPM]: https://npmjs.org/
[exemplo Hello World em Node.js]: {% post_url 2013-2-12-exemplo-hello-world-em-node-js %}