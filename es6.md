#ES2015

## Let et constants

> Principe d'hoisting : lorsqu'il trouve le mot clé "var", le moteur de Javascript se charge de remettre ces variables dans le scope global de la fonction, ce qui fait qu'une variable est accessible partout dans la fonction même si les conditions font qu'elles ne devraient pas être déclarées par exemple.

Pour corriger cela, utiliser "let", afin que le scope de la variable sois bien son bloc courant (cela lèvera donc bien des erreurs et pas des `undefined`).

```js
let animals = ['lion', 'wolf', 'llama'];
```

Pour déclarer des variables en lecture seule (pas de redéclaration ou de réassignement possible). Un nom de constante majuscule est recommandé pour la lisibilité du code. Attention, de la même manière que let, const a bien un scope correspondant à son bloc courant.

```js
const MAX_ANIMALS = 3;
```

`Note` : utiliser let dans le cas de variable qu'on souhaitera ré-assigner et const pour des variables qui n'ont pas vocation à être ré-assignées.

### Boucles for avec let

Afin d'éviter l'effet d'hoisting dans une boucle `for` de type `var in`, l'utilisation de let est obligatoire.

```js
for (let i in animals) {
  _fetchProfile("/animals/" + animals[i], function() {
    console.log('Fetcher for ', animals[i]);
  });
}
```

## Fonctions

### Paramètres par défaut

```js
// à éviter:

function loadProfiles(animals) {
  let names = typeof userNames !== 'undefined' ? usernames : [];
  let namesLength = names.length;
}

// ES2015 : si pas d'argument spécifié ou argument au mauvais format, initialise usernames comme un tableau vide par défaut

function loadProfiles(animals = []) {
  let animalsLength = usernames.length;
}
```

### Paramètres nommés

```js
// à éviter:

function displayAnimal(name, caract = {}) {
  console.log('Name: ', name);
  console.log('Weight: ', caract.weight);
  console.log('Height: ', caract.height);
}

// ES2015 : si pas d'argument spécifié ou argument au mauvais format, initialise usernames comme un tableau vide par défaut

function displayAnimal(name, {poids, taille} = {}) {
  console.log('Name: ', name);
  console.log('Weight: ', weight);
  console.log('Height: ', height);
}

displayAnimal("lion");
`> Name: lion`
`> Weight: undefined`
`> Height: undefined`
displayAnimal('lion', {poids: 200, taille: 2});
`> Name: lion`
`> Weight: 200`
`> Height: 2`
```

### Fonctions avec un nombre variable de paramètres (fonctions variadiques)

```js
function addAnimals(...animals) {
  for (let i in animals) {
    let animal = animals[i];
    addToZoo(animal);
  }
}

addAnimals("lion", "loup");
`> lion ajouté`
`> loup ajouté`
addAnimals("lion");
`> lion ajouté`
```

`Note` : Le rest parameter doit toujours être placé comme dernier argument de la fonction.

```js
// erreur
function displayAnimals(...animals, targetDiv) {
  ...
}

// recommandé
function displayAnimals(targetDiv, ...animals) {
  ...
}
```

### Convertir un tableau en une liste d'objets pour l'appel d'une fonction variadique ?

```js
getRequest('/animals' function(data) {
  let animals = data.animals;
  displayAnimals(...animals); // ajouter la notation ...x
  //revient à faire : displayAnimals(animal, animal, animal);
});
```

### Encapsuler le code dans des composants

`Note` : nous verrons par la suite que cette syntaxe peut être améliorée.

```js
let zooComponent = new zooComponent(targetDiv, '/animals');
zooComponent.render();

----

function zooComponent(target, urlPath) {
  this.targetElement = target;
  this.urlPath = urlPath;
}

TagComponent.prototype.render = function() {
  getRequest(this.urlPath, (data) => {
    let animals = data.animals;
    displayAnimals(this.targetElement, ...animals);
  });
}
```

## Objets et String

### Nouvelle façon d'assigner des objets plus courte et plus propre

