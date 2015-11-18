---
layout: post
title:  "map & set & weakmap & weakset"
date:   2015-10-16 21:25:20
categories: JavaScript 
---

Junto com alguns outros tipos de estruturas de dados a ES6 quatro estruturas de dados bem interessantes: Map, WeakMap, Set e WeakSet. Esse artigo explica um pouco melhor como elas funcionam.
Map

JavaScript sempre teve uma standard library bem simplista. Certamente estava faltando estruturas para mapeamento de valores para valores. O melhor que nos podiamos fazer em ECMAScript 5 era abusar um pouco de objetos e criar uma especie de mapa de strings para valores arbitrarios, mas poderiam existir alguns problemas poderiam acontecer por causa da falta dessa estrutura na linguagem.

A estrutura de dados Map nos deixa usars valores arbitrarios como chaves e sao extremamente bem vindos.
Operacoes basicas

Trabalhando com entradas simples.

let map = new Map();

map.set('foo', 123);  
map.get('foo'); // 123

map.has('foo'); // true  
map.delete('foo'); // true  
map.has('foo'); // false  

Determinar o tamanho de um mapa e limpa-lo.

let map = new Map();  
map.set('foo', true);  
map.set('bar', false);

map.size // 2  
map.clear();  
map.size // 0  

Iniciando um map

Voce pode iniciar um map de um jeito intineravel usando 'pares' (arrays com dois elementos). Voce tem duas opcoes sendo a primeira usando um array e sendo intineravel:

let map = new Map([  
    [1, 'um'],
    [2, 'dois'],
    [3, 'tres']    
]);

O metodo set pode ser usado como chain:

let map = new Map();  
    .set(1, 'um')
    .set(2, 'dois')
    .set(3, 'tres');

Keys

Qualquer valor pode ser uma key, ate mesmo um objeto:

let map = new Map();  
CONST KEY1 = {};  
map.set(KEY1, 'Ola');  
console.log(map.get(KEY1)); // Ola

const KEY2 = {};  
map.set(KEY2, 'Mundo');  
console.log(map.get(KEY2)) // Mundo  

Quais chaves sao consideradas iguais?

A maior parte dos operacoes map checka se o valor e igual a uma das suas keys. Elas funcionam de maneira parecida com o ===, mas considera que NaN seja igual NaN.

Vamos ver como o === lida com o NaN?

NaN === NaN // false  

Por convencao voce pode usar o NaN como key no maps, assim como qualquer outro valor.

let map = new Map();  
map.set(NaN, 123);  
map.get(NaN); // 123  

Assim como no ===, +0 e -0 sao considerados o mesmo valor (oque normalmente e a melhor maneira de se lidar com zeros).

map.set(-0, 123);  
map.get(+0) //123  

Objetos diferentes sempre sao considerados diferentes. Isso e algo que nao pode ser configurado.

new Map().set({}, 1).set({}, 2).size // 2  

Tentar recuperar um chave que nao existe ira retornar undefined.
Intinerando

Vamos iniciar um map para demostrar seu comportamento.

let map = new Map([  
    [false, 'nao'],
    [true, 'sim']
]);

Maps guardam a ordem em que os valores foram inseridos e intineram respeitando essa order.

for(let key of map.keys()) {  
    console.log(key);
}
// Output:
// false
// true

A funcao value() intinera um map pelo seus valores.

for(let value of map.values()){  
    console.log('value');
}
// output
// nao
// sim

A funcao entries() retorna as entradas do mapa em valores pares (key,value);

for (let [key, value] of map.entries()){  
    console.log(key, value);
}

o valor default de intineracao em maps e a funcao entries():
Usando o operador spread

Para poder usar funcoes como map() e filter() em arrays, voce tera que fazer um pequeno hack.

    converter o mapa para um array
    fazer as operacoes
    converter o resultado de volta para map.

let map0 = new Map()  
    .set(1, 'a')
    .set(2, 'b')
    .set(3, 'c');

let map1 = new Map(  
    [...map0] //primeiro passo
    .filter(([k, v]) => k <3 ) // segunda etapa
);
// map1 === {1 => 'a', 2 => 'b'} // etapa tres

Map API
Com valores unicos

map.prototype.get(key) : any? Retorna o valor da chave que foi passada como parametro, retorna undefined se o valor relativo a essa chave nao existir.

map.prototype.set(key, value) : this: Insere o valor passado para respectiva chave, se essa chave ja existir o valor e atualizado, caso contrario ela e criada.

