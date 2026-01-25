# Conventions & Bonnes Pratiques Node.js

## 📋 Conventions de Nommage

### Fichiers et Dossiers

- **kebab-case** ou **camelCase** pour les fichiers

```
user-controller.js
userController.js
database-config.js
```

### Variables et Fonctions

- **camelCase** pour les variables et fonctions

```javascript
const userName = "John";
const getUserById = (id) => {};
```

### Classes et Constructeurs

- **PascalCase** pour les classes

```javascript
class UserService {
  constructor() {}
}
```

### Constantes

- **UPPER_SNAKE_CASE** pour les constantes

```javascript
const MAX_CONNECTIONS = 10;
const API_BASE_URL = "https://api.example.com";
```

---

## 🏗️ Structure de Projet

### Organisation MVC/Clean Architecture

```
project/
├── src/
│   ├── config/
│   │   ├── database.js
│   │   └── environment.js
│   ├── controllers/
│   │   └── user.controller.js
│   ├── models/
│   │   └── user.model.js
│   ├── services/
│   │   └── user.service.js
│   ├── routes/
│   │   └── user.routes.js
│   ├── middlewares/
│   │   ├── auth.middleware.js
│   │   └── error.middleware.js
│   ├── utils/
│   │   └── logger.js
│   ├── validators/
│   │   └── user.validator.js
│   └── app.js
├── tests/
│   ├── unit/
│   └── integration/
├── .env
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

---

## ✅ Bonnes Pratiques Générales

### 1. Variables d'environnement

```javascript
// ✅ Bon - utiliser dotenv
require("dotenv").config();

const config = {
  port: process.env.PORT || 3000,
  dbUrl: process.env.DATABASE_URL,
  jwtSecret: process.env.JWT_SECRET,
  nodeEnv: process.env.NODE_ENV || "development",
};

// ❌ Jamais de secrets en dur
const API_KEY = "secret123"; // Mauvais
```

### 2. Gestion des erreurs asynchrones

```javascript
// ✅ Bon - avec async/await et try/catch
const getUserById = async (req, res, next) => {
  try {
    const user = await User.findById(req.params.id);
    if (!user) {
      return res.status(404).json({ error: "User not found" });
    }
    res.json(user);
  } catch (error) {
    next(error); // Passer à l'error middleware
  }
};

// Wrapper pour éviter la répétition
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

const getUserById = asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user);
});
```

### 3. Middleware d'erreur global

```javascript
// ✅ Error middleware
const errorHandler = (err, req, res, next) => {
  console.error(err.stack);

  const statusCode = err.statusCode || 500;
  const message = err.message || "Internal Server Error";

  res.status(statusCode).json({
    error: {
      message,
      ...(process.env.NODE_ENV === "development" && { stack: err.stack }),
    },
  });
};

// À la fin de app.js
app.use(errorHandler);

// Erreurs personnalisées
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

throw new AppError("User not found", 404);
```

### 4. Validation des entrées

```javascript
// ✅ Avec Joi
const Joi = require("joi");

const userSchema = Joi.object({
  name: Joi.string().min(3).max(50).required(),
  email: Joi.string().email().required(),
  age: Joi.number().integer().min(18).max(120),
});

const validateUser = (req, res, next) => {
  const { error } = userSchema.validate(req.body);
  if (error) {
    return res.status(400).json({ error: error.details[0].message });
  }
  next();
};

// Route
router.post("/users", validateUser, createUser);
```

---

## 🔐 Sécurité

### 1. Helmet pour sécuriser les headers

```javascript
const helmet = require("helmet");
app.use(helmet());
```

### 2. Rate limiting

```javascript
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limite à 100 requêtes
  message: "Too many requests from this IP",
});

app.use("/api/", limiter);
```

### 3. CORS

```javascript
const cors = require("cors");

// Simple
app.use(cors());

// Configuré
const corsOptions = {
  origin: process.env.ALLOWED_ORIGINS?.split(",") || "*",
  credentials: true,
  optionsSuccessStatus: 200,
};

app.use(cors(corsOptions));
```

### 4. Protection contre les injections

```javascript
// ✅ Utiliser des requêtes paramétrées
const user = await db.query("SELECT * FROM users WHERE id = $1", [userId]);

// ❌ Jamais de concaténation
const query = `SELECT * FROM users WHERE id = ${userId}`; // Dangereux
```

### 5. Hashage des mots de passe

```javascript
const bcrypt = require("bcrypt");

// Hashage
const hashPassword = async (password) => {
  const saltRounds = 10;
  return await bcrypt.hash(password, saltRounds);
};

// Vérification
const verifyPassword = async (password, hash) => {
  return await bcrypt.compare(password, hash);
};
```

### 6. JWT Authentication

```javascript
const jwt = require("jsonwebtoken");

// Générer un token
const generateToken = (userId) => {
  return jwt.sign({ userId }, process.env.JWT_SECRET, { expiresIn: "7d" });
};

// Middleware d'authentification
const authMiddleware = (req, res, next) => {
  const token = req.headers.authorization?.split(" ")[1];

  if (!token) {
    return res.status(401).json({ error: "No token provided" });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.userId = decoded.userId;
    next();
  } catch (error) {
    return res.status(401).json({ error: "Invalid token" });
  }
};
```

---

## 🚀 Performance

### 1. Compression

```javascript
const compression = require("compression");
app.use(compression());
```

### 2. Caching

```javascript
// Cache en mémoire simple
const NodeCache = require("node-cache");
const cache = new NodeCache({ stdTTL: 600 }); // 10 minutes

