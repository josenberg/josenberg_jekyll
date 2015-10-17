---
layout: post
title:  "let & const"
date:   2015-10-16 21:25:20
categories: JavaScript 
---

Você já deve saber que para iniciar variaveis no JavaScript você precisa usar a keyword var, mas isso é um pouco nebuloso, pois não fica claro o escopo dessa variavel, para melhorar esse aspecto no EC6 você vai poder declarar variaveis usando tanto let quanto const.
let

Ao declarar uma variavel usando let você cria ela em um escopo local, um jeito mais otimizado de se fazer isso em relação ao var.
Sintax

let var1 [= value1] [, var2 [= value2]] [, ..., varN [= valueN]];
Descrição

O letnos permite declarar variaveis limitadas no escopo de um bloco, ou expressão. Essa é a principal diferença dela com o var (que define a varivel no escopo global, ou localmente em uma função independende de seus blocos).

Escopo de bloco com let

Use let para definir variaveis dentro de um bloco.

if (x > y) {  
    let gamma = 12.7 + y;
    i = gamma * x;
}

Ele faz o codigo ficar bem mais limpo quando funções internas são utilizadas.

var list = document.getElementById("list");

for (var i = 1; i <= 5; i++) {  
    var item = document.createElement("LI");
    item.appendChild(document.createTextNode("Item " + i));

    let j = i;
    item.onclick = function (ev) {
        console.log("Item " + j + " is clicked.");
    };
    list.appendChild(item);
}

O exemplo acima funciona como planejado porque as cinco instancias da função inner se referem à cinco instancias diferentes da variavel j.

Regras de escopo

O escopo das variaveis let pertence ao bloco na qual elas foram criadas (assim como aos sub-blocos dentro desse bloco). Veja abaixo os exemplos:

function varTest(){  
    var x = 31;
    if (true) {
        var x = 71; // Mesma variavel
        console.log(x); // 71
    }
    console.log(x); // 71
}

functoin letTest(){  
    let x = 31;
    if(true){
        let x = 71;
        console.log(x); // Variavel diferente
    }
    console.log(x) // 31
}

Quando usadas no nivel mais alto tanto o let quanto o var trabalham do mesmo jeito:

var x = 'global';  
let y = 'global';  
console.log(this.x);  
console.log(this.y);  

O output desse codigo irá ser "global" nos dois casos.

Dead Zone e erros do let

Redeclarar duas variaveis com o mesmo nome usando let irá retornar um erro do tipo TypeError.

if(x){  
    let foo;
    let foo; // TypeError thrown.
}

Ao contrario do var, o let não faz hoist das variaveis no topo do bloco. Se você referenciar uma variavel dentro de um bloco com let e tentar usar ela antes de efetivamente ela ter sido declarada você vai receber um erro ReferenceError, isso é porque a variavel foi declarada dentro de uma dead zone(Dead zone é o codigo que fica entre a declaração da sua variavel e o inicio do bloco, nesse espaço não existe referencia à ela).

function do_something(){  
    console.log(foo); // ReferenceError
    let foo = 2;
}

Tome cuidado quando for usar let dentro de switch's porque esse tipo de estrutura conta apenas como um bloco.

switch (x) {  
    case 0:
        let foo;
        break;

    case 1:
        let foo; // TypeError por ela já ter sido declarada.
        break;
}

Let dentro de loops for

Você pode usar a keyword let para setar variaveis dentro do escopo do for. A diferença entre declarar com let e com var é que var faz a variavel ser visivel para todo o resto da função.

var i = -;  
for (let i=i; i < 10; i ++){  
    console.log(i);
}

Demostrações

*let vs var *

Quando usada dentro de um bloco, let limita o escopo da variavel ao bloco na qual ela foi criada. Veja as diferenças no exemplo abaixo:

var a = 5;  
var b = 10;

if (a === 5) {  
    let a = 4; // O escopo esta definido como dentro do bloco
    var b = 1; // O escopo se refere à toda a function

    console.log(a);  // 4
    console.log(b);  // 1
} 

console.log(a); // 5  
console.log(b); // 1  

Const

Uma das diferenças entre let/var e const é que uma variavel declarada com const é uma variavel de valor fixo, só é possivel fazer a leitura dessa varivel.
Sintaxe

const name1 = value1 [, name2 = value2 [, ... [, nameN = valueN]]]]; nameN - Nome da constante. Pode ser qualquer identificador válido.

valueN - Valor atribuido a uma constante. Pode ser qualquer expressão valida.
Descrição

O escopo do const é block-scoped igual ao do let, O valor de uma constante não pode ser alterado e ela tambem não pode ser redeclarada. É obrigatorio que um valor seja atribuido à ela assim que ela for declarada. Uma constante não pode compartilhar o nome de uma função ou variavel do mesmo escopo.

// define my_fav como uma constante e atribui o valor 7
const my_fav = 7;

// isto falha mas não emite erros no Firefox e Chrome (porém não falha no Safari)
my_fav = 20;

// retorna 7
console.log("my favorite number is: " + my_fav);

// tentar redeclarar a constante emite um erro 
const my_fav = 20;

// o nome my_fav está reservado para a constante acima, logo também irá falhar
var my_fav = 20; 

// my_fav ainda é 7
console.log("my favorite number is " + my_fav);

// Atribuir valores a uma variável const é um erro de sintaxe
const a = 1; a = 2;

// const deve ser inicializada
const foo; // SyntaxError: missing = in const declaration

// const também funciona com objetos
const myObject = {"key": "value"};

// Sobrescrever o objeto também falha (no Firefox e Chrome mas não no Safari)
myObject = {"otherKey": "value"};

// Entretando, atributos de objetos não estão protegidos,
// logo a seguinte instrução é executada sem problemas 
myObject.key = "otherValue";  

Links:

"https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/const"

"https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let"
