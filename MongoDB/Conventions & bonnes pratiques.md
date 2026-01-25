# Conventions & Bonnes Pratiques MongoDB

## 📋 Conventions de Nommage

### Bases de données

- **snake_case** ou **camelCase** en minuscules

```javascript
// ✅ Bon
use user_management;
use ecommerce;
use blogPlatform;

// ❌ Éviter
use UserManagement;
use ECOMMERCE;
```

### Collections

- **Pluriel** pour les collections
- **camelCase** ou **snake_case**

```javascript
// ✅ Bon
db.users;
db.products;
db.orderItems;
db.blog_posts;

// ❌ Éviter
db.User;
db.product;
```

### Champs

- **camelCase** pour les champs
- Noms descriptifs

```javascript
// ✅ Bon
{
  _id: ObjectId("..."),
  firstName: "John",
  lastName: "Doe",
  emailAddress: "john@example.com",
  createdAt: ISODate("2024-01-01"),
  updatedAt: ISODate("2024-01-01")
}

// ❌ Éviter
{
  fn: "John",
  ln: "Doe",
  email: "john@example.com"
}
```

---

## 🏗️ Structure des Documents

### Modèle de document bien structuré

```javascript
// ✅ Structure claire et cohérente
{
  _id: ObjectId("507f1f77bcf86cd799439011"),
  username: "johndoe",
  email: "john@example.com",
  profile: {
    firstName: "John",
    lastName: "Doe",
    avatar: "https://example.com/avatar.jpg",
    bio: "Developer"
  },
  settings: {
    notifications: {
      email: true,
      push: false
    },
    privacy: {
      profilePublic: true
    }
  },
  roles: ["user", "premium"],
  metadata: {
    createdAt: ISODate("2024-01-01T00:00:00Z"),
    updatedAt: ISODate("2024-01-15T10:30:00Z"),
    lastLogin: ISODate("2024-01-15T10:30:00Z")
  }
}
```

### Embedding vs Referencing

#### Embedding (Données imbriquées)

```javascript
// ✅ Bon pour relations 1-à-peu
// Informations qui sont toujours consultées ensemble
{
  _id: ObjectId("..."),
  title: "Article Title",
  content: "Article content...",
  author: {
    _id: ObjectId("..."),
    name: "John Doe",
    email: "john@example.com"
  },
  comments: [
    {
      _id: ObjectId("..."),
      text: "Great article!",
      author: "Jane",
      createdAt: ISODate("2024-01-01")
    }
  ]
}
```

#### Referencing (Références)

```javascript
// ✅ Bon pour relations 1-à-beaucoup ou plusieurs-à-plusieurs
// Post
{
  _id: ObjectId("post1"),
  title: "Article Title",
  content: "Content...",
  authorId: ObjectId("user1"),
  categoryIds: [ObjectId("cat1"), ObjectId("cat2")]
}

// User
{
  _id: ObjectId("user1"),
  name: "John Doe",
  email: "john@example.com"
}

// Category
{
  _id: ObjectId("cat1"),
  name: "Technology"
}
```

---

## ✅ Bonnes Pratiques

### 1. Utiliser des index

```javascript
// ✅ Index simple
db.users.createIndex({ email: 1 }, { unique: true });

// Index composé
db.orders.createIndex({ userId: 1, createdAt: -1 });

// Index text pour recherche full-text
db.articles.createIndex({ title: "text", content: "text" });

// Index partiel
db.users.createIndex(
  { email: 1 },
  {
    unique: true,
    partialFilterExpression: { email: { $exists: true } },
  },
);

// Index TTL (expiration automatique)
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 });

// Lister les index
db.users.getIndexes();

// Supprimer un index
db.users.dropIndex("email_1");
```

### 2. Projection pour limiter les champs

```javascript
// ✅ Bon - récupérer uniquement les champs nécessaires
db.users.find({ status: "active" }, { name: 1, email: 1, _id: 0 });

// ❌ Éviter de tout récupérer
db.users.find({ status: "active" });
```

### 3. Limiter les résultats

```javascript
// ✅ Pagination
const page = 1;
const limit = 20;
const skip = (page - 1) * limit;

db.products.find().sort({ createdAt: -1 }).skip(skip).limit(limit);

// Compter le total
const total = db.products.countDocuments({ status: "active" });
```

