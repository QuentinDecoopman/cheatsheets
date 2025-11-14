# GraphQL

## Documentation

https://graphql.org/learn/

## Présentation rapide

GraphQL est un langage de requête pour les API et un environnement d'exécution pour exécuter ces requêtes sur vos
données. Les clients demandent exactement les champs dont ils ont besoin (`query`), peuvent modifier des données
(`mutation`) et s'abonner aux changements (`subscription`).

## Commandes courantes & outils

Voici les commandes et actions les plus fréquemment utilisées lors du développement avec GraphQL.

### Requêtes HTTP (curl)

Exécuter une query simple avec `curl` :

```sh
curl -X POST http://localhost:4000/graphql \
  -H "Content-Type: application/json" \
  -d '{ "query": "{ users { id name email } }" }'
```

Exécuter une mutation :

```sh
curl -X POST http://localhost:4000/graphql \
  -H "Content-Type: application/json" \
  -d '{ "query": "mutation { createUser(input: { name: \"Alice\", email: \"a@a.com\" }) { id } }" }'
```

### Outils CLI & dev

- Installer Apollo Server (exemple) :

```sh
npm install apollo-server graphql
```

- Lancer un serveur GraphQL local (script npm) :

```json
"scripts": {
  "dev": "node src/index.js",
  "start": "node dist/index.js"
}
```

- Télécharger le schéma depuis un endpoint (Apollo CLI / `rover`) :

```sh
npx apollo client:download-schema --endpoint=http://localhost:4000/graphql schema.graphql
# ou avec rover (si installé): rover graph introspect http://localhost:4000/graphql > schema.graphql
```

### GraphQL Code Generator (générer types)

Installer :

```sh
npm install -D @graphql-codegen/cli
npx graphql-codegen init
```

Commandes fréquentes :

```sh
npx graphql-codegen --config codegen.yml
```

### Exemple rapide client (graphql-request)

```js
import { request, gql } from "graphql-request";
const endpoint = process.env.GRAPHQL_URL || "http://localhost:4000/graphql";

const query = gql`
  {
    users {
      id
      name
    }
  }
`;
const data = await request(endpoint, query);
console.log(data);
```

### Playground / GraphiQL / Altair

- Lancer GraphQL Playground intégré (souvent disponible à `/graphql` en dev).
- Utiliser Altair ou Insomnia pour tester les requêtes graphiquement.

## Exemples de requêtes

Query :

```graphql
query GetUsers {
  users {
    id
    name
    email
  }
}
```

Mutation :

```graphql
mutation CreateUser($input: CreateUserInput!) {
  createUser(input: $input) {
    id
    name
  }
}
```

Subscription (WebSocket) :

```graphql
subscription OnUserCreated {
  userCreated {
    id
    name
  }
}
```

## Bonnes pratiques rapides

- Valider et limiter la profondeur des requêtes pour éviter l'abus.
- Mettre en cache côté client et utiliser `persisted queries` si besoin.
- Documenter le schéma et utiliser des descriptions sur types/champs.

## Ressources

- Learn GraphQL : https://graphql.org/learn/
- Apollo Docs : https://www.apollographql.com/docs/

