# `Array.prototype.sort()`

Atenție! Această metodă modifică definitiv array-ul original.

Sortează elementele unui array și returnează acel array.
Fii avertizat că această metodă modică array-ul. Există un mic truc care protejează array-ul original.

```javascript
const arrOrig = [23, 'ceva', 43, 8];
const arrCopie = arrOrig.slice().sort(); // [ 23, 43, 8, "ceva" ]
```

Dacă nu este pasat un argument, de fapt o funcție care să îndeplinească sortarea, aceasta se va face prin compararea ***punctelor de cod*** Unicode.

Ceea ce se întâmplă este o conversie a elementelor de array la string urmată de compararea lor ca valori Unicode.

Pentru șiruri funcția de comparare poate fi astfel:

```javascript
// atenție, acesta este pseudocod
function compare(primulCaracter, aldoileaCaracter) {
  if (primulCaracter este mai mic decât aldoileaCaracter folosind un criteriu de ordonare) {
    return -1;
  }
  if (primulCaracter este mai mare decât aldoileaCaracter folosind un criteriu de ordonare) {
    return 1;
  }
  // primulCaracter trebuie să fie egal cu aldoileaCaracter
  return 0;
}
["c", "a", "d"].sort(compare);
```

### Compararea și sortarea numerelor

Pentru că valorile de lucru sunt cele ale tabelei Unicod și oarecum nu sunt evidente pentru cel care lucrează cu valorile array-ului, practica a găsit o metodă de a face sortarea să se comporte mai apropiat de ceea ce se așteaptă de la o astfel de metodă.

Acest lucru se transpune în pasarea unei funcții cu rol de sortare. Funcția pasată va compara două elemente x și y. Rezultatul comparării conduce la o decizie în ceea ce privește reorganizarea array-ului. Există trei cazuri în care comportamentul de sortare este modelat de ceea ce este returnat de funcția pasată:

- valoarea returnată mai mică de 0: x va sta înaintea lui y
- valoarea este mai mare de 0: y va sta înaintea lui x
- valoarea este egală cu 0: x și y își vor păstra pozițiile.

O formă pentru a înțelege pașii este următoarea secvență care se autodescrie prin verbozitatea codului.

```javascript
var arr = [-23, -2, 102, 3, -54];

function aranjament(x, y) {
  var jalon = 0;
  if ( x > y ) {
    jalon = 1;
  } else if ( y > x) {
    jalon = -1;
  };
  return jalon;
};

arr.sort(aranjament); // [ -54, -23, -2, 3, 102 ]
```

O formulă mai contrasă este următoarea secvență:

```javascript
[-23, -2, 102, 3, -54].sort( function (x, y) {
  if (x < y) { return -1; };
  // dacă true pune x pe un index mai mic decât y: deplasare stânga.
  if (x > y) { return 1; };
  // dacă y este mai mic decât x, acordă un index mai mic.
  return 0;
  // dacă valorile sunt sortate lasă neschimbată poziția unuia față de celălalt.
}); // Array [ -54, -23, -2, 3, 102 ]
```

Se poate condensa mai mult folosindu-se operatorul ternar:

```javascript
[-23, -2, 102, 3, -54].sort( function (x, y) {
  return x < y ? -1 : ( x > y ? 1 : 0 );
}); // [ -54, -23, -2, 3, 102 ]
```

Încă o treaptă folosindu-se un fat arrow:

```javascript
[-23, -2, 102, 3, -54].sort( (x, y) => {
  return x < y ? -1 : ( x > y ? 1 : 0 );
}); // [ -54, -23, -2, 3, 102 ]
```

O altă variantă de comparator pentru numere este pur și simplu scăderea unuia din celălalt:

```javascript
[-23, -2, 102, 3, -54].sort( (x, y) => x - y );
```

Și obiectele pot fi sortate dacă este dată o valoare a uneia dintre proprietăți.

```javascript
var colectie = [
  {nume: 'Gina', valoare: 20},
  {nume: 'Dobrin', valoare: 16},
  {nume: 'Sanda', valoare: -12},
  {nume: 'Nicolae', valoare: 40},
  {nume: 'Pavel', valoare: -6}
];

// sortare după o valoare arbitrară
colectie.sort( function (x, y) {
  if ( x.valoare > y.valoare ) {return 1};
  if ( x.valoare < y.valoare ) {return -1};
  return 0;
});

// contras
colectie.sort( (x, y) => x.valoare - y.valoare );

// sortare după o valoare text
colectie.sort( function (x, y) {
  var numeX = x.nume.toUpperCase(); // uniformizezi caracterele
  var numeY = y.nume.toUpperCase();
  if ( numeX < numeY ) {return -1};
  if ( numeX > numeY ) {return 1};
  return 0;
});
```

Rețineți tactica de a nivela diferențele pe care majusculele și minusculele le dau. S-a folosit metoda `toUpperCase()` pentru a nivela aceaste diferențe.

Mergând ceva mai departe, dacă extragem funcția separat, putem, la nevoie să inversăm rezultatul sortării.

```javascript
var arr = [-23, -2, 102, 3, -54];

function aranjament(x, y) {
  var jalon = 0;
  if ( x > y ) {
    jalon = 1;
  } else if ( y > x) {
    jalon = -1;
  };
  return jalon * -1; // inversarea sortării
};

arr.sort(aranjament); // [ 102, 3, -2, -23, -54 ]
```

Și acum, că tot am ajuns la asta, este posibil ca la un moment dat să faci o sortare, dar să poți și inversa sortarea la nevoie.

```javascript
var colectie = [
  {nume: 'Gina', valoare: 20},
  {nume: 'Dobrin', valoare: 16},
  {nume: 'Sanda', valoare: -12},
  {nume: 'Nicolae', valoare: 40},
  {nume: 'Pavel', valoare: -6}
];

function comparValori (cheia, ordonare = 'asc') {
  return function (x, y) {
    let comparator = 0;

    // lucrăm strict doar cu proprietățile obiectului
    if ( !x.hasOwnProperty(cheia) || !y.hasOwnProperty(cheia) ) {
      return 0;
    };

    // uniformizează cheia la CAPS
    const X = (typeof x[cheia] === 'string') ? x[cheia].toUpperCase() : x[cheia];
    const Y = (typeof y[cheia] === 'string') ? y[cheia].toUpperCase() : y[cheia];

    if (X > Y) { comparator = 1 } else if (X < Y) { comparator = -1 };

    return ( (ordonare == 'desc') ? (comparator * -1) : comparator );
  };
};

colectie.sort(comparValori('valoare'));
colectie.sort(comparValori('nume'));
colectie.sort(comparValori('valoare', 'desc'));
```
