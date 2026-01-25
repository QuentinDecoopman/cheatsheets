# Conventions & Bonnes Pratiques Express

## 📋 Conventions de Nommage

### Structure du projet

```
src/
├── config/
│   ├── database.ts
│   └── env.ts
├── controllers/
│   ├── authController.ts
│   └── userController.ts
├── middlewares/
│   ├── auth.ts
│   ├── errorHandler.ts
│   └── validation.ts
├── models/
│   ├── User.ts
│   └── Post.ts
├── routes/
│   ├── index.ts
│   ├── authRoutes.ts
│   └── userRoutes.ts
├── services/
│   ├── authService.ts
│   └── userService.ts
├── utils/
│   ├── logger.ts
│   └── helpers.ts
├── validators/
│   └── userValidator.ts
├── types/
│   └── index.ts
├── app.ts
└── server.ts
```

---

## 🏗️ Structure de Base

### app.ts (Configuration)

```typescript
import express, { Application } from "express";
import helmet from "helmet";
import cors from "cors";
import morgan from "morgan";
import compression from "compression";
import rateLimit from "express-rate-limit";

import routes from "./routes";
import { errorHandler } from "./middlewares/errorHandler";
import { notFoundHandler } from "./middlewares/notFoundHandler";

export const createApp = (): Application => {
  const app = express();

  // Security
  app.use(helmet());
  app.use(
    cors({
      origin: process.env.ALLOWED_ORIGINS?.split(","),
      credentials: true,
    }),
  );

  // Rate limiting
  const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
  });
  app.use("/api/", limiter);

  // Body parsing
  app.use(express.json({ limit: "10mb" }));
  app.use(express.urlencoded({ extended: true, limit: "10mb" }));

  // Compression
  app.use(compression());

  // Logging
  if (process.env.NODE_ENV !== "test") {
    app.use(morgan("combined"));
  }

  // Routes
  app.use("/api", routes);

  // Health check
  app.get("/health", (req, res) => {
    res.status(200).json({ status: "ok" });
  });

  // Error handlers (à la fin)
  app.use(notFoundHandler);
  app.use(errorHandler);

  return app;
};
```

### server.ts (Démarrage)

```typescript
import { createApp } from "./app";
import { logger } from "./utils/logger";

const PORT = process.env.PORT || 3000;

const app = createApp();

const server = app.listen(PORT, () => {
  logger.info(`Server running on port ${PORT}`);
});

// Graceful shutdown
process.on("SIGTERM", () => {
  logger.info("SIGTERM signal received: closing HTTP server");
  server.close(() => {
    logger.info("HTTP server closed");
    process.exit(0);
  });
});
```

---

## 🛣️ Routes

### routes/index.ts

```typescript
import { Router } from "express";
import authRoutes from "./authRoutes";
import userRoutes from "./userRoutes";
import postRoutes from "./postRoutes";

const router = Router();

router.use("/auth", authRoutes);
router.use("/users", userRoutes);
router.use("/posts", postRoutes);

export default router;
```

### routes/userRoutes.ts

```typescript
import { Router } from "express";
import * as userController from "../controllers/userController";
import { authenticate } from "../middlewares/auth";
import { validate } from "../middlewares/validation";
import {
  createUserSchema,
  updateUserSchema,
} from "../validators/userValidator";

const router = Router();

// Public routes
router.get("/", userController.getAllUsers);
router.get("/:id", userController.getUserById);

// Protected routes
router.use(authenticate); // Toutes les routes suivantes nécessitent l'authentification

router.post("/", validate(createUserSchema), userController.createUser);
router.put("/:id", validate(updateUserSchema), userController.updateUser);
router.delete("/:id", userController.deleteUser);

export default router;
```

---

## 🎮 Controllers

