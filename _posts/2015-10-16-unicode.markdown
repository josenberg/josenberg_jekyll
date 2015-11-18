---
layout: post
title:  "Unicode"
date:   2015-10-16 21:25:20
categories: JavaScript 
---

// Explicaçar oque é unicode
Melhorias no objeto String

Nos já vimos templates literals nos capitulos anterios, e talvez você se lembre que eles podem ser usados para fazer um mix entre strings e variaveis para produzir uma nova string.

function greet(name){  
    return `hello ${name}`;
}
greet('ponyFoo');  
// <- "Hello ponyfoo!"

Alem dos template literals, diversos metodos foram adicionados à strings no ES6. Eles podem ser dividos entre metodos de manipulação, e metodos unicode.

Manipulação de strings

* String.prototype.startsWith
* String.prototype.endsWith
* String.prototype.includes
* String.prototype.repeat
* String.prototype[Symbol.iterator]

Unicode * String.prototype.codePointAt * String.fromCodePoint * String.prototype.normalize

Nos iremos começar com os de manipulamento de strings, e depois olharesmos os de unicode.
String.prototype.startsWith

Uma função comum quando se trata de strings é se determinada string começa com outra string, no ES5 nos podiamos descobrir isso usando o metodo .indexOf().

'ponyfoo'.indexOf('foo');  
// <- 4
'ponyfoo'.indexOf('pony')  
// <- 0 
'ponyfoo'.indexOf('horse');  
// <- -1

Se nos precisassemos saber se uma string começa com a outra, teriamos que pegar o resultado desse metodo e comparar se ele é igual à zero (ele retorna a posição na qual a outra string se encontra, e o primeiro caracter da string esta na posição zero).

'ponyfoo'.indexOf('pony') === 0  
// <- true
'ponyfoo'.indexOf('foo') === 0  
// <- false
'ponyfoo'.indexOf('horse') === 0  
// <- false

Agora você pode usar um metodo mais simples para fazer isso, o .startsWith

'ponyfoo'.startsWith('pony')  
// <- true
'ponyfoo'.startsWith('foo')  
// <- false
'ponyfoo'.startsWith('horse')  
// <- false

No caso do index, se nos precisassemos saber se uma string começa em determinada posição de outra nos teriamos que fazer:

'ponyfoo'.slice(4).indexOf('foo') === 0  
// <- true

A razão pela qual não podemos usar === 4 é porque essa comparação pode gerar falsos negativos, quando a query é encontrada antes de atingir determinado index:

'foo,foo'.indexOf('foo') === 4  
// <- false, because result was 0

É claro que você poderia usar o parametro startIndex do indexOf para evitar isso. Note que nos ainda estamos comparando ele com 4 nesse caso, porque a string não estava dividida em partes menores.

'foo,foo'.indexOf('foo', 4) === 4  
// <- true

Melhor do que guardar todos esses detalhes na sua cabeça e ter que mapear esses pensamentos antes de manipular uma string, você pode começar a usar o startsWith passando um parametro para o startIndex tambem.

'foo,foo'.startsWith('foo', 4)  
// <- true

Sem duvidas é um pouco confuso que esse metodo se chame startWith e receba um argumento como startIndex, mas sem duvidas ele é bem melhor que o indexOf quando precisamos retornar um booleano.
String.prototype.endsWith

Esse metodo é praticamente uma versão espelhada do startWith, e trabalha da mesma maneira que o .lastIndexOf. Ele nos diz se uma string termina com a outra.

'ponyfoo'.endsWith('foo')  
// <- true
'ponyfoo'.endsWith('pony')  
// <- false

Assim como no .startWith, nos temos uma posição do index em que ele pode considerar o fim da string, o valor default é o tamanho dessa string.

'ponyfoo'.endsWith('foo', 7)  
// <- true
'ponyfoo'.endsWith('pony', 0)  
// <- false
'ponyfoo'.endsWith('pony', 4)  
// <- true

Outra solução que simplifica o uso do .indexOf é a .includes
String.prototype.includes

Você pode usar o .includes para verificar se uma string contem outra.

'ponyfoo'.includes('ny')  
// <- true
'ponyfoo'.includes('sf')  
// <- false

Esse o equivalente para o uso do indexOf da ES5 quando comparamos o indexOf é comparado com -1

'ponyfoo'.indexOf('ny') !== -1  
// <- true
'ponyfoo'.indexOf('sf') !== -1  
// <- false

Naturalmente você tambem pode pessar um parametro para definir onde essa busca irá começar.

'ponyfoo'.includes('ny', 3)  
// <- false
'ponyfoo'.includes('ny', 2)  
// <- true

Pronto, agora veremos uma função que não veio para substituir o .indexOf
String.prototype.repeat

Esse metodo te permite repetir uma string por count vezes.

'na'.repeat(0)  
// <- ''
'na'.repeat(1)  
// <- 'na'
'na'.repeat(2)  
// <- 'nana'
'na'.repeat(5)  
// <- 'nanananana'

O valor de count deve ser positivo e um numero finito.

