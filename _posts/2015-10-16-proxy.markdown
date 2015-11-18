---
layout: post
title:  "Proxy"
date:   2015-10-16 21:25:20
categories: JavaScript 
---

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy

'E um espaco reservado para objetos que contem traps
traps

Sao metodos que permitem acesso a suas propriedades. Sao um analogo a traps em sistemas operacionais.
Target

Objeto em que o proxy sera virtualizado. Isso e usado para armazenar as Invariantes de um proxy (semanticas que nao seram mudadas), levando em conta a nao-extentiabilidade ou variaveis de configuracao que serao verificadas de acordo com o target.
Sintaxe

var p = new Proxy(target, handler);  

Parametros

target O objeto target (pode ser qualquer tipo de objeto inclusive um array, uma funcao ou ate mesmo outro proxy) que ira envolver o Proxy.

handler Um objeto cujo a propriedades sao funcoes que serao definidas pelo comportamento do Proxy quando suas operacoes forem executadas.
Metodos

Proxy.revocable()  

Cria um objeto proxy revocable.
Metodos para o objeto handler

O handler object e um espaco para objeto que contem traps para a Proxy.

Todas as traps sao opcionais. Se uma trap nao tiver sido definida o comportamento padrao e seguir com adiante com a operacao no target.

handler.getPrototypeOf() A trap for Object.getPrototypeOf. handler.setPrototypeOf() A trap for Object.setPrototypeOf. handler.isExtensible() A trap for Object.isExtensible. handler.preventExtensions() A trap for Object.preventExtensions. handler.getOwnPropertyDescriptor() A trap for Object.getOwnPropertyDescriptor. handler.defineProperty() A trap for Object.defineProperty. handler.has() A trap for the in operator. handler.get() A trap for getting property values. handler.set() A trap for setting property values. handler.deleteProperty() A trap for the delete operator. handler.enumerate() A trap for for...in statements. handler.ownKeys() A trap for Object.getOwnPropertyNames. handler.apply() A trap for a function call. handler.construct() A trap for the new operator.
Exemplos
Exemplo basico

Nesse exemplo o numero 37 e retornado como valor defult quando a propriedade name nao e um objeto. Usando o handler get.

var handler = {  
    get: function(target, name) {
        return name in target?
            target[name] :
            37;
    }
}
var p = new Proxy({}, handler);  
p.a = 1;  
p.b = undefined;

console.log(p.a, p.b) // 1, undefined  
console.log(`c` in p, p.c)  // false, 37  

No-op forwarding proxy

Nesse exemplo, nos usaremos um objeto nativo do javascript para fazer nossa proxy aplicar alteracoes em nele.

var target = {};
var p = new Proxy(targer, {});

p.a = 37; // Operacao passa para frente no target

console.log(target.a); // 37. A operacao aconteceu com sucesso.
Validacoes

Com um proxy, voce pode facilmetne validar o valor passado para um objeto. Nesse exemplo usaremos um handler set.

let validator = {  
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // The default behavior to store the value
    obj[prop] = value;
  }
};

let person = new Proxy({}, validator);

person.age = 100;  
console.log(person.age); // 100  
person.age = 'young'; // Throws an exception  
person.age = 300; // Throws an exception  

Extendendo um construtor

Uma funcao proxy pode ser facilmente extendia como construtor de um novo construtor. Esse exemplo aplica os handler construct e apply.

function extend(sup,base) {  
  var descriptor = Object.getOwnPropertyDescriptor(
    base.prototype,"constructor"
  );
  base.prototype = Object.create(sup.prototype);
  var handler = {
    construct: function(target, args) {
      var obj = Object.create(base.prototype);
      this.apply(target,obj,args);
      return obj;
    },
    apply: function(target, that, args) {
      sup.apply(that,args);
      base.apply(that,args);
    }
  };
  var proxy = new Proxy(base,handler);
  descriptor.value = proxy;
  Object.defineProperty(base.prototype, "constructor", descriptor);
  return proxy;
}

var Person = function(name){  
  this.name = name
};

var Boy = extend(Person, function(name, age) {  
  this.age = age;
});

Boy.prototype.sex = "M";

var Peter = new Boy("Peter", 13);  
console.log(Peter.sex);  // "M"  
console.log(Peter.name); // "Peter"  
console.log(Peter.age);  // 13  

Manipulando nodes DOM

Algumas vezes nos queremos misturar attributos com o nome das classes de dois elementos diferentes, como nos podemos usar set handler.

let view = new Proxy({  
  selected: null
},
{
  set: function(obj, prop, newval) {
    let oldval = obj[prop];

    if (prop === 'selected') {
      if (oldval) {
        oldval.setAttribute('aria-selected', 'false');
      }
      if (newval) {
        newval.setAttribute('aria-selected', 'true');
      }
    }

    // The default behavior to store the value
    obj[prop] = newval;
  }
});

let i1 = view.selected = document.getElementById('item-1');  
console.log(i1.getAttribute('aria-selected')); // 'true'

