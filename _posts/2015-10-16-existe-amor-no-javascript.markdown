---
layout: post
title:  "Existe amor no JavaScript"
date:   2015-10-16 21:25:20
categories: JavaScript 
---



JavaScript é uma linguagem incrivel, o grande problema dele é que ela provavelmente será a segunda, é realmente dificil sair de uma linguagem que você ainda não compreende totalmente, e ir para outra que tem alguns problemas de design, e que usa outros paradigmas além do que você foi ensinado. Se você vier com o coração aberto tenho certeza que você vai **cair de amores** pelo JavaScript. E você pode apostar, isso (provavelmente) vai ser incrivel.


<img src="{{site.url}}/assets/js_series/intro_1.jpg" />


Quando você começa a digitar seus `hello worlds` te explicam que as funções na verdade são um tipo primitivo, assim como strings ou integers. JavaScript é um linguagem que precisa ser desbravada sem preconceitos, só assim você vai conseguir entender e usar o melhor dessa linguagem.

JavaScript foi criado em 1995, ele não era uma linguagem madura quando se tornou o standard da web por causa de sua implementação no netscape, e do seu irmão JScript no internet explorer. Sendo assim alguns bugs e alguns erros na implementação da linguagem acabaram sendo oficiais, nessa mesma serie irei explicar alguns dos problemas que o JS possui. 

Mas em 2015 uma nova luz surgiu no horizonte, ECMAScript 6, uma nova especificação da linguagem que faz com que a linguagem tenha features poderosas de outras linguagens, alem de novas funções nativas para manipulação de tipos primitivos. Essa nova especificação é compativel com a antiga, ou seja, seu codigo JavaScript continua funcionando da mesma maneira, você só precisa se preocupar com compatibilidade se você estiver escrevendo EC6, mas isso é facilmente resolvido quando você usa um `transpiler` para 'converter' seu codigo para EC5 e assim continuar compativel com a maioria dos navegadores.

Procurando aprender sobre essas novas features eu acabei encontrando um [documento](https://github.com/lukehoban/es6features#readme) muito interessante, e isso me inspirou a desenvolver essa serie de artigos explicando detalhadamente cada uma delas

* [arrows](http://josenberg.com.br/ecmascript-arrows/)
* [classes](http://josenberg.com.br/ecmascript-classes/)
* enhanced object literals
* template strings
* destructuring
* default + rest + spread
* let + const
* iterators + for..of
* generators
* unicode
* modules
* module loaders
* map + set + weakmap + weakset
* proxies
* symbols
* subclassable built-ins
* promises
* math + number + string + array + object APIs
* binary and octal literals
* reflect api
* tail calls

Você não precisa usar todos os itens dessa lista, na verdade você __não__ deve usar todos os itens dessa lista. Existem novas estruturas de dados, por exemplo, que são bem interessantes, porem elas podem acabar confundindo sua equipe e assim trazer um valor negativo para seu time, o bom programador escreve codigo que outros programadores podem entender, mantenha isso na sua cabeça quando usar novos recursos de qualquer tecnologia que você estiver aprendendo. 