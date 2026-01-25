# Conventions & Bonnes Pratiques GraphQL

## 📋 Conventions de Nommage

### Schéma GraphQL

```graphql
# ✅ Types - PascalCase
type User {
  id: ID!
  firstName: String!
  lastName: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  createdAt: DateTime!
}

# ✅ Queries - camelCase, verbe descriptif
type Query {
  user(id: ID!): User
  users(limit: Int, offset: Int): [User!]!
  post(id: ID!): Post
  posts(authorId: ID): [Post!]!
  searchPosts(query: String!): [Post!]!
}

# ✅ Mutations - camelCase, verbe d'action
type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User!
  deleteUser(id: ID!): Boolean!

  createPost(input: CreatePostInput!): Post!
  updatePost(id: ID!, input: UpdatePostInput!): Post!
  deletePost(id: ID!): Boolean!
}

# ✅ Inputs - PascalCase avec suffix "Input"
input CreateUserInput {
  firstName: String!
  lastName: String!
  email: String!
}

input UpdateUserInput {
  firstName: String
  lastName: String
  email: String
}

# ✅ Enums - PascalCase, valeurs SCREAMING_SNAKE_CASE
enum Role {
  ADMIN
  USER
  MODERATOR
}

enum PostStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}
```

---

## 🏗️ Structure du Schéma

### Types scalaires personnalisés

```graphql
scalar DateTime
scalar Email
scalar URL
scalar JSON

type User {
  id: ID!
  email: Email!
  website: URL
  metadata: JSON
  createdAt: DateTime!
}
```

### Interfaces

```graphql
interface Node {
  id: ID!
}

interface Timestamped {
  createdAt: DateTime!
  updatedAt: DateTime!
}

type User implements Node & Timestamped {
  id: ID!
  name: String!
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Post implements Node & Timestamped {
  id: ID!
  title: String!
  createdAt: DateTime!
  updatedAt: DateTime!
}
```

### Unions

```graphql
union SearchResult = User | Post | Comment

type Query {
  search(query: String!): [SearchResult!]!
}

# Query avec fragment
query Search($query: String!) {
  search(query: $query) {
    ... on User {
      id
      name
    }
    ... on Post {
      id
      title
    }
    ... on Comment {
      id
      text
    }
  }
}
```

---

## ✅ Résolveurs (Resolvers)

### Structure de base

```typescript
// types.ts
export interface Context {
  db: Database;
  user?: User;
}

export interface User {
  id: string;
  firstName: string;
  lastName: string;
  email: string;
}

// resolvers/user.ts
import { GraphQLResolverMap } from "apollo-server";
import { Context } from "../types";

export const userResolvers: GraphQLResolverMap<any, Context> = {
  Query: {
    user: async (parent, { id }, context) => {
      return context.db.users.findUnique({ where: { id } });
    },

    users: async (parent, { limit = 10, offset = 0 }, context) => {
      return context.db.users.findMany({
        take: limit,
        skip: offset,
      });
    },
  },

  Mutation: {
    createUser: async (parent, { input }, context) => {
      return context.db.users.create({
        data: input,
      });
    },

    updateUser: async (parent, { id, input }, context) => {
      return context.db.users.update({
        where: { id },
        data: input,
      });
    },

    deleteUser: async (parent, { id }, context) => {
      await context.db.users.delete({ where: { id } });
      return true;
    },
  },

  // Résolveurs de champs
  User: {
    // Champ virtuel
    fullName: (parent) => {
      return `${parent.firstName} ${parent.lastName}`;
    },

    // Résolution de relation
    posts: async (parent, args, context) => {
      return context.db.posts.findMany({
        where: { authorId: parent.id },
      });
    },
  },
};
```

### DataLoader (résout le problème N+1)

```typescript
import DataLoader from "dataloader";

// DataLoader pour batch loading
const userLoader = new DataLoader(async (userIds: readonly string[]) => {
  const users = await db.users.findMany({
    where: { id: { in: [...userIds] } },
  });

  // Retourner dans le même ordre que les IDs
  return userIds.map((id) => users.find((user) => user.id === id));
});

// Context avec DataLoaders
export const createContext = ({ req }: any): Context => {
  return {
    db,
    user: req.user,
    loaders: {
      user: userLoader,
      post: postLoader,
    },
  };
};

// Utilisation dans resolver
const postResolvers = {
  Post: {
    author: async (parent, args, context) => {
      // Batch automatiquement les requêtes
      return context.loaders.user.load(parent.authorId);
    },
  },
};
```

