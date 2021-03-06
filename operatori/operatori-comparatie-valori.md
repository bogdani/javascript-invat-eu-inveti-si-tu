# Operatorii de comparație `<`, `>`, `<=`, `=>`

Evaluarea acestor operatori conduce la un răspuns `true` sau `false`.

## Mai mare `>`, mai mic `<`

Testează valorile după ordinul de mărime. `<` înseamnă testează valoarea din stânga să fie mai mică decât cea din dreapta. `>` este taman invers.

```javascript
5 > 3; // true
18 < 12; // false
```

## Mai mare sau egal `>=`, mai mic sau egal `<=`

Implică o verificare a valorilor în comparație una cu cealaltă, dar este acceptată ca true și cazul în care valorile sunt egale.

## Egalități `==` și `===`

Uneori ai nevoie să verifici dacă valorile sunt identic la fel sau dacă verificarea este mai laxă.

O verificare ușoară poate fi făcută cu dublu egal. De exemplu:

```javscript
5 == '5'; //true
```

O verificare strictă se face cu triplu egal. Această verificare caută să vadă dacă și tipul valorilor este același.

```javascript
5 === '5'; // false
```

Folosind operatorul logic de negare pus în fața operatorilor de verificare a egalității poți testa dacă egalitatea nu s-a realizat.
De regulă vei folosi această combinație în blocurile decizionale pentru a lua un anumit curs de execuție în funcție de cazul în care nu se realizează egalitatea.

```javascript
var ceva = 10;
var altceva = "10";
ceva == altceva // true
ceva === altceva // false
ceva !== altceva // true
```