'na'.repeat(Infinity)  
// <- RangeError
'na'.repeat(-1)  
// <- RangeError

Ele irá tentar converter valores não numeros para numeros inteiros.

'na'.repeat('na')  
// <- ''
'na'.repeat('3')  
// <- 'nanana'

Usar um NaN é a mesma coisa que usar 0

'na'.repeat(NaN)  
// <- ''

Valores decimais são arrendondados para baixo:

'na'.repeat(3.9)  
// <- 'nanana', count foi arrendondado para 3

Valores entre (-1. 0) serão arredondados para -0 porque o count passado pelo ToInteger, como especificado na documentação. A etapa que seta o valor especificado seria alguma coisa parecida com a formula abaixo:

function ToInteger (number) {  
  return Math.floor(Math.abs(number)) * Math.sign(number)
}

O codigo acima converte qualquer numero entre (-1, 0) para -0. Numeros abaixo disso não irão funcionar como o esperado, porque eles não podem ser arredondados pelo Math.floor, já que são valores negativos.

'na'.repeat(-0.1)  
// <- '', conta como se estivesse sido arredondado para -0

Um bom exemplo do uso de .repeat, talvez seja seu metodo "padding", O metodo abaixo recebe strings de varias linas e faz cria espaço para alinha-as.

function pad(text, spaces){  
    return text.split('\n').map(line => ' '.repeat(spaces) + line).join('\n');
}
pad('a\n\b\nc', 2);  
// <- ' a\n b\n c'

String.prototype[Symbol.iterator]

Antes da ES6, você podia acessar cada char da string via seus indices - algo parecido com arrays. Isso significa que você podia fazer loops com strings:

var text = 'foo';  
for(let i = 0; i < text.length; i++){  
    console.log(text[i]);
}

No ES6, você irá poder fazer loop sobre os code points da uma string usando o for ... of, porque strings são intineraveis.

for (let codePoint of 'foo'){  
    console.log(codePoint);
}

O que é uma variavel codePoint? existe uma pequena diferença entre code points e code units. Vamos mudar os protocolos e falar sobre Unicode.
Unicode

Strings no JavaScript são representadas usando UTF-16 code units. Cada code unit pode ser usado para representar um code point entre [U+0000, U+FFFF] - tambem conhecido como "basic multilingual plane" (BMP). Você pode representar individualmente esses pontos no BMP usando a syntax '\u3456'. Você tambem pode representar os codigos usando seus unit code usando a notação \x00 ... \xff``. Para representar o "»" por exemplo, nos podemos usar'\xbb'(187), como nos podemos ver que você pode fazerparseInt('bb', 16)` ou String.fromCharCode(187).

Para codigos acima de U+FFFF você pode representalos usando um pair surrogate. Isso é a mesma coisa que dizer, por exemplo, o emoticon de cavalo '🐎', no qual seu code point é representado por '\ud83d\udc0e' continuamente code units. Na Notação da ES6 você tambem pode representar esse emoticon usando a notação '\u{1f40e}' (tambem o emoticon do cavalo). Note que a representação interna não mudou, isso continua usando dois codeUnits então logicamente, '\u{1f40e}'.length é equivalente à 2.

A string '\ud83d\udc0e\ud83d\udc71\u2764' encontra alguns emoticons.

'\ud83d\udc0e\ud83d\udc71\u2764'  
// <- '🐎👱❤'

Enquanto a string tem cinco code units, nos sabemos que o tamanho dela deveria ser tres - ela representa tres emoticons.

'\ud83d\udc0e\ud83d\udc71\u2764'.length  
// <- 5
'🐎👱❤'.length  
// <- ainda equivale à 5

Antes do ES6, não era possivel para o JavaScript entender essas comportamento dessas coisas do unicode. Então nos tinhamos que 'contar' esses caracteres nos mesmo, o mesmo que contar cartas (ou code points). Pegue uma instancia de Object.keys, ela continua com cinco code units de tamanho.

console.log(Object.keys('🐎👱❤'));  
// <- ['0', '1', '2', '3', '4']

Se nos voltarmos para nosso loop, agora nos vamos perceber onde existe o problema, nos queremos '🐎', '👱', '❤', mas nos não recebemos isso.

var text = '🐎👱❤'  
for (let i = 0; i < text.length; i++) {  
  console.log(text[i])
  // <- '?'
  // <- '?'
  // <- '?'
  // <- '?'
  // <- '❤'
}

No lugar disso nos pegamos algumas caixinhas estranhas de unicode, e isso se nos formos sortudos e estivermos olhando no firefox.

Isso é errado, por isso no ES6 nos podemos usar um intinerador de strings passando por code points. Esses intineradores sabem dessa limitação então eles fazem yield com os code points.

for (let codePoint of '🐎👱❤'){  
    console.log(codePoint);
    // <- 🐎
    // <- 👱
    // <- ❤
}

Se nos quisermos medir o comprimento dessa string, nos teremos problemas usando a propriedade length. Nos podemos usar um intinerador para cortar as strings por code points - como vimos no exemplo de for..of. Isso significa que teriamos o tamanho exato independente se ela fosse unicode ou não. Ou nos podemos usar um operador spread. Para colocar nosso codigo dentro de um array, e usar o lenght desse array que será correto.

[...'🐎👱❤'].length

Mas não se esqueça que se você quiser dividir uma string em code points de maneira 100% precisa, isso não será o suficiente. Veja por exemplo o `“combining overline” \iu0305``. O representa apenas um overline

