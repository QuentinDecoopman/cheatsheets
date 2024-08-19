# Programmation orientée objet

- [Index](/Readme.md)

## Class

```js
class Person {
    firstname;
    lastname;
    age;

    constructor(firstname, lastname, age) {
        this.firstname = firstname;
        this.lastname = lastname;
        this.age = age;
    }

    sayHello() {
        console.log(
            `Hello ${this.firstname} ${this.lastname} et j'ai ${this.age} ans`
        );
    }
}
```

### ou

```js
class Voiture {
    #sound;
    #type = "voiture";

    constructor(soundValue) {
        this.#sound = soundValue;
    }

    start() {
        console.log(this.#sound);
    }

    get type() {
        return this.#type;
    }
}

let voiture = new Voiture("tutut");
voiture.start();
```

## mixins

Permet de partager des features avec plusieurs classes.

````js
class A {
}

class B {
}

const mixin = {
    methode1() {
    },
    methode2() {
    }
};

Object.assign(A.prototype, mixin);
Object.assign(B.prototype, mixin);

const instanceA = new A();
const instanceB = new B();

instanceA.methode1();
instanceB.methode2();
````