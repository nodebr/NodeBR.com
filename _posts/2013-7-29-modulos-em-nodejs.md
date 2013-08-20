---
layout: post
title: Módulos em Node.JS
description: Módulos permitem incluir outros arquivos JavaScript em sua aplicação, o Node tem um sistema simples de carregamento de módulos
author:
  name: Rafael Henrique Moreira
  email: rafadev7@gmail.com
  url: http://rafadev.com/
  github: rafadev7
  twitter: rafadev7
tags: [conceito, intermediário, módulos]
---
Node tem um sistema simples de carregamento de módulos, a utilização de módulos permite incluir outros arquivos JavaScript em sua aplicação, este sistema utiliza o formáto de módulos CommonJS e de fato grande maioria das funcionalidades do núcleo do Node é implementada utilizando módulos escritos em Javascript, o que significa que você pode ler o código fonte das bibliotecas do núcleo (**core**) [no Github][].

Módulos são cruciais para construção de aplicações em Node pois eles permitem incluir bibliotecas externas, como bibliotecas de acesso ao banco de dados, e ajudam a organizar seu código em partes separadas com responsabilidades limitadas. Você deve tentar indentificar partes reusáveis do seu código e transformá-las em módulos separados para reduzir a quantidade de código por arquivo e para ficar mais fácil de ler e manter seu código.

Utilizar módulos em Node é simples, você usa a função `require()`, que recebe um argumento: o nome da biblioteca do core ou o caminho do arquivo do módulo que você quer carregar. É aconselhável compreender [como funciona a função require do Node.js][]. Outro artigo aqui também já tratou [como utilizar módulos em sua aplicação Node.js][].

A mais expressiva e simples solução para módulos em JavaScript foi adotada como padrão pelo Node, o padrão [CommonJS][], que é usado de forma levemente diferente no Node. CommonJS te dá uma boa sintaxe, e mais importante, **módulos de primeira classe**. E se você estiver se perguntando porque módulos de primeira classe são importantes, a resposta é "pela mesma razão que funções de primeira classe são importantes".

Lembrando que na teoria de linguagens de programação, diz-se que algo é um objeto de primeira classe em uma linguagem quando ele pode ser construído em tempo de execução, passado como parâmetro, atribuído a uma variável, devolvido como resultado de uma função, incluído em uma estrutura de dados maior, etc. Na maioria das linguagens de programação os números e as strings são objetos de primeira classe, mas em muitas linguagens funções não são objetos de primeira classe. Em Javascript, Python, Ruby, C# e muitas outras linguagens, sabemos que funções são objetos de primeira classe, e essa facilidade acaba sendo chamado de "funções de primeira classe".

Em Node, arquivos e módulos tem correspondência de um para um. Observe o exemplo a seguir onde nosso módulo `calculo.js` carrega o módulo `circulo.js` salvo no mesmo diretório.

{% highlight javascript %}
// calculo.js
var circulo = require('./circulo.js');
console.log( 'Um circulo de raio 4 tem area de '
             + circulo.area(4));
{% endhighlight %}

{% highlight javascript %}
// circulo.js
var PI = Math.PI;

exports.area = function (r) {
  return PI * r * r;
};

exports.circunferencia = function (r) {
  return 2 * PI * r;
};
{% endhighlight %}

Neste exemplo o múdolo `circulo.js` exportou as funções `area()` e `circunferencia()` . Aqui é apresentado o principal conceito do sistema de carregamento módulos do Node, para um módulo exportar um objeto, ou um construtor, basta adicioná-lo no objeto especial `exports` . Todas as demais variáveis declaradas dentro do módulo serão variáveis privadas do módulo e não serão exportadas, neste exemplo a variável `PI` é privada do módulo `circulo.js` e não é acessível no escopo de `calculo.js` .

É importante frisar que `exports` é uma referência para `module.exports` utilizado apenas para o acréscimo de objetos, caso você queira exportar um único item, como um construtor, você vai precisar usar o objeto `module.exports` diretamente. Acompanhe o exemplo do nosso módulo `construtor.js` apresentado a seguir.