### 4. Utiliser les opérateurs appropriés

```javascript
// Comparaison
db.products.find({ price: { $gt: 100, $lte: 500 } });

// In / Not In
db.users.find({ role: { $in: ["admin", "moderator"] } });
db.users.find({ status: { $nin: ["banned", "deleted"] } });

// Exists
db.users.find({ phone: { $exists: true } });

// Regex
db.users.find({ name: { $regex: /^John/i } });

// And / Or
db.products.find({
  $and: [{ price: { $lt: 100 } }, { category: "electronics" }],
});

db.products.find({
  $or: [{ status: "new" }, { featured: true }],
});

// Array operators
db.posts.find({ tags: "javascript" }); // Contient
db.posts.find({ tags: { $all: ["javascript", "node"] } }); // Contient tous
db.posts.find({ "comments.2": { $exists: true } }); // Au moins 3 commentaires
```

---

## 🔄 CRUD Operations

### Create (Insert)

```javascript
// Insert un document
db.users.insertOne({
  name: "John Doe",
  email: "john@example.com",
  createdAt: new Date(),
});

// Insert plusieurs documents
db.users.insertMany([
  { name: "John", email: "john@example.com" },
  { name: "Jane", email: "jane@example.com" },
]);

// Avec validation
const result = await db.users.insertOne(userData);
console.log(result.insertedId);
```

### Read (Find)

```javascript
// Find all
db.users.find();

// Find one
db.users.findOne({ email: "john@example.com" });

// Find avec conditions
db.users.find({
  status: "active",
  age: { $gte: 18 },
});

// Find avec projection
db.users.find({ status: "active" }, { name: 1, email: 1 });

// Tri
db.users.find().sort({ createdAt: -1 }); // Décroissant
db.users.find().sort({ name: 1 }); // Croissant
```

### Update

```javascript
// Update one
db.users.updateOne(
  { email: "john@example.com" },
  {
    $set: {
      status: "active",
      updatedAt: new Date(),
    },
  },
);

// Update many
db.users.updateMany({ status: "inactive" }, { $set: { status: "archived" } });

// Opérateurs d'update
db.users.updateOne(
  { _id: userId },
  {
    $set: { name: "New Name" }, // Définir
    $unset: { tempField: "" }, // Supprimer
    $inc: { views: 1 }, // Incrémenter
    $push: { tags: "new-tag" }, // Ajouter à array
    $pull: { tags: "old-tag" }, // Retirer de array
    $addToSet: { tags: "unique-tag" }, // Ajouter si pas déjà présent
    $rename: { oldName: "newName" }, // Renommer
    $currentDate: { updatedAt: true }, // Date actuelle
  },
);

// FindAndModify (retourne le document)
db.users.findOneAndUpdate(
  { email: "john@example.com" },
  { $set: { status: "active" } },
  { returnDocument: "after" }, // ou "before"
);

// Upsert (insert si n'existe pas)
db.users.updateOne(
  { email: "john@example.com" },
  { $set: { name: "John", status: "active" } },
  { upsert: true },
);
```

### Delete

```javascript
// Delete one
db.users.deleteOne({ email: "john@example.com" });

// Delete many
db.users.deleteMany({ status: "banned" });

// Find and delete
const deletedUser = db.users.findOneAndDelete({ email: "john@example.com" });
```

---

## 🎯 Aggregation Pipeline

### Pipeline de base

```javascript
db.orders.aggregate([
  // Stage 1: Filtrer
  { $match: { status: "completed" } },

  // Stage 2: Grouper
  {
    $group: {
      _id: "$userId",
      totalAmount: { $sum: "$amount" },
      orderCount: { $sum: 1 },
      avgAmount: { $avg: "$amount" },
    },
  },

  // Stage 3: Trier
  { $sort: { totalAmount: -1 } },

  // Stage 4: Limiter
  { $limit: 10 },
]);
```

### Opérateurs d'agrégation courants

