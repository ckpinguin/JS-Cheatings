<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [JS-Learnings/Cheatings](#js-learningscheatings)
- [Vererbung vs. Komposition/Delegation => always use the composition/delegation paradigma](#vererbung-vs-kompositiondelegation--always-use-the-compositiondelegation-paradigma)
  - [Function overwriting => don't do it](#function-overwriting--dont-do-it)
  - [Semikolons?](#semikolons)
  - [prototype vs. `__proto__`](#prototype-vs-__proto__)
    - [prototype](#prototype)
      - [Anwendungsbeispiel](#anwendungsbeispiel)
      - [`prototype.constructor`?](#prototypeconstructor)
    - [`̲__proto__`](#%CC%B2__proto__)
  - [3 types of prototypal inheritance](#3-types-of-prototypal-inheritance)
    - [Factory functions](#factory-functions)
    - [Mixin style](#mixin-style)
    - [Functional Inheritance](#functional-inheritance)
  - [(Behaviour)-Delegation & `[[Prototype]]`](#behaviour-delegation--prototype)
  - [`new` vs. `Object.create()` resp. `Object.create()` vs. `Object.setPrototypeOf()`](#new-vs-objectcreate-resp-objectcreate-vs-objectsetprototypeof)
  - [`instanceof`](#instanceof)
  - [(Lexical) Closures](#lexical-closures)
    - [Beispiel: Closures für private Daten](#beispiel-closures-f%C3%BCr-private-daten)
  - [`this` Binding](#this-binding)
    - [Vor ES6](#vor-es6)
    - [Ab ES6](#ab-es6)
    - [Beispiel explizites `this`-Binding](#beispiel-explizites-this-binding)
    - [Beispiel hartes explizites `this`-Binding](#beispiel-hartes-explizites-this-binding)
    - [Beispiel lexikales `this`-Binding](#beispiel-lexikales-this-binding)
    - [Currying & Spreading mit `apply` & `bind`](#currying--spreading-mit-apply--bind)
      - [Null-Objekt als `this` zur Sicherheit](#null-objekt-als-this-zur-sicherheit)
  - [Immediately Invoked Function Expression (IIFE)‣](#immediately-invoked-function-expression-iife%E2%80%A3)
  - [Variable-Hoisting](#variable-hoisting)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# JS-Learnings/Cheatings
My personal JS-Cheatsheet reflecting my learnings about the nuts & bolts of JS/ECMA whatever you prefer calling it. It is currently in German, because that fits better in my brain.
A lot of credit for my understanding of JS goes to the »[You Don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/README.md)« book series as well as mpjme's Youtube-Series »[funfunfunction](https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q)«. Also not to forget [Mozilla's really great »MDN« JavaScript Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript).

If you need a really professional short-oversight of core Javasript, look here http://dmitrysoshnikov.com/ecmascript/javascript-the-core/

If this page becomes a useful cheatsheet (at least for me), I might try to translate in to English in the future God willing...

# Vererbung vs. Komposition/Delegation => always use the composition/delegation paradigma
Um es kurz zu machen: Man sollte immer Objekt-Komposition verwenden also _OOLO_ (_Objects Link to Other Objects_). Vererbung ist ein künstlich aufgesetztes Konzept in JavaScript, um mehr »klassische« OO-Programmierer anzulocken. Das Thema hier in aller Kürze auszuführen, dürfte schwierig werden, vielleicht später ;-)
## Function overwriting => don't do it
Auch wenn man Delegation nutzt, können in der sogenannten _``[[Prototype]]``-chain_ Funktionen/Methoden überschrieben werden. Das solltem man möglichst nie tun! Stattdessen soll jede Funktion eine wohldefinierte Aufgabe erledigen und dies soll sich auch in ihrem Namen widerspiegeln.

## Semikolons?
Was viele nicht wissen: JS fügt automatisch fehlende Semikolons hinzu! Das führt i.d.R. zu keinen Problemen, ausser: wenn eine öffnende Klammer am Anfang einer Linie steht (kann z.B. passieren, wenn eine [_IIFE_](https://github.com/ckpinguin/JS-Cheatings/blob/master/README.md#immediately-invoked-function-expression-iife) verwendet wird).
Es ist eine Frage des persönlichen Geschmacks resp. Faulheit, ob man ; setzt oder nicht.

## prototype vs. `__proto__`
### proto
Kurzdefinition: `__proto__` ist das Objekt, welches effektiv in der _Lookup chain_ benutzt wird, um Methoden, Properties etc. aufzulösen. `prototype` hingegen ist das Objekt, welches dazu benutzt wird, den `__proto__` zu erstellen, wenn ein Object mit `new` kreiert wird.

### prototype
**Nur Funktionen** haben das _accessor-property_ namens `prototype`. Es hat nichts mit der Prototype-Lookup chain (siehe  [Link](#proto) ) zu tun, sondern wird (vermutlich)  nur für die `new` Funktionalität genutzt, weil es auf den jeweiligen `contstructor` der zu erzeugenden Klasse zeigt, welcher wiederum nichts anderes als die Funktionsdefinition mit dem Klassennamen ist. Damit werden Klassen simuliert mittels Funktionen. Objekte, welche mit `new` erzeugt wurden, haben dieses Property nicht, aber ihr `__proto__` zeigt auf das entsprechende Objekt, z.B. `Foo.prototype`. Ein Bild kann vielleicht helfen in der Verwirrung: 
![Contsructor Proto Chain (copyright by Dmitry Soshnikov)](https://github.com/ckpinguin/JS-Cheatings/blob/master/constructor-proto-chain.png "Contsructor Proto Chain (copyright by Dmitry Soshnikov), see: http://dmitrysoshnikov.com/ecmascript/javascript-the-core/#constructor")

#### Anwendungsbeispiel
Erweiterung einer Funktion (resp. Fake-Klasse, da es nur eine Funktion mit grossem Anfangsbuchstaben ist) um weitere Methoden:
```javascript
Cat.prototype.say = function(word) {
    console.log('Miau', word);
}
```
Info: `Function` (das Objekt) hat `function` (die JS-Primitive) als `prototype`, während `Object` selber `Object` als `prototype` hat. Wenn allerdings eine Funktion mit `new Function()` definiert wird, hat diese wiederum `Object` als `prototype`. Verwirrend? Ja, denn der Name `prototype` ist sehr leicht mit dem `__proto__` resp. dem echten _Prototype-Behaviour-Delegation_-Mechanismus zu verwechseln (welcher `__proto__` nutzt), hat damit jedoch nichts zu tun! Es wird noch lustiger, wenn man `Object.getPrototypeOf(Function.prototype)` usw. anschaut, d.h. den `__proto__` Delegations-Mechanismus für das `prototype`-Property eines Objektes verwendet. Muahahaha.
Vererbung wird bspw. mit `Button.prototype = Object.create(Widget.prototype)` simuliert und benötigt vor ES6 übel aussehende Hacks bez. `call()` und `apply()` für `super()`-Aufrufe oder überschriebene Methoden. Dass dies ab ES6 mit einfacherer Syntax funktioniert löst nicht das eigentliche Problem, dass die OO-Programmierung resp. Klassenorientierung in JS künstlich aufgesetzt ist.

#### `prototype.constructor`?
Ein Merksatz vorausgeschickt: Es gibt in JavaScript keine Konstruktoren oder Konstruktor-Funktionen. Es gibt nur normale Funktionen, die per `new` aufgerufen (oder besser: hijacked?) werden, also _constructor calls_.

`prototype.constructor` ist nichts weiter als eine Referenz zu der Funktion, welche per `new` ein Objekt „konstruierte“ (das trifft nicht in allen Fällen zu, also wieder ein relativ unbrauchbares „Feature“, um Klassen zu faken):
```javascript
function Foo() {
...
}
Foo.prototype.constructor === Foo; // true

var a = new Foo();
a.constructor === Foo; // true
```
Hier werden wir allerdings hinters Licht geführt, denn das Objekt `a` besitzt kein Property `constructor`, die Auflösung funktioniert nur wegen der Delegation an `Foo`. `Foo` ist kein Konstruktor und auch keine Klasse, sondern eine normale Funktion mit einem grossen Anfangsbuchstaben. Der Aufruf von `new` trägt diese Referenz ein. Beide Namen, `prototype` und `constructor` scheinen fast absichtlich so verwirrend benannt worden zu sein.

Fazit: Hier wurde also keine Klasse instanziert oder ein Objekt aus einer „Vorlage“ kopiert, aber die Semantik von `new` und `class` etc. versuchen, genau dies vorzuspiegeln. Ein Grund dafür, dass ES6 all dies offiziell unterstützt ist sicherlich, dass die meisten Programmierer in Objektorientierung ausgebildet wurden und mit der Delegation und Functional Programming vorerst nicht viel anfangen können (ich gehörte auch dazu). Wir sollten besser _Factory-Funktionen_ benutzen. Sie sind einfach Konstruktor-Funktionen ohne `new`-Zwang und ohne die Gefahr, dass Elemente wie `this` usw. plötzlich global neu definiert werden, wenn _strict mode_ nicht genutzt wird.
[Dan Abramov's pragrmatischer Ansatz](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4#.1ftpmlpau) in dieser Diskussion: Nutze Klassen, wenn Du musst, z.B. in React.js aber generiere auf keinen Fall Hierarchien damit und rufe niemals `super()` auf!

### `̲__proto__`
Jedes Objekt (ausser Object) hat das `__proto__` Property. Dabei handelt es sich um das Zentrum des sogenannten _`[[Prototype]]`-Delegation_ oder auch _Behaviour-Delegation_-Mechanismus von JS. Es zeigt auf das jeweils gelinkte Objekt eines Objekts.
So kann eine Link-Kette entstehen, die in jedem Fall bei `Object` endet, welches einige Standardproperties und Funktionalitäten anbietet; wie eben `__proto__`, welches ganz genau gesehen Teil des `Object`-Prototyps ist: `Object.prototype.__proto__`, d.h. es ist eine Methode von Object. Durch das lexikale `this`-Binding von Javascript wird immer der Aufrufkontext in `this` gebunden, wodurch die Behaviour-Delegation sehr elegant brauchbar wird. Interessanterweise zeigt `__proto__` von Object und Function jeweils auf `function` (alles Kleinbuchstaben), womit gezeigt ist, dass JS tatsächlich eine rein „funktionale“ Sprache ist, die man aber auch als „Objektorientiert“ im richtigen Sinn (Objekte, keine Klassen!) bezeichnen darf.
State (Variablen) sollte immer in den delegierenden Objekten sein, nicht in den „Empfänger“-Objekten. Programmatisch kann man `__proto__` mit `Object.getPrototypeOf(obj)` sowie `Object.isPrototypeOf(obj, proto)` und `Object.setPrototypeOf(obj, proto)` nutzen. Laut »You Dont't Know JS« Büchern ist der Begriff _OOLO_ (_Objects Link to Other Objects_) angezeigt, um das komplett von _OO_ verschiedene Design zu bezeichnen.

## 3 types of prototypal inheritance
Credit goes to [Eric Elliott](https://medium.com/javascript-scene/3-different-kinds-of-prototypal-inheritance-es6-edition-32d777fa16c9#.laor60hxv)

### Factory functions
Factory-Funktionen sind die bessere Alternative zu Konstruktor-Funktionsaufrufen. Das `this`-Binding ist korrekt (niemals versehentlich global) und man wird nicht zum Verwenden von `new` gezwungen.
```javascript
// The prototype
const proto = {
    hello () {
        return `Hello, my name is ${ this.name }`;
    }
};

// The factory
// Simplified variant using ES6 fat-arrow function syntax
// Property delegation to `proto` can be used with `Object.create(proto)`
const greeter = (name) => Object.assign(Object.create(proto), {
    name
});

// Using the factory
const george = greeter('george');

// Using the prototype function on the generated object
console.log( george.hello() );
```
### Mixin style
```javascript
const proto = {
    hello: function hello() {
        return `Hello, my name is ${ this.name }`;
    }
};

// Here we copy the proto properties instead of referencing them
const george = Object.assign({}, proto, {name: 'George'});

const msg = george.hello();

console.log(msg);
```
Dies ist nützlich, wenn ein State verwendet wird im Prototyp, denn bei der Referenzierung würde immer der Prototyp selber verändert werden. Hier hingegen wird alles kopiert und pro Objekt geführt. So kann nun jedes beliebige Objekt per Mixin in ein anderes verwandelt werden, z.B. indem ein Event-Emitter-Object hineingemixt resp. -kopiert wird. Das wäre mit `[[Prototype]]`-Delegation nicht so einfach möglich, weil in einem Event-Emitter Stati genutzt werden.

### Functional Inheritance
Man nutzt hier eine Funktion anstatt eines Objekts zur Erweiterung. Eine Funktion ist per Definition eine Closure somit erhalten wir damit private Stati. Beispiel (ohne Erklärung):
```javascript
import Events from 'eventemitter3';

const rawMixin = function () {
    const attrs = {};

    return Object.assign(this, {
        set (name, value) {
            attrs[name] = value;

            this.emit('change', {
                prop: name,
                value: value
            });
        },

        get (name) {
            return attrs[name];
        }
    }, Events.prototype);
};

const mixinModel = (target) => rawMixin.call(target);

const george = { name: 'george' };
const model = mixinModel(george);

model.on('change', data => console.log(data));

model.set('name', 'Sam');
```

## (Behaviour)-Delegation & `[[Prototype]]`
`[[Prototype]]` ist eine Bezeichnung aus der ECMA-Spezifikation. Es handelt sich um eine simple __Referenz zu einem anderen Objekt__. Jedes Objekt erhält so eine Referenz zu seiner Erstellungszeit (Ausnahmen sind möglich aber selten). Durch diese Referenz wird Funktionalität resp. Verhalten (Behaviour) delegiert an andere gleichberechtigte Objekte. Es gibt im Gegensatz zu OO-Sprachen keine Eltern-Kind-Beziehung! Wenn ein Property resp. eine Methode eines Objektes aufgerufen wird und dieses in diesem Objekt nicht direkt auffindbar ist, wird automatisch sein `[[Prototype]]` durchsucht. Dieses Objekt hat wiederum einen `[[Prototype]]` gesetzt usw. So entsteht eine Delegationskette bis zu `Object.prototype`, wo einige grundsätzliche Methoden und Properties definiert sind. Beispiel:
```javascript
const cat = {
    sayHello: function() {
        console.log('Meow, my name is ' + this.name);
    },
    breed: 'Strassenkatze'
};
const myCat = Object.create(cat);
myCat.name = 'Flauschi';
myCat.sayHello();
```
Man könnte vereinfacht sagen, dass OO-Design oder genauer Vererbung bedeutet, dass eine Applikation um Komponenten designt wird, auf Basis ihres Typs, also was sie sind. Demgegenüber wird bei der Delegation resp. Komposition designt um Komponenten auf Basis dessen, was sie tun (Funktionalität). Der Unterschied hat nicht nur mit Typisierung zu tun, sondern ist eine fundamental andere Sicht auf das Design.

## `new` vs. `Object.create()` resp. `Object.create()` vs. `Object.setPrototypeOf()`
`new()` sollte möglichst nicht verwendet werden, es dient der Simulation (fake) von Klassen resp. OO-Programmierung. Um ein Objekt mit Delegation zu erzeugen, verwendet man am besten `Object.create()`. Beispiel: `var myCat = Object.create(cat)`. Hier ist `cat` der Prototyp resp. das Objekt, an welches `myCat` Funktionalitäten oder nicht findbare Aufrufe von Properties und Funktionen delegiert. Wenn also Properties oder Funktionalität benötigt wird, welche `myCat` nicht bietet, wird dies implizit oder explizit delegiert. Man könnte dies auch mit `Object.setPrototypeOf(obj, proto)` nachträglich machen, aber das hat eine eher schlechte Performanz VERIFY?.

Wir können auch ein Objekt ohne Referenz auf ein Objekt erstellen:
```javascript
Object.create(null);
```
Dies wird für sogenannte _Dictionaries_ gemacht, welche einfach nur Objekte mit Daten sind, also flache Datenspeicher ohne überraschende Nebeneffekte.

## `instanceof`
Am besten nicht benutzen! Wird irrtümlicherweise häufig als eine Art »Typcheck« für Objekte genutzt. `instanceof` führt aber lediglich einen Verleich zwischen dem `[[Prototype]]` des Objekts und dem `Constructor.prototype` Property der Objekt-Konstruktor-Funktion durch.
```javascript
function foo() {}
const bar = { a: 'a'};

foo.prototype = bar;

// Is bar an instance of foo? Nope!
console.log(bar instanceof foo); // false

// Ok... since bar is not an instance of foo,
// baz should definitely not be an instance of foo, right?
const baz = Object.create(bar);

// ...Wrong.
console.log(baz instanceof foo); // true. oops.
```
## (Lexical) Closures
Definition: Closures (dt. »Funktions-Abschluss«)  ist die Kombination einer Funktion zusammengepackt (enclosed) mit Referenzen zum Status ihrer Umgebung (lexical environment). Eine Closure gibt uns Zugriff auf den Scope der äusseren Funktion von einer inneren Funktion aus. In JavaScript wird immer eine Closure erzeugt zum Zeitpunkt, wenn eine Funktion erzeugt wird. Man nutzt sie aktiv, indem man eine Funktion innerhalb einer anderen Funktion definiert. Damit hat man Zugriff auf die Umgebung der äusseren Funktion, auch wenn diese schon längst zurückgekehrt ist. 

Closures sind Teil des sogenannten _Execution Context_ in JavaScript. Dieser Execution Context besteht aus mehreren Objekten, welche für jede aufgerufene Funktion auf einem Stack gelegt werden.

In JavaScript kreiert man eine _Closure_ (Deutsch: Funktionsabschluss) indem man `function` innerhalb einer anderen `function` benutzt:
```javascript
function sayHello2(name) {
    var text = 'Hello ' + name; // Local variable
    var say = function() { console.log(text); }
    return say;
}
var say2 = sayHello2('Bob');
say2(); // logs 'Hello Bob'
```
Obiges Beispiel ist zugleich ein Beispiel von _Currying_ allerdings nur über ein Argument ;-)
Somit sehen wir, dass JS für Funktionsreferenzen auch eine versteckte Referenz auf die Closure. Die Variablen werden nicht kopiert, sondern existieren im Original, solange die äussere Funktion existiert und damit auch die Referenz auf sie (Closure). Exakt zum Zeitpunkt der Erstellung sichert die Funktion die sogeanannte _scope chain_ ihres Eltern-Scopes (z.B. die umgebende Funktion). Damit hat eine Closure auch auf freie Variablen (d.h. keine Parameter oder eigene Properties, sondern Variablen in äusseren bis hin zum globalen `[[Scope]]`). Wenn allerdings in obigem Beispiel die lokale Variable also noch irgendwie verändert wird vor dem `return say` also dem zurückgeben der inneren Funktion, so kann sie sich noch ändern. Das kann kaum passieren, da es nur bei der Funktionsdefinition gemacht werden darf, zudem schützen die neuen Deklarationen `let` und `const` zusätzlich davor und machen den Code zudem besser lesbar und verständlicher. Da jede normale Funktion zum Deklarationszeitpunk ihren `[[Scope]]` speichert, kann man sogar sagen, dass in JavaScript alle Funktionen Closures sind. Die Frage ist nur, ob man so eine Closure später nutzt oder nicht. Wenn mehrere Funktionen denselben `[[Scope]]` haben, wird er gemeinsam genutzt. Das kann zu Problemen führen, wie einige Diskussionen auf Stackoverflow zeigen. Wenn man die gemeinsame Scope-Referenzierung im Kopf behält, versteht man die Ursache jedoch besser. Die letzte Funktionsdefinition in der `for`-Schleife läuft im Scope mit `k=3` (letzter Schleifendurchgang). Der Scope gilt aber genauso für alle im Loop definierten Funktionen!
```javascript
var data = [];
 
for (var k = 0; k < 3; k++) {
    data[k] = function () {
      console.log(k);
    };
}
 
data[0](); // 3, but not 0
data[1](); // 3, but not 1
data[2](); // 3, but not 2
```
Das Problem lässt sich mit _IIFE_ beheben (zusätzliches Scope-Objekt), was nicht sehr schön aussieht und kaum jemand sich merken kann. Seit ES6 gibt es _Block-Scope-Binding_ mit der `let` Deklaration als Abhilfe:
```javascript
let data = [];
 
for (let k = 0; k < 3; k++) {
    data[k] = function () {
      console.log(k);
    };
}
 
data[0](); // 0
data[1](); // 1
data[2](); // 2
```
### Beispiel: Closures für private Daten
```javascript
const getSecret = (secret) => {
    return {
      get: () => secret
    };
};

test('Closure for object privacy.', assert => {
    const msg = '.get() should have access to the closure.';
    const expected = 1;
    const obj = getSecret(1);

    const actual = obj.get();

    try {
      assert.ok(secret, 'This throws an error.');
    } catch (e) {
      assert.ok(true, `The secret var is only available
        to privileged methods.`);
    }

    assert.equal(actual, expected, msg);
    assert.end();
});
```

## `this` Binding
Wie die Closures ist auch `this` ein Teil des _Execution Context_. Es wird auch als _context object_ bezeichnet, weil es definiert in welchem Kontext die Ausführung (execution) aktiviert wurde (im Gegensatz zur Closure, die statisch/lexikalisch zum Deklarationszeitpunkt gesichert wird). Merke: `this` ist keine Referenz auf die Funktion selbst (wie ein gedachtes self o.ä.) und ebensowenig eine Referenz auf den lexikalen Scope. `this` ist ein Binding, welches wie erwähnt zum Zeitpunkt/Ort (_call-site_) des Funktionsaufrufes erstellt wird.

### Vor ES6 ###
Vor ES6  müssen wir `this` strikt vom Variablen-Objekt (ebenfalls Teil des Execution Context) unterscheiden. Es wird nicht zur üblichen Auflösung von Identifikatoren herangezogen (identifier resolution process). Wenn wir also beispielsweise `console.log(this.a)` schreiben, so greifen wir direkt und explizit auf das `this`-Objekt zu. Daher ist `a` niemals der gleiche Bezeichner wie `this.a` (ausser man weist dies entsprechend selber zu). `this` wird genau einmal definiert und zwar beim Eintreten in den Kontext.

### Ab ES6 ###
Ab ES6 ist `this` allerdings ein Teil des lexikalen Kontext und damit ein Property des _variables object_ vom Execution Context. Dies wurde gemacht, um die Pfeil-Operatoren von ES6 zu unterstützen.

### Beispiel explizites `this`-Binding ###
```javascript
function identify() {
    return this.name.toUpperCase();
}
function speak() {
    var greeting = 'Hello, I am ' + identify.call(this);
    console.log(greeting);
}
var me = {
    name: 'Chris'
};
var you = {
    name: 'A reader'
};

// identify.apply(me) would also work (differs in 2nd arg which we don't use here)
identify.call(me); // CHRIS
identify.call(you); // A READER

speak.call(me); // Hello, I am CHRIS
speak.call(you); // Hello, I am A READER
```
### Beispiel hartes explizites `this`-Binding
Die Funktion `call` nimmt (wie auch `apply`) als erstes Argument den gewünschten `this`-Kontext entgegen, womit die aufgerufene Funktion dann arbeitet. Dieses explizite Binden ist schon relativ sicher, jedoch kann der `this`-Kontext immer noch verlorengehen bei Callbacks, Events u.ä. Um es ganz sicher zu machen, nutzt man ein sogenanntes _hard explicit binding_ wie folgt (wiederverwendbares Beispiel):
```javascript
function foo(something) {
    console.log(this.a, something);
    return this.a + something;
}

// Simple `bind` helper
function bind(fn, obj) {
    return function() {
        return fn.apply(obj, arguments);
    };
}

var obj = {
    a: 2
};

var bar = bind(foo, obj); 

var b = bar(3); // 2 3
console.log(b); // 5
```
Dieses Muster ist seit ES5 in `Function.prototype.bind` verfügbar für alle Funktionen. Ab ES6 wird noch das Property `name` für die Funktion gesetzt, womit die Bindung der Funktion abrufbar wird:
```javascript
var bar = foo.bind(obj);
bar.name; // "bound foo"
foo.name; // "foo"
```
Anstatt den `this`-Kontext beim Aufruff (`call` resp. `apply`) mitzugeben, wird beim harten expliziten Binding die Funktion selber fest mit dem Kontext gebunden.
### Beispiel lexikales `this`-Binding
Ab ES6 wird der Pfeil-Operator für eine Kurzschreibweise von Funktionen angeboten. Dieser übernimmt für das `this`-Binding den Umgebungs-Scope (Funktion oder global):
```javascript
function foo() {
    return (a) => {
        console.log(this.a);
    };
}
var obj1 = {
    a: 2
};
var obj2 = {
    a: 3
};
var bar = foo.call(obj1);
bar.call(obj2); // 2, not 3! `this` is not overwritten!
```
Dies wird vor allem in Callbacks wie Event handler und Timer genutzt.

### Currying & Spreading mit `apply` & `bind`
```javascript
function foo(a,b) {
    console.log('a:' + a + ', b:' + b);
}

// Spreading (With ES6 we have this as a own operator)
foo.apply(null, [2, 3]); // a:2, b:3

// Currying
var bar = foot.bind(null, 2);
bar(3); // a:2, b:3
```
#### Null-Objekt als `this` zur Sicherheit
Damit beim Currying nicht irgendwie ein ungewolltes `this`-Binding reinkommt, sollte man anstall `null` ein eigens erzeugtes »Null-Objekt« nehmen:
```javascript
var zero = Object.create(null);
```
Durch die Verwendung von `Object.create()` wird ein komplett leeres Objekt erzeugt. Würde man nur `{}` nehmen, wäre das Objekt nicht ganz leer, denn es hätte noch eine Referenz nach `Object.prototype` im `__proto__`.

## Immediately Invoked Function Expression (IIFE)‣
```javascript
(function count() {
    for (let i = 0; i < 10; i++) {
        console.log(i);
    }
})()
```
Die (häufig anonyme) Funktion wird gleich mit der Definition aufgerufen.

## Variable-Hoisting
```javascript
function count() {
    for (var i = 0; i < 10; i++) {
        console.log(i);
    }
}
```
`var` wird hier überall in der Funktion `count()` verfügbar (`let` wäre sauberer wegen Block-Scope und könnte dies verhindern) auch für Code, welcher vor dem `for`-Loop steht. Das geschieht durch das sogenannte _Hoisting_ der Variablen-Deklaration an den Anfang ihres Scopes (hier die Funktion). Im Extremfall führt Hoisting zur versehentlichen Global-Definition (bspw. im `window`-Objekt bei Browser-basiertem JS), wenn `var` hier im `for`-loop vergessen geht und nur eine Wertzuweisung stattfindet (`i=0` anstatt `var i=0`). Dies ist unbedingt zu vermeiden, indem man an den Anfang von jedem JS ein `
use strict";` einfügt. Bei ES6 / React.js etc. ist dies schon implizit der Fall VERIFY?
