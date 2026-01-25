# Conventions & Bonnes Pratiques Sécurité

## 📋 Principes Fondamentaux

### Principe du moindre privilège

```
Donner uniquement les permissions nécessaires
- Utilisateurs
- Applications
- Services
- API
```

### Défense en profondeur

```
Plusieurs couches de sécurité
- Réseau
- Application
- Base de données
- OS
- Physique
```

### Ne jamais faire confiance aux données utilisateur

```
Valider et nettoyer TOUTES les entrées
- Formulaires
- URL
- Headers HTTP
- Cookies
- API
```

---

## 🔐 Authentification

### Mots de passe

#### Hashing

```javascript
// ✅ Bon - bcrypt (recommandé)
import bcrypt from "bcrypt";

const saltRounds = 10;
const hashedPassword = await bcrypt.hash(password, saltRounds);

// Vérification
const isValid = await bcrypt.compare(password, hashedPassword);

// ✅ Alternative - argon2
import argon2 from "argon2";

const hashedPassword = await argon2.hash(password);
const isValid = await argon2.verify(hashedPassword, password);

// ❌ JAMAIS - MD5, SHA1, SHA256 seuls
const hash = md5(password); // NON!
const hash = sha256(password); // NON!
```

#### Politique de mots de passe

```javascript
// ✅ Recommandations ANSSI/OWASP
const passwordPolicy = {
  minLength: 12,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true,
  preventCommon: true, // Pas de "password123"
  preventUserInfo: true, // Pas de nom, email, etc.
};

// Validation
import zxcvbn from "zxcvbn";

const strength = zxcvbn(password);
if (strength.score < 3) {
  throw new Error("Mot de passe trop faible");
}
```

### JWT (JSON Web Tokens)

#### Bonnes pratiques

```javascript
import jwt from "jsonwebtoken";

// ✅ Bon - Configuration sécurisée
const token = jwt.sign(
  { userId: user.id, role: user.role },
  process.env.JWT_SECRET, // Secret fort, dans .env
  {
    expiresIn: "15m", // Courte durée
    algorithm: "HS256",
    issuer: "myapp",
    audience: "myapp-users",
  },
);

// Refresh token (durée plus longue)
const refreshToken = jwt.sign(
  { userId: user.id, type: "refresh" },
  process.env.JWT_REFRESH_SECRET,
  { expiresIn: "7d" },
);

// Vérification
try {
  const decoded = jwt.verify(token, process.env.JWT_SECRET, {
    algorithms: ["HS256"],
    issuer: "myapp",
    audience: "myapp-users",
  });
} catch (err) {
  // Token invalide ou expiré
}

// ❌ Éviter
const token = jwt.sign(data, "secret123"); // Secret faible
const token = jwt.sign(data, secret, { expiresIn: "30d" }); // Trop long
const decoded = jwt.decode(token); // Ne vérifie PAS la signature!
```

### Sessions

#### Configuration sécurisée

```javascript
// Express session
import session from "express-session";

app.use(
  session({
    secret: process.env.SESSION_SECRET, // Secret fort
    name: "sessionId", // Pas 'connect.sid' (par défaut)
    resave: false,
    saveUninitialized: false,
    cookie: {
      secure: true, // HTTPS uniquement
      httpOnly: true, // Pas accessible via JavaScript
      sameSite: "strict", // Protection CSRF
      maxAge: 1000 * 60 * 60, // 1 heure
      domain: ".example.com",
    },
    store: new RedisStore({
      // Pas en mémoire
      client: redisClient,
    }),
  }),
);
```

---

## 🛡️ Protection des Données

### Chiffrement

#### En transit (HTTPS/TLS)

```nginx
# Nginx - Configuration SSL
server {
    listen 443 ssl http2;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    # TLS 1.2 et 1.3 uniquement
    ssl_protocols TLSv1.2 TLSv1.3;

    # Ciphers sécurisés
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers on;

    # HSTS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
}
```

#### Au repos