Au sein d'une fonction :
```js
function buildZooGuardian(first, last) {
  let fullName = first + " " last;
  return { first: first, last: last, fullName: fullName }
}

// ES 2015 assignation en utilisant le nom de la variable
function buildZooGuardian(first, last) {
  let fullName = first + " " last;
  return { first, last, fullName }
}
```

Ou à n'importe quel endroit du code :
```js
let name = "Edward";
let age = 44;
let responsibleOf = ["birds", "wolves"];

let zooGuardian = {name, age, responsibleOf};

console.log(zooGuardian.name);
`> Edward`
console.log(zooGuardian.age);
`> 44`
console.log(zooGuardian.name);
`> ["birds", "wolves"]`
```

### Destructuration

```js
// à éviter
let guardian = buildZooGuardian('Edward', 'Elric');

let first = guardian.first;
let last = guardian.last;
let fullName = guardian.fullName;

// ES 2015
let {first, last, fullName} = buildZooGuardian('Edward', 'Elric');
```

Il n'y a aucune obligation de mettre tous les attributs, on peut également spécifier celui qu'on souhaite.
```js
let {first} = buildZooGuardian('Edward', 'Elric');
```

### Déclaration de fonctions à l'intérieur d'un objet

```js
return {
  first,
  last,
  fullName,
  age,
  isOld() {
    return age >=60
  }
}
```

### Concaténation de chaînes de caractères

```js
// à éviter
let fullName = first + ' ' + last;

// ES 2015
let fullName = `${first} ${last}`;
```

`Note` : cette méthode est d'autant plus recommandée qu'elle évite pour les textes longs la gestion des retours à la ligne.

## Arrays (tableaux)

### Destructuration

Moins de code, facile à appréhender.

```js
let wolves = ['Ghost', 'Summer', 'Grey Wind'];

// à éviter
let a = wolves[0];
let b = wolves[1];
let c = wolves[2];

// ES 2015
let [a, b, c] = wolves;

//Note : values can be discarded :
let [a, , b] = wolves;

//Note : works with return values too :
let [a, b, c] = getWolves();
```

### Combiner la destructuration et les rest params

```js
let wolves = ['Ghost', 'Summer', 'Grey Wind'];

// ES 2015
let [a, ...rest] = wolves;

console.log(a, rest);
`>Ghost ["Summer" , "Grey Wind"]`
```

### for of

Préfèrer un for in lorsque l'on souhaite un index chiffré dans le cas contraire :

```js
for (let guardian of guardians) {
  console.log(guardian.fullName);
}
```

`Note` : Un for of ne peut être utilisé pour itérer sur les propriétés d'un objet JS.

## Maps

`Rappel` : une map est une collection de paires clé-valeur. Les clés sont uniques et une valeur ne peut-être associés qu'a une seule clé (néanmoins les valeurs ne sont-elles pas forcément uniques, on peut retrouver la même valeur au bout de plusieurs clés différentes).

```js
let guardian1 = { fullName: "Edward Elric" };
let guardian2 = { fullName: "Alphonse Elric" };

// à éviter
let totalContributions = {};
totalContributions[guardian1] = 10;
totalContributions[guardian2] = 15;

// ES 2015
let totalContributions = new Map();
totalContributions.set( guardian1, 10);
totalContributions.set( guardian2, 15);
```

### Parcourir une map avec for of

```js
for(let [key,value] of totalContributions) {
  console.log(`${key.fullName} = ${value}`);
}
`>Edward Elric = 10`
`>Alphonse Elric = 15`
```

### Weakmap

Une Weakmap est un type de map ou **seul un objet peut-être passé en paramètre**. Les types primitifs ne sont pas supportés. Elles ne sont pas itérables. Elles sont appellées comme ça car elles peuvent être détruites par le garbage collector pendant qu'elles existent encore.

## Sets

Un array ne garantis pas l'unicité des valeurs en son sein. C'est ici que l'utilisation d'un Set prend son sens.

```js
let species = new Set();
species.add('felidaes');
species.add('birds');
species.add({fish : 'barracuda'});
species.add('birds'); // double : not added

species.has('birds'); // true
species.has('bears'); // false

species.delete('birds');
```

