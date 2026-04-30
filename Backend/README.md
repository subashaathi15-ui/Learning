# 📘 Backend Starter – Node.js + TypeScript + Express + Drizzle ORM (MySQL)

A production-ready backend boilerplate using Node.js, Express, TypeScript, and Drizzle ORM with MySQL.

This project is designed as a scalable and reusable foundation for backend applications.

--------------------------------------------------

# 🚀 Tech Stack

- Node.js
- Express.js
- TypeScript
- Drizzle ORM
- MySQL
- dotenv
- Nodemon / tsx

--------------------------------------------------

# 📦 1. Project Initialization

npm init -y

--------------------------------------------------

# Install Dependencies

npm install express drizzle-orm mysql2 dotenv
npm install -D typescript ts-node nodemon tsx drizzle-kit @types/node @types/express

--------------------------------------------------

# ⚙️ 2. TypeScript Setup

npx tsc --init

--------------------------------------------------

# tsconfig.json

{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true
  },
  "exclude": ["node_modules", "drizzle.config.ts"]
}

--------------------------------------------------

# 🔐 3. Environment Setup

Create .env file:

DATABASE_URL=mysql://root:password@localhost:3306/mydb

Special characters encoding:
! → %21
@ → %40
# → %23

--------------------------------------------------

# 📁 4. Project Structure


src/
│
├── app.ts
├── server.ts
│
├── db/
│   ├── index.ts
│   └── schema/
│       ├── user/
│       │   └── user.schema.ts
│       ├── auth/
│       │   └── auth.schema.ts
│       └── order/
│           └── order.schema.ts
│
├── routes/
│   ├── route.ts
│   ├── user/
│   │   └── user.route.ts
│   ├── auth/
│   │   └── auth.route.ts
│   └── order/
│       └── order.route.ts
│
├── controllers/
│   ├── user/
│   │   └── user.controller.ts
│   ├── auth/
│   └── order/
│
├── services/
│   ├── user.service.ts
│   ├── auth.service.ts
│   └── order.service.ts
│
├── middlewares/
│   ├── auth.middleware.ts
│   └── error.middleware.ts
│
├── utils/
│   ├── response.ts
│   └── logger.ts
│
└── config/
│   └── env.ts
├── drizzle.config.ts
├── .env
├── package.json
├── package-lock.json
├── tsconfig.json
├── nodemon.json (optional)
└── README.md


--------------------------------------------------

# 🧱 5. Drizzle Setup

drizzle.config.ts

import type { Config } from "drizzle-kit";
import "dotenv/config";

export default {
  schema: "./src/db/schema/**/*.ts",
  out: "./drizzle",
  dialect: "mysql",
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
} satisfies Config;

--------------------------------------------------

# DB Connection (src/db/index.ts)

import "dotenv/config";
import { drizzle } from "drizzle-orm/mysql2";
import mysql from "mysql2/promise";

const pool = mysql.createPool({
  host: "localhost",
  user: "root",
  password: "your_password",
  database: "mydb",
});

export const db = drizzle(pool);

--------------------------------------------------

# User Schema (src/db/schema/user/user.schema.ts)

import { mysqlTable, int, varchar } from "drizzle-orm/mysql-core";

export const users = mysqlTable("users", {
  id: int("id").primaryKey().autoincrement(),
  name: varchar("name", { length: 255 }).notNull(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  age: int("age").notNull(),
});

--------------------------------------------------

# 🛠️ 6. Database Setup

CREATE DATABASE mydb;

--------------------------------------------------

# Generate Migration

npx drizzle-kit generate

--------------------------------------------------

# Push Migration

npx drizzle-kit push

--------------------------------------------------

# 🧠 7. Express Setup

app.ts

import express from "express";
import router from "./routes/route";

const app = express();

app.use(express.json());
app.use("/api", router);

export default app;

--------------------------------------------------

server.ts

import app from "./app";

const PORT = 3001;

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});

--------------------------------------------------

# 🧭 8. Routes

routes/route.ts

import express from "express";
import userRouter from "./user/user.route";

const router = express.Router();

router.use("/users", userRouter);

export default router;

--------------------------------------------------

routes/user/user.route.ts

import express from "express";
import { createUser } from "../../controllers/user/user.controller";

const userRouter = express.Router();

userRouter.post("/", createUser);

export default userRouter;

--------------------------------------------------

# 🎮 9. Controller

createUser controller

import { Request, Response } from "express";
import { db } from "../../db";
import { users } from "../../db/schema/user/user.schema";

export const createUser = async (req: Request, res: Response) => {
  const { name, age, email } = req.body;

  if (!name || !age || !email) {
    return res.status(400).send("Please send name, age, email");
  }

  await db.insert(users).values({
    name,
    age: Number(age),
    email,
  });

  res.send("User created successfully");
};

--------------------------------------------------

# 📡 10. API Endpoints

POST /api/users → Create user
GET /api/users → (future extension)

--------------------------------------------------

# ▶️ 11. Run Project

npm run dev

--------------------------------------------------

# package.json script

"scripts": {
  "dev": "nodemon src/server.ts"
}

--------------------------------------------------

Clean, scalable, production-ready backend structure for fast development.