```javascript
// Chiffrement symétrique (AES)
import crypto from "crypto";

const algorithm = "aes-256-gcm";
const key = crypto.scryptSync(process.env.ENCRYPTION_KEY, "salt", 32);

function encrypt(text) {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv(algorithm, key, iv);

  let encrypted = cipher.update(text, "utf8", "hex");
  encrypted += cipher.final("hex");

  const authTag = cipher.getAuthTag();

  return {
    iv: iv.toString("hex"),
    encrypted,
    authTag: authTag.toString("hex"),
  };
}

function decrypt(encryptedData) {
  const decipher = crypto.createDecipheriv(
    algorithm,
    key,
    Buffer.from(encryptedData.iv, "hex"),
  );

  decipher.setAuthTag(Buffer.from(encryptedData.authTag, "hex"));

  let decrypted = decipher.update(encryptedData.encrypted, "hex", "utf8");
  decrypted += decipher.final("utf8");

  return decrypted;
}
```

### Gestion des secrets

#### Variables d'environnement

```bash
# .env (ne JAMAIS commiter!)
DB_PASSWORD=strong_password_here
JWT_SECRET=very_long_random_secret_string
API_KEY=secret_api_key

# .gitignore
.env
.env.local
.env.*.local
```

#### Secrets management

```javascript
// AWS Secrets Manager
import {
  SecretsManagerClient,
  GetSecretValueCommand,
} from "@aws-sdk/client-secrets-manager";

const client = new SecretsManagerClient({ region: "eu-west-3" });
const response = await client.send(
  new GetSecretValueCommand({ SecretId: "myapp/db/password" }),
);
const secret = response.SecretString;

// Azure Key Vault
import { SecretClient } from "@azure/keyvault-secrets";

const client = new SecretClient(vaultUrl, credential);
const secret = await client.getSecret("db-password");
```

---

## 🚫 Validation et Sanitization

### Validation des entrées

```javascript
import { z } from "zod";

// Schéma de validation
const userSchema = z.object({
  email: z.string().email(),
  password: z.string().min(12),
  age: z.number().int().min(18).max(120),
  role: z.enum(["user", "admin"]),
});

// Validation
try {
  const validData = userSchema.parse(req.body);
} catch (error) {
  // Données invalides
}

// ✅ Avec express-validator
import { body, validationResult } from "express-validator";

app.post(
  "/register",
  body("email").isEmail().normalizeEmail(),
  body("password").isLength({ min: 12 }),
  body("age").isInt({ min: 18, max: 120 }),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // Traitement
  },
);
```

### Sanitization

```javascript
import DOMPurify from "isomorphic-dompurify";
import validator from "validator";

// HTML
const clean = DOMPurify.sanitize(userInput);

// SQL (utiliser des paramètres préparés)
// ✅ Bon
const result = await db.query("SELECT * FROM users WHERE id = $1", [userId]);

// ❌ JAMAIS
const result = await db.query(`SELECT * FROM users WHERE id = ${userId}`);

// String escape
const safe = validator.escape(userInput);
const trimmed = validator.trim(userInput);
```

---

## 🔒 Headers de Sécurité

### Configuration Express

```javascript
import helmet from "helmet";

app.use(helmet());

// Ou configuration manuelle
app.use(
  helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'"],
        imgSrc: ["'self'", "data:", "https:"],
      },
    },
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true,
    },
    referrerPolicy: { policy: "same-origin" },
  }),
);

// Headers additionnels
app.use((req, res, next) => {
  res.setHeader("X-Content-Type-Options", "nosniff");
  res.setHeader("X-Frame-Options", "DENY");
  res.setHeader("X-XSS-Protection", "1; mode=block");
  res.setHeader("Permissions-Policy", "geolocation=(), microphone=()");
  next();
});
```

---

## 🛑 Rate Limiting

### Express Rate Limit

