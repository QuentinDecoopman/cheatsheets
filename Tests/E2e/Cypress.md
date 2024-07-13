# Cypress

- [Index](/Readme.md)
- [Doc](https://www.cypress.io/)

## Installation

```bash
npm install cypress --save-dev
```

## Initialisation
```bash
npx cypress open
```

## Ecriture de test
test basique
```js
describe('My First Test', () => {
  it('Does not do much!', () => {
    expect(true).to.equal(true);
  });
});

```

### Accéder à une Page
```js
describe('My First Test', () => {
  it('Visits the Kitchen Sink', () => {
    cy.visit('https://example.cypress.io');
  });
});
```

## Sélection d'éléments

```js
cy.get('.btn');              // Sélection par classe
cy.get('#main');             // Sélection par ID
cy.get('button');            // Sélection par tag
cy.get('[data-test-id]');    // Sélection par attribut

```

## Intéractions avec les éléments
```js
cy.get('input').type('Hello, World!');      // Taper du texte
cy.get('button').click();                   // Cliquer sur un bouton
cy.get('form').submit();                    // Soumettre un formulaire
cy.get('select').select('option1');         // Sélectionner une option
cy.get('input').check();                    // Cocher une case
cy.get('input').uncheck();                  // Décocher une case
cy.get('input').clear();                    // Effacer un champ

```

## Assertions
```js
cy.get('.message').should('contain', 'Hello, World!');
cy.url().should('include', '/home');
cy.get('.btn').should('be.visible');
cy.get('input').should('have.value', 'Hello, World!');
cy.get('input').should('not.be.disabled');
```

## Attendre des éléments
```js
cy.wait(1000);                         // Attendre un temps spécifique
cy.get('.btn').should('exist');        // Attendre que l'élément existe
cy.get('.btn').should('be.visible');   // Attendre que l'élément soit visible
```

## Tests Réseaux (stub & mock)
## Intercépter une requête
```js
cy.wait(1000);                         // Attendre un temps spécifique
cy.get('.btn').should('exist');        // Attendre que l'élément existe
cy.get('.btn').should('be.visible');   // Attendre que l'élément soit visible
```

## Attendre une requête spécifique
```js
cy.intercept('POST', '/api/login').as('loginRequest');
cy.get('button[type=submit]').click();
cy.wait('@loginRequest').its('response.statusCode').should('eq', 200);
```

## Fixtures (fichiers JSON contenant des données de test)
```js
cy.fixture('example.json').then((data) => {
  cy.get('input[name=firstName]').type(data.firstName);
});
```