---

## 🔐 Authentification et Autorisation

### Authentification

```typescript
import { AuthenticationError } from "apollo-server";
import jwt from "jsonwebtoken";

// Middleware d'authentification
export const createContext = ({ req }: any): Context => {
  const token = req.headers.authorization?.replace("Bearer ", "");

  let user = null;
  if (token) {
    try {
      user = jwt.verify(token, process.env.JWT_SECRET!);
    } catch (error) {
      // Token invalide
    }
  }

  return {
    db,
    user,
  };
};

// Vérification dans resolver
const userResolvers = {
  Query: {
    me: (parent, args, context) => {
      if (!context.user) {
        throw new AuthenticationError("Not authenticated");
      }
      return context.user;
    },
  },
};
```

### Autorisation

```typescript
import { ForbiddenError } from "apollo-server";

// Directive @auth
const authDirective = {
  typeDefs: `
    directive @auth(requires: Role = USER) on OBJECT | FIELD_DEFINITION
    
    enum Role {
      ADMIN
      USER
    }
  `,

  transformer: (schema) => {
    // Implementation de la directive
  },
};

// Vérification manuelle
const postResolvers = {
  Mutation: {
    deletePost: async (parent, { id }, context) => {
      if (!context.user) {
        throw new AuthenticationError("Not authenticated");
      }

      const post = await context.db.posts.findUnique({ where: { id } });

      if (post.authorId !== context.user.id && context.user.role !== "ADMIN") {
        throw new ForbiddenError("Not authorized");
      }

      await context.db.posts.delete({ where: { id } });
      return true;
    },
  },
};
```

---

## 📄 Pagination

### Cursor-based (recommandé)

```graphql
type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

type PostEdge {
  node: Post!
  cursor: String!
}

type PostConnection {
  edges: [PostEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type Query {
  posts(first: Int, after: String, last: Int, before: String): PostConnection!
}
```

```typescript
// Resolver avec cursor-based pagination
const queryResolvers = {
  posts: async (parent, { first = 10, after }, context) => {
    const posts = await context.db.posts.findMany({
      take: first + 1,
      cursor: after ? { id: after } : undefined,
      orderBy: { createdAt: "desc" },
    });

    const hasNextPage = posts.length > first;
    const edges = posts.slice(0, first).map((post) => ({
      node: post,
      cursor: post.id,
    }));

    return {
      edges,
      pageInfo: {
        hasNextPage,
        hasPreviousPage: !!after,
        startCursor: edges[0]?.cursor,
        endCursor: edges[edges.length - 1]?.cursor,
      },
      totalCount: await context.db.posts.count(),
    };
  },
};
```

### Offset-based (simple)

```graphql
type PostList {
  items: [Post!]!
  total: Int!
  page: Int!
  perPage: Int!
}

type Query {
  posts(page: Int = 1, perPage: Int = 10): PostList!
}
```

```typescript
const queryResolvers = {
  posts: async (parent, { page = 1, perPage = 10 }, context) => {
    const [items, total] = await Promise.all([
      context.db.posts.findMany({
        take: perPage,
        skip: (page - 1) * perPage,
      }),
      context.db.posts.count(),
    ]);

    return { items, total, page, perPage };
  },
};
```

---

## 🚀 Optimisations

### Limitation de profondeur de query

```typescript
import depthLimit from "graphql-depth-limit";

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [depthLimit(5)],
});
```

### Limitation de complexité

```typescript
import { createComplexityLimitRule } from "graphql-validation-complexity";

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [
    createComplexityLimitRule(1000, {
      onCost: (cost) => console.log("query cost:", cost),
    }),
  ],
});
```

### Caching

```typescript
// Field-level caching avec @cacheControl
const typeDefs = gql`
  type Query {
    posts: [Post!]! @cacheControl(maxAge: 60)
    user(id: ID!): User @cacheControl(maxAge: 300)
  }

  type Post @cacheControl(maxAge: 30) {
    id: ID!
    title: String!
  }
`;

const server = new ApolloServer({
  typeDefs,
  resolvers,
  cacheControl: {
    defaultMaxAge: 0,
  },
});
```

---

## 🔄 Subscriptions

### Schéma

```graphql
type Subscription {
  postAdded: Post!
  postUpdated(id: ID!): Post!
  messageReceived(chatId: ID!): Message!
}
```

