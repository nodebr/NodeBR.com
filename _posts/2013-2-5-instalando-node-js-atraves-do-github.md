---
layout: post
title : Instalando Node.js através do GitHub
description: Este é um post prático que mostra como instalar o Node.js via repositório GitHub.
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags : [primeiros passos, básico]
---
Este é um post prático que mostra como instalar o **Node.js** usando o repositório [Git][] no [GitHub][].

## Mac

Se você está usando o excelente gerenciador de pacotes [homebrew][] do **Mac**, você pode instalar **Node** com apenas um comando: <code>brew install node</code>.

Caso contrário, siga os passos abaixo:
- [Instale o Xcode][]
- [Instale o Git][]
- Rode os seguintes comandos em seu prompt de comandos:

{% highlight console %}
git clone git://github.com/ry/node.git
cd node
./configure
make
sudo make install
{% endhighlight %}

## Ubuntu

No **Ubuntu** você vai os seguir dois passos abaixo para instalar o **Node** através do repositório git:

- Instale todas as dependências:

{% highlight console %}
sudo apt-get install g++ curl libssl-dev apache2-utils
sudo apt-get install git-core
{% endhighlight %}

- Rode os seguintes  comandos:

{% highlight console %}
git clone git://github.com/ry/node.git
cd node
./configure
make
sudo make install
{% endhighlight %}

## Windows

Atualmente você deve usar o [cygwin][] para instalar o **Node** no **Windows**. Para fazer isso siga os passos abaixo:

- [Instalar cygwin][].
- Usar o <code>setup.exe</code> na pasta do cygwin para instalar os seguintes pacotes:

{% highlight console %}
devel → openssl
devel → g++-gcc
devel → make
python → python
devel → git
{% endhighlight %}

- Abra a linha de comando do cygwin com Start > Cygwin > Cygwin Bash Shell.
- Rode os comandos abaixo para baixar e construir o Node.

{% highlight console %}
git clone git://github.com/ry/node.git
cd node
./configure
make
sudo make install
{% endhighlight %}

Você pode verificar se a instalação foi bem concluída com um simples [exemplo Hello World em node][]!

[Git]: http://git-scm.com/
[GitHub]: https://github.com/
[homebrew]: https://github.com/mxcl/homebrew
[cygwin]: http://www.cygwin.com/
[Instale o Xcode]: https://developer.apple.com/xcode/
[Instale o Git]: http://help.github.com/mac-git-installation/
[Instalar cygwin]: http://www.mcclean-cooper.com/valentino/cygwin_install/
[exemplo Hello World em Node.js]: {% post_url 2013-2-12-exemplo-hello-world-em-node-js %}