---
layout: post
title:  "generators"
date:   2015-10-16 21:25:20
categories: JavaScript 
---



http://www.2ality.com/2015/03/es6-generators.html

Uma nova feature do ES6 Genarators são funções que podem ser pausadas e resumidas. Isso pode ser usado de diversas maneiras como em iterators, programação assincrona, por exemplo. Esse artigo irá explicar essa nova feature do JS e mostrar suas aplicações.

As duas principais aplicações para generators são:
* Implementando iterables * Travando chamadas de funções assincronas

Implementando iterables via generators

A seguinte função retorna um iterable sobre as propriedades de um objeto, por par de propriedades.

// O asterisco depois de 'function' significa
// que 'objectsEntries' é um generator
function* objectEntries(obj){  
    let propKeys = Reflect.ownKeys(obj);

    for(let propKey of propKeys){
        // `yield` retorna um valor e pausa
        // o `generateor`. Depois ele pode ser
        // retomado do mesmo lugar onde foi pausado.
        yield [propKey, obj[propKey]]
    }
}

Como esse generator funciona exatamente será explicado depois. Mas ele é usado da seguinte forma:

let jane = { first: "Jane", last: "Doe" };  
for (let [key, value] of objectEntries(jane)) {  
    console.log(`${key}: ${value}`);
}
// Output:
// first: Jane
// last: Doe

Bloqueando chamadas de funções assincronas

No codigo a seguir, nos usamos uma biblioteca que nos retorna assincronamente dois JSON's. Veja como, na linha (A), e execução dos blocos espera até a conclusão do Primise.all(). Isso faz com que o codigo pareça ser sincrono quando na verdade ele faz operações assincronas.

    co(function* () {
        try {
            let [croftStr, bondStr] = yield Promise.all([  // (A)
                getFile('http://localhost:8000/croft.json'),
                getFile('http://localhost:8000/bond.json'),
            ]);
            let croftJson = JSON.parse(croftStr);
            let bondJson = JSON.parse(bondStr);

            console.log(croftJson);
            console.log(bondJson);
        } catch (e) {
            console.log('Failure to read: ' + e);
        }
    });

getFile(url) nos retorna uma arquivo pela url. Essa implementação será mestrada mais tarde, eu tambem explicarei como co funciona.

O que são generators?

Generators são funções que podem ser pausadas e resumidas, veja no exemplo abaixo considerando uma função chamada genFunc.

function* genFunc() {  
    console.log('First');
    yeild; // (a)
    console.log('Second'); // (b)
}

Duas coisas tornam essa função diferentes de uma função normal:

    Ela começa com a keyword function*
    Ela será pausada no meio via yield

Chamar a função genFunc não faz com ela seja executada. Em vez disso ela retonar um objeto generator no qual podemos controlar.

let genObj = genFunc();  

genFunc() é inicialmente pausado no começo do seu body. O Metodo next() faz que a execução desse generator continue até o proximo yield:

genObj.Next()  
First  
{ value : undefined, done: false }

Como você pode ver na ultima linha, genObj.next() tambem retorna um objeto. Vamos ignorar isso por enquanto, ficará tudo mais claro quando olharmos os generators como iterator.

genFunc esta pausada na linha (a). Executando next() novamente, a execução será retomada e a linha (b) será executada:

genObj.next();  
Second  
{ value: undefined, done: true }

Depois disso a execução dessa função esta finalizada, qualquer chamada do metodo next() será ignorada e não tera efeito nenhum.
Maneiras de criar um generator

Existem quatro maneiras na qual você pode criar um generator.:

    Declarando ele como uma função declaration

function* genFunc(){ . . . }  
let genObj = genFunc();  

    Declarando ela como uma função expression

const genFunc = function* () { . . . };  
let genObj = genFunc()  

    Como se ele fosse um metodo definido dentro de um objeto literal:

let obj = {  
    *generatorMethod(){
        . . . 
    }
};
let genObj = obj.generatorMethod();  

    Chamando ele depois de como se ele fosse um Metodo definido de uma classe.

class MyClass {  
    *generatorMethod(){
        . . . 
    }
}
let myInst = new MyClass();  
let genObj = myInst.generatorMethod();  

Papeis que um generator podem desempenhar

Generator podem executar três papais:

    Iterators (produtores de dados): Cada yield pode retornar um valor via next(), isso significa que você pode produzir sequencias de valores via loops e funções recursivas.

    Observers (consumidores de dados) : yield tambem pode ser usado para receber valores apartir do next() (como um parametro). Isso significa que generator podem consumir dadas a partir das pausas usando valores inseridos pelo next().

    Coroutines (Produtores e consumidores de dados): Dado ao fato que generators podem tanto consumir quanto produzir dados, não é necessario muito trabalho para tornar eles coroutines.

Abaixo existem explicações mais detalhadas para esses papeis.
3. Generators como iterators (produtores de dados)

Como explicado anteriormente, objetos generators podem tanto consumir quanto fornecer dados, aqui nos implementados um generator com ambos os papeis:

interface Iterable {  
    [Symbol.iterator]() : Iterator;
}
interface Iterator {  
    next() : IteratorResult;
    return?(value? any) : IteratorResult;
}
interface IteratorResult {  
    value: any;
    done: boolean;
}

Uma função generator produz uma sequencia de valores via yield, um consumir de dados recebe os dados a partir de parametros passados pelo metodo next(). Por exemplo, o generator a seguir produz os valores 'a', 'b':

function* genFunc() {  
    yield 'a';
    yield 'b';
}

Jeitos de usar um generator dentro de um loop

Você pode passar generators como parametros de estruturas de repetição, os três principais jeitos de fazer isso são:

for-of

for(let x of genFunc()){  
    console.log(x);
}
// Output:
// a
// b

O segundo jeito é usando o operador spread, que torna sequencias intineraveis em elementos de um array.

let arr = [...genFunc()]; // ['a', 'b']  

E por ultimo, destructuring:

let [x, y] = getFunc();  
// x = 'a'
// y = 'b'

retornando valores de um generator

E generator anterior não possuia return de forma explicita. Quando você não define um return o generator retorna undefined. Vamos analizar agora um generator com um return.

function* genFuncWithReturn() {  
    yield 'a';
    yield 'b';
    return 'result';
}

O valor do return é retornado como o ultimo valor que next(), assim como a propriedade done for verdadeira.

Ainda irei fazer um artigo sobre como usar os poderes de um generator em funções recursivas e em loops, mas esse conceitos são um poucos avançados, então se você se interessar, por favor leia esse post (É, aparentemente eu ainda não o escrevi).