let i2 = view.selected = document.getElementById('item-2');  
console.log(i1.getAttribute('aria-selected')); // 'false'  
console.log(i2.getAttribute('aria-selected')); // 'true'  

Correcoes de valores e uma propriedade extra

Os produtos de um objeto proxy, avalia o valor passado e converte ele para um array caso seja necessario.
O objeto tambem suporta uma extra propriedade chamada latestBrowser, ambos como um getter e um setter.

let products = new Proxy({  
  browsers: ['Internet Explorer', 'Netscape']
},
{
  get: function(obj, prop) {
    // An extra property
    if (prop === 'latestBrowser') {
      return obj.browsers[obj.browsers.length - 1];
    }

    // The default behavior to return the value
    return obj[prop];
  },
  set: function(obj, prop, value) {
    // An extra property
    if (prop === 'latestBrowser') {
      obj.browsers.push(value);
      return;
    }

    // Convert the value if it is not an array
    if (typeof value === 'string') {
      value = [value];
    }

    // The default behavior to store the value
    obj[prop] = value;
  }
});

console.log(products.browsers); // ['Internet Explorer', 'Netscape']  
products.browsers = 'Firefox'; // pass a string (by mistake)  
console.log(products.browsers); // ['Firefox'] <- no problem, the value is an array

products.latestBrowser = 'Chrome';  
console.log(products.browsers); // ['Firefox', 'Chrome']  
console.log(products.latestBrowser); // 'Chrome'  

Encontrando o item de um array por sua propriedade

Esses proxys podem extender um array para usar algumas funcoes utilitarias. Como voce pode ver, voce pode de maneira flexivel usando o Object.defineProperties. Esse exemplo pode ser adaptado para achar a celula de uma tabela, nesse caso o target seria algo como table.rows.

let products = new Proxy([  
    { name: 'Firefox', type: 'browser' },
    { name: 'SeaMonkey', type: 'browser' },
    { name: 'ThunderBird', type: 'mailer' }
], {
get: function(obj, prop) {  
    // The default behavior to return the value; prop is usually an integer
    if (prop in obj) {
      return obj[prop];
    }

    // Get the number of products; an alias of products.length
    if (prop === 'number') {
      return obj.length;
    }

    let result, types = {};

    for (let product of obj) {
      if (product.name === prop) {
        result = product;
      }
      if (types[product.type]) {
        types[product.type].push(product);
      } else {
        types[product.type] = [product];
      }
    }

    // Get a product by name
    if (result) {
      return result;
    }

    // Get products by type
    if (prop in types) {
      return types[prop];
    }

    // Get product types
    if (prop === 'types') {
      return Object.keys(types);
    }

    return undefined;
    }

});


console.log(products[0]); // { name: 'Firefox', type: 'browser' }  
console.log(products['Firefox']); // { name: 'Firefox', type: 'browser' }  
console.log(products['Chrome']); // undefined  
console.log(products.browser); // [{ name: 'Firefox', type: 'browser' }, { name: 'SeaMonkey', type: 'browser' }]  
console.log(products.types); // ['browser', 'mailer']  
console.log(products.number); // 3  

````````
Uma lista completa de traps

Agora vamos uma lista completa com exemplos de traps, para uso didatico, nos vamos tendar fazer proxy em um objeto nao nativo chamada docCookies.

/*
  var docCookies = ... get the "docCookies" object here:  
  https://developer.mozilla.org/en-US/docs/DOM/document.cookie#A_little_framework.3A_a_complete_cookies_reader.2Fwriter_with_full_unicode_support
*/

var docCookies = new Proxy(docCookies, {  
  "get": function (oTarget, sKey) {
    return oTarget[sKey] || oTarget.getItem(sKey) || undefined;
  },
  "set": function (oTarget, sKey, vValue) {
    if (sKey in oTarget) { return false; }
    return oTarget.setItem(sKey, vValue);
  },
  "deleteProperty": function (oTarget, sKey) {
    if (sKey in oTarget) { return false; }
    return oTarget.removeItem(sKey);
  },
  "enumerate": function (oTarget, sKey) {
    return oTarget.keys();
  },
  "ownKeys": function (oTarget, sKey) {
    return oTarget.keys();
  },
  "has": function (oTarget, sKey) {
    return sKey in oTarget || oTarget.hasItem(sKey);
  },
  "defineProperty": function (oTarget, sKey, oDesc) {
    if (oDesc && "value" in oDesc) { oTarget.setItem(sKey, oDesc.value); }
    return oTarget;
  },
  "getOwnPropertyDescriptor": function (oTarget, sKey) {
    var vValue = oTarget.getItem(sKey);
    return vValue ? {
      "value": vValue,
      "writable": true,
      "enumerable": true,
      "configurable": false
    } : undefined;
  },
});

/* Cookies test */

console.log(docCookies.my_cookie1 = "First value");  
console.log(docCookies.getItem("my_cookie1"));

docCookies.setItem("my_cookie1", "Changed value");  
console.log(docCookies.my_cookie1);  

