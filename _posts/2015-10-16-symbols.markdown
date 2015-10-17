---
layout: post
title:  "Symbols"
date:   2015-10-16 21:25:20
categories: JavaScript 
---

Simbols sao um novo tipo primitivo do JavaScript no ECMAScript 6. Eles sao tokens que server como ID unicos. Voce pode criar um simbolo, usando a funcao Symbol(), Isso funciona de modo parecido como se voce estivesse chamando uma a funcao String)
Sintaxe

let symbol1 = Symbol();  

symbol pode receber uma string como parametro, esse parametro vira a descricao desse symbol.

> let symbol2 = Symbol('symbol2');
> String(symbol2);
'Symbol(symbol2)'  

Cada symbol retornado pelo Symbol() nao podendo assim ser comparados.

> symbol1 === symbol2
false  

Simbolos como propriedades

Simbolos poder ser usados com propriedade.

const MY_KEY = Symbol();  
let obj = {};

obj[MY_KEY] = 123;  
console.log(obj[MY_KEY]);  

Classes o objetos literais tem uma feature chamada computed property keys voce pode especificar uma chave para a propriedade por uma expressao, colocando ela dentro de chaves []. No seguinte objeto literal, nos podemos usar uma property key para fazer o valor de MY_KEY uma key de propriedade.

const MY_KEY = Symbol();  
let obj = {  
    [MY_KEY]: 123
}

Tambem podemos utilizar symbols na declaracao de metodos.

const FOO = Symbol();  
let obj = {  
    [FOO](){
        return "bar";
    }
};

Criando suas propriety keys

Dado que agora existe um novo tipo de valor que pode ser key de uma propriedade em um objeto, uma nova terminologia e usada para ECMAScript 6:

    Property keys sao tanto para strings quando para simbolos

    Property names sao strings

    Vamos examinar a API para enumerar nossas prorpias chaves criando nosso primeiro objeto.

let obj = {  
    [Symbol('my_key')]: 1,
    enum: 2,
    nonEnum: 3
};
Object.defineProperty(obj, 'nonEnum', {enumerable: false});  

Object.getOwnPropertyNames() ignora as keys que forem Symbols.

  > Object.getOwnPropertyNames(obj)
    ['enum', 'nonEnum']

Object.getOwnPropertySymbols() ignora keys strings.

> Object.getOwnPropertySymbols(obj)
[Symbol(my_key)]

Reflect.ownKeys() considera todos os tipos de chave.

Reflect.ownKeys(obj);  
[Symbol(my_key), 'enum', 'nonEnum']

O nome de Object.keys() nao funciona de verdade, ela so considera as keys que forem strings.
Usando simbols para representar conceitos.

No ECMAScript 5, um dos mais importante dos conceitos, e