```javascript
import rateLimit from "express-rate-limit";

// Limiter général
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requêtes
  message: "Trop de requêtes, réessayez plus tard",
  standardHeaders: true,
  legacyHeaders: false,
});

app.use("/api/", limiter);

// Limiter pour login
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  skipSuccessfulRequests: true,
});

app.post("/login", loginLimiter, loginHandler);

// Avec Redis (distribué)
import RedisStore from "rate-limit-redis";

const limiter = rateLimit({
  store: new RedisStore({
    client: redisClient,
  }),
  windowMs: 15 * 60 * 1000,
  max: 100,
});
```

---

## 🔐 CORS

### Configuration

```javascript
import cors from "cors";

// ✅ Bon - restreindre les origines
app.use(
  cors({
    origin: ["https://myapp.com", "https://admin.myapp.com"],
    methods: ["GET", "POST", "PUT", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"],
    credentials: true,
    maxAge: 86400,
  }),
);

// Ou avec fonction
app.use(
  cors({
    origin: (origin, callback) => {
      const allowedOrigins = process.env.ALLOWED_ORIGINS.split(",");
      if (!origin || allowedOrigins.includes(origin)) {
        callback(null, true);
      } else {
        callback(new Error("Not allowed by CORS"));
      }
    },
  }),
);

// ❌ Éviter
app.use(cors()); // Autorise tout le monde
app.use(cors({ origin: "*" })); // Pareil
```

---

## 🔍 Logging et Monitoring

### Logs sécurisés

```javascript
import winston from "winston";

const logger = winston.createLogger({
  level: "info",
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: "error.log", level: "error" }),
    new winston.transports.File({ filename: "combined.log" }),
  ],
});

// ✅ Bon - masquer les données sensibles
logger.info("User login", {
  userId: user.id,
  email: user.email.replace(/(.{2}).*(@.*)/, "$1***$2"),
  ip: req.ip,
});

// ❌ JAMAIS logger
logger.info("Login attempt", {
  password: password, // NON!
  token: token, // NON!
  creditCard: card, // NON!
});
```

### Monitoring

```javascript
// Détecter les attaques
const suspiciousActivity = {
  multipleFailedLogins: 5,
  rapidRequests: 50,
  unusualIPChange: true,
};

// Alertes
if (failedLoginAttempts > 5) {
  await sendAlert({
    type: "SECURITY",
    message: `Multiple failed login attempts for ${email}`,
    ip: req.ip,
  });

  // Bloquer temporairement
  await blockIP(req.ip, "1h");
}
```

---

## 📋 Checklist Sécurité

### Application

- [ ] HTTPS/TLS configuré
- [ ] Headers de sécurité (Helmet)
- [ ] CORS configuré correctement
- [ ] Rate limiting en place
- [ ] Validation des entrées
- [ ] Protection CSRF
- [ ] Protection XSS
- [ ] Protection injection SQL
- [ ] Secrets dans variables d'environnement
- [ ] Logs sécurisés (pas de données sensibles)

### Authentification

- [ ] Hashing bcrypt/argon2 pour mots de passe
- [ ] Politique de mots de passe forts
- [ ] 2FA disponible
- [ ] JWT avec expiration courte
- [ ] Refresh tokens sécurisés
- [ ] Sessions avec httpOnly + secure cookies
- [ ] Timeout de session

### Base de données

- [ ] Paramètres préparés (pas de concaténation)
- [ ] Moindre privilège pour utilisateur DB
- [ ] Chiffrement des données sensibles
- [ ] Backups réguliers et chiffrés
- [ ] Audit logs activés

### Infrastructure

- [ ] Firewall configuré
- [ ] Mises à jour régulières
- [ ] Monitoring et alertes
- [ ] Plan de réponse aux incidents
- [ ] Tests de pénétration réguliers

---

## 📚 Ressources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/)
- [ANSSI - Recommandations](https://www.ssi.gouv.fr/)
- [CWE - Common Weakness Enumeration](https://cwe.mitre.org/)
- [Mozilla Security Guidelines](https://infosec.mozilla.org/guidelines/)
- [Security Headers](https://securityheaders.com/)
