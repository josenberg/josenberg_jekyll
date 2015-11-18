---
layout: post
title:  "Modules"
date:   2015-10-16 21:25:20
categories: JavaScript 
---

http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html

Modulos são uma padrão comum do JavaScript. Geralmente eles são bem simples, mas existem algumas usos avançados que requerem bastante atenção. Nesse artigo vamos revisar alguns termos basicos mas tamber entender quão poderosos podem ser essa nova feature.
Closures anonimas

É a base de tudo, de longe a melhor feature do Javascript. Nos vamos criar uma função anonima, e executar ela imediatamente. Todo o codigo de dentro dessa função vai rodar em uma closure, oque nos permite ter privacidade e estado durante o tempo de vida da nossa aplicação.

(function (){
    // todas as variaveis e funções de escopo
    // ainda pode acessar as variaveis globais
}())

Perceba que existe um () em torno dessa função. Isso é um exigido pela linguagem, já que todos os lugares onde a keywork function é usada ela é considerada uma função declarativa. Incluindo o () que faz uma function expression.
Global Import

JavaScript é conhecido por ter suas globais. Sempre que um nome é usado, o interpretador procura por todo o escopo uma variavel declarada com esse nome. Se nenhuma for encontrada, então essa variavel é definida como global. Se usada em uma atribuição, e a variavel global ainda não existe. Isso faz com que criar uma variavel global dentro de uma função anonima ser algo trivial. Porem isso é considerado uma pratica ruim, já que torna dificil a manutenção porque requer um grande mapeamento mental.

Para nossa sorte nossas funções anonimas nos permite usar um alternativa. Passando variaveis globais como parametros para nossa função, nos importamos elas para nosso codigo, oque torna mais facil e claro trabalhar com globais.

(function($, YAHOO){
    // Agora temos acessos as globais jQuery(como $) e YAHOO aqui dentro
}(jQuery, YAHOO))

Module Export

Algumas vezes nos não queremos apenas usar globais, mas nos tambem queremos declarar elas. Nos podemos fazer isso facilmente exportando elas, usando um valor de retorno ara essa função anonima. Assim fazemos um exemplo completo do module pattern aqui vai um exemplo completo:

var MODULE = (function(){  
    var my = {};
    privateVariable = 1;

    function privateMethod( ){
        // ..
    }

    my.moduleProperty = 1;
    my.moduleMethod = function(){
        // ...
    }

   return my;
}());

Perceba que nos declaramos uma variavel global chamada MODULE, com duas propriedades publicas: um metodo chamado moduleMethod e uma propriedade chamada moduleProperty. Assim podemos manter um estado privado usando closures como função anonimas. Alem de tambem podermos importar variaveis globais de maneira facil usando a estrutura que aprendemos acima.
Patterns Avançadas

Enquanto a parte mais basica já vai resolver a maior parte dos problemas, nos podemos ir um pouco alem disso e construir coisas realmente poderosas. Vamos começar criando um modulo chamado MODULE.
Augmentation

Uma limitação do module pattern é que todo o modulo deve ser mantido em um unico arquivo. Qualquer um que já tenha trabalhado em um sistema grande sabe a importancia de repartir o codigo em diversos arquivos. Mas isso não é um problema, pois existe uma solução bacana chamada augment modules. Primeiro nos importamos o modulo, adicionamos propriedades à ele, e então exportamos. Aqui vai um exemplo dessa tecnica:

var MODULE = (function(my){  
    my anotherMethod = function(){
        // funcionalidades do metodo
    };

    return my;
}(MODULE));

Nos usamos a keyworkd var por consistencia, mesmo sabendo que não faria diferença já que esse modulo já esta em um escopo global. Depois desse codigo rodar nosso modulo terá ganho um metodo publico chamado .anotherMethod. Essa tecnica tambem pode ser usada para manter estados privados e imports.
Loose augmentation

Enquanto nosso exemplo acima requer que o modulo seja craido primeiro para que aconteça augmentation, isso não é sempre necessario. Uma das melhores partes do JavaScript é que a sua aplicação pode carregar os scripts de forma asyncrona. Assim criamos flexibilidade com modulos que podem importar a si mesmo durante o tempo de execução, isso se chama Loose augmentation. Cada arquivo deve ter a seguinte estrutura.

var MODULE = (function(my){  
    // funcionalidades

    return my;
}(MODULE || {}));

Nesse patern, usar a palavra var é sempre necessaria, preste atenção que nesse caso tambem é importante importar os arquivos caso ele já existe.
Tight Augmentation

Enquanto o loose augmentation é interessante, ele deixa um pouco a desejar quando precisamos dar override em propriedades de modo seguro. Nos tambem não podemos usar propriedades de outros arquivos durante a inicialização. Tight augmentarion é quando você configura uma ordem de carregamento, aqui vai um exemplo:

var MODULE = (function(my){  
    var old_moduleMethod = my.moduleMethod;

    my.moduleMethod = function(){
        // isso sobrescreve o metodo antigo
    }
    return my;
}());

Assim nos damos override do metodo antigo, mas guardamos sua referencia caso precisamos dele para alguma coisa.
Cloning e inheritance

var MODULE_TWO = (function(old){  
    var my = {};
    var key;
    for (key in old){
        if(old.hasOwnProperty(key)){
            my[key] = old[key];
        }
    }

    var super_moduleMethod = old.moduleMethod;
    my.moduleMethod = function(){
        // da override no metodo superior e guard a referencia na variavel super_    
    };
    return my;
}(MODULE))

Esse padrão talvez seja o menos flexivel. Ele nos permite fazer composições bem interessantes, porem ele cobra seu preço na flexibilidade. Assim que forem escritas as propriedades não serão duplicadas, se elas existem em um objeto eles teram propriedades com as mesmas referencias, mudar uma tambem irá mudar a outra. Isso pode ser resolvido usando um processo recursivo de clonar objetos, mas não pode ser usado para funções.
Cross-File Private State

Uma limitação grande que temos quando repartimos um modulo em mais de um arquivo e a perca de estado privado, cada arquivo não terá acesso as variaveis privadas do outro arquivo. Iso pode ser resolvido usando um metodo com loose augmentarion para manter o estado privado por todos os arquivos.

var MODULE = (function(my){  
    var _private = my._private = my._private || {};
    var _seal = my._seal = my._seal || function() {
        delete my._private;
        delete my._seal;
        delete my._unseal;
    },
    var _unseal = my_unseal = my_unseal || function(){
        my._private = _private;
        my._seal = _seal;
        my._unseal = _unseal;
    };


    return my;
}(MODULE || {}))

Submodulos

Nossa ultima pattern é a mais simples. Existem muitos casos onde se é interessante criar um sub-modulo. Faça como você varia com um normal.

var MODULE.sub = (function(){  
    var my = {};
    // ...

    return my;
}());

Pode parecer meio obvio, mas é sempre bom deixar isso explicito.
Conclusões

A maior parte dessas patterns podem ser combinadas para criar padrões ainda mais uteis.

Eu acabei não tocando no assunto performace, porem modulos são extremamente performaticos, eles minificam de maneira bacana. Alem de poder usasr loose augumentation para fazer o download dos scripts de forma assincrona.

// http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html

Vocẽ escreveu isso errado, conteudo antigo, tente encaixar no livro senão apenas delete e GG.
