## Enunțul `for`

Aceast enunț, numit de standard `IterationStatement`, creează o buclă care produce un set de rezultate sau parcurge o structură de date existentă.

Această buclă va fi creată ținându-se cont de câteva expresii opționale care se introduc între paranteze, așa-numitul **bloc de inițializare**, fiind urmate de un **bloc de execuție**, care conține codul ce va fi executat pentru fiecare pas al buclei.

Oricare dintre *expresiile opționale* pot fi utilizate sau nu în blocul de inițializare.

```javascript
var x = 0;
for (; x < 5; x++) {
  console.log(x);
};
```

Expresiile opționale conțin:

- **un contor**, care este o valoare ce va porni de la o anumită valoare prestabilită (standardul spune că este un LexicalDeclaration Expression).
- **un comparator**, care ia valoarea din contor și o compară cu o altă valoare, de regulă dimensiunea unui array adusă prin proprietatea `array.length`.
- **un incrementor/decrementor**

Privind la expresiile folosite pentru a face funcțional un for, nu poți să nu privești la expresia `do...while` și să nu te întrebi, de ce nu ai folosi-o în continuare pe aceasta. Singura diferență este că la `do...while`, inițializarea sau contorul ca expresie stă afară înainte iar condiția sau comparatorul este în argumente iar blocul are la final expresia de incrementare. Răspunsul este legat de concizia pe care o oferă `for`. Este pur și simplu mai ușor de urmărit.

De cele mai multe ori vei întâlni în cod numele identificatorului variabilei pentru contor ca fiind litera `i`. Acesta vine ca prescurtare la termenul *index* și este larg utilizat. Atenție, nu este necesar să se folosească `i`. Poți numi variabila cum dorești.

În cazul în care se decide omiterea comparatorului, se va crea o buclă infinită, care poate fi întreruptă doar folosind comanda `break`.

```javascript
for (var x = 0; ; x++) {
  console.log(x);
  if (x > 5) break;
};
```

Pot fi omise toate cele trei expresii opționale, dar vor trebui tratate în blocul de execuție. Altfel, se intră într-o buclă infinită. Atenție, nu dorești acest lucru! Totul se oprește și de cele mai multe ori browserul se blochează. Fii foarte atent să pui condițiile de ieșire din buclă.

```javascript
console.time("start");
var x = 0;
for (;;) {
  console.log(x);
  // dacă nu introduci decizia
  // ai o buclă infinită
  if (x > 5) break;
  x++;
};
console.timeEnd("final");
```

Motivul pentru care bucla este una infinită este că blocul de condiție va fi evaluat la `undefined`, care este transformat într-un „falsey”, ceea ce conduce la execuția infinită.

## Declararea variabilelor în `for`

Declararea unei variabile într-un `for`, are ca efect, declararea unei variabile în funcția în care rulează bucla.

Pentru cei ingrijorați de blocul delimitat de acolade. Doresc să vă liniștesc temerile. În acest moment, conform standardului un bloc de cod `for` nu creează un scope (mediu lexical separat). Ține minte că doar funcțiile creează unul.

### Păstrarea vie a mediului lexical per fiecare iterație

Declararea variabilelor cu `let` la contor, le va „lega” de blocul de execuție a lui `for` per fiecare iterație. Aici vorbim despre avantajul folosirii lui `let` în cazul constituirii de closure-uri. Mai fin spus, se leagă de necesitarea ca funcțiile care au fost definite în corpul enunțului `for` să păstreze viu mediul lexical existent la momentul unei singure iterații.

```javascript
var colectie = [];
for (let i = 0; i < 5; i++) {
  var valDeIterație = 10;
  colectie.push(function ruleazaMa () {
    console.log(`Iterația ${i} și ${valDeIterație++}`);
  });
};
colectie[0](); // Iterația 0 și 10
colectie[1](); // Iterația 1 și 11
```

În cazul folosirii lui `var`, în exemplul nostru am fi rămas cu valoarea ultimei iterații.

```javascript
var colectie = [];
for (var i = 0; i < 5; i++) {
  var valDeIterație = 10;
  colectie.push(function ruleazaMa () {
    console.log(`Iterația ${i} și ${valDeIterație++}`);
  });
};
colectie[0](); // Iterația 5 și 10
colectie[1](); // Iterația 5 și 11
```

### Sari peste o iterație - iterare cu verificare

În cazul în care este necesar, se poate face un `salt`, evitându-se execuția  unuia dintre pașii buclei.

```javascript
for (var x = 0; x < 10; x++) {
  if (x === 5) {
    // daca nu verifici și tipul cu ===,
    // intri intr-o buclă infinită
    document.writeln(`am ajuns la ${x}`);
    continue;
  };
  console.log(x);
};
```

În exemplul prezentat am ajuns la 5, care este afișat în browser. Consola sare peste afișarea lui 5.

### Enunțul `for` poate fi folosit și în lucrul cu șirurile de caractere

Pur și simplu este cazul în care la un caracter poți adăuga un altul până când numărul lor va atinge limita specificată prin valoarea lui `length`.

```javascript
for (var i = "»"; i.length < 10; i += "~") {
  console.log(i);
}
```

### Folosirea lui `for` în lucrul cu Document Object Model - DOM

Buclele cu `for` pot fi folosite pentru introducerea de elemente în DOM.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Introducerea de elemente cu for</title>
  </head>
  <body>
    <h1>Introducere elemente în DOM folosind iterarea</h1>
    <div class="" id="carlig"></div>
  </body>
  <script>
    var x, y;
    console.time("start");
    for (x = 0; x < 20; x++) {
      y = document.createElement('p');
      y.innerText = x;
      document.body.appentChils(y);
    };
    console.timeEnd("final");
  </script>
</html>
```

## Folosirea lui for cu multiple variabile

În enunțurile `for` se pot introduce mai multe variabile. Un exemplu pentru a căuta membri ai șirului lui Fibonacci implică declararea mai multor variabile inițiale iar apoi în expresia de incrementare, acestea sunt manipulate pentru a avansa.

```javascript
for(let temp, i = 0, j = 1; j < 4; temp = i, i = j, j = i + temp) {
  console.log(j);
};
```

![Ilustrație pentru ciclurile for](for-ilustratie-for-fibonacci.png)



## Referințe

Ethan Brown. Learning JavaScript