{% highlight javascript %}
// construtor.js
function MeuConstrutor (opts) {
  //...
}

// CUIDADO A LINHA ABAIXO NAO EXPORTA NADA
exports = MeuConstrutor;

// A linha abaixo exporta o construtor
module.exports = MeuConstrutor;
{% endhighlight %}

Note que a linha `exports = MeuConstrutor;` não irá ter o comportamento esperado, caso o exeplo parasse nesta linha apenas um objeto vazio seria exportado. Para exportar um único objeto, ou um construtor como neste exemplo, deve-se utilizar a variável `module.exports` .

O sistema de módulos é implementado no módulo `require("module")` .


### Importações cíclicas

Quando temos chamadas cíclicas de módulos utilizando a função `require()` um módulo não é retornado enquanto não tiver sido executado por completo, e o módulo será salvo em cache a primeira vez que for carregado.

Acompanhe a situação apresentada a seguir para compreender o dinâmica das importações cíclicas.

{% highlight javascript %}
// a.js
console.log('a iniciando');
exports.pronto = false;
var b = require('./b.js');
console.log('dentro de a, b.pronto = %j', b.pronto);
exports.pronto = true;
console.log('a pronto');
{% endhighlight %}

{% highlight javascript %}
// b.js
console.log('b iniciando');
exports.pronto = false;
var a = require('./a.js');
console.log('dentro de b, a.pronto = %j', a.pronto);
exports.pronto = true;
console.log('b pronto');
{% endhighlight %}

{% highlight javascript %}
// principal.js
console.log('principal iniciando');
var a = require('./a.js');
var b = require('./b.js');
console.log('dentro de principal, a.pronto=%j, b.pronto=%j', a.pronto, b.pronto);
console.log('principal pronto');
{% endhighlight %}

Quando o módulo `principal.js` carrega `a.js`, por sua vez `a.js` carrega `b.js`, neste ponto `b.js` tenta carregar `a.js` . Para evitar um cíclo infinito, de `a.js` carregando `b.js` e vice-versa, uma **cópia incompleta** do objeto `exports` de `a.js` é retornada para o módulo `b.js` . Então `b.js` completa seu carregamento, e seu objeto `exports` é fornecido para o módulo `a.js` .

Quando `principal.js` terminar de carregar, ambos os módulos estarão prontos. A saída deste programa seria assim:

{% highlight javascript %}
$ node principal.js
principal iniciando
a iniciando
b iniciando
dentro de b, a.pronto = false
b pronto
dentro de a, b.pronto = true
a pronto
dentro de principal, a.pronto=true, b.pronto=true
principal pronto
{% endhighlight %}

Esta foi a forma que os desenvolvedores do Node utilizaram para resolver o problema de importações cíclicas, e caso você tenha dependências cíclicas de módulos no seu programa, se certifique de planejar corretamente para que elas funcionem como o esperado.


### Módulos do núcleo do Node

O Node tem muitos módulos compilados em arquivos binários. Estes módulos estão descritos em detalhes na [documentação da API do Node][] - em Inglês.

Os módulos do núcleo (em Inglês **core***) estão dentro da pasta `lib/ ` na raiz do Node.

O Node sempre dá preferência em carregar os módulos do núcleo se seu identificador é passado para a função `require()` . Por exemplo `require('http')` irá sempre retornar o módulo `HTTP` embutido no núcelo do Node, mesmo que tenha um arquivo com o mesmo nome nesta pasta.


### Arquivo dos Módulos

Caso o nome do arquivo passado para a função `require()` não for encontrado, então o Node irá tentar carregar um arquivo com o nome passado adicionando a extensão ` .js`, ` .json`, e por fim ` .node` .

