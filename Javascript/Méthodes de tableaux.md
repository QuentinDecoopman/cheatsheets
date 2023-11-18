# Méthode de tableaux

### values():

Cette méthode renvoie un itérateur
qui fournit les valeurs pour chaque indice dans
le tableau. Elle ne prend aucun argument.

```js
const arr = ["Pomme", "Poire", "Banane"];

const iterator = arr.values();

for (const value of iterator) {
  console.log(value);
} //renvoie: Pomme, Poire, Banane
```

### length():

Cette propriété renvoie la longueur du tableau

```js
    const arr = ['Pomme', 'Poire', 'Banane'];

    console.log(arr.length);
    } //renvoie: 3
```

### reverse():

Cette méthode inverse l'ordre des éléments du tableau.

```js
const arr = ["Pomme", "Poire", "Banane"];
arr.reverse();

console.log(arr); //renvoie: [ 'Banane', 'Poire', 'Pomme' ]
```

### sort():

Cette méthode trie les éléments du tableau dans l'ordre alphabétique. Elle peut prendre une fonction de comparaison facultative
comme argument.

```js
const arr = ["Pomme", "Poire", "Banane"];
arr.sort();
console.log(arr); //renvoie: [ 'Banane', 'Pomme', 'Poire' ]
```

### at():

Cette méthode renvoie l'élément à la
indice spécifié dans le tableau. Elle prend un
argument : l'index.

```js
const arr = ["Pomme", "Poire", "Banane"];
console.log(arr.at(1)); // renvoie Poire
```

### fill()

Cette méthode remplit tous les éléments d'un
tableau d'un indice de départ à un indice de fin avec une
valeur statique. Elle peut prendre jusqu'à trois arguments :
la valeur à remplir, l'indice de départ et l'indice de fin.

```js
const arr = ["Pomme", "Poire", "Banane"];
arr.fill("fraise", 1, 2);
console.log(arr); // renvoie ['Pomme', "orange", "banane"]
```

### from():

Cette méthode crée un nouveau tableau à partir
d'un objet semblable à un tableau ou d'un objet itérable. Elle peut
prendre jusqu'à deux arguments : l'objet à convertir
en un tableau, et une fonction de mappage à appliquer à
chaque élément du nouveau tableau.

```js
const obj = { 0: "pomme", 1: "poire", 2: "banane", lenght: 3 };
const arr = Array.from(obj);
console.log(arr); // renvoie: ["pomme", "poire", "banane"]
```

### join():

Cette méthode concatène tous les éléments d'un
tableau en une chaîne de caractères en utilisant un séparateur spécifié.
Elle prend un argument facultatif : le séparateur
à utiliser.

```js
const arr = ["Pomme", "Poire", "Banane"];
const str = arr.join(",");

console.log(str); // renvoie: Pomme, Poire, Banane
```

### toString():

Cette méthode renvoie une chaîne de caractères
représentant le tableau et ses éléments.

```js
const arr = ["pomme", "banane", "fraise"];
const str = arr.toString();
console.log(str); // renvoie: "pomme,banane,fraise"
```

### pop():

Cette méthode supprime le dernier élément d'un
tableau et renvoie cette élément.

```js
const arr = ["pomme", "banane", "fraise"];
const lastElement = arr.pop();
console.log(lastElement); // renvoie: 'fraise'
console.log(arr); // renvoie: [ 'pomme', 'banane' ]
```

### forEach():

Cette méthode exécute une fonction fournie
une fois pour chaque élément du tableau. Elle ne renvoie
rien, elle exécute simplement la fonction de rappel
sur chaque élément du tableau.

```js
let fruits = ["pomme", "banane", "fraise"];
fruits.forEach(function (fruit) {
  console.log(fruit);
}); // renvoie: pomme, banane, fraise
```

### shift():

Cette méthode supprime le premier élément d'un
tableau et renvoie cet élément.
Cette méthode change la longueur du tableau.

