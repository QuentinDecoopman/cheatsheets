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