```javascript
// $lookup (JOIN)
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user",
    },
  },
  { $unwind: "$user" }, // Déplier l'array
  {
    $project: {
      orderId: "$_id",
      amount: 1,
      userName: "$user.name",
    },
  },
]);

// $project (Reshape)
db.users.aggregate([
  {
    $project: {
      name: 1,
      email: 1,
      fullName: { $concat: ["$firstName", " ", "$lastName"] },
      age: {
        $subtract: [{ $year: new Date() }, { $year: "$birthDate" }],
      },
    },
  },
]);

// $addFields (Ajouter des champs)
db.products.aggregate([
  {
    $addFields: {
      discountedPrice: {
        $multiply: ["$price", 0.9],
      },
    },
  },
]);

// $unwind (Déplier arrays)
db.posts.aggregate([
  { $unwind: "$tags" },
  { $group: { _id: "$tags", count: { $sum: 1 } } },
]);

// $bucket (Grouper par plages)
db.products.aggregate([
  {
    $bucket: {
      groupBy: "$price",
      boundaries: [0, 50, 100, 200, 500],
      default: "Other",
      output: {
        count: { $sum: 1 },
        products: { $push: "$name" },
      },
    },
  },
]);
```

### Agrégations complexes

```javascript
// Analytics example
db.orders.aggregate([
  // Filtrer par date
  {
    $match: {
      createdAt: {
        $gte: ISODate("2024-01-01"),
        $lt: ISODate("2024-02-01"),
      },
    },
  },

  // Joindre avec users
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user",
    },
  },
  { $unwind: "$user" },

  // Grouper par pays
  {
    $group: {
      _id: "$user.country",
      totalRevenue: { $sum: "$amount" },
      orderCount: { $sum: 1 },
      uniqueUsers: { $addToSet: "$userId" },
    },
  },

  // Ajouter des calculs
  {
    $addFields: {
      avgOrderValue: { $divide: ["$totalRevenue", "$orderCount"] },
      customerCount: { $size: "$uniqueUsers" },
    },
  },

  // Trier
  { $sort: { totalRevenue: -1 } },

  // Formater le résultat
  {
    $project: {
      country: "$_id",
      _id: 0,
      totalRevenue: { $round: ["$totalRevenue", 2] },
      orderCount: 1,
      customerCount: 1,
      avgOrderValue: { $round: ["$avgOrderValue", 2] },
    },
  },
]);
```

---

## 🚀 Performance et Optimisation

### 1. Explain pour analyser les requêtes

```javascript
// Analyser une requête
db.users.find({ email: "john@example.com" }).explain("executionStats");

// Vérifier si un index est utilisé
db.users.find({ email: "john@example.com" }).explain("queryPlanner");
```

### 2. Index hints

```javascript
// Forcer l'utilisation d'un index
db.users.find({ name: "John", email: "john@example.com" }).hint({ email: 1 });
```

### 3. Bulk operations

```javascript
// ✅ Bulk write pour plusieurs opérations
const bulk = db.users.initializeUnorderedBulkOp();

bulk.insert({ name: "User1", email: "user1@example.com" });
bulk.insert({ name: "User2", email: "user2@example.com" });
bulk.find({ status: "inactive" }).update({ $set: { status: "archived" } });

const result = bulk.execute();
```

### 4. Éviter les scans complets

```javascript
// ❌ Scan complet (lent)
db.users.find({ name: /john/i });

// ✅ Utiliser un index
db.users.find({ name: "John Doe" });
```

---

## 🔒 Sécurité

### 1. Validation de schéma

```javascript
// Créer une collection avec validation
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["email", "name"],
      properties: {
        email: {
          bsonType: "string",
          pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$",
        },
        name: {
          bsonType: "string",
          minLength: 3,
          maxLength: 50,
        },
        age: {
          bsonType: "int",
          minimum: 0,
          maximum: 150,
        },
        roles: {
          bsonType: "array",
          items: {
            enum: ["user", "admin", "moderator"],
          },
        },
      },
    },
  },
  validationAction: "error",
});

// Modifier la validation
db.runCommand({
  collMod: "users",
  validator: {
    /* nouvelle validation */
  },
  validationLevel: "strict",
});
```

### 2. Authentification et rôles

