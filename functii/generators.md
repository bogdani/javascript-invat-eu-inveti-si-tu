# Generators

Sunt un nou tip de funcții introduse în ECMAScript 2015. Acest nou tip de lucru cu funcțiile se bazează pe faptul că accesul la date se face cu ajutorul iteratoarelor. Datele noastre sunt obiecte iterator pe care le putem parcurge.

```javascript
var iterator = [1,2,3][Symbol.iterator](),
    element;
while( !(element = iterator.next()).done ) {
  console.log(element.value);
};
```

Odată cu ECMAScript 2015, beneficiem de enunțul `for..of`, care va face exact ce am realizat mai sus construind obiectul iterator.

```javascript
for(var x of [1,2,3]){
  console.log(x);
};
```

Parcurgerea se face automat, rezultatele fiind oferite la încheierea iterării. Ce te faci în momentul în care dorești să ai acces secvențial la valorile unei colecții? În acest caz, vom apela la funcțiile generator. Punerea unei steluțe după cuvântul cheie `function`, va semnala că avem de a face cu o funcție generator.

Apelarea unui generator nu îl execută, ci doar este trimisă funcția în call-stack. De fapt, la executare este returnată o instanță a funcției.

```javascript
function* faCeva () {
  console.log('o metoda executata la inceput');
  console.log('o metoda executata la final');
};
const exempluGen = faCeva(); // instanța generatorului
exempluGen.next();
/*
o metoda executata la inceput
o metoda executata la final
{ value: undefined, done: true }
 */
```

Pentru a opri execuția din funcția generator se va folosi cuvântul rezervat `yield`.

```javascript
function* faCeva () {
  console.log('o metoda executata la inceput');
  yield;
  console.log('o metoda executata la final');
};
const exempluGen = faCeva();
exempluGen.next();
```

Returnează doar evaluarea primei metode `log`.
Pentru a obține și rezultatul celei de-a doua metode `log`, se va aplica din nou metoda `next`.

Se creează și scope-ul aferent. Ca efect este creat și returnat un obiect iterator prin care putem cere valori din generator. Obiectul iterator ține o referință către contextul de execuție a generatorului care este în call-stack.
La apelarea funcției generatorului, acesta este scoasă din call-stack (nu este garbage collected), iar variabila care va identifica iteratorul, va ține o referință către funcția generatorului și astfel la contextul de execuție prin obiectul iterator care tocmai s-a creat. Mecanismul seamănă cu cel al closure-urilor.

Execuția metodei `next()` nu creează un nou execution context ca în cazul clasic, ci doar reactivează contextul de execuție a generatorului pe care-l împinge din nou în call-stack, funcția generatorului continuând execuția de unde a rămas.

Dacă nu mai este niciun `yield`, funcția generator returnează.

La prima cerere prin `next()`, Generatorul produce o valoare (prin `yield`) și își suspendă execuția asteptând următoarea cerere prin (`next()`).

Este o funcție care generează o serie de valori dar nu toate odată, ci la cerere folosind `yield`. Funcția „de generare” va răspunde cu o valoare sau va notifica că nu mai are.
O astfel de funcție nu-și încheie execuția între cereri, doar o suspendă.

Sunt funcții din care poți ieși și apoi reintra mai târziu în funcționarea programului. Ceea ce le face importante este faptul că rețin valorile între diferitele accesări.

```javascript
function* generator(){
  yield true;
};
```

## Trimiterea mesajelor

Un lucru foarte interesant care privește funcțiile generator este că se pot trimite mesaje din funcție în instanță și invers.

### Trimiterea unui mesaj din funcție în instanță

Funcția generator va returna un obiect atunci când va face yield.

```javascript
function* unGenerator () {
  yield 'primul mesaj'
};
const emitator = unGenerator();
console.log(emitator.next());
// returneaza { value: "primul mesaj", done: false }
```

