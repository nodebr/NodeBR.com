---
layout: post
title: Javascript não é bagunça
description: Quero escrever sobre um assunto que gosto muito: SOLID. Começo pela letra O do acrônimo por acreditar ser o de mais fácil implementação, inclusive em códigos existentes.
author:
  name: Paulo Silva
  email: neocite@gmail.com
  url: https://medium.com/@Neocite
  github: neocite
  twitter: paulosilva31
tags: [padrões de projeto, clean code,avançado]
---
# Javascript não é bagunça
Eu realmente gosto de Javascript. Quando vi o nascimento da
plataforma node.js (node.js não é linguagem, ok?), fiquei empolgado com a idéia de poder fazer um scriptizinho.js (é ótimo ouvir isso de um gerente, não?) no lado back da força. Os primeiros exemplos de implementação sobre a plataforma não se preocupavam com a qualidade do código sugeridas pelos Jedis (Uncle Bob, Martin Fowler, Kent Beck, Booch, Gangue de Quatro* e etc.). Confesso que isso me deixava desanimado e me questionei se eu deveria investir o meu tempo neste novo mundo. Acertei! Hoje a coisa toda evoluiu e podemos afirmar que Javascript não é bagunça (ou talvez nunca tenha sido).

# “TÁ” FECHADO, MAS "TÁ" ABERTO?

