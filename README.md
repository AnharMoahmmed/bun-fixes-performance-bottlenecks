# Bun: The Complete Guide to High-Performance JavaScript Workflows

This repository serves as a practical guide for using **Bun** as a drop-in replacement for Node.js and npm. It addresses common bottlenecks in large-scale projects and provides solutions for runtime compatibility issues.

## Table of Contents
1. [What is Bun?](#what-is-bun)
2. [Why Switch? (The Issues We Solved)](#why-switch-the-issues-we-solved)
3. [Installation (Native Windows)](#installation-native-windows)
4. [Creating a Medusa v2 Project with Bun](#creating-a-medusa-v2-project-with-bun)
5. [The Hybrid Workflow (Best Practice)](#the-hybrid-workflow-best-practice)
6. [Core Commands](#core-commands)
7. [GitHub-Reported Issues & Troubleshooting](#troubleshooting--fixes)

---

## What is Bun?
Bun is an all-in-one JavaScript toolkit designed for speed. It functions as:
*   **A Runtime:** To run your server-side code (replaces Node.js).
*   **A Package Manager:** To install libraries (replaces npm/yarn).
*   **A Bundler:** To package code for production (replaces Vite/Webpack).
*   **A Test Runner:** To run unit tests.

---

## Why Switch? (The Issues We Solved)

In large-scale backend projects like **Medusa**, we encountered three primary bottlenecks that Bun resolved:

### 1. The "Install Lag" Issue
**The Problem:** Traditional package managers (npm/yarn) were taking 5 to 10 minutes to install dependencies for a single project, causing significant downtime during development and CI/CD.
**The Fix:** Switching to `bun install` reduced installation times by **over 80%**, often completing in less than 30 seconds.

### 2. CLI Tool Overhead
Running project creators via `npx` often has a noticeable delay. `bunx` executes the Medusa installer instantly.

### 3. Environment Complexity
Medusa relies heavily on `.env` files for DB strings. Bun natively loads these, eliminating the need for manual `dotenv` configuration.

---

## Installation (Native Windows)

1.  **Run the Installer (PowerShell):**
    ```powershell
    powershell -c "irm bun.sh/install.ps1 | iex"
    ```
2.  **Verify Setup:**
    ```powershell
    bun --version
    ```

---

## Creating a Medusa v2 Project with Bun

Follow these steps to initialize a high-performance Medusa store.

### Prerequisites
*   **PostgreSQL:** Create an empty database (e.g., `medusa-db`).
*   **Redis:** Ensure Redis is running locally for background tasks.

### Step 1: Create the Project
```bash
bunx create-medusa-app@latest
```
*   **Project Name:** `my-medusa-store`
*   **Database:** Enter your Postgres string (e.g., `postgres://localhost:5432/medusa-db`).

### Step 2: Full Conversion to Bun
The installer may default to npm. Force Bun's lockfile:
```bash
cd my-medusa-store
rm -rf node_modules package-lock.json yarn.lock
bun install
```

### Step 3: Database & Seed
```bash
bun run build
bunx medusa db:migrate
bunx medusa db:seed # Optional: Add demo products
```

### Step 4: Create Admin User (CLI)
Since Medusa doesn't allow "Sign Up" on the dashboard for security:
```bash
bunx medusa user -e admin@me.com -p mypassword
```

### Step 5: Start Development
```bash
bun run dev
```
*   **Backend API:** `http://localhost:9000`
*   **Admin Dashboard:** `http://localhost:9000/admin`

---

## The Hybrid Workflow (Best Practice)

For projects using **Native C++ Modules** (like Medusa's image processing), use this approach:

1.  **Manage with Bun:** Use `bun install` for speed.
2.  **Execute with Node (if needed):** If a specific plugin fails under Bun's runtime, use Bun to install but Node to run.

```bash
bun install
node index.js
```

---

## Core Commands

| Action | Bun Command | Replaces |
| :--- | :--- | :--- |
| Create New Project | `bunx create-medusa-app` | `npx ...` |
| Install All Packages | `bun install` | `npm install` |
| Add a Plugin | `bun add <plugin-name>` | `npm install` |
| Run Dev Server | `bun run dev` | `npm run dev` |
| Run Migrations | `bunx medusa db:migrate` | `npx medusa...` |

---

## GitHub-Reported Issues & Troubleshooting

### 1. Engine Mismatch Warnings
**Issue:** GitHub users report Bun failing because Medusa’s `package.json` requires `node >=18`.
**Fix:** Use the `--ignore-scripts` flag during install or set `Strict-Engines=false`.

### 2. Sharp (Image Processing) Crashes
**Issue:** The `sharp` library used by Medusa for images sometimes fails when installed via Bun on Windows.
**Fix:** Force a native build:
```bash
bun add sharp --build-from-source
```

### 3. CLI Path Issues
**Issue:** Running `medusa` in the terminal returns "command not found."
**Fix:** Always prefix commands with `bunx` (e.g., `bunx medusa info`) to ensure Bun looks in the local `.bin` folder.

### 4. Admin Dashboard 404
**Issue:** Users looking for `/app` (common in v1) find nothing.
**Fix:** Medusa v2 defaults strictly to `http://localhost:9000/admin`.

---

## Verdict
Using **Bun with Medusa v2** eliminates the longest wait times in the development cycle. By following the "CLI-First" approach for user management and migrations, you gain a massive speed advantage without sacrificing stability.