```typescript
// controllers/userController.ts
import { Request, Response, NextFunction } from "express";
import * as userService from "../services/userService";
import { AppError } from "../utils/AppError";

// ✅ Async handler wrapper
export const asyncHandler =
  (fn: Function) => (req: Request, res: Response, next: NextFunction) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };

// GET /api/users
export const getAllUsers = asyncHandler(async (req: Request, res: Response) => {
  const { page = 1, limit = 10 } = req.query;

  const users = await userService.getAllUsers({
    page: Number(page),
    limit: Number(limit),
  });

  res.status(200).json({
    success: true,
    data: users,
  });
});

// GET /api/users/:id
export const getUserById = asyncHandler(
  async (req: Request, res: Response, next: NextFunction) => {
    const user = await userService.getUserById(req.params.id);

    if (!user) {
      return next(new AppError("User not found", 404));
    }

    res.status(200).json({
      success: true,
      data: user,
    });
  },
);

// POST /api/users
export const createUser = asyncHandler(async (req: Request, res: Response) => {
  const user = await userService.createUser(req.body);

  res.status(201).json({
    success: true,
    data: user,
  });
});

// PUT /api/users/:id
export const updateUser = asyncHandler(
  async (req: Request, res: Response, next: NextFunction) => {
    const user = await userService.updateUser(req.params.id, req.body);

    if (!user) {
      return next(new AppError("User not found", 404));
    }

    res.status(200).json({
      success: true,
      data: user,
    });
  },
);

// DELETE /api/users/:id
export const deleteUser = asyncHandler(
  async (req: Request, res: Response, next: NextFunction) => {
    const deleted = await userService.deleteUser(req.params.id);

    if (!deleted) {
      return next(new AppError("User not found", 404));
    }

    res.status(204).send();
  },
);
```

---

## 🔧 Services

```typescript
// services/userService.ts
import { User } from "../models/User";
import { AppError } from "../utils/AppError";
import bcrypt from "bcrypt";

interface CreateUserDto {
  name: string;
  email: string;
  password: string;
}

interface UpdateUserDto {
  name?: string;
  email?: string;
}

export const getAllUsers = async ({
  page,
  limit,
}: {
  page: number;
  limit: number;
}) => {
  const offset = (page - 1) * limit;

  const [users, total] = await Promise.all([
    User.findAll({ limit, offset }),
    User.count(),
  ]);

  return {
    users,
    pagination: {
      page,
      limit,
      total,
      pages: Math.ceil(total / limit),
    },
  };
};

export const getUserById = async (id: string) => {
  return User.findByPk(id);
};

export const createUser = async (data: CreateUserDto) => {
  // Vérifier si l'email existe déjà
  const existingUser = await User.findOne({ where: { email: data.email } });
  if (existingUser) {
    throw new AppError("Email already exists", 400);
  }

  // Hash du mot de passe
  const hashedPassword = await bcrypt.hash(data.password, 10);

  return User.create({
    ...data,
    password: hashedPassword,
  });
};

export const updateUser = async (id: string, data: UpdateUserDto) => {
  const user = await User.findByPk(id);

  if (!user) {
    return null;
  }

  return user.update(data);
};

export const deleteUser = async (id: string) => {
  const user = await User.findByPk(id);

  if (!user) {
    return false;
  }

  await user.destroy();
  return true;
};
```

---

## 🛡️ Middlewares

### Authentication

```typescript
// middlewares/auth.ts
import { Request, Response, NextFunction } from "express";
import jwt from "jsonwebtoken";
import { AppError } from "../utils/AppError";

export interface AuthRequest extends Request {
  user?: {
    id: string;
    email: string;
    role: string;
  };
}

export const authenticate = async (
  req: AuthRequest,
  res: Response,
  next: NextFunction,
) => {
  try {
    const token = req.headers.authorization?.replace("Bearer ", "");

    if (!token) {
      return next(new AppError("No token provided", 401));
    }

    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as any;
    req.user = decoded;

    next();
  } catch (error) {
    next(new AppError("Invalid token", 401));
  }
};

export const authorize = (...roles: string[]) => {
  return (req: AuthRequest, res: Response, next: NextFunction) => {
    if (!req.user || !roles.includes(req.user.role)) {
      return next(new AppError("Forbidden", 403));
    }
    next();
  };
};

// Utilisation
router.delete("/:id", authenticate, authorize("admin"), deleteUser);
```

### Validation