Notar que arquivos ` .js` são interpretados como arquivos de texto **JavaScript** e arquivos ` .json` são analisados como arquivos de texto **JSON**. Já arquivos ` .node` são interpretados como um módulo compilado carregado com `dlopen` .

Um módulo com prefixo ` / ` será buscado no caminho absoluto sistema de arquivos. Por exemplo, `require('/home/node/exemplo.js')` irá carregar o arquivo presente em ` /home/node/exemplo.js` .

Um módulo com prefixo ` ./ ` será buscado no caminho relativo ao módulo que chamou a função `require()` . Como no exemplo apresentado, `circulo.js` deve estar no mesmo diretório de `calculo.js` para que `require('./circulo.js')` encontre ele.

Já um módulo com prefixo ` ../ ` será buscado na pasta superior relativa ao módulo que chamou a função `require()` .

Caso o módulo requisitado não contenha o prefixo ` / ` ou ` ./ ` para indicar a localização do arquivo o módulo será considerado ou um módulo do núcleo ou um módulo instalado na pasta `node_modules`, gerenciada pela `NPM` .

Agora se o caminho do arquivo não existir, `require()` vai emitir um Erro com sua propriedade `code` igua a `'MODULE_NOT_FOUND'` .



### Carregando da pasta node_modules

Se o identificador passado para `require()` não é um módulo nativo, e não começa com ` / `, ` ../ `, ou ` ./ `, então Node começa a buscar no diretório pai do módulo atual, e adiciona ` /node_modules`, e espera carregar o módulo requisitado neste local. Se ele não for encontrado lá, então ele move para o diretório pai do diretório atual, e assim sucessivamente, até atingir o diretório raíz do sistema.

Por exemplo, se o arquivo ` /home/node/projetos/exemplo.js` chamou `require('meu_modulo.js')`, então o Node deve procurar nos seguintes locais, nesta ordem:

{% highlight console %}
/home/node/projetos/node_modules/meu_modulo.js
/home/node/node_modules/meu_modulo.js
/home/node_modules/meu_modulo.js
/node_modules/meu_modulo.js
{% endhighlight %}



### Pastas como Módulos

É conveniente organizar programas e bibliotecas dentro de seus diretórios independentes, e então fornecer um único acesso para esta biblioteca. Há três maneiras que uma pasta pode ser passada para a função `require()` como um argumento.

A primeira é criando um arquivo `package.json` na raíz da pasta, que especifica o módulo principal - `main` - e um nome - `name` . Veja um exemplo do arquivo `package.json` a seguir:


{% highlight javascript %}
{ "name" : "some-library",
  "main" : "./lib/uma-biblioteca.js" }
{% endhighlight %}

Se este exemplo estivesse na pasta ` ./uma-biblioteca`, então uma chamada `require('./uma-biblioteca')` tentaria carregar o arquivo ` ./uma-biblioteca/lib/uma-biblioteca.js` .

Caso não haja um arquivo `package.json` presente no diretório, então node tentaria carregar um arquivo `index.js` ou `index.node` presentes neste diretório. Se no exemplo dado não existisse o arquivo `package.json`, então uma chamada a `require('./uma-biblioteca')` tentaria carregar o arquivo ` ./uma-biblioteca/index.js` ou ` ./uma-biblioteca/index.node` .


### Módulos salvos em Cache

Os módulos são salvos em Cache após a primeira vez que eles são carregados. Isto significa, entre outras coisas, que toda chamada `require('modulo')` vai retornar exatamente o mesmo objeto retornado na primeira chamada, se ela fosse importar o mesmo arquivo.

Múltiplas chamadas para `require('modulo')` não pode fazer com que o código do módulo seja executado múltiplas vezes. Esta é uma característica importante. Com isso, objetos podem ser retornados, permitindo assim que dependências transitivas possam ser carregadas mesmo se elas causarem ciclos - isto é, tiverem importações cíclicas.

Caso você precise que um módulo execute um código várias vezes, então exporte uma função contendo este código e faça chamadas para esta função.