```js
let fruits = ["pomme", "banane", "fraise"];
let firstFruit = fruits.shift();
console.log(firstFruit); // renvoie: "pomme"
console.log(firstFruit); // renvoie: [ 'banane', 'fraise' ];
```

### copyWithin():

Cette méthode copie les éléments d'un
tableau dans un autre tableau. Elle prend
deux arguments : l'index de debut, et l'index de fin.

```js
let numbers = [1, 2, 3, 4, 5];
numbers.copyWithin(2, 0, 2);
console.log(numbers); // renvoie: [ 1,2,1,2,5]
```

### push():

La méthode ajoute un ou plusieurs éléments à
la fin d'un tableau et renvoie la nouvelle longueur du tableau.

```js
let fruits = ["pomme", "banane"];
fruits.push("fraise", "orange");
console.log(fruits); // renvoie [ 'pomme', 'banane', 'fraise', 'orange' ]
```

### unshift():

La méthode ajoute un ou plusieurs éléments au
début d'un tableau et renvoie la nouvelle
longueur du tableau.

```js
let fruits = ["pomme", "banane"];
fruits.unshift("fraise", "orange");
console.log(fruits); // renvoie [ 'orange', 'fraise', 'pomme', 'banane' ]
```

### concat():

La méthode est utilisée pour fusionner deux
ou plusieurs tableaux. Cette méthode ne modifie pas
les tableaux existants, mais renvoie plutôt un nouveau tableau.

```js
let fruits = ["pomme", "banane"];
let moreFruits = ["fraise", "orange"];
let allFruits = fruits.concat(moreFruits);
console.log(allFruits); // renvoie [ 'pomme', 'banane', 'fraise', 'orange' ]
```

### splice():

La méthode modifie le contenu d'un
tableau en supprimant ou remplaçant des
éléments existants et/ou en ajoutant de nouveaux éléments sur place.

```js
const fruits = ["pomme", "banane", "fraise", "orange"];
fruits.splice(2, 1, "mangue", "kiwi");
console.log(fruits); // renvoie [ 'pomme', 'banane', 'mangue', 'kiwi', 'orange' ]
```

### flat():

Cette méthode crée un nouveau tableau avec tous
les éléments de sous-tableau concaténés en lui
récursivement jusqu'à la profondeur spécifiée.

```js
const numbers = [1, [2, [3]], 4];
const flattened = numbers.flat(Infinity);
console.log(flattened); // renvoie [ 1, 2, 3, 4 ]
```

### lastIndexOf():

Cette méthode renvoie le dernier
indice auquel un élément donné peut être trouvé dans
le tableau.

```js
const numbers = [1, 2, 3, 4, 5, 3];
const LastIndex = numbers.lastIndexOf(3);
console.log(LastIndex); // renvoie: 5
```

### indexOf():

Cette méthode renvoie l'indice de la
première occurrence d'un élément spécifié dans un
tableau. Si l'élément n'est pas présent, elle renvoie -1.

```js
const arr = [5, 10, 15, 20];
const index = arr.indexOf(10);
console.log(index); // renvoie: 1
```

### of():

Cette méthode crée une nouvelle instance de tableau
avec un nombre variable d'arguments,
peu importe le nombre ou le type des arguments.

```js
const arr = Array.of(1, 2, 3);
console.log(arr); // renvoie: [ 1, 2, 3 ]
```

### every():

Cette méthode vérifie si tous les éléments d'un
tableau réussissent un test (fourni sous forme de fonction). Elle
renvoie true si tous les éléments passent le test ;
sinon, elle renvoie false.

```js
const arr = [1, 2, 3, 4, 5];
const isEven = (num) => num % 2 === 0;
const result = arr.every(isEven);
console.log(result); // renvoie: true
```

### slice():

Cette méthode renvoie une copie superficielle d'une
portion d'un tableau dans un nouvel objet tableau
sélectionné de début à fin.

