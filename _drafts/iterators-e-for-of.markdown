---
layout: post
title:  "iterators & for..of"
date:   2015-10-16 21:25:20
categories: JavaScript 
---



Esse artigo foi baseado em um artigo em ingles, qualquer duvida verifique o artigo original.

Como você faz um loop com os elementos de um array? Quando JavaScript foi criado, vinte anos atras, você faria algo parecido com isso:

for(var index = 0; index < myArray.length; index++){  
    console.log(myArray[index]);
}

Desde a ES5 você pode usar a função forEach:

myArray.forEach(function(value){  
    console.log(value);
});

Isso deixa as coisas mais simples, porem ainda existem alguns problemas, você não pode usar break para parar a execução dessa função, nem fazer isso usando return;

Sem duvidas seria interessante se existisse um for que simplesmente intineresse pelos elementos do seu array.

Surge então, o loop for-in:

for (var index in myArray){ // Não faça isso  
    console.log(myArray[index]);
}

Porem isso é uma ideia ruim por diversas razões:
* Os valores que forem atribuidos para o index são strings, e não numeros; Então você não podera usa-las para fazer operações matematicas (seria algo como "2" + 1 "21"), isso na melhor da hipoteses é um grande incoveniente. * O loop será executado não apenas nos elementos desse array, mas tambem em suas propriedades não numerica do tipo myArray.name, essa propriedade irá ser intinerada no loop, com o index "name". Até mesmo propriedades do prototype podem ser acessadas. * Por mais estranho que pareça, em alguns casos ele não ira fazer o loop em uma ordem que você não espera.

Resumindo, for-in é uma ferramenta bacana para trabalhar com objetos simples com keys do tipo string. Para Arrays ele não é uma solução muito bacana.
A ascenção do for-of loop

O EC6 não deve quebrar nenhuma das funções anteriores da linguagem, então em vez de 'consertar' o for-in (o que iria quebrar milhares de sites que hoje usam a função de maneira 'errada'), então um novo operador foi criado, ele é o for-of, veja abaixo sua sintaxe:

for (var value of myArray){  
    console.log(value);
}

Imagine o for-of como um irmão bem sucedido do for-in a sintaxe dele quase a mesma, porem existem algumas vantagens
* Ele é uma sintaxe mais simples de fazer o loop pelos elementos de um array. * Ele não tem os problemas do irmão for-in * Ao contrario do forEach(), você pode usar break, continue, e return.

O for-in faz um loop pelas propriedades de um objeto.
O for-of faz um loop pelos dados de um array.

Mas isso não é tudo.

Outras coleções suportadas pelo for-of

for-of pode ser usados por outras coisas, ele tambem funciona em objetos array-like como DOM ou NodeLists.

Ele tambem funciona em strings, tratando elas como uma sequencia de characters Unicode.

for(var chr of "Caracter em japones"){  
    alert(chr);
}

Tambem existem jeitos de usar o for-of usando objetos do tipo Map e Set, mas isso é assunto para outro dia.

Entenda como funciona a familia for-in e for-of e use-os de maneira adequada, antes de trocar um for-in por um for-of faça alguns testes e verifique se ele terá realmente o mesmo comportamento que você deseja.