### Advertências para Módulos salvos em Cache

Um módulo é salvo no Cache baseado no seu endereço em disco, por exemplo ` /home/node/projetos/node_modules/meu_modulo.js` . Uma vez que uma chamada para a função `require('meu_modulo')` faz uma busca pelo módulo em vários diretórios, seguindo a sequência passada, caso ela encontre o módulo em uma pasta diferente da já salva em Cache, por exemplo no diretório atual ` ./meu_modulo.js`, então o módulo será considerado um módulo novo e não será usado a referência já salva em Cache.


## O objeto module

Em cada módulo `module` é uma variável re referencia para o objeto que representa o módulo atual. Em particular `module.exports` é acessível através do módulo global `exports` . O objeto `modulo` na realidade não é global, mas um objeto local de cada módulo.

### module.exports

O objeto `module.exports` é criado pelo sistema de módulos do Node e a variável `exports` aponta para este objeto. Seu módulo pode retornar vários objetos e funções simplesmente adicionando-os a variável `export`, por exemplo `exports.falar = function(){ console.log('Bom dia!') };` . Porém algumas vezes gostaríamos que nosso módulo retorne a instância de uma classe. Atribua o objeto desejado para ser exportado em `module.exports` . Atenção, neste caso não utilize a variável `exports` . Por exemplo suponha que estivéssemos fazendo um módulo chamado `a.js` .

{% highlight javascript %}
// a.js
var EventEmitter = require('events').EventEmitter;

module.exports = new EventEmitter();

// Executar algum trabalho, e apos algum tempo emitir
// o evento 'pronto' para o proprio modulo.
setTimeout(function() {
  module.exports.emit('pronto');
}, 1000);
{% endhighlight %}

Então em outro arquivo nós poderíamos fazer:

{% highlight javascript %}
// main.js
var a = require('./a');
a.on('pronto', function() {
  console.log('O modulo a esta pronto!');
});
{% endhighlight %}

É importante saber que atribuições a `module.exports` devem ser feitas imediatamente, elas não podem ocorrer em nenhum **callback**. O exemplo a seguir **não** irá funcionar.

{% highlight javascript %}
// x.js

setTimeout(function() {
  module.exports = { a: "Ola" };
}, 0);
{% endhighlight %}

{% highlight javascript %}
// y.js

var x = require('./x');
console.log(x.a);
{% endhighlight %}

Um módulo Node tem variáveis disponíveis por padrão no escopo de cada módulo, acompanhe a lista abaixo contendo as mais interessantes:

- __filename: O nome do arquivo do código que está sendo executado
- __dirname: O nome do diretório que está salvo o script que está sendo executado
- process: Um objeto que é associado ao presente processo em execução. Além de variáveis, este objeto tem métodos como `process.exit`, `process.cwd` e `process.uptime`
- process.argv: Um **array** contendo os argumentos de linha de comando. O primeiro elemento será `node`, o segundo elemento será o nome do arquivo JavaScript, e os próximos serão todos os argumentos de linha de comandos adicionais, caso sejam atribuídos
- process.stdin, process.stout, process.stderr: **Streams** que correspondem à entrada padrão, a saída padrão, e a saída de erro padrão do processo atual
- process.env: Um objeto contendo as variáveis de ambiente do usuário do processo atual
- **require.main**: Quando um arquivo é executado diretemente pelo Node, `require.main` é atribuído à este `module` .

Execute o exemplo a seguir para que você acompanhar a utilização destas variáveis dentro de um módulo, você pode executar o código abaixo salvando-o em um arquivo `exemplo.js` e executando através da linha de comando `node exemplo.js` .

{% highlight javascript %}
// exemplo.js

console.log('__filename: ', __filename);
console.log('__dirname: ', __dirname);
console.log('process.argv: ', process.argv);
console.log('process.env: ', process.env);
if(module === require.main) {
  console.log('Este e o modulo principal sendo executado.');
}
{% endhighlight %}