const getCachedData = (key) => {
  const cached = cache.get(key);
  if (cached) return cached;

  const data = fetchData();
  cache.set(key, data);
  return data;
};

// Avec Redis
const redis = require("redis");
const client = redis.createClient({
  url: process.env.REDIS_URL,
});

await client.connect();

// Set
await client.setEx("key", 3600, JSON.stringify(data));

// Get
const cached = await client.get("key");
const data = cached ? JSON.parse(cached) : null;
```

### 3. Clustering

```javascript
const cluster = require("cluster");
const os = require("os");

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;

  console.log(`Master ${process.pid} is running`);

  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on("exit", (worker) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork();
  });
} else {
  // Workers can share TCP connection
  require("./app");
  console.log(`Worker ${process.pid} started`);
}
```

### 4. Connexion à la base de données

```javascript
// ✅ Pool de connexions
const { Pool } = require("pg");

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 20, // Maximum de connexions
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

// Utilisation
const getUsers = async () => {
  const client = await pool.connect();
  try {
    const result = await client.query("SELECT * FROM users");
    return result.rows;
  } finally {
    client.release();
  }
};
```

---

## 📝 Logging

### Winston Logger

```javascript
const winston = require("winston");

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json(),
  ),
  defaultMeta: { service: "user-service" },
  transports: [
    new winston.transports.File({ filename: "logs/error.log", level: "error" }),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

// Console en développement
if (process.env.NODE_ENV !== "production") {
  logger.add(
    new winston.transports.Console({
      format: winston.format.simple(),
    }),
  );
}

// Utilisation
logger.info("User created", { userId: 123 });
logger.error("Error occurred", { error: err.message });

module.exports = logger;
```

---

## 🔄 Streams et Buffers

### Streams pour les gros fichiers

```javascript
const fs = require("fs");

// ✅ Bon - avec streams
const readStream = fs.createReadStream("large-file.txt");
const writeStream = fs.createWriteStream("output.txt");

readStream.pipe(writeStream);

// Avec transformation
const { Transform } = require("stream");

const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  },
});

readStream.pipe(upperCaseTransform).pipe(writeStream);

// ❌ Éviter pour gros fichiers
const data = fs.readFileSync("large-file.txt"); // Charge tout en mémoire
```

---

## 🧪 Tests

### Jest

```javascript
// user.service.test.js
const UserService = require("../services/user.service");

describe("UserService", () => {
  describe("createUser", () => {
    it("should create a user successfully", async () => {
      const userData = {
        name: "John Doe",
        email: "john@example.com",
      };

      const user = await UserService.createUser(userData);

      expect(user).toBeDefined();
      expect(user.email).toBe(userData.email);
    });

    it("should throw error for invalid email", async () => {
      const userData = {
        name: "John Doe",
        email: "invalid-email",
      };

      await expect(UserService.createUser(userData)).rejects.toThrow(
        "Invalid email",
      );
    });
  });
});
```

### Supertest pour tests d'API

```javascript
const request = require("supertest");
const app = require("../app");

describe("User API", () => {
  it("GET /api/users should return all users", async () => {
    const response = await request(app).get("/api/users").expect(200);

    expect(Array.isArray(response.body)).toBe(true);
  });

  it("POST /api/users should create a user", async () => {
    const userData = {
      name: "John Doe",
      email: "john@example.com",
    };

    const response = await request(app)
      .post("/api/users")
      .send(userData)
      .expect(201);

    expect(response.body.email).toBe(userData.email);
  });
});
```

---

## 📦 Package.json Best Practices

```json
{
  "name": "my-node-app",
  "version": "1.0.0",
  "description": "Description de l'application",
  "main": "src/app.js",
  "scripts": {
    "start": "node src/app.js",
    "dev": "nodemon src/app.js",
    "test": "jest --coverage",
    "test:watch": "jest --watch",
    "lint": "eslint src/**/*.js",
    "lint:fix": "eslint src/**/*.js --fix",
    "format": "prettier --write \"src/**/*.js\""
  },
  "keywords": [],
  "author": "",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "nodemon": "^3.0.0",
    "eslint": "^8.0.0"
  },
  "engines": {
    "node": ">=18.0.0",
    "npm": ">=9.0.0"
  }
}
```

---

## 🛠️ Outils Recommandés

### ESLint Configuration

```javascript
// .eslintrc.js
module.exports = {
  env: {
    node: true,
    es2021: true,
    jest: true,
  },
  extends: ["eslint:recommended"],
  parserOptions: {
    ecmaVersion: "latest",
  },
  rules: {
    "no-console": "warn",
    "no-unused-vars": "error",
    "prefer-const": "error",
    "no-var": "error",
  },
};
```

### Prettier

```json
{
  "semi": true,
  "trailingComma": "none",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

---

## 📚 Ressources

- [Node.js Documentation](https://nodejs.org/docs/)
- [Express.js Best Practices](https://expressjs.com/en/advanced/best-practice-performance.html)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [The Twelve-Factor App](https://12factor.net/)
