# Cheatsheets Jest

- [Index](/Readme.md)
- [Docs](https://jestjs.io/fr/docs/getting-started)

# Installation

```
npm install --save-dev jest
```

## Commande de test
```bash
# Lancer les tests
npx jest

# Ou ajouter un script dans package.json
"scripts": {
  "test": "jest"
}

# Lancer les tests via npm
npm test

```

## Structure de base des tests
```js
// example.test.js
describe('Group of tests', () => {
  test('should work correctly', () => {
    expect(true).toBe(true);
  });
});
```

## Matchers courants
```js
// Exactitude
expect(value).toBe(value); // Utilisé pour les primitives
expect(obj).toEqual(obj); // Utilisé pour les objets et les tableaux

// Véracité
expect(value).toBeNull();
expect(value).toBeDefined();
expect(value).toBeUndefined();
expect(value).toBeTruthy();
expect(value).toBeFalsy();

// Numéros
expect(value).toBeGreaterThan(number);
expect(value).toBeGreaterThanOrEqual(number);
expect(value).toBeLessThan(number);
expect(value).toBeLessThanOrEqual(number);
expect(value).toBeCloseTo(number, numberOfDigits);

// Chaînes de caractères
expect(string).toMatch(/regex/);

// Tableaux et itérables
expect(array).toContain(item);

```

## Mock Functions
```js
// Création d'une fonction mock
const myMock = jest.fn();

// Mock d'une implémentation
myMock.mockImplementation(() => 'mocked value');

// Vérification des appels
expect(myMock).toHaveBeenCalled();
expect(myMock).toHaveBeenCalledWith(arg1, arg2);

// Mock d'un module
jest.mock('moduleName');
```

## tests asynchrones
## Callbacks
```js
test('fetchData callback', done => {
  function callback(data) {
    try {
      expect(data).toBe('peanut butter');
      done();
    } catch (error) {
      done(error);
    }
  }

  fetchData(callback);
});
```

## Promises
```js
test('fetchData promise', () => {
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});

test('fetchData promise with resolves/rejects', () => {
  return expect(fetchData()).resolves.toBe('peanut butter');
});

test('fetchData promise rejects', () => {
  return expect(fetchData()).rejects.toMatch('error');
});
```

## Async/Await
```js
test('fetchData async/await', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('fetchData async/await with resolves/rejects', async () => {
  await expect(fetchData()).resolves.toBe('peanut butter');
  await expect(fetchData()).rejects.toMatch('error');
});
```

## Setup et Teardown
```js
// Setup avant tous les tests
beforeAll(() => {
  // Code à exécuter avant tous les tests
});

// Setup avant chaque test
beforeEach(() => {
  // Code à exécuter avant chaque test
});

// Teardown après chaque test
afterEach(() => {
  // Code à exécuter après chaque test
});

// Teardown après tous les tests
afterAll(() => {
  // Code à exécuter après tous les tests
});
```

## Couverture de code
```bash
# Générer un rapport de couverture de code
npx jest --coverage

# Ajouter un script dans package.json
"scripts": {
  "test": "jest --coverage"
}
```

## Configuration de Jest
jest.config.js

```js
module.exports = {
  verbose: true,
  testEnvironment: 'node', // ou 'jsdom' pour les tests de DOM
  setupFilesAfterEnv: ['./jest.setup.js'],
  moduleNameMapper: {
    '\\.(css|less)$': 'identity-obj-proxy',
  },
  collectCoverage: true,
  collectCoverageFrom: ['src/**/*.{js,jsx}', '!src/index.js'],
};

```