
--

# 💬 Convex Chat Application

A real-time chat application built using **Convex** as the backend database and realtime data platform.
This project demonstrates how to configure, integrate, and use a Convex database with a modern frontend to enable **live synchronized messaging**.

---

## 📚 Project Overview

This project was created to **learn and implement realtime applications** using Convex. It showcases:

* ✅ Configuring and integrating a Convex backend
* ✅ Using a document-relational database
* ✅ Writing TypeScript backend functions
* ✅ Creating transactional mutations
* ✅ Subscribing to realtime queries
* ✅ Automatic frontend synchronization via WebSockets

The application allows users to send and receive chat messages instantly with live updates across connected clients.

---

## 🚀 Getting Started

### 1️⃣ Clone the Repository

```bash
git clone git@github.com:M-Subhan-Ali/convex-chat-app.git
cd convex-chat-app
```

### 2️⃣ Install Dependencies

```bash
npm install
```

### 3️⃣ Run Development Server

```bash
npm run dev
```

---

## 🧠 Understanding Convex

### Database

The **Convex database** is a **document-relational database**, meaning:

* Data is stored as JSON-like documents
* Each document has an auto-generated `_id`
* Relationships can be created between documents
* Tables are created automatically when data is inserted

You interact with the database using:

* **Mutations** → Write operations
* **Queries** → Read operations

---

## 🔄 Convex Architecture

```
Frontend (React)
        │
        │ WebSockets
        ▼
Convex Client Library
        │
        ▼
Queries & Mutations (TypeScript)
        │
        ▼
Convex Database + Sync Engine
```

The **sync engine** automatically reruns queries whenever data changes and updates all connected clients in real time.

---

## ✏️ Backend Implementation

Create a file:

```
convex/chat.ts
```

---

### ✅ Mutation: Send Message

```ts
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const sendMessage = mutation({
  args: {
    user: v.string(),
    body: v.string(),
  },
  handler: async (ctx, args) => {
    console.log("This TypeScript function is running on the server.");
    await ctx.db.insert("messages", {
      user: args.user,
      body: args.body,
    });
  },
});
```

#### What this does:

* Creates a public backend API
* Runs inside a **database transaction**
* Validates arguments using Convex validators
* Inserts chat messages into the database

If an error occurs, all changes automatically roll back.

---

### ✅ Query: Get Messages

Update `chat.ts`:

```ts
import { query, mutation } from "./_generated/server";

export const getMessages = query({
  args: {},
  handler: async (ctx) => {
    const messages = await ctx.db
      .query("messages")
      .order("desc")
      .take(50);

    return messages.reverse();
  },
});
```

#### What this does:

* Reads data only (no writes allowed)
* Fetches latest 50 messages
* Returns messages in chronological order

---

## 🎨 Frontend Integration

### Import Convex Hooks

```ts
import { useQuery, useMutation } from "convex/react";
import { api } from "../convex/_generated/api";
```

---

### Call Mutation from UI

```ts
const sendMessage = useMutation(api.chat.sendMessage);

await sendMessage({
  user: NAME,
  body: newMessageText,
});
```

Steps:

1. `useMutation` connects frontend to backend function
2. Calling it executes the database transaction

---

### Subscribe to Realtime Data

```ts
const messages = useQuery(api.chat.getMessages);
```

This single line:

* Subscribes to backend queries
* Automatically reruns when data changes
* Updates React UI instantly
* Keeps all clients synchronized

---

## ⚡ Realtime Sync Explained

Convex automatically:

* Detects database changes
* Reruns affected queries
* Pushes updates via WebSockets
* Rerenders your UI

No manual polling or state management required.

---

## 🧪 Convex Dashboard

Open:

```
https://dashboard.convex.dev
```

Features:

* 📊 View database tables
* 🧾 Inspect messages data live
* 🪵 View backend logs
* 🐞 Debug mutations and queries

The `messages` table is automatically created after sending the first message.

---

## ✅ What I Learned

* Created a **mutation TypeScript function** that inserts chat messages inside a transaction
* Created a **query function** that keeps the UI updated with latest data
* Integrated Convex client library for realtime synchronization
* Built a fully reactive backend without manual APIs or polling

---

## 🛠 Tech Stack

* **React**
* **TypeScript**
* **Convex**
* **WebSockets (via Convex client)**
* **Node.js**

---

## 📌 Key Concepts

| Concept        | Description                  |
| -------------- | ---------------------------- |
| Mutation       | Transactional database write |
| Query          | Read-only data subscription  |
| Sync Engine    | Automatic realtime updates   |
| Client Library | Connects frontend to backend |

---

## 📷 Demo Behavior

* Send a message → instantly appears in database
* All connected clients update automatically
* No refresh required

---