'\u0305'  
// <- ' ̅'

Tentativas de pegar o comprimento dessa strings funcionam corretamente usando o lenght.

'foo\u0305'.length  
// <- 4
'foo̅'.length  
// <- 4
[...'foo̅'].length
// <- 4

Isso pode gerar alguns problemas, mas de qualquer forma vamos ver outros metodos.
String.prototype.codePointAt

Você pode usar a função .codePointAt para pegar a representação numerica em base 10 de um code point. Note que uma string é indexida por code unit, e não por code point. No exemplo vamos usar a mesma string do exemplo passado, e mandar ele printar os codigos numericos dos nossos emoticons.

'\ud83d\udc0e\ud83d\udc71\u2764'.codePointAt(0)  
// <- 128014
'\ud83d\udc0e\ud83d\udc71\u2764'.codePointAt(2)  
// <- 128113
'\ud83d\udc0e\ud83d\udc71\u2764'.codePointAt(4)  
// <- 10084

Descobrir esses indices pode ser um problema, por isso nesses casos devemos montar um loop e tratar essa string com mais cuidado e chamar um codePointAt(0) para cada codePoint da sequencia.

for (let codePoint of '\ud83d\udc0e\ud83d\udc71\u2764'){  
    console.log(codePoint.codePointAt(0));
}

Ou simplesmente usar uma combinação de spread e .map

[...'\ud83d\udc0e\ud83d\udc71\u2764'].map(cp => cp.codePointAt(0))
// <- [128014, 128113, 10084]

Você pode pegar a representação hexadecimal (base-16) desses numeros com base 10 e escrever eles usando a sintaxe `\u{codePoint}``. Essa sintax nos permite representar os codigos alem do “basic multilingual plane” (BMP).

Vamos atualizar o exemplo acima para printar a versão em hexadecimal do nosso codigo

for (let codePoint of '\ud83d\udc0e\ud83d\udc71\u2764') {  
  console.log(codePoint.codePointAt(0).toString(16))
  // <- '1f40e'
  // <- '1f471'
  // <- '2764'
}

Você pode colocar essa variavel dentro de '\u{codePoint}' e pronto, você já terá seus emojis.

'\u{1f40e}'  
// <- '🐎'
'\u{1f471}'  
// <- '👱'
'\u{2764}'  
// <- '❤'

String.fromCodePoint

Esse metodo recebe um numero e retorna o code point. Note como eu posso usar o prefixo 0x em base -16 que nos descobrir no metodo acima.

String.fromCodePoint(0x1f40e)  
// <- '🐎'
String.fromCodePoint(0x1f471)  
// <- '👱'
String.fromCodePoint(0x2764)  
// <- '❤'

Obviamente você tambem pode passar um valor com base-10 para atingir o mesmo resultado.

String.fromCodePoint(128014)  
// <- '🐎'
String.fromCodePoint(128113)  
// <- '👱'
String.fromCodePoint(10084)  
// <- '❤'

Você pode passar quantos points você quiser.

String.fromCodePoint(128014, 128113, 10084)  
// <- '🐎👱❤'

Revistar a pagina e verificar se é realmente necessario todo o conteudo original.
String.prototype.normalize

Existem diversas maneiras de representar strings que parecem ser iguais ao olhar humano mesmo que seus codePoints sejam diferentes.

'mañana' === 'mañana'  
// <- false

O que se passa na comparação acima é que nos temos uma combinação do til com a letra n na direita, quando na esquerda temos apenas um ñ. Eles parecem ser iguais, mas se você olhar seus codePoints você vai perceber que são diferentes.

[...'mañana'].map(cp => cp.codePointAt(0).toString(16))
// <- ['6d', '61', 'f1', '61', '6e', '61']
[...'mañana'].map(cp => cp.codePointAt(0).toString(16))
// <- ['6d', '61', '6e', '303', '61', '6e', '61']

Assim como no exemplo com 'foo̅', a segunda string tem o comprimento de 7, mesmo que visualmente ela só tenha 6 simbolos.

'mañana'.length  
// <- 6
'mañana'.length  
// <- 7

Se nos usarmos o metodo .normalize na segunda string teremos uma versão igual que tinhamos na primeira.

var normalized = 'mañana'.normalize()  
[...normalized].map(cp => cp.codePointAt(0).toString(16))
// <- ['6d', '61', 'f1', '61', '6e', '61']
normalized.length  
// <- 6

Essas são todas as funções adicionadas com unicode no ES6, como vocês puderam notar elas não são usadas no daily basis de um programador, porem unicode é uma area comum para ter bugs sinistros, é importante saber como as strings funcionam para poder solucionar bugs que parecem ser sem solução.
