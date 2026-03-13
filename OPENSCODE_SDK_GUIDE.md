# OpenCode SDK Guide for Desktop IDE Users

*A beginner-friendly top-down approach to using the OpenCode SDK*

---

## Quick Start Summary

1. **Install OpenCode Desktop** (or use the IDE extension)
2. **Get API credentials** (from Zen, Anthropic, OpenAI, etc.)
3. **Connect your provider** in the app settings
4. **Use the SDK** to programmatically control OpenCode

---

## Table of Contents

- [What is OpenCode?](#what-is-opencode)
- [What is the SDK?](#what-is-the-sdk)
- [Do I Need the Desktop Client Running?](#do-i-need-the-desktop-client-running)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Basic Usage](#basic-usage)
- [Sessions Deep Dive](#sessions-deep-dive)
  - [What is a Session?](#what-is-a-session)
  - [Session Lifecycle](#session-lifecycle)
  - [Creating Sessions](#creating-sessions)
  - [Listing and Getting Sessions](#listing-and-getting-sessions)
  - [Sending Messages](#sending-messages)
  - [Managing Session History](#managing-session-history)
  - [Child Sessions](#child-sessions)
  - [Session Sharing](#session-sharing-1)
  - [Session Configuration](#session-configuration)
  - [Session Cleanup](#session-cleanup)
  - [Best Practices](#session-best-practices)
  - [Common Session Patterns](#common-session-patterns)
- [Tools Deep Dive](#tools-deep-dive)
  - [What Are Tools?](#what-are-tools)
  - [Why Tools Matter](#why-tools-matter)
  - [Built-in Tools](#built-in-tools)
    - [File Operations](#file-operations)
    - [Search & Discovery](#search--discovery)
    - [Code Intelligence](#code-intelligence)
    - [Shell & System](#shell--system)
    - [Web & External](#web--external)
    - [User Interaction](#user-interaction)
    - [Agent Skills](#agent-skills)
  - [Tool Permissions](#tool-permissions)
  - [Custom Tools](#custom-tools)
  - [Using Tools via SDK](#using-tools-via-sdk)
  - [Tool Best Practices](#tool-best-practices)
- [Common Questions](#common-questions)
- [Next Steps](#next-steps)

---

## What is OpenCode?

OpenCode is an open-source AI coding agent that helps you write code. It's available in three forms:

- **Desktop App** - Standalone application (Windows, macOS, Linux)
- **Terminal Interface (TUI)** - Command-line interface
- **IDE Extension** - Integration with VS Code, JetBrains, etc.

Key features:
- Works with 75+ LLM providers (Claude, GPT, Gemini, local models)
- Auto-loads LSPs for better code understanding
- Multi-session support
- Share conversation links
- GitHub Copilot & ChatGPT Plus integration

---

## What is the SDK?

The **SDK (Software Development Kit)** is a TypeScript/JavaScript library that lets you:

- **Control OpenCode programmatically** from your own applications
- **Build custom integrations** and tools
- **Automate workflows** that use AI coding assistance
- **Embed OpenCode** into your own tools and platforms

**Important**: The SDK is for **developers who want to build on top of OpenCode**, not for everyday coding tasks. If you just want to use AI to help write code, use the Desktop App or IDE Extension directly.

---

## Do I Need the Desktop Client Running?

**Short answer**: It depends on how you use the SDK.

### Scenario 1: SDK Starts Its Own Server

```typescript
import { createOpencode } from "@opencode-ai/sdk"
const { client } = await createOpencode()
```

When you use `createOpencode()`, the SDK **automatically starts an OpenCode server** in the background (on localhost:4096 by default). You don't need the desktop client running.

✅ **No desktop client needed** - the SDK manages the server itself.

### Scenario 2: Connect to Existing Desktop Client

```typescript
import { createOpencodeClient } from "@opencode-ai/sdk"
const client = createOpencodeClient({
  baseUrl: "http://localhost:4096"
})
```

When you use `createOpencodeClient()`, you connect to an already-running OpenCode instance.

❌ **Desktop client must be running** on localhost:4096 (or wherever your desktop app runs its server).

### Recommendation for Beginners

If you're just getting started with the SDK, **use `createOpencode()`** - it's simpler and doesn't require manually managing the desktop app. The SDK will handle starting and stopping the server automatically.

---

## Prerequisites

### For SDK Development

1. **Node.js** (v18+ recommended)
   - Download from [nodejs.org](https://nodejs.org)
   - Verify: `node --version`

2. **TypeScript** (optional but recommended)
   - The SDK is type-safe and works best with TypeScript
   - Install: `npm install -g typescript`

3. **A code editor** (VS Code, WebStorm, etc.)

4. **Basic JavaScript/TypeScript knowledge**

### For Using OpenCode (The AI Provider)

1. **An API key** from one of these sources:
   - [OpenCode Zen](https://opencode.ai/zen) - curated models, easy setup
   - Anthropic (Claude)
   - OpenAI (GPT)
   - Google (Gemini)
   - Or any of 75+ supported providers

2. **An OpenCode account** (free)
   - Sign up at [opencode.ai](https://opencode.ai)
   - Used for billing and provider management

---

## Installation

### Install the SDK

```bash
npm install @opencode-ai/sdk
```

Or with other package managers:

```bash
# Bun
bun install @opencode-ai/sdk

# Yarn
yarn add @opencode-ai/sdk

# pnpm
pnpm add @opencode-ai/sdk
```

### Install OpenCode Desktop (Optional)

If you want to use the desktop app separately:

1. **Download** from [opencode.ai/download](https://opencode.ai/download)
2. **Install** like any other desktop application
3. **Launch** it from your applications folder/start menu

The desktop app runs a local server on `localhost:4096` by default.

---

## Configuration

### Method 1: Use Zen (Easiest for Beginners)

[OpenCode Zen](https://opencode.ai/zen) provides pre-configured models that work great with coding agents.

1. Go to [opencode.ai/auth](https://opencode.ai/auth)
2. Sign in / create account
3. Add billing (pay-per-use, no subscription)
4. Copy your API key
5. Use it in your SDK code:

```typescript
const opencode = await createOpencode({
  config: {
    provider: "opencode",  // Use Zen
    model: "claude-sonnet-3-7"  // Or your preferred model
  }
});
```

### Method 2: Use Other Providers

Configure API keys for your preferred provider:

```typescript
const opencode = await createOpencode({
  config: {
    provider: "anthropic",  // or "openai", "google", etc.
    model: "claude-3-5-sonnet-20241022"
  }
});
```

Then set the API key:

```typescript
await client.auth.set({
  path: { id: "anthropic" },
  body: {
    type: "api",
    key: "your-anthropic-api-key"
  }
});
```

### Method 3: Use Config File

Create an `opencode.json` in your project root:

```json
{
  "provider": "anthropic",
  "model": "claude-3-5-sonnet-20241022",
  "analytics": false
}
```

The SDK automatically picks up this configuration.

---

## Basic Usage

### Create a Client and Start a Session

```typescript
import { createOpencode } from "@opencode-ai/sdk";

async function main() {
  // Start OpenCode server and create client
  const { client, server } = await createOpencode();

  try {
    // Step 1: Create a new session with a clear purpose
    const session = await client.session.create({
      body: {
        title: "API Documentation Review",
        description: "Review and improve REST API documentation"
      }
    });
    console.log("✅ Session created:", session.data.id);

    // Step 2: Send your first prompt
    const response = await client.session.prompt({
      path: { id: session.data.id },
      body: {
        parts: [
          { 
            type: "text", 
            text: "Review the API endpoints in src/api/users.ts and suggest improvements" 
          }
        ]
      }
    });
    console.log("🤖 Response:", response.data.text);

    // Step 3: Continue the conversation (multiple turns)
    const followUp = await client.session.prompt({
      path: { id: session.data.id },
      body: {
        parts: [
          { type: "text", text: "Can you also add OpenAPI annotations?" }
        ]
      }
    });
    console.log("🤖 Follow-up:", followUp.data.text);

    // Step 4: List all sessions to see what you have
    const sessions = await client.session.list();
    console.log(`📊 Total sessions: ${sessions.data.length}`);

    // Example of getting session details
    const details = await client.session.get({ path: { id: session.data.id } });
    console.log(`📝 Session: ${details.data.title}`);
    console.log(`   Messages: ${details.data.message_count}`);
    console.log(`   Created: ${new Date(details.data.created_at).toLocaleDateString()}`);

  } catch (error) {
    console.error("❌ Error:", error);
  } finally {
    // Always close the server when done
    server.close();
    console.log("👋 Server stopped");
  }
}

main();
```

### Explore File System

```typescript
// Search for text in files
const results = await client.find.text({
  query: { pattern: "function.*initialize" }
});
console.log("Found:", results.data);

// Read a specific file
const file = await client.file.read({
  query: { path: "src/index.ts" }
});
console.log("File content:", file.data.content);
```

### Create Structured Output

Get JSON responses with a defined schema:

```typescript
interface CompanyInfo {
  company: string;
  founded: number;
  products: string[];
}

const result = await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [{ type: "text", text: "Tell me about Anthropic" }],
    format: {
      type: "json_schema",
      schema: {
        type: "object",
        properties: {
          company: { type: "string", description: "Company name" },
          founded: { type: "number", description: "Year founded" },
          products: {
            type: "array",
            items: { type: "string" },
            description: "Main products"
          }
        },
        required: ["company", "founded"]
      }
    }
  }
});

const companyInfo = result.data.info.structured_output as CompanyInfo;
console.log(companyInfo.company);  // "Anthropic"
```

### Run Shell Commands

```typescript
const result = await client.session.shell({
  path: { id: sessionId },
  body: {
    command: "ls -la"
  }
});
console.log("Command output:", result.data);
```

---

## Sessions Deep Dive

Sessions are the core concept in OpenCode. Think of a session as a **conversation** between you and the AI, with memory, context, and the ability to make changes to your codebase.

### What is a Session?

A session is a persistent conversation that:
- **Remembers the conversation history** - All messages sent and received
- **Tracks the project context** - Knows which files you're working with
- **Can make changes** - Edit files, run commands, create new code
- **Can be paused/resumed** - Stop and continue later
- **Can be shared** - Let team members view or continue the conversation

Each session is independent. You can have multiple sessions for different tasks:
- Session 1: Building a new feature
- Session 2: Debugging an issue
- Session 3: Code review

### Session Lifecycle

```
┌─────────────┐
│   Created   │ ◄── You create it with a title
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Active     │ ◄── Send messages, make changes
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Completed  │ ◄── Task finished (optional)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Deleted    │ ◄── Cleaned up (optional)
└─────────────┘
```

### Creating Sessions

#### Basic Creation

```typescript
const session = await client.session.create({
  body: {
    title: "Implement user authentication"
  }
});
console.log("Session ID:", session.data.id);
// Output: Session ID: abc123-def456-ghi789
```

#### With Initial Context

```typescript
const session = await client.session.create({
  body: {
    title: "Fix login bug",
    // Optional: provide initial instructions
    initialAnnouncement: "Focus on the login form in packages/auth",
    // Optional: set initial prompt (AI responds immediately)
    initialPrompt: {
      parts: [
        { type: "text", text: "Review the login form and identify potential issues" }
      ]
    }
  }
});
```

#### With Specific Model

By default, sessions use the project's configured model. You can override:

```typescript
const session = await client.session.create({
  body: {
    title: "Code review",
    model: {
      providerID: "anthropic",
      modelID: "claude-3-5-sonnet-20241022"
    }
  }
});
```

### Listing and Getting Sessions

#### List All Sessions

```typescript
const sessions = await client.session.list();
console.log(`Found ${sessions.data.length} sessions`);

sessions.data.forEach(session => {
  console.log(`- ${session.title} (${session.id})`);
  console.log(`  Created: ${new Date(session.created_at).toLocaleString()}`);
  console.log(`  Messages: ${session.message_count}`);
});
```

#### Get a Specific Session

```typescript
const session = await client.session.get({
  path: { id: "your-session-id" }
});
console.log("Session details:", session.data);
```

#### Filter and Sort Sessions

```typescript
// List sessions with pagination (limit 10)
const recentSessions = await client.session.list({
  query: { limit: 10 }
});

// Get current active session
const current = await client.session.current();
```

### Sending Messages

#### Send a Prompt (Main Interaction)

```typescript
const response = await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [
      { type: "text", text: "How do I implement a debounce function?" }
    ],
    // Optional: mention specific files
    // Example: "@utils/hooks.ts check this implementation"
  }
});

// The response contains:
// - response.data.text: The AI's reply as plain text
// - response.data.info: Message metadata
// - response.data.parts: Structured parts (text, image, etc.)
console.log("AI says:", response.data.text);
```

#### Send a Prompt Without AI Response

Sometimes you want to **inject context** without getting a reply:

```typescript
// Add background info to the session
await client.session.prompt({
  path: { id: sessionId },
  body: {
    noReply: true,  // <-- Don't generate a response
    parts: [
      { 
        type: "text", 
        text: "The project uses React 18 and TypeScript 5.0" 
      }
    ]
  }
});
// No response returned, but context is added to session history
```

This is useful for:
- Setting context at session start
- Adding reference materials
- Providing rules the AI should follow

#### Send a Command

Commands are **special actions** the AI can take. They're different from prompts:

```typescript
const result = await client.session.command({
  path: { id: sessionId },
  body: {
    command: "/init",  // Re-analyze the project
    // Optional: arguments
    // args: "--force"
  }
});
console.log("Command output:", result.data);
```

Common commands:
- `/init` - Analyze project structure
- `/undo` - Undo last changes
- `/redo` - Redo undone changes
- `/clear` - Clear session history (keeps files)
- `/share` - Generate share link

#### Run Shell Commands

Have the AI execute shell commands in your project directory:

```typescript
const result = await client.session.shell({
  path: { id: sessionId },
  body: {
    command: "npm test",
    // Optional: timeout (ms)
    timeout: 30000
  }
});
console.log("Exit code:", result.data.exit_code);
console.log("Output:", result.data.stdout);
```

### Managing Session History

#### View Message History

```typescript
// Get all messages in a session
const messages = await client.session.messages({
  path: { id: sessionId }
});

// messages.data is an array of { info, parts }
messages.data.forEach(msg => {
  const role = msg.info.user ? "You" : "AI";
  const text = msg.parts.find(p => p.type === "text")?.text || "";
  console.log(`${role}: ${text.substring(0, 100)}...`);
});
```

#### Get Specific Message

```typescript
const message = await client.session.message({
  path: { id: sessionId },
  query: { messageId: "message-id-here" }
});
console.log("Full message:", message.data);
```

#### Undo/Revert Changes

The AI can make changes to your files. If you want to undo:

```typescript
// Undo the last AI message (reverts file changes)
const reverted = await client.session.revert({
  path: { id: sessionId },
  // Optional: which message to revert to
  // body: { message_id: "target-message-id" }
});
console.log("Reverted to:", reverted.data.current_message_id);
```

#### Redo Changes

```typescript
const redone = await client.session.unrevert({
  path: { id: sessionId }
});
console.log("Restored to:", redone.data.current_message_id);
```

You can call `/undo` and `/redo` multiple times to navigate history.

### Child Sessions

Sometimes the AI creates **child sessions** to break down complex tasks. These are sub-conversations that:
- Have their own message history
- Report back to the parent session
- Can be viewed independently

#### List Child Sessions

```typescript
const children = await client.session.children({
  path: { id: parentSessionId }
});
console.log(`Parent has ${children.data.length} child sessions`);

children.data.forEach(child => {
  console.log(`- ${child.title} (${child.id})`);
});
```

This is useful for:
- Understanding how OpenCode decomposed a complex problem
- Debugging specific sub-tasks
- Viewing intermediate results

### Session Sharing

#### Share a Session

Create a read-only link to share with teammates:

```typescript
const shared = await client.session.share({
  path: { id: sessionId }
});
console.log("Share URL:", shared.data.url);
// Example: https://opencode.ai/s/abc123-def456
```

Shared sessions include:
- Full conversation history
- All code changes
- AI reasoning
- **But**: No editing capabilities for viewers

#### Unshare a Session

```typescript
const unshared = await client.session.unshare({
  path: { id: sessionId }
});
console.log("Is shared?", unshared.data.shared);
```

### Session Configuration

#### Update Session Properties

```typescript
const updated = await client.session.update({
  path: { id: sessionId },
  body: {
    title: "New title for this session",
    description: "What we're trying to accomplish",
    // Optional: change model for this session
    model: {
      providerID: "openai",
      modelID: "gpt-4-turbo-preview"
    }
  }
});
console.log("Updated:", updated.data.title);
```

#### Initialize/Re-analyze Project

Run project analysis to update the `AGENTS.md` file:

```typescript
const initialized = await client.session.init({
  path: { id: sessionId },
  body: {
    // Optional: force re-analysis
    // force: true
  }
});
console.log("Analysis complete:", initialized.data);
```

This is useful when:
- Project structure changed
- New dependencies added
- You want OpenCode to re-understand the codebase

### Session Cleanup

#### Delete a Session

```typescript
const deleted = await client.session.delete({
  path: { id: sessionId }
});
console.log("Deleted:", deleted.data);  // true/false
```

⚠️ **Warning**: Deleting a session removes its conversation history permanently.

#### Summarize a Session

Create a summary before deleting (or for documentation):

```typescript
const summarized = await client.session.summarize({
  path: { id: sessionId },
  body: {
    // Optional: custom instructions
    prompt: "Summarize key decisions and changes made"
  }
});
console.log("Summary:", summarized.data);
```

Summaries are stored and can be retrieved later.

### Session Best Practices

#### ✅ Do

1. **Use descriptive titles**
   ```typescript
   // Good
   title: "Add OAuth2 login with Google"
   
   // Not so good
   title: "Work in progress"
   ```

2. **Create separate sessions for separate concerns**
   ```typescript
   // Feature development
   const feature = await client.session.create({ body: { title: "Add user profiles" } });
   
   // Bug fix
   const bug = await client.session.create({ body: { title: "Fix login timeout" } });
   
   // Code review
   const review = await client.session.create({ body: { title: "Review PR #42" } });
   ```

3. **Initialize sessions properly**
   ```typescript
   const session = await client.session.create({ body: { title: "..." } });
   await client.session.init({ path: { id: session.data.id } });
   ```

4. **Share sessions for team collaboration**
   ```typescript
   const shared = await client.session.share({ path: { id: sessionId } });
   // Send the URL to your teammate
   ```

5. **Clean up old sessions**
   ```typescript
   // Periodically delete sessions you no longer need
   const oldSessions = await client.session.list({ query: { limit: 100 } });
   oldSessions.data.forEach(async (s) => {
     if (isOld(s.created_at)) {
       await client.session.delete({ path: { id: s.id } });
     }
   });
   ```

#### ❌ Don't

1. **Don't reuse sessions for unrelated tasks**
   - Each session should have a focused purpose
   - Mixing topics confuses the AI's context

2. **Don't store sensitive data in session titles/descriptions**
   - These may be logged or shared

3. **Don't create unlimited sessions without cleanup**
   - Sessions consume disk space
   - Implement retention policies

4. **Don't ignore undo/redo for destructive operations**
   - Always test with `/undo` before committing changes
   - Keep sessions around until you're sure

5. **Don't share sessions containing secrets**
   - Session sharing includes all conversation content
   - API keys, passwords, etc. will be exposed

### Common Session Patterns

#### Pattern 1: Interactive CLI Tool

```typescript
import { createOpencode } from "@opencode-ai/sdk";

async function interactiveCLI() {
  const { client, server } = await createOpencode();
  
  try {
    // Create dedicated session
    const session = await client.session.create({
      body: { title: "CLI Assistant" }
    });
    
    console.log(`Session ${session.data.id} ready! Type 'exit' to quit.\n`);
    
    while (true) {
      const input = await prompt("You: ");
      
      if (input === "exit") break;
      if (input === "/history") {
        // Show conversation history
        const msgs = await client.session.messages({ path: { id: session.data.id } });
        console.log(`\n${msgs.data.length} messages in this session\n`);
        continue;
      }
      
      try {
        const response = await client.session.prompt({
          path: { id: session.data.id },
          body: { parts: [{ type: "text", text: input }] }
        });
        console.log("\nAI:", response.data.text, "\n");
      } catch (error) {
        console.error("Error:", error);
      }
    }
    
    console.log("\nSession summary:", session.data.id);
    // Optionally: summarize or delete
  } finally {
    server.close();
  }
}

interactiveCLI();
```

#### Pattern 2: Batch Processing with Sessions

```typescript
import { createOpencode } from "@opencode-ai/sdk";

async function processMultipleTasks() {
  const { client, server } = await createOpencode();
  
  try {
    const tasks = [
      "Review security practices",
      "Check for code smells",
      "Update documentation"
    ];
    
    const sessions = [];
    
    for (const task of tasks) {
      // Create isolated session for each task
      const session = await client.session.create({
        body: { 
          title: task,
          initialPrompt: {
            parts: [{ type: "text", text: task }]
          }
        }
      });
      sessions.push(session);
      console.log(`Started: ${task} (${session.data.id})`);
    }
    
    // Wait for all to complete, check status
    for (const session of sessions) {
      const info = await client.session.get({ path: { id: session.data.id } });
      console.log(`${info.data.title}: ${info.data.message_count} messages`);
    }
  } finally {
    server.close();
  }
}
```

#### Pattern 3: Session Pool for Parallel Tasks

```typescript
import { createOpencode } from "@opencode-ai/sdk";

class SessionPool {
  private sessions: string[] = [];
  private client: any;
  
  constructor(client: any, poolSize = 3) {
    this.client = client;
    this.poolSize = poolSize;
  }
  
  async initialize() {
    for (let i = 0; i < this.poolSize; i++) {
      const session = await this.client.session.create({
        body: { title: `Worker ${i + 1}` }
      });
      this.sessions.push(session.data.id);
    }
  }
  
  async getSession(): Promise<string> {
    // Simple round-robin (in production, track which are busy)
    return this.sessions[Math.floor(Math.random() * this.sessions.length)];
  }
  
  async cleanup() {
    for (const id of this.sessions) {
      await this.client.session.delete({ path: { id } });
    }
  }
}

// Usage
const { client, server } = await createOpencode();
const pool = new SessionPool(client, 3);
await pool.initialize();
// ... use pool.getSession() for parallel work ...
await pool.cleanup();
server.close();
```

#### Pattern 4: State Persistence

```typescript
import fs from 'fs';
import { createOpencode } from "@opencode-ai/sdk";

class SessionManager {
  private sessionFile = 'opencode-sessions.json';
  
  async saveSession(sessionId: string, metadata: any) {
    const sessions = this.loadSessions();
    sessions[sessionId] = {
      ...metadata,
      created_at: new Date().toISOString(),
      last_used: new Date().toISOString()
    };
    fs.writeFileSync(this.sessionFile, JSON.stringify(sessions, null, 2));
  }
  
  loadSessions() {
    if (fs.existsSync(this.sessionFile)) {
      return JSON.parse(fs.readFileSync(this.sessionFile, 'utf-8'));
    }
    return {};
  }
  
  async resumeSession(client: any, sessionId: string) {
    const sessions = this.loadSessions();
    if (sessions[sessionId]) {
      // Resume existing session
      const session = await client.session.get({ path: { id: sessionId } });
      sessions[sessionId].last_used = new Date().toISOString();
      fs.writeFileSync(this.sessionFile, JSON.stringify(sessions, null, 2));
      return session;
    }
    return null;
  }
}
```

---

## Common Questions

### Q: Do I need the desktop client running?

**Answer**: Only if you use `createOpencodeClient()` to connect to an existing instance. If you use `createOpencode()`, the SDK starts its own server automatically. For beginners, use `createOpencode()`.

### Q: Can I use the SDK with my existing VS Code extension?

**Answer**: Yes! The SDK can connect to any running OpenCode instance. If you have the OpenCode VS Code extension installed, you can connect to its server using `createOpencodeClient({ baseUrl: "http://localhost:4096" })`.

### Q: What's the difference between the SDK and the CLI?

**Answer**:
- **CLI**: Command-line tool for interactive use (`opencode` command)
- **SDK**: JavaScript library for programmatic control (import in your code)
- Both interface with the same OpenCode server

### Q: How do I know which model to use?

**Answer**: Start with these beginner-friendly options:
- **Zen**: `"opencode"` provider, any model from their curated list
- **Claude**: `"anthropic/claude-3-5-sonnet-20241022"` (excellent for coding)
- **GPT-4**: `"openai/gpt-4-turbo-preview"`

### Q: Can I use local models?

**Answer**: Yes! OpenCode supports local models through providers like Ollama, LM Studio, and others. See the [Providers documentation](https://opencode.ai/docs/providers).

### Q: Do I have to pay?

**Answer**:
- OpenCode software: **Free** (open source)
- AI API costs: **Pay-per-use** from providers (Anthropic, OpenAI, etc.)
- Zen: Pay-per-use with no subscription
- Costs are typically fractions of a cent per conversation

### Q: How do I handle errors?

**Answer**: The SDK returns structured errors:

```typescript
try {
  const result = await client.session.prompt({...});
} catch (error) {
  if (error instanceof Error) {
    console.error("Failed:", error.message);
  }
}
```

For structured output errors:

```typescript
if (result.data.info.error?.name === "StructuredOutputError") {
  console.error("Validation failed:", result.data.info.error.message);
}
```

### Q: Can I use the SDK in a browser?

**Answer**: No, the SDK requires a local OpenCode server which needs Node.js. It's designed for server-side or desktop applications, not browser environments.

### Q: How do I stop the server?

**Answer**: If you started it with `createOpencode()`, call `server.close()`:

```typescript
const { client, server } = await createOpencode();
// ... use client ...
server.close();  // Clean shutdown
```

If you connected to an external server with `createOpencodeClient()`, don't close it - the external process manages it.

---

## Tools Deep Dive

Tools are the **actions** that OpenCode (and the AI) can perform in your codebase. Think of them as capabilities or superpowers that the LLM can use to help you.

### What Are Tools?

When you ask OpenCode to do something, it doesn't just generate text—it can **act**. Tools are the mechanisms for those actions:

- **Read a file** (`read` tool)
- **Edit code** (`edit` tool)
- **Run a command** (`bash` tool)
- **Search the codebase** (`grep`, `glob` tools)
- **Fetch documentation** (`webfetch` tool)
- **Ask you questions** (`question` tool)

OpenCode includes **15+ built-in tools** and supports:
- **Custom tools** - Write your own in any language
- **MCP servers** - Connect to external services

### Why Tools Matter

Without tools, an AI is just a chat bot—it can only talk. With tools, it becomes an **agent** that can:

✅ **See** your codebase (read, grep, glob)
✅ **Change** your code (edit, write, patch)
✅ **Do** things (bash commands, LSP queries)
✅ **Learn** from the web (webfetch, websearch)
✅ **Ask** for clarification (question)

This is what makes OpenCode a **coding agent** rather than just another Copilot.

---

## Built-in Tools

Here's a comprehensive guide to all built-in tools, organized by category.

### File Operations

#### `read` - Read File Contents

Reads the contents of a file. Supports reading specific line ranges.

**When the AI uses it**: To understand existing code before making changes.

**Example**:
```
Human: "Add error handling to src/utils/api.ts"
AI → Uses `read` tool to see current code
AI → Suggests changes
AI → Uses `edit` tool to apply them
```

**Permissions**: Requires `read: "allow"` in `opencode.json`

**Configuration**:
```json
{
  "permission": {
    "read": "allow"
  }
}
```

**SDK usage**:
```typescript
const content = await client.file.read({
  query: { path: "src/utils/api.ts" }
});
console.log(content.data.content);
```

---

#### `edit` - Edit Existing Files

Modifies files by performing exact string replacements. This is the **primary** way OpenCode changes code.

**When the AI uses it**: To modify specific lines in an existing file.

**Important**: `edit` replaces **exact text matches**. If the content doesn't match exactly, it fails. This prevents accidental changes.

**Permissions**: Requires `edit: "allow"`

**Configuration**:
```json
{
  "permission": {
    "edit": "allow"
  }
}
```

**SDK usage**:
```typescript
await client.file.edit({
  query: { path: "src/index.ts" },
  body: {
    edits: [
      {
        old_string: "const oldCode = 'deprecated';",
        new_string: "const newCode = 'updated';"
      }
    ]
  }
});
```

**Tip**: If you want to allow all file modifications (`edit`, `write`, `patch`, `multiedit`), set:
```json
{
  "permission": {
    "edit": "allow"
  }
}
```

---

#### `write` - Write New Files

Creates new files or overwrites existing ones.

**When the AI uses it**: To create new files (config files, new components, tests, etc.)

**Permissions**: Controlled by `edit` permission (same as `edit` tool)

**SDK usage**:
```typescript
await client.file.write({
  query: { path: "src/new-feature.ts" },
  body: {
    content: `export function newFeature() {
  console.log("Hello from new feature!");
}`
  }
});
```

---

#### `patch` - Apply Patches

Applies patch files (diff format) to your codebase. Useful for bulk changes or applying patches from pull requests.

**When the AI uses it**: When you provide a git diff or patch file.

**Permissions**: Controlled by `edit` permission

**SDK usage**:
```typescript
await client.file.patch({
  query: { path: "." },
  body: {
    patch: `--- a/src/index.ts
+++ b/src/index.ts
@@ -1,5 +1,6 @@
 function main() {
   console.log("Hello");
+  console.log("World");
 }
`
  }
});
```

---

### Search & Discovery

#### `grep` - Search File Contents

Searches for text patterns using regular expressions. Very fast, uses ripgrep under the hood.

**When the AI uses it**: To find specific code patterns, function calls, imports, etc.

**Permissions**: Requires `grep: "allow"`

**Examples**:
```
Search for: "function.*useState"
Search for: "import.*react"
Search for: "TODO|FIXME"
```

**SDK usage**:
```typescript
const results = await client.find.text({
  query: { 
    pattern: "function.*useState",
    // Optional filters:
    // glob: "src/**/*.ts",  // Only in .ts files
    // type: "file"           // Return file paths only
  }
});

results.data.forEach(match => {
  console.log(`${match.path}:${match.line_number}: ${match.lines.text}`);
});
```

---

#### `glob` - Find Files by Pattern

Finds files and directories using glob patterns like `**/*.js` or `src/components/**/*.tsx`.

**When the AI uses it**: To discover project structure, find all test files, locate configuration files, etc.

**Permissions**: Requires `glob: "allow"`

**Examples**:
```
Find all test files: "**/*.test.ts"
Find all config files: "**/*.config.{js,ts}"
Find all documentation: "**/*.md"
```

**SDK usage**:
```typescript
const files = await client.find.files({
  query: { 
    query: "**/*.ts",
    type: "file",      // "file" or "directory"
    limit: 100         // Max results
  }
});
console.log("Found files:", files.data);
```

---

#### `list` - List Directory Contents

Lists files and directories in a given path (similar to `ls` command).

**When the AI uses it**: To explore directory structure.

**Permissions**: Requires `list: "allow"`

**SDK usage**:
```typescript
const entries = await client.file.list({
  query: { 
    path: "src/components",  // Defaults to project root
    pattern: "*.tsx"         // Optional filter
  }
});
console.log("Directory contents:", entries.data);
```

---

### Code Intelligence

#### `lsp` (experimental) - Language Server Protocol

Interacts with LSP servers for advanced code intelligence: definitions, references, hover info, implementations, call hierarchy.

**Note**: This tool is **experimental** and requires `OPENCODE_EXPERIMENTAL_LSP_TOOL=true` environment variable.

**When the AI uses it**: To answer questions like:
- "Where is this function defined?"
- "Where is this variable used?"
- "What's the type of this parameter?"

**Supported operations**:
- `goToDefinition` - Jump to symbol definition
- `findReferences` - Find all references to a symbol
- `hover` - Get hover information (type, docstring)
- `documentSymbol` - List symbols in current file
- `workspaceSymbol` - Search symbols across workspace
- `goToImplementation` - Find implementations of an interface/abstract method
- `prepareCallHierarchy` / `incomingCalls` / `outgoingCalls` - Call graph analysis

**Permissions**: Requires `lsp: "allow"`

**SDK usage**:
```typescript
// Get hover information for a symbol
const hover = await client.lsp.hover({
  query: {
    path: "src/components/Button.tsx",
    line: 10,
    character: 15  // Column position
  }
});
console.log("Hover info:", hover.data);

// Find references
const refs = await client.lsp.references({
  query: {
    path: "src/utils/helpers.ts",
    line: 5,
    character: 10
  }
});
console.log("Found at:", refs.data.map(r => `${r.path}:${r.range.start.line}`));
```

**Configuration**: See [LSP Servers docs](https://opencode.ai/docs/lsp) to configure which LSP servers are available.

---

### Shell & System

#### `bash` - Execute Shell Commands

Runs shell commands in your project environment. This is **extremely powerful** and potentially dangerous.

**When the AI uses it**: To:
- Install dependencies: `npm install`, `pip install -r requirements.txt`
- Run tests: `npm test`, `pytest`
- Build projects: `npm run build`, `go build`
- Check status: `git status`, `git diff`
- Start servers: `npm start`, `python app.py`
- Any terminal command

**Permissions**: Requires `bash: "allow"` or `bash: "ask"` (recommended)

**Configuration**:
```json
{
  "permission": {
    "bash": "ask"  // "ask" requires user approval for each command
  }
}
```

**SDK usage**:
```typescript
const result = await client.session.shell({
  path: { id: sessionId },
  body: {
    command: "npm test",
    timeout: 30000  // 30 second timeout (optional)
  }
});

console.log("Exit code:", result.data.exit_code);
console.log("Stdout:", result.data.stdout);
console.log("Stderr:", result.data.stderr);
```

**⚠️ Security Warning**: The `bash` tool can run **any command**. Best practices:
- Use `"ask"` permission to review each command
- Never use with `bash: "allow"` in production/enterprise without oversight
- Limit what commands can be run via custom tools or agent permissions
- Be aware that malicious prompts could try to delete files, exfiltrate data, etc.

---

### Web & External

#### `webfetch` - Fetch Web Content

Fetches and reads web pages. Useful for looking up documentation, API references, or online resources.

**When the AI uses it**: When it needs up-to-date information or documentation.

**Permissions**: Requires `webfetch: "allow"`

**SDK usage**:
```typescript
const result = await client.file.webfetch({
  query: { url: "https://developer.mozilla.org/en-US/docs/Web/JavaScript" }
});
console.log("Fetched content length:", result.data.content.length);
```

---

#### `websearch` - Search the Web

Performs web searches using Exa AI. Finds relevant information online (news, current events, technical content).

**When the AI uses it**: When it needs to search for something rather than fetch a specific URL.

**Enable**: Set `OPENCODE_ENABLE_EXA=1` environment variable

**Permissions**: Requires `websearch: "allow"`

**SDK usage**:
```typescript
const results = await client.file.websearch({
  query: { 
    query: "React server components best practices 2025"
  }
});
console.log("Found:", results.data.results);
```

---

### User Interaction

#### `question` - Ask the User Questions

Allows the AI to ask you questions during execution. Essential for clarification and decision-making.

**When the AI uses it**: When it needs:
- Your preferences ("Should I use Redux or Context API?")
- Clarification on ambiguous requirements ("Do you want authentication with JWT or sessions?")
- Decisions on implementation direction ("Which design pattern: MVC or Flux?")
- Confirmation ("I'm about to delete 3 files. Proceed?")

**Permissions**: Requires `question: "allow"`

**SDK usage**:
```typescript
// Show a toast/question to the user (in TUI)
await client.tui.showToast({
  body: {
    message: "Need clarification on authentication method",
    variant: "info"
  }
});
```

**Example interaction**:
```
AI: "I need to know which database to use. Options: PostgreSQL, MongoDB, SQLite"
You: [Select from options or type custom answer]
```

---

### Agent Skills

#### `skill` - Load Agent Skills

Loads a `SKILL.md` file that provides specialized knowledge or capabilities for the agent.

**When the AI uses it**: To access domain-specific expertise, project-specific rules, or specialized workflows defined in `SKILL.md` files.

**Permissions**: Requires `skill: "allow"`

**What is a SKILL.md?**: Similar to `AGENTS.md` (which describes the project), a `SKILL.md` describes skills the agent can use. For example:
- "You are an expert in React Native"
- "You know how to use our internal design system"
- "Follow these testing practices..."

---

#### `todowrite` / `todoread` - Manage Todo Lists

Tools for managing task lists during complex operations.

**When the AI uses it**: To break down multi-step tasks and track progress.

**Permissions**: Requires `todowrite: "allow"` and `todoread: "allow"`

**Disabled by default** for subagents (to prevent infinite loops), but can be enabled.

**Example**:
```
AI creates todo:
1. [ ] Analyze codebase structure
2. [ ] Design API endpoints
3. [ ] Implement database models
4. [ ] Write tests
```

As it completes steps, it marks them done using these tools.

---

## Tool Permissions

You control which tools the AI can use via the `permission` field in `opencode.json`:

```json
{
  "permission": {
    "read": "allow",
    "edit": "allow",
    "bash": "ask",
    "webfetch": "allow",
    "websearch": "deny",
    "question": "ask"
  }
}
```

### Permission Levels

| Level | Behavior |
|-------|----------|
| `allow` | Tool can be used automatically without confirmation |
| `deny` | Tool cannot be used; AI will skip it |
| `ask` | AI must request permission; user approves each use |

### Wildcards

You can use wildcards to control groups of tools:

```json
{
  "permission": {
    "mymcp_*": "ask",     // All MCP server tools require approval
    "web*": "deny",       // Deny all web-related tools
    "*": "ask"            // Everything requires approval (paranoid mode)
  }
}
```

### Default Permissions

By default, all built-in tools are `allow`. Start with more restrictive settings (`bash: "ask"`) and loosen as needed.

---

## Custom Tools

Beyond built-in tools, you can create **your own** custom tools in any programming language.

### Where to Put Custom Tools

Create a `.opencode/tools/` directory in your project root:

```
my-project/
├── .opencode/
│   └── tools/
│       ├── database.ts
│       ├── api-client.py
│       └── deploy.sh
├── src/
└── opencode.json
```

### Tool Structure

Tools are defined in TypeScript/JavaScript files that export a tool object:

```typescript
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Query the project database",
  args: {
    query: tool.schema.string().describe("SQL query to execute"),
  },
  async execute(args) {
    // Your tool logic here
    const result = await database.query(args.query);
    return JSON.stringify(result);
  }
});
```

**The filename becomes the tool name**:
- `.opencode/tools/database.ts` → `database` tool
- `.opencode/tools/math_add.ts` → `math_add` tool

### Using Zod for Validation

`tool.schema` is Zod under the hood. You can use any Zod validation:

```typescript
args: {
  email: tool.schema.string().email(),
  age: tool.schema.number().min(0).max(120),
  tags: tool.schema.array(tool.schema.string()),
  config: tool.schema.object({
    timeout: tool.schema.number().default(5000),
    retries: tool.schema.number().optional()
  })
}
```

### Context Parameter

The `execute` function receives a `context` object:

```typescript
async execute(args, context) {
  const {
    agent,           // Agent name
    sessionID,       // Current session ID
    messageID,       // Current message ID
    directory,       // Session working directory
    worktree,        // Git worktree root
    env              // Environment variables
  } = context;
  
  return `Working in ${directory}`;
}
```

Use `context.directory` to know where to operate.

### Multiple Tools Per File

Export multiple named exports; each becomes a separate tool:

```typescript
export const add = tool({ /* ... */ });    // tool name: "math_add"
export const subtract = tool({ /* ... */ }); // tool name: "math_subtract"
```

### Writing Tools in Other Languages

Your tool definition is TypeScript, but the actual work can be in **any language**:

**Python tool** (`.opencode/tools/analyze.py`):
```python
import sys
import json

data = json.loads(sys.argv[1])
result = {"processed": data["input"].upper()}
print(json.dumps(result))
```

**TypeScript wrapper** (`.opencode/tools/python_wrapper.ts`):
```typescript
import { tool } from "@opencode-ai/plugin"
import path from "path"

export default tool({
  description: "Analyze data using Python script",
  args: {
    input: tool.schema.string()
  },
  async execute(args, context) {
    const scriptPath = path.join(context.worktree, ".opencode/tools/analyze.py");
    const result = await Bun.$`python ${scriptPath} ${JSON.stringify(args)}`.text();
    return JSON.parse(result);
  }
});
```

### Example: Database Query Tool

```typescript
// .opencode/tools/database.ts
import { tool } from "@opencode-ai/plugin"
import { Pool } from 'pg';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL
});

export default tool({
  description: "Execute SQL queries on the project database (read-only)",
  args: {
    query: tool.schema.string().describe("SELECT query to execute")
  },
  async execute(args, context) {
    // Safety: Only allow SELECT
    if (!args.query.trim().toUpperCase().startsWith('SELECT')) {
      throw new Error("Only SELECT queries allowed");
    }
    
    const result = await pool.query(args.query);
    return {
      rows: result.rows,
      rowCount: result.rowCount
    };
  }
});
```

### Example: API Testing Tool

```typescript
// .opencode/tools/test-api.ts
import { tool } from "@opencode-ai/plugin"
import fetch from 'node-fetch';

export default tool({
  description: "Test API endpoints",
  args: {
    method: tool.schema.enum(['GET', 'POST', 'PUT', 'DELETE']),
    url: tool.schema.string().url(),
    body: tool.schema.object({}).optional()
  },
  async execute(args) {
    const response = await fetch(args.url, {
      method: args.method,
      body: args.body ? JSON.stringify(args.body) : undefined,
      headers: { 'Content-Type': 'application/json' }
    });
    
    return {
      status: response.status,
      statusText: response.statusText,
      data: await response.json()
    };
  }
});
```

### Registering Custom Tools

Custom tools are **automatically discovered** from `.opencode/tools/` directory. No registration needed!

OpenCode loads them on startup. Restart OpenCode after adding tools.

---

## Using Tools via SDK

In the SDK, you typically **don't call tools directly**. Instead, you send prompts and let the AI decide which tools to use.

However, the SDK exposes low-level tool execution if needed:

```typescript
// Direct tool execution (advanced use)
const result = await client.tool.execute({
  path: { sessionId },
  body: {
    name: "bash",  // Tool name
    input: {
      command: "ls -la"
    }
  }
});
console.log("Tool output:", result.data);
```

### Tool Calls in Messages

When you use `client.session.prompt()`, the AI may call tools automatically:

```typescript
const response = await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [{ type: "text", text: "What files are in the src directory?" }]
  }
});

// The response includes tool calls if tools were used
console.log("AI response:", response.data.text);
console.log("Tool calls:", response.data.tool_calls);
```

**Tool call structure**:
```typescript
interface ToolCall {
  name: string;          // Tool name (e.g., "bash", "read")
  input: object;         // Arguments passed to tool
  output: any;           // Result returned by tool
}
```

---

## Tool Best Practices

### ✅ DO

1. **Start with safe defaults**
   ```json
   {
     "permission": {
       "bash": "ask",      // Require approval for shell commands
       "edit": "allow"     // But allow file edits (reversible via /undo)
     }
   }
   ```

2. **Use `read` before `edit`**
   - AI should read files first to understand context
   - This prevents accidental modifications

3. **Validate custom tool inputs**
   ```typescript
   args: {
     path: tool.schema.string().refine(p => !p.includes('..'), {
       message: "Path traversal not allowed"
     })
   }
   ```

4. **Handle errors gracefully**
   ```typescript
   async execute(args) {
     try {
       return await riskyOperation();
     } catch (error) {
       return `Error: ${error.message}`;
     }
   }
   ```

5. **Use context for project awareness**
   ```typescript
   const projectRoot = context.worktree;
   const filePath = path.join(projectRoot, args.filename);
   ```

6. **Test tools independently**
   - Write unit tests for your custom tools
   - Test with various inputs (valid, invalid, edge cases)

7. **Document your custom tools**
   ```typescript
   description: `
     Deploy the application to production.
     
     This tool:
     1. Builds the application
     2. Runs tests
     3. Deploys to AWS
     
     Requires AWS credentials in env vars.
   `
   ```

### ❌ DON'T

1. **Don't give `bash` full `allow` without oversight**
   ```json
   // DANGEROUS:
   { "permission": { "bash": "allow" } }
   
   // BETTER:
   { "permission": { "bash": "ask" } }
   ```

2. **Don't store secrets in tool code**
   ```typescript
   // BAD:
   const API_KEY = "sk-...";  // Hardcoded secret!
   
   // GOOD:
   const API_KEY = process.env.API_KEY;  // Use env vars or config
   ```

3. **Don't allow path traversal**
   ```typescript
   // Validate paths to prevent ../ attacks
   args: {
     filename: tool.schema.string().refine(
       name => !name.includes('..') && !name.startsWith('/'),
       "Invalid filename"
     )
   }
   ```

4. **Don't make network calls without timeouts**
   ```typescript
   // Always set timeouts
   const controller = new AbortController();
   const timeoutId = setTimeout(() => controller.abort(), 10000);
   const response = await fetch(url, { signal: controller.signal });
   ```

5. **Don't block the event loop**
   - Use async operations
   - Don't run CPU-intensive tasks without offloading

6. **Don't ignore context.worktree**
   - Always operate relative to `context.worktree` or `context.directory`
   - Never assume current working directory

7. **Don't duplicate built-in tool functionality**
   - Before creating a custom tool, check if a built-in tool already does it
   - Example: Use `grep` instead of writing your own search tool

---

## Common Tool Patterns

### Pattern 1: Tool Chain

AI uses multiple tools in sequence:

```
Human: "Add a new API endpoint"

AI:
1. read - Check existing routes
2. read - Look at controllers
3. write - Create new route file
4. write - Create controller
5. bash - npm run build
6. question - "Should I add tests?"
```

---

### Pattern 2: Tool with Feedback Loop

AI uses tools, evaluates results, and iterates:

```
AI: Search for "TODO" comments
  → tool: grep pattern="TODO"
  → Result: Found 5 TODOs

AI: For each TODO, read the file
  → tool: read (5 times)
  → Result: Context for each

AI: Fix each TODO
  → tool: edit (5 times)
```

---

### Pattern 3: Tool as Decision Input

AI uses tools to gather information before deciding:

```
AI: question - "Which database? PostgreSQL or MongoDB?"
  → User selects: PostgreSQL

AI: webfetch - Fetch PostgreSQL docs
  → tool: webfetch(url="https://postgresql.org/docs")
  
AI: bash - Check if pg_dump is available
  → tool: bash(command="which pg_dump")

AI: Based on info, implement PostgreSQL solution
```

---

### Pattern 4: Permission-Aware Tools

With `"ask"` permission, user approves each tool call:

```
TUI shows: "AI wants to run: bash - npm install"
[Approve] [Deny] [Always allow] [Never allow]

User selects: [Approve]
→ Command executes
```

Use this for:
- Destructive operations (`bash rm`, `bash git reset --hard`)
- External calls (`webfetch`, `websearch`)
- Cost-incurring operations (API calls)

---

## Tool Configuration Reference

### In opencode.json

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "read": "allow",
    "edit": "allow",
    "write": "allow",
    "bash": "ask",
    "grep": "allow",
    "glob": "allow",
    "list": "allow",
    "lsp": "allow",
    "patch": "allow",
    "skill": "allow",
    "todowrite": "allow",
    "todoread": "allow",
    "webfetch": "allow",
    "websearch": "deny",
    "question": "ask"
  }
}
```

### Environment Variables

| Variable | Purpose |
|----------|---------|
| `OPENCODE_ENABLE_EXA=1` | Enable `websearch` tool |
| `OPENCODE_EXPERIMENTAL_LSP_TOOL=true` | Enable `lsp` tool |
| `OPENCODE_EXPERIMENTAL=true` | Enable all experimental features |

---

## Troubleshooting Tools

### Problem: Tool not found

**Solution**: 
- Check spelling: tool names are case-sensitive
- For custom tools: ensure `.opencode/tools/` exists and contains `.ts`/`.js` files
- Restart OpenCode after adding custom tools

### Problem: Tool permission denied

**Solution**:
- Check `opencode.json` permissions
- If tool requires `"ask"`, look for approval prompt in TUI
- Use `/permissions` command in TUI to see current settings

### Problem: `bash` command fails

**Solution**:
- Check if command exists in PATH
- Commands run from project root (or `context.directory`)
- Ensure environment variables are set
- Check if command requires interactive terminal (some don't work)

### Problem: `lsp` tool not working

**Solution**:
- Enable experimental: `OPENCODE_EXPERIMENTAL_LSP_TOOL=true`
- Configure LSP servers in `opencode.json` or `.vscode/settings.json`
- Ensure LSP server is installed and in PATH

### Problem: Custom tool not executing

**Solution**:
- Check syntax errors in tool file
- Look at OpenCode logs (stderr)
- Ensure `@opencode-ai/plugin` is installed
- Use absolute paths in custom tools (use `context.worktree`)

---

## Resources

| Resource | URL |
|----------|-----|
| Tools Documentation | https://opencode.ai/docs/tools |
| Custom Tools Guide | https://opencode.ai/docs/custom-tools |
| Permissions Guide | https://opencode.ai/docs/permissions |
| LSP Configuration | https://opencode.ai/docs/lsp |
| MCP Servers | https://opencode.ai/docs/mcp-servers |
| SDK Tool Methods | https://opencode.ai/docs/sdk#files |

---

## Summary

**Tools are OpenCode's superpowers**:

- **Read/Edit/Write** - Modify files safely
- **Grep/Glob/List** - Search and explore
- **Bash** - Run commands (use with caution!)
- **LSP** - Advanced code intelligence
- **WebFetch/WebSearch** - Access external info
- **Question** - Get user input
- **Custom Tools** - Extend with your own code

**Key takeaways**:
1. Control tools via `opencode.json` permissions
2. Use `"ask"` for dangerous tools (`bash`, `websearch`)
3. Custom tools go in `.opencode/tools/`
4. Tools operate relative to `context.worktree`
5. The AI decides which tools to use automatically

**Next**: Learn about [Permissions](https://opencode.ai/docs/permissions) for fine-grained control.

### 1. Explore the API Reference

All available methods are documented in the [Official SDK Docs](https://opencode.ai/docs/sdk).

**Core Concept**: Everything revolves around **sessions**. A session is a conversation with the AI.

Key namespaces:
- `client.session` - **Most important!** Create and manage conversations
  - `create()`, `list()`, `get()`, `delete()`
  - `prompt()` - Send messages to AI
  - `command()` - Execute OpenCode commands
  - `shell()` - Run shell commands
  - `messages()` - View conversation history
  - `revert()`/`unrevert()` - Undo/redo changes
  - `share()`/`unshare()` - Collaboration
  - `children()` - View sub-sessions
- `client.file` / `client.find` - File operations and search
  - `file.read()` - Read file contents
  - `find.text()` - Search for text patterns
  - `find.files()` - Find files by name
  - `find.symbols()` - Find code symbols
  - `file.status()` - Get git status
- `client.config` - Configuration management
  - `config.get()` - Get current config
  - `config.providers()` - List available AI providers
- `client.project` - Project operations
  - `project.list()` - All projects OpenCode knows about
  - `project.current()` - Current project info
- `client.path` - Path information
- `client.tui` - Control the terminal UI (if running)
- `client.event` - Real-time event subscriptions
- `client.auth` - Authentication/API key management
- `client.global` - Global operations (health checks)
- `client.app` - Application logging and agents

**Remember**: Start with `client.session.create()` and build from there!

### 2. Build Something!

Try these beginner projects:
- **Chatbot**: Simple CLI tool that uses OpenCode to answer questions
- **Code reviewer**: Automatically review pull requests
- **Documentation generator**: Generate docs from code
- **Learning assistant**: Explain code concepts interactively

### 3. Check Out Community Examples

- [Ecosystem examples](https://opencode.ai/docs/ecosystem)
- [GitHub repository](https://github.com/anomalyco/opencode)
- Community Discord: [opencode.ai/discord](https://opencode.ai/discord)

### 4. Learn More About OpenCode

- [Getting Started Guide](https://opencode.ai/docs)
- [Configuration Options](https://opencode.ai/docs/config)
- [Provider Setup](https://opencode.ai/docs/providers)
- [IDE Integration](https://opencode.ai/docs/ide)

---

## Tips for Success

✅ **Start small** - Get a basic "Hello World" working before building complex features

✅ **Use TypeScript** - Get full autocomplete and type safety from the SDK

✅ **Handle errors** - Always wrap SDK calls in try-catch blocks

✅ **Close the server** - If you started it, close it when done

✅ **Read the docs** - The official docs are comprehensive and up-to-date

✅ **Join Discord** - Fastest way to get help from the community

❌ **Don't hardcode API keys** - Use environment variables or config files

❌ **Don't ignore errors** - The SDK provides detailed error information - use it!

❌ **Don't run as root/admin** - The server doesn't need elevated permissions

---

## Example: Complete Mini-Project

Create `task-manager.ts` - a session-based task manager:

```typescript
import { createOpencode } from "@opencode-ai/sdk";
import * as fs from 'fs';

interface Task {
  id: string;
  description: string;
  status: 'pending' | 'in-progress' | 'completed';
  sessionId?: string;  // OpenCode session ID for this task
}

class TaskManager {
  private client: any;
  private tasks: Task[] = [];
  private storageFile = 'tasks.json';
  
  constructor(client: any) {
    this.client = client;
    this.loadTasks();
  }
  
  private loadTasks() {
    if (fs.existsSync(this.storageFile)) {
      this.tasks = JSON.parse(fs.readFileSync(this.storageFile, 'utf-8'));
    }
  }
  
  private saveTasks() {
    fs.writeFileSync(this.storageFile, JSON.stringify(this.tasks, null, 2));
  }
  
  async createTask(description: string): Promise<Task> {
    const task: Task = {
      id: `task-${Date.now()}`,
      description,
      status: 'pending'
    };
    this.tasks.push(task);
    this.saveTasks();
    return task;
  }
  
  async startTask(taskId: string): Promise<void> {
    const task = this.tasks.find(t => t.id === taskId);
    if (!task) throw new Error(`Task ${taskId} not found`);
    
    // Create an OpenCode session for this task
    const session = await this.client.session.create({
      body: { title: task.description }
    });
    task.sessionId = session.data.id;
    task.status = 'in-progress';
    this.saveTasks();
    
    console.log(`🚀 Started task: ${task.description}`);
    console.log(`   Session: ${session.data.id}`);
  }
  
  async askTask(taskId: string, question: string): Promise<void> {
    const task = this.tasks.find(t => t.id === taskId && t.sessionId);
    if (!task || !task.sessionId) {
      throw new Error(`Task ${taskId} has no active session`);
    }
    
    try {
      const response = await this.client.session.prompt({
        path: { id: task.sessionId },
        body: { parts: [{ type: "text", text: question }] }
      });
      console.log(`\n💡 ${response.data.text}\n`);
    } catch (error) {
      console.error("❌ Error:", error);
    }
  }
  
  async completeTask(taskId: string): Promise<void> {
    const task = this.tasks.find(t => t.id === taskId);
    if (!task || !task.sessionId) {
      throw new Error(`Task ${taskId} has no session to summarize`);
    }
    
    // Get session summary before cleanup
    const summary = await this.client.session.summarize({
      path: { id: task.sessionId }
    });
    
    task.status = 'completed';
    this.saveTasks();
    
    console.log(`✅ Completed: ${task.description}`);
    console.log(`📋 Summary: ${summary.data}`);
  }
  
  async cleanup() {
    // Delete all old sessions (optional cleanup)
    for (const task of this.tasks) {
      if (task.sessionId && task.status === 'completed') {
        try {
          await this.client.session.delete({ 
            path: { id: task.sessionId } 
          });
          console.log(`🗑️  Deleted session: ${task.sessionId}`);
          task.sessionId = undefined;
        } catch (error) {
          console.warn(`Could not delete session ${task.sessionId}:`, error);
        }
      }
    }
    this.saveTasks();
  }
  
  listTasks() {
    console.log("\n📋 Tasks:");
    this.tasks.forEach(task => {
      const icon = task.status === 'completed' ? '✅' : 
                   task.status === 'in-progress' ? '🔄' : '⏳';
      console.log(`  ${icon} ${task.id}: ${task.description}`);
      if (task.sessionId) console.log(`     Session: ${task.sessionId}`);
    });
    console.log("");
  }
}

async function main() {
  const { client, server } = await createOpencode();
  
  try {
    const manager = new TaskManager(client);
    
    // Create a task
    const task = await manager.createTask(
      "Review authentication middleware in src/middleware/auth.ts"
    );
    console.log(`📝 Created task: ${task.id}`);
    
    // Start OpenCode session for the task
    await manager.startTask(task.id);
    
    // Interact with the task
    await manager.askTask(task.id, "What security issues do you see?");
    await manager.askTask(task.id, "Suggest improvements");
    
    // Mark as complete
    await manager.completeTask(task.id);
    
    // List all tasks
    manager.listTasks();
    
    // Cleanup old sessions (optional)
    // await manager.cleanup();
    
  } finally {
    server.close();
  }
}

main().catch(console.error);
```

Run it:

```bash
npx tsx task-manager.ts
```

Features demonstrated:
- ✅ Creating and managing multiple tasks with sessions
- ✅ Association between tasks and OpenCode sessions
- ✅ Multi-turn conversations per task
- ✅ Session summarization
- ✅ Persistence to disk
- ✅ Clean resource management

---

## Resources

| Resource | URL |
|----------|-----|
| Official Docs | https://opencode.ai/docs |
| SDK Reference | https://opencode.ai/docs/sdk |
| GitHub Repo | https://github.com/anomalyco/opencode |
| Discord Community | https://opencode.ai/discord |
| Download Desktop | https://opencode.ai/download |
| Zen (Models) | https://opencode.ai/zen |

---

**Happy coding with OpenCode!** 🚀

*Last updated: March 2026*