Valoarea lui `done` este `false` pentru că execuția funcției doar a fost oprită temporar. Funcția nu și-a încheiat execuția. Pentru a încheia în cazul nostru, va trebui să mai chemăm odată `next()`.

```javascript
console.log(emitator.next());
// returneaza { value: undefined, done: true }
```

### Trimiterea unui mesaj din instanță către generator

```javascript
function* altGenerator () {
  const mesajPrimit = yield;
  console.log(mesajPrimit);
};

const instantaAltGenerator = altGenerator();
console.log(instantaAltGenerator.next());
// { value: undefined, done: false }
console.log(instantaAltGenerator.next('mesaj spre generator'));
// mesaj spre generator
// returneaza si { value: undefined, done: true }
```

Pentru că o funcție generator este totuși sincronă, se pot trimite mesaje în cazul apariției unor erori folosind un bloc `try...catch`.

```javascript
function* facCevaSecvential () {
  try {
    const x = yield;
    console.log(`Salut ${x}`);
  } catch (err) {
    console.log('Eroare', err);
  };
};
const ziCeva = facCevaSecvential();
ziCeva.next(); // { value: undefined, done: false }
ziCeva.throw('Auleu, ceva e rău.');
// Eroare Auleu, ceva e rău.
// { value: undefined, done: true }
```

## Scoaterea datelor dintr-un generator cu `for..of`

Enunțul `for..of` trece prin generator și returnează chiar valorile existente.

```javascript
function* emiteFormule(){
  yield "Salutare!";
  yield "Hai noroc!";
  yield "Noapte bună";
};
for(let salutare of emiteFormule()){
  console.log(salutare);
};
/*
Salutare!
Hai noroc!
Noapte bună
 */
```

Se observă că o funcție generator se consumă cu o secvență `for..of`.

Funcționarea acestei funcții se bazează pe crearea unui obiect de tip `iterator`. De fapt, la apelarea funcției acest obiect se creează.
Dacă creem o referință la funcție, putem parcurge valorile obiectului creat de invocare (obiectul de tip `iterator`) folosing un cursor. Un cursor este o funcție care te ajută să parcurgi un șir de valori. Poți să-ți imaginezi cursorul ca pe un creion cu care urmărești textul atunci când citești o pagină a unei cărți.

Pentru exemplul de mai sus, să spunem că dorim să accesăm prima valoare. În acest caz, pur și simplu punem cursorul pe ea (o metodă oferită de iterator).

```javascript
function* emiteFormule(){
  yield "Salutare!";
  yield "Hai noroc!";
  yield "Noapte bună";
};
const refIterator = emiteFormule();
// în acest moment s-a creat un nou iterator

const primaValoare = refIterator.next();
console.log(typeof primaValoare);
console.log(primaValoare);  // { value: "Salutare!", done: false }

const aDouaValoare = refIterator.next();
console.log(aDouaValoare);  // { value: "Hai noroc!", done: false }

const aTreiaValoare = refIterator.next();
console.log(aTreiaValoare); // { value: "Noapte bună", done: false }

const aPatraValoare = refIterator.next();
refIterator.log(aPatraValoare); // { value: undefined, done: true }
```

Cursorul aduce valoarea iar funcția generator își suspendă execuția la momentul în care a adus valoarea cu `yield`.
Ceea ce trebuie înțeles este faptul că prin `yield` este adusă o valoare intermediară din generator, nu tot ceea ce poate oferi funcția generator.
Proprietatea done setată la false indică faptul că mai există valori care pot fi cerute.
Metoda done, așa cum se observă aduce un obiect care are drept proprietatea `value`, chiar valoarea evaluată prin primul `yield`.

Valorile pot fi iterate și printr-un `while` pentru a scoate valoarea din obiectul returnat de cursor. Pentru fiecare iterație testezi dacă done este true, adică dacă nu mai sunt obiecte de returnat. Aceasta va fi limita la care se va opri ciclarea.
Formularea condiției: `!(let element = refIterator.next()).done`.
Explicație:
- creezi o referință către obiectul adus de fiecare yield: `let obi;`
- `obi = refIterator.next()` aduce obiectul.
- pui expresia între paranteze pentru a o evalua. Evaluarea este obiectul adus de cursor: `(obi = refIterator.next())`
- Valoarea lui `done` o negi pentru toate obiectele returnate care au proprietate `value`, adică false va deveni true pentru ca bucla while să poată avansa.