Un set est destructurable au même titre qu'un array. Les **WeakSet** fonctionnent de la même manière que les WeakMap.


## Classes

La syntaxe `class` n'introduit pas un nouveau modèle objet à Javascript, il s'agit d'un sucre syntaxique pour s'affranchir de la syntaxe basée sur le modèle objet `prototype`.

### Syntaxe de base

```js
//à éviter

function Bird(name, category, shout, weight, height) {
  this.name = name;
  // ...
}

let bird = new Bird('black-winged kite', 'accipitridae', 'KiiitKiiit', 240, 20);

// ES 2015

class Bird {
  constructor (name, category, shout, weight, height) {
    this.name = name;
    // ...
  }
  render() {
    let mass = this._calculateMass(this.weight, this.height);
  }

  // Préfixer une méthode par un underscore est une convention qui indique que ceci doit rester une méthode privée.
  _calculateMass(weight, eight) {
    // ...
  }
}

let bird = new Bird('black-winged kite', 'accipitridae', 'KiiitKiiit', 240, 20);
```

### Héritage

Il est possible d'utiliser l'héritage pour réduire la répétition inutile du code. Une classe enfant hérite et spécialise le comportement définie dans la classe parent.

On utilise le mot clé `extends` pour faire hériter une classe enfant.

```js
// Parent class

class Animal {
  constructor(name, shout, weight, height, area) {
    this.name = name
    // ...
  }

  yell() {
    return this.shout.toUpperCase();
  }
}

// Child class

class Bird extends Animal {
  constructor(name, shout, weight, height, area, canFly, spoutLength) {
    super();
    this.canFly = canFly;
    //...
  }

  render() {
    let shout = this.yell(this.shout);
    let parseArea = this._parseArea(this.area)
  }
}
```

### Surcharge de méthode

Il suffit de créer une méthode au même nom que la méthode présente dans le parent pour que celle-ci soit surchargée, pas besoin d'annotation supplémentaire.

## Modules

### Default export

flash-message.js
```js
export default function(message) {
  console.log(message);
}
```

app.js
```js
import flashMessage from './flash-message.js'

flashMessage('Hello zoo!');
`>Hello zoo!`
```

### Custom export

flash-message.js
```js
function logMessage(message) {
  console.log(message);
}

function alertMessage(message) {
  alert(message);
}

export {alertMessage, logMessage}
```

app.js
```js
// Notation * car plusieurs fonctions sont exportés par le même fichier, flash est donc le préfixe.
import * as flash from './flash-message.js'

flash.alertMessage('Alert! Zoo is attacked!');
flash.logMessage('Hello zoo!');
`>Hello zoo!`
```

### Export constants

constants.js
 ```js
const MAX_ANIMALS = 200;
const MAX_GUARDIANS = 10;

export {MAX_ANIMALS, MAX_GUARDIANS};
 ```

app.js
  ```js
import {MAX_ANIMALS} from './constants.js'
  ```

### Export classes

flash-message.js
```js
class FlashMessahe {
  constructor() { }

  logMessage() { }

  alertMessage() { }
}

export { FlashMessage }
```

app.js
```js
import {FlashMessage} from './flash-message';
let flash = new FlashMessage();
```

## Promesses

```js

function getInfosFromSpecies(specieName) {
  return new Promise(function(resolve, reject) {
    let url = `/species/${specie}`;
    let request = XMLHttpRequest();
    request.open('GET', url, true);
    request.onload = () => {
      if (request.status >= 200 && request.status < 400) {
        resolve(request.response);
      } else {
        reject (new Error(request.status));
      }
    };

    request.onerror = () => {
      reject(new Error('Error fetching results'));
    }
  });
}

getInfosFromSpecies('Felidaes')
  .then((results) => {
    return results.filter((result) => result.family === 'pantherinae')
  })
  .then((resultFromPantherinae) => {
    console.log(resultFromPantherinae);
  })
  .catch((error) => {
    console.error(`Error: ${error}`);
  });
```

## Iterators

TODO

## Generators

TODO
