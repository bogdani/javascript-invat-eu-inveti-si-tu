# Popularea obiectelor

Conținutul unui obiect constă din valori (de oricare tip) stocate într-o locație anume care poartă un nume și pe care le numim proprietăți.

Odată ce poți crea un obiect, ai nevoie să-l faci funcțional populându-l.

## Crearea obiectelor - alternative

Toate alternativele de mai jos au același efect: creează un obiect gol.

```javascript
var obi = new Object();
var obi = Object.create(null);
var obi = {};
let obi = Object.create(Object.prototype);
```

Atenție, obiectele create cu `new` și cu `Object.create` nu au constructor. `.constructor` va trimite la funcția la care a fost atașat prototype la momentul declarării.

```javascript
// Cazul object literal
let obi1 = {};
obi1.constructor; // function Object()

// Cazul new
function FaCeva(){
  console.log("Salut!");
};

let obi = new FaCeva();
obi.constructor; // function FaCeva()

// Cazul Object.create
let obi2 = Object.create(null);
obi2.constructor; // undefined
```

## Popularea obiectelor folosind noile posibilități oferite de ES6

### Property initializer

Dacă parametrii vor fi folosiți pentru a da valorile proprietăților unui obiect literal returnat de o funcție, atunci ES6 permite meționarea doar a proprietății. Este o prescurtare, un artificiu util pentru inițializarea proprietăților (`property initializer`).

```javascript
function construiesteObi(unu, doi){
  return {
    unu,
    doi
  };
}; var obiNou = construiesteObi(1, 2); // Object { unu: 1, doi: 2 }
```

### Metode în formă concisă `concise method`

Această înlesnire în redactarea proprietăților care au metode definite ca funcții anonime.
Pornind de la formula clasică:

```javascript
var obiNou = {
  primaProp: 'proprietăți',
  aDouaProp: function(){console.log(this.primaProp);}
};
```

se poate contrage la:

```javascript
var obiNou = {
  primaProp: 'proprietăți',
  aDouaProp(){console.log(this.primaProp);}
}; obiNou.aDouaProp();
```

### Nume de proprietăți computate

Dacă ai un obiect literal, poți să-i pui numele proprietății o valoare string oferită de o variabilă, de fapt orice expresie validă care poate fi evaluată:

```javascript
var prop = 'identificator';
var comp = 'primul';
var obi = {
  [prop]: 189439,
  [comp + ' lucru']: 'o balenă'
};
console.log(obi[prop]); // 189439
console.log(obi['primul lucru']); // o balenă
```

## Populare folosind notația cu punct - Dot notation

```javascript
var newObj = {};            // Creează obiectul
newObj.oCheie = 'Salutare'; // Scrie proprietăți
var cheie = newObj.oCheie;  // Accesează proprietățile
```

## Populare folosind sintaxa parantezelor drepte - Square bracket syntax

```javascript
var newObj = {};                // Creează obiectul
newObj['oCheie'] = 'Salutare';  // Scrie proprietăți
var cheie = newObj['oCheie'];   // Accesează proprietățile
```

## Populare folosind `Object.defineProperty()`

```javascript
var newObj = {}; // Creează obiectul literal

Object.defineProperty(newObj, 'numeCheieNoua', {
  value: 'Salutare',
  writable: true,
  enumerable: true,
  configurable: true
});
```

Exemplul de mai sus ar putea fi rescris astfel:

```javascript
var newObj = {};  // Creează obiectul literal

var defProp = function(obiect, cheie, valoare){
  config.value = valoare;
  Object.defineProperty(obiect, cheie, config);
};

defProp(newObj, 'oCheieNoua', 'Santinel');  // creează o proprietate
defProp(newObj, 'oAltaCheieNoua', 'an');    // adaugă o proprietate

```

## Populare folosind `Object.defineProperties()`

```javascript
Object.defineProperties(newObj, {
  'oCheie': {
    value: 'Salutare',
    writable: true
  },
  'oAltaCheie': {
    value: 'Ce mai faci?',
    writable: true
  }
});
```

ATENȚIE!
Folosirea unei funcții într-un obiect drept „metodă”, nu este decât apelarea unei funcții în cadrul obiectului. Nu se poate spune că obiectul „conține” funcția. Obiectul doar face o referință. Funcției cu rol de metodă i se pasează `this`, care este, de fapt obiectul în care este metodă.

```javascript
function actiune() {
	console.log( "actiune" );
}

var aceeasiActiune = actiune;	// variabila „conține” referința către `actiune`

var obiect = {
	acceasiActiune: actiune
};

actiune;				// function actiune(){..}
acceasiActiune;			// function actiune(){..}
obiect.acceasiActiune;	// function actiune(){..}
```

## Cum se șterge un membru al unui obiect

```javascript
var obi = {unu: 'ceva', doi: 'altceva', trei: 'diferit', patru: 'prop'};

delete obi.doi;

for(var i in obi){
  if(obi.hasOwnProperty(i)){
    console.log(i, ' '+obi[i]);
  };
};
```

## Getters și setters

```javascript
var obiect = {
  colectie: [],
  set ceva(valoare){
    this.colectie[this.colectie.length] = valoare;
  },
  get ceva(){
    return this.colectie.join(', ');
  }
};

obiect.ceva = 10;
obiect.ceva = 'un ceva';
obiect.colectie // "un ceva, 10"

delete obiect.ceva; //true
obiect // Object { colectie: Array[2] }
```

Reține faptul că funcțiile în JavaScript sunt obiecte și că, orice funcție în JS, poate face referință la variabile definite în scope-ul extern.

```javascript
function unObiect(){
  var oValoare;
  return {
    set: function(modVal){ oValoare = modVal; },
    get: function(){ return oValoare; },
    tip: function(){ return typeof oValoare; }
  };
};
let x = unObiect(); // typeof x => "object"
x.get();    // undefined
x.set(10);  // undefined
x.get();    // 10
x.tip();    // "number"
```

### Definirea unui setter pe un obiect folosind `defineProperty`

```javascript
var obiect = {
  colectie: []
};

Object.defineProperty(obiect, 'ceva', {
  set: function(valoare){this.colectie[this.colectie.length] = valoare;},
  get: function(return this.colectie.join(', ');)
});

obiect.ceva = 'test';

obiect.colectie; //Array [ "test" ]
```