Vom continua completând exemplul de mai sus.

```javascript
let obi;
while( !(obi = refIterator.next()).done ){
  console.log(obi.value);
};
// =>
/*
Salutare!
Hai noroc!
Noapte bună
 */
```

Modalitatea de a pargurge un generator cu o buclă `while` este mai greoaie față de ceea ce oferă `for...of`.

```javascript
function* emiteFormule(){
  yield "Salutare!";
  yield "Hai noroc!";
  yield "Noapte bună";
};
for(let obi of emiteFormule()){
  console.log(obi);
};
// =>
/*
Salutare!
Hai noroc!
Noapte bună
 */
```

Dintr-un generator poți apela alte generatoare.

```javascript
function* traduceri(){
  yield 'Salut!';
  yield 'Holla!';
  yield 'Ciao!';
  yield 'Konnichiwa!';
  yield 'Hello!';
};
function* emiteFormule(){
  yield 'Formule de salut in mai multe limbi';
  yield* traduceri();
};

for(let obi of emiteFormule()){
  console.log(obi);
};

// =>
/*
Formule de salut in mai multe limbi
Salut!
Holla!
Ciao!
Konnichiwa!
Hello!
 */
```

O chestie foarte faină care ține de felul în care funcționează generatoarele, este că se pot construi bucle infinite fără a avea temerea că se va returna erori de către mediul în care programul rulează. Acest lucru este pentru că indiferent de faptul că limita este la infinit, generarea valorilor este controlată prin `yield`. Se poate ușor închipui o listă cu bilete de ordine sau orice necesită o listă care să se prelungească la infinit.

### Parcurgerea DOM folosing o funcție generator.

```javascript
function* parcurgDOM(element){
  yield element;
  element = element.firstElementChild;
  while(element){
    yield* parcurgDOM(element);
    element = element.nextElementSibling;
  };
};
const elementDOM = document.getElementById("fragment");
for(let element of parcurgDOM(elementDOM)){
  console.log(element);
};
```

## Introducerea și modificarea datelor dintr-un generator

Reține faptul că funcțiile generator pot primi date și după ce au pornit execuția.

### Prin intermediul argumentelor la momentul invocării

Prima metodă de a trimite date în generator este prin intermediul argumentelor la invocare.

```javascript
function* faCeva(ceva){
  yield ("Cineva a primit " + ceva);
};

let obiIterator = faCeva("o dudă");

// console.log(obiIterator.next());  // Object { value: "Cineva a primit o dudă", done: false }
let afirm = obiIterator.next();
console.log(afirm.value); // Cineva a primit o dudă
```

### Prin pasarea de argumente în metoda `next()`

Nu pot fi pasate valori la prima apelare a lui next() pentru că metoda next(), de fapt primite o valoare unui yield care așteaptă. Dacă nu există vreun yield care să aștept, nici valoare nu are cui să-i fie pasată.
Valoarea pasată este folosită de generator ca valoare a întregii expresii yield în care a fost înghețat generatorul.

```javascript
function* faCeva(ceva){
  let intern = yield ("Cineva a primit " + ceva);
  yield ("Altcineva a primit " + ceva +  "\nValoarea lui next anterior este " + intern);
};

let obiIterator = faCeva("o dudă");

let primulObi = obiIterator.next();
let primaVal = primulObi.value;
console.log(primaVal);  // Cineva a primit o dudă

let alDoileaObi = obiIterator.next("altceva");
let aDouaVal = alDoileaObi.value;
console.log(aDouaVal);
/*
Altcineva a primit o dudă
Valoarea lui next anterior este altceva
 */
```
