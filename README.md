# JS-Learnings/Cheatings
My personal JS-Cheatsheet reflecting my learnings about the nuts & bolts of JS/ECMA whatever you prefer calling it. It is currently in German, because that better stuffs in my brain.
A lot of credit for my understanding of JS goes to the »[You Don't Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/README.md)« book series as well as mpjme's Youtube-Series »[funfunfunction](https://www.youtube.com/channel/UCO1cgjhGzsSYb1rsB4bFe4Q)«. Also not to forget [Mozilla's really great »MDN« JavaScript Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript).
If this page becomes a useful cheatsheet (at least for me), I might try to translate in to English in the future...

## Semikolons?
Was viele nicht wissen: JS fügt automatisch Semikolons hinzu! Das führt i.d.R. zu keinen Problemen, ausser: wenn eine öffnende Klammer am Anfang einer Linie steht (kann z.B. passieren, wenn eine [_IIFE_](https://github.com/ckpinguin/JS-Cheatings/blob/master/README.md#immediately-invoked-function-expression-iife) verwendet wird).
Es ist eine Frage des persönlichen Geschmacks resp. Faulheit, ob man ; nutzt oder nicht.

## prototype vs. __proto__

### prototype
Nur Funktionen haben das _accessor-property_ `prototype`. Es wird (vermutlich)  nur für die `new()` Funktionalität genutzt, weil es auf den jeweiligen `contstructor` der zu erzeugenden Klasse zeigt, welcher wiederum nichts anderes als die Funktionsdefinition mit dem Klassennamen ist. Damit werden Klassen simuliert mittels Funktionen.
#### Anwendungsbeispiel
Erweiterung einer Funktion (resp. Fake-Klasse, da es nur eine Funktion mit grossem Anfangsbuchstaben ist) um weitere Methoden:

    Cat.prototype.say = function(word) {
        console.log('Miau', word);
    }
Info: `Function` (das Objekt) hat `function` (die JS-Primitive) als `prototype`, während `Object` selber `Object` als `prototype` hat. Wenn allerdings eine Funktion mit `new Function()` definiert wird, hat diese wiederum `Object` als `prototype`. Verwirrend? Ja, denn der Name `prototype` ist sehr leicht mit dem `__proto__` resp. dem echten _Prototype-Behaviour-Delegation_-Mechanismus zu verwechseln (welcher `__proto__` nutzt), hat damit jedoch nichts zu tun! Es wird noch lustiger, wenn man `Object.getPrototypeOf(Function.prototype)` usw. anschaut, d.h. den `__proto__` Delegations-Mechanismus für das `prototype`-Property eines Objektes verwendet. Muahahaha.
Vererbung wird bspw. mit `Button.prototype = Object.create(Widget.prototype)` simuliert und benötigt vor ES6 übel aussehende Hacks bez. `call` und `apply` für super-Aufrufe oder überschriebene Methoden. Dass dies ab ES6 mit einfacherer Syntax funktioniert löst nicht das eigentliche Problem, dass die OO-Programmierung resp. Klassenorientierung in JS künstlich aufgesetzt ist.

### `̲__proto__`
Jedes Objekt (ausser Object) hat das `__proto__` Property. Dabei handelt es sich um das Zentrum des sogenannten _`[[Prototype]]`-Delegation_ oder auch _Behaviour-Delegation_-Mechanismus von JS. Es zeigt auf das jeweils gelinkte Objekt eines Objekts.
So kann eine Link-Kette entstehen, die in jedem Fall bei `Object` endet, welches einige Standardproperties und Funktionalitäten anbietet; wie eben `__proto__`, welches ganz genau gesehen Teil des `Object`-Prototyps ist: `Object.prototype.__proto__`, d.h. es ist eine Methode von Object. Durch das lexikale `this`-Binding von Javascript wird immer der Aufrufkontext in `this` gebunden, wodurch die Behaviour-Delegation sehr elegant brauchbar wird. Interessanterweise zeigt `__proto__` von Object und Function jeweils auf `function` (alles Kleinbuchstaben), womit gezeigt ist, dass JS tatsächlich eine rein „funktionale“ Sprache ist, die man aber auch als „Objektorientiert“ im richtigen Sinn (Objekte, keine Klassen!) bezeichnen darf.
State (Variablen) sollte immer in den delegierenden Objekten sein, nicht in den „Empfänger“-Objekten. Programmatisch kann man `__proto__` mit `Object.getPrototypeOf(obj)` sowie `Object.isPrototypeOf(obj, proto)` und `Object.setPrototypeOf(obj, proto)` nutzen. Laut »You dont't know JS« Büchern ist der Begriff _OOLO_ (Objects Link to Other Objects) angezeigt, um das komplett von _OO_ verschiedene Design zu bezeichnen.

## `new()` vs. `Object.create` resp. `Object.create` vs. `Object.setPrototypeOf`
`new()` sollte möglichst nicht verwendet werden, es dient der Simulation (fake) von Klassen resp. OO-Programmierung. Um ein Objekt mit Delegation zu erzeugen, verwendet man am besten `Object.create`. Beispiel: `var myCat = Object.create(cat)`. Hier ist `cat` der Prototyp resp. das Objekt, an welches `myCat` delegiert, wenn Properties oder Funktionalität benötigt wird, welche `myCat` nicht bietet. Man könnte dies auch mit `Object.setPrototypeOf(obj, proto)` nachträglich machen, aber das hat eine sehr schlechte Performanz.

## `this` binding & closures
In JavaScript kreiert man eine _Closure_ (Deutsch: Funktionsabschluss) indem man `function` innerhalb einer anderen `function` benutzt:

    function sayHello2(name) {
      var text = 'Hello ' + name; // Local variable
      var say = function() { console.log(text); }
      return say;
    }
    var say2 = sayHello2('Bob');
    say2(); // logs 'Hello Bob'

Obiges Beispiel ist zugleich ein Beispiel von _Currying_ allerdings nur über ein Argument ;-)
Somit sehen wir, dass JS für Funktionsreferenzen auch eine versteckte Referenz auf die Closure. Die Variablen werden nicht kopiert, sondern existieren im Original, solange die äussere Funktion existiert und damit auch die Referenz auf sie (Closure). Wenn die lokale Variable also noch irgendwie verändert wird vor dem referenzierten Closure-Aufruf, so kann sie sich noch ändern. Das kann kaum passieren, da es nur bei der Funktionsdefinition gemacht werden darf, zudem schützen die neuen Deklarationen `let` und `const` zusätzlich davor und machen den Code zudem besser lesbar und verständlicher.

## Immediately Invoked Function Expression (IIFE):

    (function count(){
      for (let i = 0; i < 10; i++)
        {console.log(i);}
    })()
    Die (häufig anonyme) Funktion wird gleich mit der Definition aufgerufen.

    Variable-Hoisting
    function count(){
      for (var i = 0; i < 10; i++)
        {console.log(i);}
    }
`var` wird hier überall in der Funktion `count()` verfügbar (`let` wäre sauberer wegen Block-Scope und könnte dies verhindern) auch für Code, welcher vor dem `for`-Loop steht. Das geschieht durch das sogenannte _Hoisting_ der Variablen-Deklaration an den Anfang ihres Scopes (hier die Funktion). Im Extremfall führt Hoisting zur versehentlichen Global-Definition (bspw. im `window`-Objekt bei Browser-basiertem JS), wenn `var` hier im `for`-loop vergessen geht und nur eine Wertzuweisung stattfindet (`i=0` anstatt `var i=0`). Dies ist unbedingt zu vermeiden, indem man an den Anfang von jedem JS ein `
use strict";` einfügt. Bei ES6 / React.js etc. ist dies schon implizit der Fall.