```javascript
// Créer un utilisateur
use admin;
db.createUser({
  user: "appUser",
  pwd: "securePassword",
  roles: [
    { role: "readWrite", db: "myDatabase" }
  ]
});

// Rôles courants :
// - read : Lecture seule
// - readWrite : Lecture et écriture
// - dbAdmin : Administration de la base
// - userAdmin : Gestion des utilisateurs
// - clusterAdmin : Administration du cluster

// Créer un rôle personnalisé
db.createRole({
  role: "customRole",
  privileges: [
    {
      resource: { db: "myDatabase", collection: "users" },
      actions: ["find", "update"]
    }
  ],
  roles: []
});
```

### 3. Échapper les injections

```javascript
// ✅ Bon - utiliser les méthodes MongoDB
const email = req.body.email;
db.users.findOne({ email: email });

// ❌ Ne pas construire de requêtes avec des strings
const query = `{ email: "${req.body.email}" }`; // Dangereux
```

---

## 🎨 Mongoose (ODM)

### Définir un schéma

```javascript
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [true, "Name is required"],
      trim: true,
      minlength: 3,
      maxlength: 50,
    },
    email: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
      match: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
    },
    age: {
      type: Number,
      min: 0,
      max: 150,
    },
    role: {
      type: String,
      enum: ["user", "admin", "moderator"],
      default: "user",
    },
    profile: {
      bio: String,
      avatar: String,
      social: {
        twitter: String,
        github: String,
      },
    },
    tags: [String],
    friends: [
      {
        type: mongoose.Schema.Types.ObjectId,
        ref: "User",
      },
    ],
  },
  {
    timestamps: true, // Ajoute createdAt et updatedAt
  },
);

// Index
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ name: "text" });

// Virtuals
userSchema.virtual("fullName").get(function () {
  return `${this.firstName} ${this.lastName}`;
});

// Methods
userSchema.methods.comparePassword = async function (password) {
  return await bcrypt.compare(password, this.password);
};

// Statics
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};

// Middleware
userSchema.pre("save", async function (next) {
  if (this.isModified("password")) {
    this.password = await bcrypt.hash(this.password, 10);
  }
  next();
});

const User = mongoose.model("User", userSchema);
```

### CRUD avec Mongoose

```javascript
// Create
const user = new User({
  name: "John Doe",
  email: "john@example.com",
});
await user.save();

// Ou
const user = await User.create({
  name: "John Doe",
  email: "john@example.com",
});

// Read
const user = await User.findById(userId);
const users = await User.find({ status: "active" });
const user = await User.findOne({ email: "john@example.com" });

// Avec populate (JOIN)
const user = await User.findById(userId)
  .populate("friends", "name email")
  .exec();

// Update
await User.updateOne({ _id: userId }, { $set: { name: "New Name" } });

const user = await User.findByIdAndUpdate(
  userId,
  { name: "New Name" },
  { new: true, runValidators: true },
);

// Delete
await User.deleteOne({ _id: userId });
const user = await User.findByIdAndDelete(userId);
```

---

## 🔄 Transactions

### Transactions multi-documents

```javascript
const session = await mongoose.startSession();
session.startTransaction();

try {
  // Opération 1
  await Account.updateOne(
    { _id: fromAccountId },
    { $inc: { balance: -amount } },
    { session },
  );

  // Opération 2
  await Account.updateOne(
    { _id: toAccountId },
    { $inc: { balance: amount } },
    { session },
  );

  // Commit
  await session.commitTransaction();
} catch (error) {
  // Rollback en cas d'erreur
  await session.abortTransaction();
  throw error;
} finally {
  session.endSession();
}
```

---

## 📊 Monitoring et Maintenance

### Statistiques de collection

```javascript
// Taille et statistiques
db.users.stats();

// Statistiques détaillées
db.users.stats({ scale: 1024 }); // En KB
```

### Backup et Restore

```bash
# Backup
mongodump --db myDatabase --out /backup/

# Restore
mongorestore --db myDatabase /backup/myDatabase/

# Export en JSON
mongoexport --db myDatabase --collection users --out users.json

# Import depuis JSON
mongoimport --db myDatabase --collection users --file users.json
```

---

## 📚 Ressources

- [MongoDB Documentation](https://docs.mongodb.com/)
- [Mongoose Documentation](https://mongoosejs.com/docs/)
- [MongoDB University](https://university.mongodb.com/)
- [MongoDB Best Practices](https://www.mongodb.com/developer/products/mongodb/schema-design-anti-pattern-summary/)
