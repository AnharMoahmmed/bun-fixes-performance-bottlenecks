# Bun: The Complete Guide to High-Performance JavaScript Workflows

This repository serves as a practical guide for using **Bun** as a drop-in replacement for Node.js and npm. It addresses common bottlenecks in large-scale projects and provides solutions for runtime compatibility issues.

## Table of Contents
1. [What is Bun?](#what-is-bun)
2. [Why Switch? (The Issues We Solved)](#why-switch-the-issues-we-solved)
3. [Installation (Native Windows)](#installation-native-windows)
4. [The Hybrid Workflow (Best Practice)](#the-hybrid-workflow-best-practice)
5. [Core Commands](#core-commands)
6. [Troubleshooting & Fixes](#troubleshooting--fixes)

---

## What is Bun?
Bun is an all-in-one JavaScript toolkit designed for speed. It functions as:
*   **A Runtime:** To run your server-side code (replaces Node.js).
*   **A Package Manager:** To install libraries (replaces npm/yarn).
*   **A Bundler:** To package code for production (replaces Vite/Webpack).
*   **A Test Runner:** To run unit tests.

It uses the **JavaScriptCore** engine, which is optimized for faster startup times compared to the V8 engine.

---

## Why Switch? (The Issues We Solved)

In large-scale backend projects (such as headless commerce engines or modular frameworks), we encountered three primary bottlenecks that Bun resolved:

### 1. The "Install Lag" Issue
**The Problem:** Traditional package managers (npm/yarn) were taking 5 to 10 minutes to install dependencies for a single project, causing significant downtime during development and CI/CD.
**The Fix:** Switching to `bun install` reduced installation times by **over 80%**, often completing in less than 30 seconds.

### 2. The CLI Tool Overhead
**The Problem:** Running initialization tools or project creators via `npx` had a noticeable delay and often required re-downloading the same tool multiple times.
**The Fix:** `bunx` executes packages almost instantly by using a more efficient caching mechanism.

### 3. Environment Complexity
**The Problem:** Managing `.env` files required extra dependencies like `dotenv`, which added unnecessary bloat to the project's entry point.
**The Fix:** Bun natively loads environment variables, allowing for cleaner code and less configuration.

---

## Installation (Native Windows)

You no longer need WSL. Install Bun directly into your PowerShell terminal.

1.  **Run the Installer:**
    ```powershell
    powershell -c "irm bun.sh/install.ps1 | iex"
    ```
2.  **Verify Setup:**
    ```powershell
    bun --version
    ```

---

## The Hybrid Workflow (Best Practice)

For complex projects that rely on **Native C++ Modules** (like image processing or specific database drivers), a "Hybrid Workflow" is the most stable approach:

1.  **Use Bun for Management:** Use `bun install` and `bun add` for all package operations. This saves hours of development time.
2.  **Use Node for Execution (If Needed):** If the framework is strictly optimized for the V8 engine, use Bun to install the packages, but use Node to run the development server.

```bash
# Example Hybrid Workflow
bun install
npm run dev
```

---

## Core Commands

| Action | Bun Command | Replaces |
| :--- | :--- | :--- |
| Create New Project | `bun init` | `npm init` |
| Run Creator Tools | `bunx <tool-name>` | `npx <tool-name>` |
| Install All Packages | `bun install` | `npm install` |
| Add a Package | `bun add <package>` | `npm install <package>` |
| Run Dev Server | `bun run dev` | `npm run dev` |
| Run Tests | `bun test` | `npm test` |

---

## Troubleshooting & Fixes

### Fix 1: Resolving Native Module Crashes
**Issue:** A project installs perfectly with Bun, but crashes when running the server because of a "Native Module" (like `sharp` or `better-sqlite3`).
**Solution:** These modules are often compiled specifically for Node.js. Install with Bun (for speed), but run the start command with Node:
```bash
bun install
node index.js
```

### Fix 2: PowerShell Execution Policy
**Issue:** PowerShell blocks the `bun` command after installation.
**Solution:** Set the execution policy to allow local scripts:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### Fix 3: Forcing Bun in CLI Tools
**Issue:** A project uses a built-in CLI that keeps defaulting to `npm`.
**Solution:** Use the `--bun` flag to force the tool to stay within the Bun runtime:
```bash
bunx --bun <cli-command>
```

---

## Verdict
Bun is the most significant upgrade to the JavaScript ecosystem in years. By using Bun for **package management**, you eliminate the longest wait times in your development cycle, even if you continue to use Node.js for production execution.

---