map.prototype.has(key) : boolean: Retorna se o valor existe em determinado map.

map.prototype.delete(key) : boolen: Se a chave existir ele deleta ela e retorna true, caso contraio ele retornara false.
Metodos do map

get Map.prototype.size : number: Retorna quantas entradas existem no map.

Map.prototype.clear() : void: Remove todas os registros do map/.
Intinerando com o map

Sempre acontece na ordem em que os registros sao inseridos no map.

Map.prototype.entries(): Ele retorna um valor intineravel das chaves x valores de todos os registros desse map.

Map.prototype.forEach((value, key, collection)) => void, thisArg?): Esse e o primeiro callback e invocado a qualquer entrada.

Map.prototype.keys(): retorna um valor intineravel de todas as keys do map.

Map.prototype.value: retorna um valor intineravel de todos os values do map.

Map.prototypeSymbol.intinerador: A maneira padrao de intinerar maps, ela se refere a Map.prototype.entries.
WeakMap

WeakMap nada mais e que um map que nao protege seus dados do garbage colector, isso significa que voce pode usar ele sem se preocupar com memery leaks.

As unicas duas diferencas entre Maps e WeakMaps e que voce nao pode intinerar um WeakMap, e tambem nao pode usar a funcao clear().

As razoes por essa restricao sao:
* A volatibilidade dos WeaksMaps nao permitem a intineracao. * O motivo por nao existir um metodo clear no WeakMaps e por seguranca, para destruir um registro voce precisa ter alem do objeto WeakMap a key desse registro.
Usando weakmaps para dados privates

O seguinte codigo usa o seguintes WeakMaps _counter e _action para armazerar dados.

let _counter = new WeakMap();  
let _action = new WeakMap();  
class Countdown(){  
    constructor(counter, action){
        _counter.set(this, counter);
        _action.set(this, action);        
    }
    dec(){
        let counter = _counter.get(this);
        if(counter < 1) return;
        counter--;
        _counter.set(this, counter);
        if(counter === 0){
            _action.get(this)();
        }
    }
}

Vamos implementar o Countdown:

let c = new Countdown(2, () => console.log('Done'));  
c.dec();  
c.dec();  // done  

O Countdown guarda apenas dados de instancia, sendo assim a instancia de C nao tem nenhuma key.

Reflect.ownKeys(c); // []  

WeakMAP API

WeakMap tem apenas quatro metodos e todos eles se comportam da mesma maneira que o map.

    WeakMap.prototype.get(key) : any
    WeakMap.prototype.set(key, value) : this
    WeakMap.prototype.has(key) : boolean
    WeakMap.prototype.delete(key) : boolean

Set

O ECMAScript 5 tambem nao possui essa estrutura de dados. Existem dois jeitos de se contornar isso.

    Use keys de um objeto para guardar elementos de colecao de strings
    Guardando um valores de um elemento dentro de um array, recuperando os valores pelo indexOf(), removendo usando filter(). Isso nao e uma solucao muito elegante. E um problema disso e que voce nao pode usar indexOf de um valor NaN, por exemplo.

Operacoes basicas

Manipulando elementos unicos.

let set = new Set();  
set.add('red')  
set.has('red') // true  
set.delete('red') // true  
set.has('has') // false  

Determinando o tamanho de um set e o limpando.

let set = new Set();  
set.add('red');  
set.add('green');

set.size // 2

set.clear();  
set.size // 0  

Iniciando um set

Voce pode iniciar um set por valores intineraveis ou por chain, assim como o map.

let set = new Set(['red', 'green', 'blue']);  

ou

let set = new Set().add('red').add('green').add('blue');  

Valores

Assim como maps, os elementos sao comparados usando uma especie de === que com excecao.

let set = new Set();  
set.add('foo');  
set.size // 1

set.add('foo');  
set.size // 1  

Intinerando

Sets sao intineraveis e o loop for-of funciona como esperado.

    let set = new Set(['red', 'green', 'blue']);
    for (let x of set) {
        console.log(x);
    }
    // Output:
    // red
    // green
    // blue

Como voce pode ver, sets tambem preserva a order de insercao como sendo a ordem de intineracao.

Set e basicamente um map para guardar registros nao pares como valores unicos.
WeakSet

Apresenta as mesmas funcoes que o Set, porem libera espaco para o garbage colector trabalhar.