```typescript
// middlewares/validation.ts
import { Request, Response, NextFunction } from "express";
import { AnySchema } from "yup";
import { AppError } from "../utils/AppError";

export const validate = (schema: AnySchema) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      await schema.validate(req.body, { abortEarly: false });
      next();
    } catch (error: any) {
      const errors = error.inner.map((err: any) => ({
        field: err.path,
        message: err.message,
      }));

      next(new AppError("Validation failed", 400, errors));
    }
  };
};

// validators/userValidator.ts
import * as yup from "yup";

export const createUserSchema = yup.object({
  name: yup.string().required().min(2).max(50),
  email: yup.string().required().email(),
  password: yup.string().required().min(8),
});

export const updateUserSchema = yup.object({
  name: yup.string().min(2).max(50),
  email: yup.string().email(),
});
```

### Error Handler

```typescript
// utils/AppError.ts
export class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number = 500,
    public errors?: any[],
  ) {
    super(message);
    this.name = "AppError";
    Error.captureStackTrace(this, this.constructor);
  }
}

// middlewares/errorHandler.ts
import { Request, Response, NextFunction } from "express";
import { AppError } from "../utils/AppError";
import { logger } from "../utils/logger";

export const errorHandler = (
  err: Error | AppError,
  req: Request,
  res: Response,
  next: NextFunction,
) => {
  logger.error(err);

  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      success: false,
      message: err.message,
      errors: err.errors,
    });
  }

  // Erreur non gérée
  res.status(500).json({
    success: false,
    message: "Internal server error",
  });
};

// middlewares/notFoundHandler.ts
export const notFoundHandler = (req: Request, res: Response) => {
  res.status(404).json({
    success: false,
    message: "Route not found",
  });
};
```

---

## 🔐 Sécurité

### Configuration helmet

```typescript
import helmet from "helmet";

app.use(
  helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
      },
    },
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true,
    },
  }),
);
```

### CORS

```typescript
import cors from "cors";

app.use(
  cors({
    origin: process.env.ALLOWED_ORIGINS?.split(",") || "*",
    credentials: true,
    optionsSuccessStatus: 200,
  }),
);
```

### Rate Limiting

```typescript
import rateLimit from "express-rate-limit";

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: "Too many requests from this IP",
  standardHeaders: true,
  legacyHeaders: false,
});

app.use("/api/", limiter);

// Limiter spécifique pour login
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: "Too many login attempts",
});

router.post("/login", loginLimiter, login);
```

### Validation des entrées

```typescript
import validator from "validator";
import mongoSanitize from "express-mongo-sanitize";
import xss from "xss-clean";

// Sanitize data
app.use(mongoSanitize());
app.use(xss());

// Validation manuelle
const sanitizeEmail = (email: string) => {
  return validator.normalizeEmail(email) || "";
};
```

---

## 📝 Logging

```typescript
// utils/logger.ts
import winston from "winston";

export const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || "info",
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json(),
  ),
  transports: [
    new winston.transports.File({ filename: "logs/error.log", level: "error" }),
    new winston.transports.File({ filename: "logs/combined.log" }),
  ],
});

if (process.env.NODE_ENV !== "production") {
  logger.add(
    new winston.transports.Console({
      format: winston.format.simple(),
    }),
  );
}

// Utilisation
logger.info("Server started");
logger.error("Error occurred", { error });
```

---

## 🧪 Tests

```typescript
// __tests__/users.test.ts
import request from "supertest";
import { createApp } from "../src/app";

describe("User API", () => {
  const app = createApp();

  describe("GET /api/users", () => {
    it("should return all users", async () => {
      const res = await request(app).get("/api/users").expect(200);

      expect(res.body.success).toBe(true);
      expect(Array.isArray(res.body.data.users)).toBe(true);
    });
  });

  describe("POST /api/users", () => {
    it("should create a new user", async () => {
      const res = await request(app)
        .post("/api/users")
        .send({
          name: "John Doe",
          email: "john@example.com",
          password: "password123",
        })
        .expect(201);

      expect(res.body.success).toBe(true);
      expect(res.body.data.email).toBe("john@example.com");
    });

    it("should return 400 for invalid data", async () => {
      await request(app).post("/api/users").send({ name: "John" }).expect(400);
    });
  });
});
```

---

## 📚 Ressources

- [Express Documentation](https://expressjs.com/)
- [Express Best Practices](https://expressjs.com/en/advanced/best-practice-security.html)
- [Node.js Security Checklist](https://blog.risingstack.com/node-js-security-checklist/)