### Implémentation (avec PubSub)

```typescript
import { PubSub } from "graphql-subscriptions";

const pubsub = new PubSub();

const resolvers = {
  Mutation: {
    createPost: async (parent, { input }, context) => {
      const post = await context.db.posts.create({ data: input });

      // Publier événement
      pubsub.publish("POST_ADDED", { postAdded: post });

      return post;
    },
  },

  Subscription: {
    postAdded: {
      subscribe: () => pubsub.asyncIterator(["POST_ADDED"]),
    },

    messageReceived: {
      subscribe: (parent, { chatId }) => {
        return pubsub.asyncIterator([`MESSAGE_${chatId}`]);
      },
    },
  },
};
```

---

## 💻 Client (Apollo Client)

### Configuration

```typescript
import { ApolloClient, InMemoryCache, HttpLink } from "@apollo/client";

const client = new ApolloClient({
  link: new HttpLink({
    uri: "http://localhost:4000/graphql",
    headers: {
      authorization: `Bearer ${token}`,
    },
  }),
  cache: new InMemoryCache({
    typePolicies: {
      Query: {
        fields: {
          posts: {
            keyArgs: false,
            merge(existing = [], incoming) {
              return [...existing, ...incoming];
            },
          },
        },
      },
    },
  }),
});
```

### Queries

```typescript
import { gql, useQuery } from '@apollo/client';

const GET_USERS = gql`
  query GetUsers {
    users {
      id
      firstName
      lastName
      email
    }
  }
`;

function UsersList() {
  const { loading, error, data } = useQuery(GET_USERS);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {data.users.map((user) => (
        <li key={user.id}>{user.firstName} {user.lastName}</li>
      ))}
    </ul>
  );
}

// Avec variables
const GET_USER = gql`
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      firstName
      lastName
    }
  }
`;

function UserProfile({ userId }) {
  const { loading, error, data } = useQuery(GET_USER, {
    variables: { id: userId },
  });

  // ...
}
```

### Mutations

```typescript
const CREATE_USER = gql`
  mutation CreateUser($input: CreateUserInput!) {
    createUser(input: $input) {
      id
      firstName
      lastName
      email
    }
  }
`;

function CreateUserForm() {
  const [createUser, { loading, error }] = useMutation(CREATE_USER, {
    // Refetch queries après mutation
    refetchQueries: [{ query: GET_USERS }],

    // Ou update cache manuellement
    update(cache, { data: { createUser } }) {
      cache.modify({
        fields: {
          users(existingUsers = []) {
            const newUserRef = cache.writeFragment({
              data: createUser,
              fragment: gql`
                fragment NewUser on User {
                  id
                  firstName
                  lastName
                }
              `,
            });
            return [...existingUsers, newUserRef];
          },
        },
      });
    },
  });

  const handleSubmit = async (formData) => {
    try {
      await createUser({
        variables: {
          input: formData,
        },
      });
    } catch (err) {
      console.error(err);
    }
  };

  return <form onSubmit={handleSubmit}>{/* form */}</form>;
}
```

---

## 🛠️ Configuration Serveur (Apollo Server)

```typescript
import { ApolloServer } from "apollo-server-express";
import express from "express";
import { readFileSync } from "fs";
import { resolvers } from "./resolvers";

const typeDefs = readFileSync("./schema.graphql", "utf-8");

const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req }) => createContext({ req }),

  // Formatage des erreurs
  formatError: (error) => {
    console.error(error);
    return {
      message: error.message,
      code: error.extensions?.code,
    };
  },

  // Plugins
  plugins: [
    {
      requestDidStart: async () => ({
        didEncounterErrors: async ({ errors }) => {
          console.error(errors);
        },
      }),
    },
  ],

  // Introspection et playground
  introspection: process.env.NODE_ENV !== "production",
  playground: process.env.NODE_ENV !== "production",
});

const app = express();
server.applyMiddleware({ app });

app.listen({ port: 4000 }, () => {
  console.log(`🚀 Server ready at http://localhost:4000${server.graphqlPath}`);
});
```

---

## 📚 Ressources

- [GraphQL Documentation](https://graphql.org/learn/)
- [Apollo Server](https://www.apollographql.com/docs/apollo-server/)
- [Apollo Client](https://www.apollographql.com/docs/react/)
- [GraphQL Best Practices](https://graphql.org/learn/best-practices/)
- [Relay GraphQL Spec](https://relay.dev/graphql/connections.htm)