### Carregando seu módulo de pastas globais

Se a variável de ambiente `NODE_PATH` é definida com uma lista de caminhos absolutos delimitados por dois pontos, então o node vai buscar nestes caminhos por módulos se eles não forem encontrados em nenhum dos locais anteriormente buscados. Lembrando que no Windows para definir uma variável de ambiente você deve acessar as propriedades do Meu Computador, acessar Configurações avançadas do sistema e entrar nas Variáveis de Ambiente, e para adicionar a variável `NODE_PATH` clique Novo..., adicione o nome da variável como `NODE_PATH` e no valor da variável você pode inserir uma lista de caminhos absolutos delimitados por **ponto e virgula** - no caso do Windows.


Adicionalmente, node vai buscar nas seguintes localizações:

{% highlight console %}
$HOME/.node_modules
$HOME/.node_libraries
$PREFIX/lib/node
{% endhighlight %}

Onde `$HOME` é o diretório Home do usuário e `$PREFIX` é a configuração `node_prefix` do Node.

Estes são, em maior parte, por razões históricas. Sendo que você sempre será altamente encorajado à colocar suas dependências localmente na pasta `node_modules` . Elas serão carregadas mais rápido e com mais segurança.

### Identificando o módulo principal

Quando um arquivo é executado diretamente no Node, `require.main` é definido como seu `module` . Isso significa que você pode determinar quando um arquivo está sendo executado diretamente fazendo o teste:

{% highlight javascript %}
require.main === module
{% endhighlight %}

Para o exemplo apresentado `principal.js`, esta operação retornará `true` se executado diretamente via `node principal.js`, porém retornará `false` se executado através de uma importação `require('./principal.js')`

Pelo fato do objeto `module` fornecer a propriedade `filename` (normalmente equivalente à variável global - diferente para cada módulo - `__filemane`), você também pode obter o endereço do ponto de entrada da aplicação, o módulo principal, através da variável `require.main.filename` .

Note que se você importar um módulo através do `REPL` do Node a variável `require.main` naturalmente retornará `undefined`, porque não há um módulo principal sendo executado, e sim a linha de comando do Node.


## NPM

Não se pode falar em módulos em Node sem falar do [NPM][], ele é o gerenciador de pacotes incluso dentro do pacote do Node. Não vou entrar neste artigo, mas vocês perceberão que ele é fantástico e você deve usá-lo. Você pode ler mais [neste artigo introdutório sobre a NPM][] e [neste outro artigo que fala mais sobre a NPM e como instalar pacotes utilizando ela][].

Se você não está usando a NPM e os módulos do Node para implementar sua aplicação, você pode estar fazendo isso errado!

-- Modularizar: Escrever pequenos módulos que fazem apenas uma coisa, e integrá-los para fazer coisas mais complexas
-- Usar o gerenciador de pacotes: Usar a NPM para administrar suas dependências e parar de se preocupar com elas
-- Utilizar módulos em Node: É um sistema de módulos simples e expressivo que facilita a modularização de sua aplicação em Node e te proporciona módulos paramétricos de primeira classe











[no Github]: https://github.com/joyent/node
[CommonJS]: http://wiki.commonjs.org/wiki/Modules/1.1
[como funciona a função require do Node.js]: {{ site.url }}{% post_url 2013-5-29-como-funciona-a-funcao-require-do-node-js %}
[como utilizar módulos em sua aplicação Node.js]: {{ site.url }}{% post_url 2013-3-23-utilizando-modulos-em-sua-aplicacao-node-js %}
[documentação da API do Node]: http://nodejs.org/api/
[NPM]: http://npmjs.org/
[neste artigo introdutório sobre a NPM]: {{ site.url }}{% post_url 2013-5-15-npm-node-package-manager %}
[neste outro artigo que fala mais sobre a NPM e como instalar pacotes utilizando ela]: {{ site.url }}{% post_url 2013-7-19-o-que-e-a-npm-do-nodejs %}