```js
const arr = [1, 2, 3, 4, 5];
const sliced = arr.slice(2, 4);
console.log(sliced); // renvoie: [ 3, 4 ]
```

### flatMap():

Cette méthode mappe chaque élément
en utilisant une fonction de mappage, puis aplati le
résultat dans un nouveau tableau.

```js
const arr = [1, 2, 3];
const result = arr.flatMap((x) => [x * 2]);
console.log(result); // renvoie: [ 2, 4, 6 ]
```

### find():

Cette méthode renvoie le premier élément
d'un tableau qui satisfait un test. Elle renvoie undefined
s'il n'y a pas de élément qui satisfait le test.

```js
const arr = [10, 20, 30, 40, 50];
const greaterThan30 = (num) => num > 30;
const result = arr.find(greaterThan30);
console.log(result); // renvoie: 40
```

### findIndex():

Cette méthode renvoie l'indice du premier élément
d'un tableau qui satisfait un test. Elle renvoie -1
s'il n'y a pas de élément qui satisfait le test.

```js
const arr = [10, 20, 30, 40, 50];
const greaterThan30 = (num) => num > 30;
const index = arr.findIndex(greaterThan30);
console.log(result); // renvoie: 3
```

### includes():

Cette méthode détermine si un
tableau inclut une certaine valeur parmi ses entrées,
renvoyant true ou false selon le cas.

```js
const arr = [10, 20, 30, 40, 50];
const has20 = arr.includes(20);
console.log(has20); // renvoie: true
```

### entries():

Cette méthode renvoie un nouvel objet itérateur de tableau
qui contient les paires clé/valeur
pour chaque indice dans le tableau.

```js
const arr = ["a", "b", "c"];
const iterator = arr.entries();
console.log(iterator.next().value); // renvoie: [ 0, 'a' ]
console.log(iterator.next().value); // renvoie: [ 1, 'b' ]
console.log(iterator.next().value); // renvoie: [ 2, 'c' ]
```

### reduce():

Cette méthode applique une fonction à
chaque élément d'un tableau et réduit le tableau
à une seule valeur.

```js
const numbers = [10, 20, 30, 40];
const sum = numbers.reduce((acc, cur) => {
  return acc + cur, 0;
});
console.log(sum); // renvoie: 100
```

### reduceRight():

Cette méthode est similaire à la
méthode reduce(). Cependant, elle itère sur les
éléments du tableau de droite à gauche au lieu de
de gauche à droite.

```js
const numbers = [10, 20, 30, 40];
const sum = numbers.reduceRight((acc, cur) => {
  return acc + cur;
});
console.log(sum); // renvoie: 100
```

### isArray():

Cette méthode détermine si un
objet est un tableau.

```js
const fruits = ["pomme", "banane", "fraise"];
console.log(Array.isArray(fruits)); // renvoie: true

const number = 123;
console.log(Array.isArray(number)); // renvoie: false
```

### filter():

Cette méthode crée un nouveau tableau avec
tous les éléments qui réussissent le test mis en œuvre par
la fonction fournie.

```js
const numbers = [10, 20, 30, 40];
const filteredNumbers = numbers.filter((num) => {
  return num > 20;
  console.log(filteredNumbers); // renvoie: [ 30, 40 ]
});
```

### keys():

Cette méthode renvoie un tableau contenant
les clés de l'objet donné.

```js
const myObj = { a: 1, b: 2, c: 3 };
const keysArray = Object.keys(myObj);
console.log(keysArray); // renvoie: [ 'a', 'b', 'c' ]
```

### map():

Cette méthode crée un nouveau tableau avec
les résultats de l'appel d'une fonction fournie sur
chaque élément dans le tableau appelant.

```js
const numbers = [1, 2, 3, 4, 5];
const squaredNumbers = numbers.map((num) => {
  return num * num;
});

console.log(squaredNumbers); // renvoie: [ 1, 4, 9, 16, 25 ]
```