Quero escrever sobre um assunto que gosto muito: [SOLID][http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29]. Começo pela letra O do acrônimo por acreditar ser o de mais fácil implementação, inclusive em códigos existentes. Em linhas gerais o que [Bertrand Meyer][http://en.wikipedia.org/wiki/Bertrand_Meyer] diz sobre o princípio do "aberto-fechado" é o seguinte:

"Uma classe só deve ser modificada quando erros forem encontrados. A classe pode ser estendida, mas nunca modificada."

É difícil entender o valor do "aberto-fechado". Talvez seja necessário escrever muitos códigos ruins para enxergar o real benefício deste princípio mas admito, minha vida ficou muito melhor depois que eu passei a escrever código limpo.

# BUSCAFRETE — "BUSCAPÉ" DOS FRETES

Acredito que exemplos de códigos sobre projetos factíveis são mais fáceis de entender e por isso, vamos imaginar o seguinte produto.

"Nós do grupo Eike Zords queremos construir uma solução inovadora para o setor logístico. Esse produto deverá ser capaz de retornar o menor custo de frete a partir de algumas variáveis: origem, destino e peso da mercadoria. Segundo nosso CEO, este produto já vale 1 bilhão de reais. "

Com o escopo do nosso [MVP][http://en.wikipedia.org/wiki/Minimum_viable_product] definido, chegou a hora de codar.

{% highlight javascript %}
function CalculadoraFrete(origem,destino,peso){
 var _distancia = googleMaps.getDistance(origem,destino);
 var _valorFrete=[];
 var _repositorio = new RepositorioTaxasFrete();
 function buscaMenorFrete(){
  if(peso <= 30){
   if(_distancia < 50){
    _valorFrete.push(_repositorio.taxaMotoFreteMunicipal());
   }else{
    _valorFrete.push(_repositorio.taxaCorreios());
   }
  }
  else if(peso>30 && peso<800){  
   if(_distancia < 140){
    _valorFrete.push(_repositorio.taxaCarro());
   }else{
    _valorFrete.push(_repositorio.taxaCaminhao());
   }
  }
  else {
   if(_distancia < 300){
    _valorFrete.push(_repositorio.taxaCaminhao());
   }else{
    _valorFrete.push(_repositorio.taxaCarreta());
   }
  }
  _valorFrete.sort();
  return _valorFrete[0];
 }
}
{% endhighlight %}

O código acima apresenta diversos problemas mas quero comentar as deficiências sobre a ótica do "open-closed principle".

Evolução dolorosa: A classe sempre será alterada quando funcionalidades forem adicionadas no produto. É o tipo de código que em pouco tempo ninguém mais quer colocar a mão. Legibilidade: As linhas acima são difíceis de ler, será complicado criar uma [linguagem ubíqua][http://martinfowler.com/bliki/UbiquitousLanguage.html] com o dono do produto. "Véio, põe gordura pra mexer nessa bagaça" será a única técnica de estimativa adequada para tarefas que envolvam mexer no código acima.

Vamos melhorar a situação desse código? Começar pelos testes é uma técnica poderosa para se encontrar bons designs de objetos.

{% highlight javascript %}

var assert = require(“assert”); // npm install -g assert
describe(‘CalculadoraFrete’, function(){
   it(‘O valor do frete tem que ser R$ 30,00 para uma distância de 15 km no mesmo município, para uma mercadoria que pesa até 10kg’, function(){

   var _CEPorigem = 01314000;
   var _CEPdestino = 01316000;
   var _pesoDaMercadoria = 10;
   var _valorDofreteEsperado = 30;
   var _resultado =    CalculadoraFrete.buscaMenorFrete(_CEPorigem,_CEPdestino,_pesoDaMercadoria);
   assert.equal(_esperado,_resultado);
   })
})
{% endhighlight %}

Para manter o foco sobre o assunto "open-closed" não vou copiar os códigos de testes que fiz. Eles realmente me ajudaram a evoluir o modelo.

Decidi criar uma especialização para cada tipo de frete. Deixo a responsabilidade de aceitar (ou não) fazer o cálculo do frete para algum especialista no assunto.

{% highlight javascript %}
function TipoMotoFrete(){
  this.distanciaMaximaAtendida = 50;
  this.pesoMaximoAtendido = 30;
  this.podeAtenderEsseFrete = function(){
    return this.distancia < this.distanciaMaximaAtendida &&
           this.peso < this.pesoMaximoAtendido;
  }
  this.obterTaxaPorKM = function() {
   return 12.76;
  };
  this.obterTaxaPorKG = function() {
   return .70;
  };
}
{% endhighlight %}

Abaixo mais um tipo de frete

{% highlight javascript %}
function TipoCorreioFrete(){
  this.pesoMaximoAtendido = 30;
  this.podeAtenderEsseFrete = function(){
    return this.peso < this.pesoMaximoAtendido;
  }
  this.obterTaxaPorKM = function() {
   return 10.97;
  };
  this.obterTaxaPorKG = function() {
   return .65;
  };
}
{% endhighlight %}

A distância não é importante para os "Correios" pois eles entregam em qualquer lugar do mundo! teoricamente.

Vou omitir a criação de todos os tipos de frete necessários para que otimizar o texto.

{% highlight javascript %}
//Essa é a abstração para tipos de frete
function FornecedorFrete(distancia,peso){
 if (this.constructor === FornecedorFrete) { 
  throw new Error(“Aqui é abstrato meu querido. Não vai estar
      podendo estar sendo construído. Nós da OO center
      agradecemos a compreensão.”);
 };
 this.podeAtenderEsseFrete = function() {
  return this;
 };
 this.obterTaxaPorKG = function() {
  return this;
 };
 this.obterTaxaPorKM = function() {
  return this;
 };
 this.distancia = distancia;
 this.peso = peso;
 this.calculaValorFrete = function(){
   var _custoPeso = this.obterTaxaPorKG() * this.peso;
   var _custoDistancia = this.obterTaxaPorKM() * this.distancia;
   return  _custoPeso+_custoDistancia;
 };
};
{% endhighlight %}

Chegou o momento de colocar gerência na coisa toda. Vamos implementar um cara que seja responsável por dividir o trabalho entre todos os fornecedores de frete.

{% highlight javascript %}
function CalculadoraFrete(distancia,peso){
 var _fornecedoresFrete = [];
 var _fretesEncontrados = [];
 _fornecedoresFrete.push(new TipoMotoFrete(distancia,peso));
 _fornecedoresFrete.push(new TipoCorreioFrete(peso));
 function obterMenorFrete(){
  for(var i=0;i<_fornecedoresFrete.length;i++){ 
   var _fornecedorFrete = _fornecedoresFrete[i];
   if(_fornecedorFrete.podeAtenderEsseFrete()){
    var _valorFrete = _fornecedorFrete.calculaValorFrete();
    _fretesEncontrados.push(_valorFrete);
   };
  };
  _fretesEncontrados.sort();
  return _fretesEncontrados[0];
 };
}
{% endhighlight %}

Terminado! Agora estamos preparados para receber diferentes tipos de fornecedores de frete e cada um deles decide se calcula o valor.

Pronto? Será que a "FornecedorFrete" não está com muita responsabilidade? Concordam que a forma de calcular o frete presente no método "calculaValorFrete" será alterada? Eu acredito que sim! Porém, quero fazer essa melhoria no momento que conversarmos sobre ["Single responsibility principle”][http://en.wikipedia.org/wiki/Single_responsibility_principle] ou "cada macaco no seu galho".

# Compartilhar para transformar

Uma vez me disseram que é importante devolver ao mundo parte do que o mundo te deu e por isso resolvi compartilhar meu conhecimento sobre desenvolvimento de software.
