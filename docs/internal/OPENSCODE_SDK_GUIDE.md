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
- [Events Deep Dive](#events-deep-dive)
  - [What Are Events?](#what-are-events)
  - [Why Use Events?](#why-use-events)
  - [Event Architecture](#event-architecture)
  - [Subscribing to Events](#subscribing-to-events)
  - [Event Types](#event-types)
    - [Session Events](#session-events)
    - [File Events](#file-events)
    - [Tool Events](#tool-events)
    - [Message Events](#message-events)
    - [Agent Events](#agent-events)
    - [System Events](#system-events)
  - [Filtering Events](#filtering-events)
  - [Async vs Sync Consumption](#async-vs-sync-consumption)
  - [Event-Driven Patterns](#event-driven-patterns)
  - [Error Handling](#error-handling)
  - [Performance Considerations](#performance-considerations)
  - [Events Best Practices](#events-best-practices)
- [Files Deep Dive](#files-deep-dive)
  - [Understanding File Operations](#understanding-file-operations)
  - [SDK File API Overview](#sdk-file-api-overview)
  - [Searching Text (grep)](#searching-text-grep)
    - [Basic Text Search](#basic-text-search)
    - [Advanced Regex Patterns](#advanced-regex-patterns)
    - [Filtering by File Type](#filtering-by-file-type)
    - [Performance Optimization](#performance-optimization)
  - [Finding Files (glob)](#finding-files-glob)
    - [Glob Pattern Basics](#glob-pattern-basics)
    - [Common Patterns](#common-patterns)
    - [Combining with Filters](#combining-with-filters)
  - [Listing Directories (list)](#listing-directories-list)
    - [Basic Directory Listing](#basic-directory-listing)
    - [Recursive Listings](#recursive-listings)
    - [Filtering Results](#filtering-results)
  - [Reading Files (read)](#reading-files-read)
    - [Complete File Read](#complete-file-read)
    - [Partial Reads (Line Ranges)](#partial-reads-line-ranges)
    - [Handling Different Encodings](#handling-different-encodings)
  - [File Status & Git Integration](#file-status--git-integration)
  - [Symbol Search](#symbol-search)
  - [Working with File Paths](#working-with-file-paths)
  - [File Operation Patterns](#file-operation-patterns)
  - [Security Considerations](#security-considerations)
  - [Performance Tips](#performance-tips)
  - [Troubleshooting File Operations](#troubleshooting-file-operations)
- [Agents Deep Dive](#agents-deep-dive)
  - [What Are Agents?](#what-are-agents)
  - [Why Use Agents?](#why-use-agents)
  - [Agent Architecture](#agent-architecture)
  - [Agent Types](#agent-types)
    - [Primary Agents](#primary-agents)
    - [Subagents](#subagents)
    - [System Agents](#system-agents)
  - [Built-in Agents](#built-in-agents)
    - [Build Agent](#build-agent)
    - [Plan Agent](#plan-agent)
    - [General Subagent](#general-subagent)
    - [Explore Subagent](#explore-subagent)
  - [Custom Agent Creation](#custom-agent-creation)
    - [Method 1: JSON Configuration](#method-1-json-configuration)
    - [Method 2: Markdown Files](#method-2-markdown-files)
    - [Method 3: CLI Creation](#method-3-cli-creation)
  - [Agent Configuration Options](#agent-configuration-options)
    - [description](#description)
    - [temperature](#temperature)
    - [steps (max-steps)](#steps-max-steps)
    - [disable](#disable)
    - [prompt](#prompt)
    - [model](#model)
    - [tools](#tools)
    - [permissions](#permissions)
    - [mode](#mode)
    - [hidden](#hidden)
    - [task-permissions](#task-permissions)
    - [color](#color)
    - [top-p](#top-p)
    - [additional options](#additional-options)
  - [Agent Permissions Deep Dive](#agent-permissions-deep-dive)
    - [Permission Levels](#permission-levels)
    - [Tool-specific Permissions](#tool-specific-permissions)
    - [Command-specific Permissions](#command-specific-permissions)
    - [Wildcard Patterns](#wildcard-patterns)
    - [Task Permissions (Subagent Invocation)](#task-permissions-subagent-invocation)
  - [Using Agents via SDK](#using-agents-via-sdk)
    - [List Available Agents](#list-available-agents)
    - [Switch Agents in Session](#switch-agents-in-session)
    - [Invoke Subagents](#invoke-subagents)
    - [Create Custom Agent Sessions](#create-custom-agent-sessions)
    - [Monitor Agent Activity](#monitor-agent-activity)
  - [Agent Patterns](#agent-patterns)
    - [Pattern 1: Specialized Workflows](#pattern-1-specialized-workflows)
    - [Pattern 2: Cost Optimization](#pattern-2-cost-optimization)
    - [Pattern 3: Quality Gates](#pattern-3-quality-gates)
    - [Pattern 4: Hierarchical Delegation](#pattern-4-hierarchical-delegation)
    - [Pattern 5: Agent Pool](#pattern-5-agent-pool)
  - [Agent vs Tool Distinction](#agent-vs-tool-distinction)
  - [Security Considerations](#security-considerations)
  - [Performance Optimization](#performance-optimization)
  - [Troubleshooting Agents](#troubleshooting-agents)
  - [Best Practices](#best-practices)
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

## Events Deep Dive

Events provide **real-time notifications** about what's happening in OpenCode. They enable you to build reactive applications that respond to changes as they occur.

### What Are Events?

OpenCode uses **Server-Sent Events (SSE)** to push notifications from the server to connected clients. Events are emitted for:

- Session state changes (started, completed, aborted)
- File modifications (created, edited, deleted)
- Tool executions (bash commands, file operations)
- Message sending/receiving
- Agent activities (sub-sessions, forks)
- System events (server connected, errors)

**Example event flow**:
```
Server → Client: {"type":"session.started","sessionID":"abc123"}
Server → Client: {"type":"message.started","messageID":"msg1"}
Server → Client: {"type":"tool.executed","tool":"bash","output":"..."}
Server → Client: {"type":"message.completed","messageID":"msg1"}
```

### Why Use Events?

Events enable **reactive programming** patterns:

✅ **Real-time UI updates** - Show live progress in your app
✅ **Logging & auditing** - Track everything that happens
✅ **Analytics** - Gather metrics on usage patterns
✅ **Notifications** - Alert users when tasks complete
✅ **Integration** - Connect OpenCode to external systems
✅ **Debugging** - See exactly what OpenCode is doing

Without events, you'd have to constantly poll the server to check status.

---

## Event Architecture

OpenCode uses **SSE (Server-Sent Events)** - a standard for streaming text-based events over HTTP.

### Connection Flow

```
1. Client connects to /event endpoint
2. Server sends initial "server.connected" event
3. Subsequent events stream continuously
4. Connection stays open until client disconnects
5. Auto-reconnects if connection drops
```

### Event Structure

Every event is a JSON object with:

```typescript
interface Event {
  type: string;           // Event type (e.g., "session.started")
  timestamp: string;      // ISO 8601 timestamp
  sessionID?: string;     // Session ID (if session-related)
  messageID?: string;     // Message ID (if message-related)
  properties: object;     // Event-specific data
}
```

Example:
```json
{
  "type": "tool.executed",
  "timestamp": "2025-03-13T10:30:00Z",
  "sessionID": "abc123",
  "messageID": "msg456",
  "properties": {
    "tool": "bash",
    "command": "npm test",
    "exitCode": 0,
    "duration": 5420
  }
}
```

---

## Subscribing to Events

### Basic Subscription

```typescript
import { createOpencode } from "@opencode-ai/sdk";

async function listenToEvents() {
  const { client, server } = await createOpencode();

  try {
    // Subscribe to all events
    const events = await client.event.subscribe();

    // Process events as they arrive
    for await (const event of events.stream) {
      console.log(`[${event.type}]`, event.properties);
      console.log(`  Time: ${event.timestamp}`);
      if (event.sessionID) console.log(`  Session: ${event.sessionID}`);
    }
  } finally {
    server.close();
  }
}

listenToEvents();
```

The `events.stream` is an **async iterator** - it yields events as they arrive.

### Filtering Events

You can filter by event type using string patterns (wildcards):

```typescript
// Only session-related events
const sessionEvents = await client.event.subscribe({
  query: { eventType: "session.*" }
});

// Only tool events for bash
const bashEvents = await client.event.subscribe({
  query: { eventType: "tool.executed" }
});

// Multiple types (OR logic)
const allFileEvents = await client.event.subscribe({
  query: { eventType: "file.created,file.edited,file.deleted" }
});

// Wildcard patterns
const allStartEvents = await client.event.subscribe({
  query: { eventType: "*.started" }  // session.started, message.started, etc.
});
```

---

## Event Types

OpenCode emits many event types. Here are the most useful ones:

### Session Events

#### `session.created`
Fired when a new session is created.

```typescript
{
  "type": "session.created",
  "sessionID": "abc123",
  "properties": {
    "title": "My new session"
  }
}
```

#### `session.started`
Emitted when the AI begins processing in a session.

```typescript
{
  "type": "session.started",
  "sessionID": "abc123",
  "properties": {
    "agent": "coder",
    "model": "claude-3-5-sonnet"
  }
}
```

#### `session.completed`
Fired when a session finishes successfully (or is marked complete).

```typescript
{
  "type": "session.completed",
  "sessionID": "abc123",
  "properties": {
    "messageCount": 15,
    "duration": 45200
  }
}
```

#### `session.aborted`
Emitted when a session is aborted (cancelled).

```typescript
{
  "type": "session.aborted",
  "sessionID": "abc123",
  "properties": {
    "reason": "user_requested"
  }
}
```

---

### File Events

#### `file.created`
A file was created (via `write` tool).

```typescript
{
  "type": "file.created",
  "sessionID": "abc123",
  "properties": {
    "path": "src/components/NewButton.tsx",
    "bytes": 1024
  }
}
```

#### `file.edited`
A file was modified (via `edit` tool).

```typescript
{
  "type": "file.edited",
  "sessionID": "abc123",
  "properties": {
    "path": "src/api/users.ts",
    "oldSize": 2048,
    "newSize": 2100,
    "edits": 3
  }
}
```

#### `file.deleted`
A file was removed.

```typescript
{
  "type": "file.deleted",
  "sessionID": "abc123",
  "properties": {
    "path": "src/old/Deprecated.ts"
  }
}
```

---

### Tool Events

#### `tool.executed`
A tool was executed (every tool call generates this).

```typescript
{
  "type": "tool.executed",
  "sessionID": "abc123",
  "messageID": "msg456",
  "properties": {
    "tool": "bash",
    "input": { "command": "npm test" },
    "output": "PASS 5\nFAIL 0",
    "duration": 12345
  }
}
```

Common tools: `bash`, `read`, `edit`, `write`, `grep`, `glob`, `webfetch`, `question`, etc.

---

### Message Events

#### `message.started`
AI began generating a response.

```typescript
{
  "type": "message.started",
  "sessionID": "abc123",
  "messageID": "msg789",
  "properties": {
    "model": "claude-3-5-sonnet"
  }
}
```

#### `message.completed`
AI finished generating a response.

```typescript
{
  "type": "message.completed",
  "sessionID": "abc123",
  "messageID": "msg789",
  "properties": {
    "tokens": 1250,
    "duration": 8900,
    "toolCalls": 3  // Number of tools used
  }
}
```

#### `message.stream`
Streaming chunk of AI response (when using streaming mode).

```typescript
{
  "type": "message.stream",
  "sessionID": "abc123",
  "messageID": "msg789",
  "properties": {
    "text": "Here's",  // Partial text
    "done": false
  }
}
```

---

### Agent Events

#### `agent.created`
A new agent/sub-agent was spawned.

```typescript
{
  "type": "agent.created",
  "sessionID": "parent123",
  "properties": {
    "agentID": "child-agent-1",
    "agent": "coder",
    "parent": "parent123"
  }
}
```

#### `agent.completed`
An agent finished its task.

```typescript
{
  "type": "agent.completed",
  "agentID": "child-agent-1",
  "properties": {
    "messageCount": 8,
    "duration": 32000
  }
}
```

---

### System Events

#### `server.connected`
First event sent on connection - indicates server is ready.

```typescript
{
  "type": "server.connected",
  "properties": {
    "version": "0.123.0",
    "features": ["sse", "experimental_lsp"]
  }
}
```

#### `error`
An error occurred on the server.

```typescript
{
  "type": "error",
  "sessionID": "abc123",
  "properties": {
    "code": "TOOL_EXECUTION_FAILED",
    "message": "Bash command failed: exit code 1",
    "details": {
      "tool": "bash",
      "command": "npm install"
    }
  }
}
```

#### `permission.required`
A tool requires user permission (when tool permission is set to `ask`).

```typescript
{
  "type": "permission.required",
  "sessionID": "abc123",
  "messageID": "msg456",
  "properties": {
    "tool": "bash",
    "input": { "command": "rm -rf dist" },
    "permissionID": "perm-789"
  }
}
```

You can respond to this via the TUI or programmatically:

```typescript
await client.postSessionByIdPermissionsByPermissionId({
  path: { id: sessionID, permissionId: "perm-789" },
  body: {
    response: "allow",  // or "deny"
    remember: true      // Remember for this session
  }
});
```

---

## Filtering Events

The `/event` endpoint accepts a query parameter `eventType` that supports:

- **Single type**: `eventType=tool.executed`
- **Multiple types**: `eventType=session.started,session.completed`
- **Wildcards**: `eventType=session.*` (all session events)
- **Prefix wildcards**: `eventType=*.started` (all "started" events)

Example SDK usage:

```typescript
// Only error events
const errors = await client.event.subscribe({
  query: { eventType: "error" }
});

// All session events
const sessionActivity = await client.event.subscribe({
  query: { eventType: "session.*" }
});

// File modifications
const fileChanges = await client.event.subscribe({
  query: { eventType: "file.created,file.edited,file.deleted" }
});

// Everything except errors
const nonErrors = await client.event.subscribe({
  query: { eventType: "*-error" }  // Not supported, use client-side filter instead
});
```

---

## Async vs Sync Consumption

### Async Iterator (Recommended)

```typescript
const events = await client.event.subscribe();

for await (const event of events.stream) {
  // Process each event as it arrives
  handleEvent(event);
}
```

This **blocks** until events arrive. Perfect for:
- Dedicated event listeners
- Long-running daemons
- Real-time processing

### Non-blocking with Promise.race

```typescript
const events = await client.event.subscribe();
const eventsPromise = events.stream[Symbol.asyncIterator]();

// Do other work while waiting for events
while (true) {
  const { done, value: event } = await Promise.race([
    eventsPromise.next(),
    someOtherAsyncTask()
  ]);
  
  if (done) break;
  console.log("Event:", event);
}
```

### Collect into Array (Batch Processing)

```typescript
const events = await client.event.subscribe();
const collected: Event[] = [];

// Collect 100 events then stop
for await (const event of events.stream) {
  collected.push(event);
  if (collected.length >= 100) break;
}

console.log(`Collected ${collected.length} events`);
// Process batch...
```

---

## Event-Driven Patterns

### Pattern 1: Progress Indicator

Show real-time progress to users:

```typescript
async function runWithProgress(sessionId: string) {
  const events = await client.event.subscribe({
    query: { eventType: "*.started,*.completed,*.executed" }
  });

  let toolCount = 0;
  let messageCount = 0;

  for await (const event of events.stream) {
    switch (event.type) {
      case 'tool.executed':
        toolCount++;
        console.log(`✓ Tool #${toolCount} completed: ${event.properties.tool}`);
        break;
      case 'message.completed':
        messageCount++;
        console.log(`✓ Message #${messageCount} done (${event.properties.tokens} tokens)`);
        break;
      case 'session.completed':
        console.log(`\n✅ Session complete!`);
        return;
    }
  }
}
```

---

### Pattern 2: Auto-Logging

Log all activity to a file or database:

```typescript
import fs from 'fs';
import { createOpencode } from "@opencode-ai/sdk";

class EventLogger {
  private logFile: fs.WriteStream;
  
  constructor(logPath = 'opencode-events.log') {
    this.logFile = fs.createWriteStream(logPath, { flags: 'a' });
  }
  
  async start(sessionId?: string) {
    const { client } = await createOpencode();
    
    const filter = sessionId 
      ? { eventType: '*', sessionID: sessionId }
      : { eventType: '*' };
    
    const events = await client.event.subscribe({ query: filter });
    
    for await (const event of events.stream) {
      this.logFile.write(JSON.stringify(event) + '\n');
      this.logFile.flush();
    }
  }
  
  stop() {
    this.logFile.close();
  }
}

// Usage: node logger.js > events.jsonl
const logger = new EventLogger();
logger.start().catch(console.error);
```

This creates a **JSONL** (JSON Lines) log file - one event per line.

---

### Pattern 3: Notification System

Send notifications when important events occur:

```typescript
import { Notification } from 'node-notifier';

async function notifyOnCompletion() {
  const { client } = await createOpencode();
  const notifier = new Notification();
  
  const events = await client.event.subscribe({
    query: { eventType: 'session.completed,error' }
  });
  
  for await (const event of events.stream) {
    if (event.type === 'session.completed') {
      notifier.notify({
        title: 'OpenCode',
        message: 'Session finished successfully!',
        sound: true
      });
    } else if (event.type === 'error') {
      notifier.notify({
        title: 'OpenCode Error',
        message: event.properties.message,
        sound: 'Basso'
      });
    }
  }
}
```

---

### Pattern 4: Session Sync (Multi-Client)

Keep multiple clients in sync:

```typescript
class SessionSync {
  private sessions: Map<string, any> = new Map();
  
  async sync(sessionId: string) {
    const { client } = await createOpencode();
    
    const events = await client.event.subscribe({
      query: { 
        eventType: 'session.*,message.*,file.*',
        sessionID: sessionId
      }
    });
    
    for await (const event of events.stream) {
      this.updateState(event);
      this.broadcastToOtherClients(event);
    }
  }
  
  private updateState(event: Event) {
    const { sessionID } = event;
    if (!sessionID) return;
    
    if (!this.sessions.has(sessionID)) {
      this.sessions.set(sessionID, { 
        id: sessionID, 
        messages: [], 
        filesChanged: [] 
      });
    }
    
    const session = this.sessions.get(sessionID)!;
    
    switch (event.type) {
      case 'message.completed':
        session.messages.push(event.properties);
        break;
      case 'file.edited':
      case 'file.created':
      case 'file.deleted':
        session.filesChanged.push(event.properties);
        break;
    }
  }
}
```

---

### Pattern 5: Metrics & Analytics

Track usage metrics:

```typescript
interface Metrics {
  sessionCount: number;
  totalMessages: number;
  totalToolCalls: Map<string, number>;
  totalDuration: number;
  errors: number;
}

async function collectMetrics(durationMs: number): Promise<Metrics> {
  const { client } = await createOpencode();
  
  const metrics: Metrics = {
    sessionCount: 0,
    totalMessages: 0,
    totalToolCalls: new Map(),
    totalDuration: 0,
    errors: 0
  };
  
  const events = await client.event.subscribe({
    query: { eventType: 'session.*,message.*,error' }
  });
  
  // Run for specified duration
  const timeout = setTimeout(() => {}, durationMs);
  
  for await (const event of events.stream) {
    switch (event.type) {
      case 'session.created':
        metrics.sessionCount++;
        break;
      case 'message.completed':
        metrics.totalMessages++;
        metrics.totalDuration += event.properties.duration || 0;
        break;
      case 'tool.executed':
        const tool = event.properties.tool;
        metrics.totalToolCalls.set(tool, (metrics.totalToolCalls.get(tool) || 0) + 1);
        break;
      case 'error':
        metrics.errors++;
        break;
    }
  }
  
  return metrics;
}

// Usage
const metrics = await collectMetrics(60000); // Collect for 60 seconds
console.log('Metrics:', metrics);
```

---

## Troubleshooting Events

### Problem: No events received

**Solution**:
- Verify server is running: `curl http://localhost:4096/global/health`
- Check that SSE is supported (all modern servers support it)
- Ensure you're reading from `events.stream` async iterator
- Look for connection errors in try-catch

### Problem: Events stop after some time

**Solution**:
- SSE connections can time out (server or proxy)
- Implement auto-reconnect
- Check server logs for crashes
- Verify network stability

### Problem: Missing expected events

**Solution**:
- Check your `eventType` filter - may be too restrictive
- Some events may be disabled by configuration
- Verify the action actually generates events (check server logs)
- Try `eventType="*"` to receive all events temporarily

### Problem: High memory usage

**Solution**:
- Don't accumulate events in arrays
- Process events immediately or in small batches
- Use stream processing patterns
- Consider if you need all events or can filter

---

## Common Questions

### Q: What's the difference between events and polling?

**Answer**:
- **Events**: Server pushes updates to you as they happen (real-time)
- **Polling**: You repeatedly ask the server for updates (inefficient)

Events are more efficient and provide lower latency.

---

### Q: Can I subscribe to events before they happen?

**Answer**: Yes! Events are real-time - you subscribe and receive them as they occur. You cannot get past events (unless the server stores them and you query separately with `client.session.list()`, etc.).

---

### Q: Are events guaranteed to be delivered?

**Answer**: No. SSE is fire-and-forget over HTTP. If the connection drops, events are lost. For guaranteed delivery, implement:
- Client-side persistence
- Server-side event storage (not built-in)
- Acknowledgment and replay mechanism

---

### Q: Can I use events in the browser?

**Answer**: The SDK is Node.js-only (requires localhost server). For browser use, you'd need:
1. CORS enabled on server (`OPENCODE_SERVER_CORS=*`)
2. Direct HTTP connection to SSE endpoint
3. Or a WebSocket proxy (not officially supported)

---

### Q: How many concurrent event subscriptions can I have?

**Answer**: Limited by:
- Server resources (file descriptors)
- Network capacity
- Each subscription is a separate SSE connection
- A single server can likely handle 100-1000 concurrent subscriptions

---

### Q: Do events include full data or just references?

**Answer**: Events include relevant data inline (sessionID, file paths, command outputs). For large data (file contents, long outputs), the event contains metadata and you'd use other API calls to fetch full details.

---

### Q: Are there rate limits on events?

**Answer**: No explicit rate limits, but:
- High-frequency events can flood clients
- Server can handle thousands of events/sec
- Implement downstream rate limiting if needed for your use case

---

### Q: Can I filter events by custom properties?

**Answer**: Currently filtering is limited to `eventType` and `sessionID`. For complex filtering, subscribe to all events and filter client-side:

```typescript
for await (const event of events.stream) {
  if (event.properties.duration > 5000) {
    // Only process long-running tools
    await handleSlowTool(event);
  }
}
```

---

### Q: How do I know when an AI response is complete?

**Answer**: Listen for `message.completed` event:

```typescript
for await (const event of events.stream) {
  if (event.type === 'message.completed' && 
      event.messageID === targetMessageID) {
    console.log('AI response ready!');
  }
}
```

---

### Q: Can I get events from multiple sessions?

**Answer**: Yes, subscribe without filters:

```typescript
const allEvents = await client.event.subscribe();
// You'll get events from ALL sessions
```

Or filter by specific sessions:

```typescript
const session1Events = await client.event.subscribe({
  query: { sessionID: 'session-1,session-2' }
});
```

---

## Summary

**Events enable real-time, reactive applications**:
- Subscribe with `client.event.subscribe()`
- Use async iteration: `for await (const event of stream)`
- Filter by `eventType` and `sessionID`
- Handle `error` events gracefully
- Implement reconnection for resilience
- Use for logging, notifications, progress, analytics

**Common use cases**:
- Live progress bars in CI/CD integrations
- Audit trails for compliance
- Multi-user collaborative editing
- Integration with external monitoring systems
- Building custom UIs that reflect OpenCode state

**Next**: Combine events with [sessions](#sessions-deep-dive) and [tools](#tools-deep-dive) to build rich, interactive OpenCode applications!

---

## Files Deep Dive

File operations are fundamental to OpenCode. The AI uses file search, reading, and modification tools to understand and change your codebase. As an SDK user, you'll use the `client.find` and `client.file` APIs to programmatically explore and manipulate files.

### Understanding File Operations

OpenCode provides **five core file operations**:

| Operation | SDK API | Purpose | Example |
|-----------|---------|---------|---------|
| **find.text** | `client.find.text()` | Search file contents | Find all TODOs |
| **find.files** | `client.find.files()` | Find files by name | Locate all .ts files |
| **file.list** | `client.file.list()` | List directory contents | Browse src/components |
| **file.read** | `client.file.read()` | Read file content | View source code |
| **file.status** | `client.file.status()` | Get git status | Check which files changed |

These are exposed as **built-in tools** (`grep`, `glob`, `list`, `read`) that the AI can use automatically, and as **SDK methods** that you can call directly.

---

## SDK File API Overview

### Namespace Structure

```typescript
// Search operations
client.find.text({ query: { pattern: "..." } })
client.find.files({ query: { query: "..." } })
client.find.symbols({ query: { query: "..." } })

// File operations
client.file.read({ query: { path: "..." } })
client.file.list({ query: { path: "..." } })
client.file.status({ query: { /* ... */ } })
```

### Common Response Structure

```typescript
// find.text returns array of matches
interface TextSearchResult {
  path: string;              // File path
  line_number: number;       // Line number (1-indexed)
  lines: {
    text: string;            // Full line text
    absolute_offset: number; // Byte offset from start
  };
  submatches: Array<{        // Regex capture groups
    match: string;
    start: number;
    end: number;
  }>;
}

// find.files returns array of paths
type FileSearchResult = string[];

// file.read returns content
interface FileReadResult {
  type: "raw" | "patch";
  content: string;
}

// file.list returns directory entries
interface FileNode {
  name: string;
  path: string;
  type: "file" | "directory";
  children?: FileNode[];     // If recursively listed
}
```

---

## Searching Text (grep)

The `client.find.text()` method searches file contents using regular expressions (backed by ripgrep).

### Basic Text Search

```typescript
// Find all occurrences of "TODO"
const todos = await client.find.text({
  query: { pattern: "TODO" }
});

todos.data.forEach(match => {
  console.log(`${match.path}:${match.line_number}: ${match.lines.text}`);
});
```

Output:
```
src/utils/helpers.ts:15: // TODO: implement caching
src/components/Button.tsx:42: // TODO: add error handling
```

---

### Advanced Regex Patterns

```typescript
// 1. Find function declarations
const functions = await client.find.text({
  query: { pattern: "function\\s+\\w+\\s*\\(" }
});

// 2. Find import statements
const imports = await client.find.text({
  query: { pattern: "^import\\s+.*from\\s+['\"']" }
});

// 3. Find console.log statements
const consoleLogs = await client.find.text({
  query: { pattern: "console\\.log\\s*\\(" }
});

// 4. Find React hooks
const hooks = await client.find.text({
  query: { pattern: "use[A-Z][a-zA-Z0-9]*\\s*\\(" }
});

// 5. Find class definitions
const classes = await client.find.text({
  query: { pattern: "class\\s+\\w+" }
});

// 6. Find error handling
const tryCatch = await client.find.text({
  query: { pattern: "try\\s*\\{" }
});

// 7. Use capture groups - find all API endpoint strings
const endpoints = await client.find.text({
  query: { pattern: "['\"]/api/[a-zA-Z/]+['\"]" }
});
endpoints.data.forEach(m => {
  const endpoint = m.submatches[0]?.match || m.lines.text;
  console.log(`API endpoint found: ${endpoint}`);
});

// 8. Multi-line patterns (using (?s) flag for dotall mode)
const multiLine = await client.find.text({
  query: { pattern: "(?s)description:\\s*\"\"\".*?\"\"\"" }
});
```

---

### Filtering by File Type

The SDK supports additional filters to narrow search scope:

```typescript
// Search only in TypeScript files
const tsOnly = await client.find.text({
  query: {
    pattern: "interface\\s+\\w+",
    // Optional filter parameters:
    glob: "**/*.ts",        // Only .ts files
    // type: "file"         // Only files (not needed, default)
  }
});

// Search in specific directory
const inFolder = await client.find.text({
  query: {
    pattern: "useState",
    directory: "src/components"  // Override search root
  }
});

// Limit results (default is typically 100)
const limited = await client.find.text({
  query: {
    pattern: "FIXME",
    limit: 10  // Return at most 10 matches
  }
});

// Case-insensitive search (using regex flag)
const caseInsensitive = await client.find.text({
  query: {
    // (?i) flag makes pattern case-insensitive
    pattern: "(?i)password|secret|token"
  }
});
```

**Important**: The `glob`, `directory`, `limit` parameters are **not** part of the pattern but are separate query parameters in some SDK implementations. Check your SDK version's exact API - some use separate fields:

```typescript
// Some SDK versions use this format:
await client.find.text({
  query: { pattern: "..." },
  glob: "**/*.ts",        // Separate field
  limit: 100              // Separate field
});
```

---

### Performance Optimization

Large codebases can have thousands of matches. Optimize searches:

```typescript
// 1. Be as specific as possible
const bad = await client.find.text({ query: { pattern: "\\w+" } });  // Too broad!
const good = await client.find.text({ 
  query: { pattern: "TODO|FIXME|XXX" }  // Specific markers
});

// 2. Use appropriate regex - avoid backtracking catastrophes
const dangerous = await client.find.text({
  query: { pattern: "(a+)+b" }  // Can be extremely slow on certain inputs!
});
const safe = await client.find.text({
  query: { pattern: "a+b" }  // Linear time
});

// 3. Filter by file type first, then search within those files
const tsConfigs = await client.find.files({
  query: { query: "**/tsconfig.json", type: "file", limit: 50 }
});
// Then read and search those specific files if needed

// 4. Search in specific directories instead of entire project
const srcOnly = await client.find.text({
  query: {
    pattern: "React\\.FC<",
    directory: "src"  // Only search in src/
  }
});

// 5. Exclude known large/irrelevant directories using .ignore file
// Create a .opencode/.ignore file:
/*
  node_modules/
  dist/
  build/
  .git/
  coverage/
*/

// 6. Paginate through results if SDK supports it
let allResults: TextSearchResult[] = [];
let offset = 0;
const pageSize = 100;

while (true) {
  const page = await client.find.text({
    query: { pattern: "import", limit: pageSize, offset }
  });
  
  if (page.data.length === 0) break;
  
  allResults.push(...page.data);
  offset += pageSize;
  
  // Optional: break early if you found what you need
  if (page.data.some(m => m.path.includes("critical"))) {
    break;
  }
}

console.log(`Found ${allResults.length} total matches`);

// 7. Cache results for repeated searches
const searchCache = new Map<string, TextSearchResult[]>();

async function cachedSearch(pattern: string): Promise<TextSearchResult[]> {
  if (!searchCache.has(pattern)) {
    const results = await client.find.text({ query: { pattern } });
    searchCache.set(pattern, results.data);
  }
  return searchCache.get(pattern)!;
}
```

---

## Finding Files (glob)

Search for files and directories by name using glob patterns.

### Glob Pattern Basics

Glob patterns use wildcards:

| Pattern | Matches | Example |
|---------|---------|---------|
| `*` | Any characters except `/` | `*.ts` (all .ts files) |
| `?` | Single character | `?.js` (a.js, b.js) |
| `**` | Any number of directories | `**/*.test.ts` (any test file) |
| `{a,b}` | Either pattern | `*.{ts,tsx,js}` |
| `[abc]` | Character class | `file[123].txt` |
| `[!abc]` | Negated character class | `file[!1].txt` |

---

### Common Patterns

```typescript
// Find all TypeScript files
const allTs = await client.find.files({
  query: { query: "**/*.ts", type: "file" }
});

// Find all React components (.tsx, .jsx)
const reactFiles = await client.find.files({
  query: { query: "**/*.{tsx,jsx}", type: "file" }
});

// Find test files (any extension)
const testFiles = await client.find.files({
  query: { query: "**/*.test.{ts,js,tsx,jsx}", type: "file" }
});

// Find configuration files
const configFiles = await client.find.files({
  query: { query: "**/*config.{js,ts,json,yaml,yml}", type: "file" }
});

// Find all Markdown documentation
const docs = await client.find.files({
  query: { query: "**/*.md", type: "file" }
});

// Find directories named "src"
const srcDirs = await client.find.files({
  query: { query: "**/src", type: "directory" }
});

// Find package.json files
const packages = await client.find.files({
  query: { query: "**/package.json", type: "file" }
});

// Find files starting with "test"
const testPrefixed = await client.find.files({
  query: { query: "**/test*", type: "file" }
});

// Find files with exactly one character before extension
const oneChar = await client.find.files({
  query: { query: "**/?.{js,ts}", type: "file" }
});

// Exclude patterns (use negative lookahead in glob)
// Note: Glob doesn't support !, so use client-side filtering
const allFiles = await client.find.files({
  query: { query: "**/*", type: "file" }
});
const notNodeModules = allFiles.data.filter(f => !f.includes("node_modules"));
```

---

### Combining with Filters

```typescript
// Find files in specific directory
const inSrc = await client.find.files({
  query: {
    query: "**/*.ts",
    directory: "src",  // Search relative to src/
    type: "file"
  }
});

// Limit depth
const shallow = await client.find.files({
  query: {
    // Only one level deep (no **)
    query: "src/*.ts",
    type: "file"
  }
});

// Find both files AND directories
const everything = await client.find.files({
  query: { query: "**/components", type: undefined }  // Both
});

// Use .gitignore by default (ripgrep respects it)
// To include ignored files, use .ignore file (see tools section)

// Sort by modification time (already sorted by ripgrep)
const sorted = await client.find.files({
  query: {
    query: "**/*.ts",
    type: "file",
    limit: 50  // Get most recently modified 50 files
  }
});
// Results are sorted by mtime (most recent first)
```

---

## Listing Directories (list)

List contents of a directory (like `ls` or `dir`).

### Basic Directory Listing

```typescript
// List project root
const root = await client.file.list({
  query: { path: "." }
});
root.data.forEach(entry => {
  console.log(`${entry.type === 'directory' ? '📁' : '📄'} ${entry.name}`);
});

// List specific directory
const src = await client.file.list({
  query: { path: "src" }
});

// List with pattern filter
const tsFiles = await client.file.list({
  query: {
    path: "src",
    pattern: "*.ts"  // Only .ts files
  }
});
```

---

### Recursive Listings

```typescript
// Recursive listing (all subdirectories)
const all = await client.file.list({
  query: {
    path: "src",
    // No pattern - get everything
  }
});

// Format recursively
function printTree(nodes: FileNode[], indent = 0) {
  nodes.forEach(node => {
    const icon = node.type === 'directory' ? '📁' : '📄';
    const prefix = '  '.repeat(indent);
    console.log(`${prefix}${icon} ${node.name}`);
    
    if (node.children) {
      printTree(node.children, indent + 1);
    }
  });
}

printTree(all.data);
```

Output:
```
📁 src
  📁 components
    📄 Button.tsx
    📄 Modal.tsx
  📁 utils
    📄 helpers.ts
    📄 api.ts
📁 tests
📄 package.json
📄 README.md
```

---

### Filtering Results

```typescript
// Filter for files only (exclude directories)
const filesOnly = (await client.file.list({
  query: { path: "." }
})).data.filter(entry => entry.type === 'file');

// Filter by extension
const jsFiles = (await client.file.list({
  query: { path: ".", pattern: "*.js" }
})).data;

// Find directories
const dirs = (await client.file.list({
  query: { path: ".", pattern: "*" }
})).data.filter(entry => entry.type === 'directory');

// Count file types
const entries = await client.file.list({ query: { path: "src" } });
const counts: Record<string, number> = {};
entries.data.forEach(entry => {
  const ext = entry.name.split('.').pop() || 'noext';
  counts[ext] = (counts[ext] || 0) + 1;
});
console.log('File type distribution:', counts);
```

---

## Reading Files (read)

Read file contents for analysis, display, or processing.

### Complete File Read

```typescript
// Read entire file as string
const pkg = await client.file.read({
  query: { path: "package.json" }
});
console.log(pkg.data.content);

// Parse as JSON
const pkgJson = JSON.parse(pkg.data.content);
console.log("Project name:", pkgJson.name);
console.log("Version:", pkgJson.version);
console.log("Dependencies:", Object.keys(pkgJson.dependencies || {}));
```

---

### Partial Reads (Line Ranges)

Large files can be partially read using line ranges:

```typescript
// Read lines 1-10 (first page)
const firstPage = await client.file.read({
  query: {
    path: "src/huge-file.ts",
    // Some SDKs support:
    // startLine: 1,
    // endLine: 10
  }
});

// Read specific section (e.g., line 100-150)
const middle = await client.file.read({
  query: {
    path: "src/index.ts",
    // offset: 100,   // Start at byte offset
    // limit: 50       // Read 50 lines
  }
});

// Read last N lines
// Implementation: first get file length, then read from end
const full = await client.file.read({ query: { path: "logs/app.log" } });
const lines = full.data.content.split('\n');
const last50 = lines.slice(-50).join('\n');
console.log("Recent log entries:\n", last50);
```

**Note**: Partial read support depends on SDK implementation. Check your SDK's API - some may use `offset` and `limit` parameters instead of line numbers.

---

### Handling Different Encodings

By default, files are read as UTF-8. For other encodings:

```typescript
// Most SDKs assume UTF-8
// If you need binary data or other encodings, check SDK options:

const binary = await client.file.read({
  query: {
    path: "image.png",
    // encoding: "base64"  // Some SDKs support this
  }
});
// binary.data.content would be base64 string

// For UTF-16 or other encodings, you may need to convert:
const raw = await client.file.read({ query: { path: "file.txt" } });
const buffer = Buffer.from(raw.data.content, 'utf8');  // Convert if needed
```

---

## File Status & Git Integration

Check which files have changed (git status):

```typescript
// Get git status for all tracked files
const status = await client.file.status({
  query: {}  // No filter - all files
});

status.data.forEach(file => {
  const statusIcon = {
    'M': '✏️Modified',
    'A': '➕Added',
    'D': '🗑️Deleted',
    'R': '🔄Renamed',
    'C': '📋Copied',
    'U': '⚠️Unmerged',
    '?': '❓Untracked'
  }[file.status] || '';

  console.log(`${statusIcon} ${file.path}`);
  if (file.oldPath) {
    console.log(`   → renamed from: ${file.oldPath}`);
  }
});

// Filter by status
const modifiedOnly = status.data.filter(f => f.status === 'M');
const untracked = status.data.filter(f => f.status === '?');

// Check if there are uncommitted changes
const hasChanges = status.data.some(f => f.status !== '');
if (hasChanges) {
  console.log("⚠️ You have uncommitted changes!");
}

// Get staged vs unstaged (if SDK provides this)
const staged = status.data.filter(f => f.staged);
const unstaged = status.data.filter(f => !f.staged);
```

---

## Symbol Search

Find **workspace symbols** - functions, classes, variables, etc. across your entire codebase:

```typescript
// Search for a symbol by name (fuzzy match)
const symbols = await client.find.symbols({
  query: { query: "useState" }
});

symbols.data.forEach(symbol => {
  console.log(`${symbol.kind}: ${symbol.name}`);
  console.log(`  Location: ${symbol.path}:${symbol.range.start.line}`);
  console.log(`  Type: ${symbol.type}`);
});

// Example output:
// Function: useState
//   Location: src/hooks/useCustomState.ts:15
//   Type: () => [any, Function]
// Function: useUserState
//   Location: src/features/user/hooks.ts:8
//   Type: () => [User | null, Function]
```

**Symbol kinds** you might see:
- `function` - Function definitions
- `class` - Class definitions
- `interface` - TypeScript interfaces
- `type` - Type aliases
- `variable` - Variables/constants
- `method` - Class methods
- `property` - Object properties
- `enum` - Enumerations
- `namespace` - Namespaces/modules

---

## Working with File Paths

### Absolute vs Relative Paths

```typescript
// Read with absolute path
const absolute = await client.file.read({
  query: { path: "/home/user/project/src/index.ts" }
});

// Read with relative path (relative to project root)
const relative = await client.file.read({
  query: { path: "src/index.ts" }
});

// Get current project root
const project = await client.project.current();
console.log("Project root:", project.data.root);

// Construct paths relative to project root
const someFile = path.join(project.data.root, "src", "utils.ts");
```

### Normalizing Paths

```typescript
import path from 'path';

// Resolve to absolute
const resolved = path.resolve("src/utils");  // /abs/path/to/src/utils

// Get directory name
const dir = path.dirname("src/components/Button.tsx");  // "src/components"

// Get extension
const ext = path.extname("file.ts");  // ".ts"

// Get basename
const base = path.basename("src/components/Button.tsx");  // "Button.tsx"

// Join paths
const fullPath = path.join("src", "components", "Button.tsx");  // "src/components/Button.tsx"
```

---

## File Operation Patterns

### Pattern 1: Find All References

Find where a function/variable is used:

```typescript
async function findReferences(symbolName: string): Promise<{path: string, line: number}[]> {
  // Search for exact word boundaries
  const pattern = `\\b${symbolName}\\b`;
  const results = await client.find.text({
    query: { 
      pattern,
      // Exclude definition line if already known
    }
  });
  
  return results.data.map(r => ({
    path: r.path,
    line: r.line_number,
    context: r.lines.text.trim()
  }));
}

// Usage
const refs = await findReferences("renderUserList");
console.log(`Found ${refs.length} references`);
refs.forEach(r => console.log(`${r.path}:${r.line} - ${r.context}`));
```

---

### Pattern 2: File Content Analysis

Calculate code metrics:

```typescript
async function analyzeCodebase() {
  const files = await client.find.files({
    query: { query: "**/*.{ts,tsx,js,jsx}", type: "file" }
  });
  
  let totalLines = 0;
  let totalFiles = 0;
  const byExtension: Record<string, {files: number, lines: number}> = {};
  
  for (const filePath of files.data) {
    try {
      const content = await client.file.read({ query: { path: filePath } });
      const lines = content.data.content.split('\n').length;
      const ext = filePath.split('.').pop() || 'unknown';
      
      totalLines += lines;
      totalFiles++;
      
      if (!byExtension[ext]) {
        byExtension[ext] = { files: 0, lines: 0 };
      }
      byExtension[ext].files++;
      byExtension[ext].lines += lines;
    } catch (error) {
      console.warn(`Could not read ${filePath}:`, error);
    }
  }
  
  console.log(`📊 Codebase Analysis:`);
  console.log(`   Total files: ${totalFiles}`);
  console.log(`   Total lines: ${totalLines}`);
  console.log(`   By extension:`);
  Object.entries(byExtension).forEach(([ext, stats]) => {
    console.log(`     .${ext}: ${stats.files} files, ${stats.lines} lines`);
  });
}

await analyzeCodebase();
```

---

### Pattern 3: Duplicate Code Detection

Find similar code blocks:

```typescript
async function findDuplicateFunctions() {
  // Step 1: Find all function declarations
  const funcMatches = await client.find.text({
    query: { pattern: "function\\s+(\\w+)\\s*\\(" }
  });
  
  // Step 2: Group by function name
  const functionsBySize = new Map<string, Array<{path: string, line: number, content: string}>>();
  
  for (const match of funcMatches.data) {
    const funcName = match.submatches[0]?.match || "unknown";
    
    // Read the function body (simplified - read entire file and extract)
    const file = await client.file.read({ query: { path: match.path } });
    const lines = file.data.content.split('\n');
    const funcLines = lines.slice(match.line_number - 1, match.line_number + 20).join('\n');
    
    if (!functionsBySize.has(funcName)) {
      functionsBySize.set(funcName, []);
    }
    functionsBySize.get(funcName)!.push({
      path: match.path,
      line: match.line_number,
      content: funcLines
    });
  }
  
  // Step 3: Find functions with multiple implementations
  for (const [name, occurrences] of functionsBySize.entries()) {
    if (occurrences.length > 1) {
      console.log(`⚠️ Duplicate function: ${name} (${occurrences.length} times)`);
      occurrences.forEach(o => {
        console.log(`   ${o.path}:${o.line}`);
      });
    }
  }
}

await findDuplicateFunctions();
```

---

### Pattern 4: Dependency Analysis

Find all imports/external dependencies:

```typescript
async function analyzeDependencies() {
  const importMatches = await client.find.text({
    query: { pattern: "^import\\s+.*from\\s+['\"']([^'\"]+)['\"']" }
  });
  
  const dependencies = new Set<string>();
  
  importMatches.data.forEach(match => {
    const module = match.submatches[0]?.match || '';
    if (module && !module.startsWith('.') && !module.startsWith('/')) {
      // External package (not relative import)
      const pkg = module.split('/')[0].split('@')[0];
      dependencies.add(pkg);
    }
  });
  
  console.log('📦 External dependencies used:');
  Array.from(dependencies).sort().forEach(dep => {
    console.log(`  - ${dep}`);
  });
  
  return Array.from(dependencies);
}

const deps = await analyzeDependencies();
```

---

### Pattern 5: Codebase Search with Context

Search and display surrounding context:

```typescript
async function searchWithContext(pattern: string, contextLines = 2) {
  const results = await client.find.text({
    query: { pattern, limit: 20 }
  });
  
  for (const match of results.data) {
    // Read the file to get context
    const file = await client.file.read({ query: { path: match.path } });
    const allLines = file.data.content.split('\n');
    
    const start = Math.max(0, match.line_number - 1 - contextLines);
    const end = Math.min(allLines.length, match.line_number + contextLines);
    
    console.log(`\n📄 ${match.path}:${match.line_number}`);
    console.log('─'.repeat(50));
    
    for (let i = start; i < end; i++) {
      const lineNum = i + 1;
      const prefix = lineNum === match.line_number ? '>>>' : '   ';
      console.log(`${prefix} ${lineNum.toString().padStart(4)}: ${allLines[i]}`);
    }
  }
}

// Usage
await searchWithContext("useEffect", 3);
```

Output:
```
📄 src/components/App.tsx:42
---------------------------------------------------
   39:   const [count, setCount] = useState(0);
>>> 42:   useEffect(() => {
   43:     console.log('Effect ran');
   44:   }, []);
   45:
```

---

## Security Considerations

### Path Traversal Prevention

Never trust user-provided paths:

```typescript
function sanitizePath(userInput: string): string {
  // Remove any .. sequences
  const cleaned = userInput.replace(/\.\./g, '');
  // Remove leading slashes
  const relative = cleaned.replace(/^\//, '');
  return relative;
}

// Use
const userPath = sanitizePath("../../../etc/passwd");
await client.file.read({ query: { path: userPath } });
```

### Avoid Reading Sensitive Files

```typescript
const sensitivePatterns = [
  /\.env$/,
  /config\.(json|yaml|yml)$/,
  /secret/i,
  /credential/i,
  /password\./i,
  /key\./i
];

async function safeRead(path: string): Promise<string | null> {
  if (sensitivePatterns.some(pattern => pattern.test(path))) {
    console.warn(`Blocked reading potentially sensitive file: ${path}`);
    return null;
  }
  
  const result = await client.file.read({ query: { path } });
  return result.data.content;
}
```

### Limit Search Scope

Don't let users search entire filesystem:

```typescript
const ALLOWED_DIRS = ['src', 'lib', 'app', 'components'];

function isPathAllowed(path: string): boolean {
  return ALLOWED_DIRS.some(dir => path.startsWith(dir + '/') || path === dir);
}

// Validate before operations
if (!isPathAllowed(userPath)) {
  throw new Error(`Access to ${userPath} is not allowed`);
}
```

---

## Performance Tips

### 1. Batch Operations

When processing many files, don't make thousands of sequential calls:

```typescript
// BAD: One at a time (slow)
for (const file of files) {
  const content = await client.file.read({ query: { path: file } });
  // process...
}

// GOOD: Use Promise.all for independent reads
const promises = files.map(f => client.file.read({ query: { path: f } }));
const results = await Promise.all(promises);

// Note: Too many at once can overwhelm server. Chunk if needed:
async function batchFetch<T>(
  items: T[],
  fn: (item: T) => Promise<any>,
  batchSize = 10
) {
  const results = [];
  for (let i = 0; i < items.length; i += batchSize) {
    const batch = items.slice(i, i + batchSize);
    const batchResults = await Promise.all(batch.map(fn));
    results.push(...batchResults);
  }
  return results;
}
```

### 2. Cache Results

```typescript
const fileCache = new Map<string, FileReadResult>();

async function getFileCached(path: string): Promise<string> {
  if (!fileCache.has(path)) {
    const result = await client.file.read({ query: { path } });
    fileCache.set(path, result);
  }
  return fileCache.get(path)!.data.content;
}
```

### 3. Filter Early

Filter using the most restrictive criteria first:

```typescript
// BAD: Read all files then filter
const allFiles = await client.find.files({ query: { query: "**/*" } });
const tsFiles = allFiles.data.filter(f => f.endsWith('.ts'));

// GOOD: Use glob to only get .ts files
const tsFiles = await client.find.files({
  query: { query: "**/*.ts", type: "file" }
});

// Even better: narrow directory
const srcTsFiles = await client.find.files({
  query: { query: "src/**/*.ts", type: "file" }
});
```

### 4. Avoid Repeated Status Checks

```typescript
// BAD: Check status repeatedly
for (const file of files) {
  const status = await client.file.status({ query: { path: file } });
  // ...
}

// GOOD: Get all status once
const allStatus = await client.file.status({ query: {} });
const statusMap = new Map(allStatus.data.map(s => [s.path, s]));
// Then lookup for each file
```

---

## Troubleshooting File Operations

### Problem: "File not found" errors

**Solution**:
- Check file path is correct (relative to project root)
- Verify file exists with `client.find.files()` first
- Check permissions (file might be in .gitignore and excluded)
- Ensure you're in the right project directory

```typescript
// Verify file exists
const exists = (await client.find.files({
  query: { query: path, type: "file" }
})).data.includes(path);

if (!exists) {
  console.error(`File not found: ${path}`);
  // List directory to debug
  const dir = path.substring(0, path.lastIndexOf('/'));
  const listing = await client.file.list({ query: { path: dir } });
  console.log(`Contents of ${dir}:`, listing.data.map(e => e.name));
}
```

---

### Problem: Search returns no results

**Solution**:
- Verify pattern syntax (use regex101.com to test)
- Check if files are excluded by .gitignore or .ignore
- Try broader pattern first
- Test with `client.file.list()` to confirm files exist

```typescript
// Debug: What files are actually searchable?
const allFiles = await client.find.files({ query: { query: "**/*", type: "file", limit: 100 } });
console.log(`Found ${allFiles.data.length} files to search`);

// Test simple pattern
const test = await client.find.text({ query: { pattern: "the" } });
console.log(`'the' appears ${test.data.length} times`);
```

---

### Problem: Slow searches

**Solution**:
- Add more specific patterns
- Use `directory` to limit scope
- Add `limit` to stop after finding enough
- Use .ignore to exclude large directories

---

### Problem: Out of memory with large files

**Solution**:
- Use partial reads (read specific sections)
- Stream processing for large results
- Increase Node.js memory limit if needed

```bash
node --max-old-space-size=4096 your-script.js
```

---

### Problem: Encoding errors (garbled text)

**Solution**:
- Files might be in non-UTF-8 encoding
- Check for BOM (Byte Order Mark)
- Use encoding-aware reading if SDK supports it

```typescript
// Detect BOM
const content = await client.file.read({ query: { path: file } });
if (content.data.charCodeAt(0) === 0xFEFF) {
  console.log('File has UTF-16 BOM');
}
```

---

## Common Questions

### Q: How is `find.text()` different from `find.files()`?

**Answer**:
- `find.text()`: Searches **inside** files for text matching a regex pattern. Returns line-by-line matches with context.
- `find.files()`: Searches for **file/directory names** using glob patterns. Returns paths only.

Use `find.text()` when you want to find **content** (e.g., all TODOs). Use `find.files()` when you want to find **files by name** (e.g., all .test.ts files).

---

### Q: Can I search binary files?

**Answer**: By default, ripgrep skips binary files. Some SDKs may have a `binary: true` option to include them, but results will be limited (binary data may not match text patterns well). For binary files, use specialized tools or base64 decode first.

---

### Q: How do I exclude directories like node_modules?

**Answer**: OpenCode respects `.gitignore` by default. `node_modules/` is typically in `.gitignore` and will be excluded. To include it, create a `.opencode/.ignore` file with:
```
!node_modules/
```

Or use the glob pattern to explicitly restrict:
```typescript
await client.find.files({ query: { query: "src/**/*.ts", type: "file" } });
```

---

### Q: What's the maximum file size that can be read?

**Answer**: There's no hard limit, but very large files (>100MB) may:
- Consume significant memory
- Slow down the server
- Time out

For large files, use line range parameters if available, or read in chunks if your SDK supports partial reads.

---

### Q: Can I get file modification time?

**Answer**: The basic `file.read()` doesn't include metadata. Use `find.files()` which returns paths, then check file stats separately, or look for SDK extensions that include metadata:

```typescript
const files = await client.find.files({ query: { query: "**/*.ts", type: "file" } });
// Paths only - no mtime in current SDK

// Alternative: Use shell command
const stat = await client.session.shell({
  path: { id: sessionId },
  body: { command: `stat -c %y ${filePath}` }
});
```

---

### Q: How do I search only modified/untracked files?

**Answer**: Combine `file.status()` with search:

```typescript
// Get modified files
const status = await client.file.status({ query: {} });
const modifiedFiles = status.data
  .filter(f => f.status === 'M' || f.status === '?')
  .map(f => f.path);

// Search only in modified files
const modifiedResults: TextSearchResult[] = [];
for (const file of modifiedFiles) {
  const search = await client.find.text({
    query: { pattern: "TODO", directory: file }
  });
  modifiedResults.push(...search.data);
}
```

---

### Q: Can I search in specific file types only?

**Answer**: Yes, use `glob` parameter or filter by extension:

```typescript
// Method 1: Use directory parameter to narrow scope
const tsResults = await client.find.text({
  query: {
    pattern: "interface",
    directory: "src"  // Only in src/
  }
});

// Method 2: Use file.find.files() to get .ts files first, then search each
const tsFiles = await client.find.files({
  query: { query: "**/*.ts", type: "file" }
});

// Then read and search those files individually if needed
```

---

### Q: Is there a way to cancel a long-running search?

**Answer**: Most SDKs don't support cancellation of individual file operations. However, you can:
- Use a timeout on the entire SDK client
- Set per-operation timeout if SDK supports it
- Use AbortSignal:

```typescript
const controller = new AbortController();
setTimeout(() => controller.abort(), 10000); // 10s timeout

try {
  const results = await client.find.text({
    query: { pattern: "..." },
    signal: controller.signal
  });
} catch (error) {
  if (error.name === 'AbortError') {
    console.log('Search timed out');
  }
}
```

---

### Q: How do I handle files with spaces or special characters in path?

**Answer**: SDKs handle paths as strings. Use proper encoding:

```typescript
const weirdPath = "src/files with spaces/weird-name (1).ts";
const result = await client.file.read({ query: { path: weirdPath } });
// Works fine - path is just a string
```

Be careful when constructing paths - use `path.join()` instead of string concatenation.

---

### Q: Can I get line counts or character counts?

**Answer**: Read the file and compute:

```typescript
const file = await client.file.read({ query: { path: "src/index.ts" } });
const content = file.data.content;
const lines = content.split('\n').length;
const chars = content.length;
const bytes = Buffer.byteLength(content, 'utf8');
console.log(`Lines: ${lines}, Chars: ${chars}, Bytes: ${bytes}`);
```

---

### Q: What's the difference between `list` and `find.files()`?

**Answer**:
- `list`: Lists directory contents (like `ls`). Can be recursive. Returns `FileNode[]` with `name`, `path`, `type`.
- `find.files()`: Finds files by name pattern using glob. Returns string[] of paths.

Use `list` when exploring a specific directory. Use `find.files()` when looking for files matching a pattern anywhere in the project.

---

## Summary

**File operations are the foundation of codebase exploration**:

- **Search text**: `client.find.text()` - grep for content
- **Find files**: `client.find.files()` - glob for filenames  
- **List dirs**: `client.file.list()` - browse directories
- **Read files**: `client.file.read()` - get file contents
- **Check status**: `client.file.status()` - git status
- **Find symbols**: `client.find.symbols()` - locate functions/classes

**Best practices**:
- ✅ Filter early and often
- ✅ Use specific patterns
- ✅ Cache results for repeated access
- ✅ Batch operations with `Promise.all`
- ✅ Handle encoding and large files
- ✅ Validate and sanitize paths
- ✅ Use .ignore to exclude irrelevant directories

**Common use cases**:
- Code analysis and metrics
- Refactoring (find all usages)
- Documentation generation
- Duplicate code detection
- Security scanning (find secrets)
- Dependency mapping

Combine file operations with [sessions](#sessions-deep-dive) and [tools](#tools-deep-dive) for powerful AI-assisted coding!

---

## Agents Deep Dive

Agents are specialized AI assistants that define **how OpenCode thinks and acts**. They're like different "personalities" or "roles" you can assign to the AI, each with their own instructions, allowed tools, and working styles.

### What Are Agents?

An **agent** is a configured instance of an LLM with:
- **Specific instructions** (system prompt)
- **Defined capabilities** (which tools it can use)
- **Behavioral constraints** (permissions, limits)
- **Optional specializations** (security reviewer, documentation writer, etc.)

Think of agents like **job roles** in a company:
- **Build Agent** - The general developer who does everything
- **Plan Agent** - The architect who designs but doesn't build
- **Explore Agent** - The researcher who investigates but doesn't modify
- **Custom Agent** - Your specialized role (code reviewer, security auditor, etc.)

---

### Why Use Agents?

✅ **Specialization** - Different tasks need different mindsets  
✅ **Safety** - Restrict dangerous operations for planning/analysis  
✅ **Cost Control** - Use cheaper models for simple tasks  
✅ **Quality** - Tailor prompts and tools for optimal results  
✅ **Parallel Work** - Multiple agents can work on different aspects  
✅ **Delegation** - Primary agents can spawn specialized subagents  

---

## Agent Architecture

OpenCode has a **hierarchical agent system**:

```
┌─────────────────────────────────────────────┐
│         PRIMARY AGENTS (User-facing)        │
│  • Build  • Plan  • Your Custom Agents     │
│  (Switch with Tab key)                      │
└───────────────┬─────────────────────────────┘
                │ Can spawn
                ▼
┌─────────────────────────────────────────────┐
│           SUBAGENTS (Specialized)           │
│  • General  • Explore  • Your Custom       │
│  (Invoke with @mention or automatically)   │
└─────────────────────────────────────────────┘
```

### Primary vs Subagents

| Aspect | Primary Agents | Subagents |
|--------|---------------|-----------|
| **How to use** | Tab key to switch | @mention or auto-spawned |
| **Visibility** | Always visible | Autocomplete menu |
| **Session scope** | Main conversation | Child sessions |
| **Typical use** | Direct interaction | Specialized tasks |
| **Tool access** | Full (configurable) | Full except todo (configurable) |
| **User control** | Directly selected | Invoked by primary or user |

---

### How Agents Are Created

Agents can be created in **three ways**:

1. **Built-in** (shipped with OpenCode)
   - Location: Server code
   - Examples: Build, Plan, General, Explore
   - Can be customized but not deleted

2. **Configuration-based** (user-defined)
   - Location: `opencode.json` or `~/.config/opencode/agents/*.md`
   - Created by editing config files
   - Full customization possible

3. **CLI-generated** (interactive creation)
   - Command: `opencode agent create`
   - Interactive wizard
   - Creates markdown file automatically

---

## Agent Types

### Primary Agents

Primary agents are the **main assistants** you directly interact with. You cycle through them using the **Tab** key (or configured keybind). They handle your primary conversation.

**Key characteristics**:
- Direct user interaction
- Can have full or restricted tool access
- One active at a time in a session
- Can spawn subagents for specialized tasks

**Built-in primary agents**:
- `build` - Default agent, all tools enabled
- `plan` - Analysis-only, no destructive tools
- `compaction` - Hidden system agent (auto)
- `title` - Hidden system agent (auto)
- `summary` - Hidden system agent (auto)

---

### Subagents

Subagents are **specialized assistants** that primary agents can invoke for specific tasks. They can also be manually invoked by typing `@agentname` in your message.

**Key characteristics**:
- Invoked by @mention or automatically
- Run as **child sessions** (linked to parent)
- Can work in parallel (multiple subagents)
- Full tool access (except `todowrite`/`todoread` by default)

**Built-in subagents**:
- `general` - General-purpose, full access
- `explore` - Read-only codebase exploration

---

### System Agents

System agents are **hidden internal agents** that OpenCode uses automatically:

- `compaction` - Compresses long conversation history into summaries
- `title` - Generates session titles automatically
- `summary` - Creates session summaries when requested

These are **not user-selectable** and have no configuration.

---

## Built-in Agents

### Build Agent

**Mode**: `primary`  
**Default**: Yes (the agent you start with)

The Build agent is your **general-purpose developer**. It has **all tools enabled** and can:
- Read and modify files
- Run shell commands
- Install dependencies
- Build and test
- Make any changes needed

**When to use**:
- Implementing new features
- Fixing bugs
- Refactoring code
- Any development work

**Configuration** (default):
```json
{
  "agent": {
    "build": {
      "mode": "primary",
      "tools": {
        "write": true,
        "edit": true,
        "bash": true,
        "grep": true,
        "glob": true,
        "list": true,
        "read": true,
        "webfetch": true,
        "patch": true
      }
    }
  }
}
```

---

### Plan Agent

**Mode**: `primary`  
**Default**: Switched to with Tab key

The Plan agent is **read-only by design**. It analyzes and suggests but **doesn't modify** files or run arbitrary commands. Its key permissions are set to `ask` (or `deny`):

**Default restrictions**:
- `edit` → `ask` (prompts for each file modification)
- `bash` → `ask` (prompts for each command)
- `webfetch` → `ask` (prompts for each fetch)

**When to use**:
- Planning architecture changes
- Reviewing code and suggesting improvements
- Creating implementation plans
- Exploring ideas without risk
- Generating requirements

**Example workflow**:
```
1. Switch to Plan agent (Tab)
2. Ask: "How should we implement authentication?"
3. Plan agent analyzes and proposes approach
4. Switch back to Build agent (Tab)
5. Execute: "Implement the plan you just suggested"
```

**Configuration**:
```json
{
  "agent": {
    "plan": {
      "mode": "primary",
      "description": "Planning and analysis only - no code changes without approval",
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      },
      "permission": {
        "edit": "ask",
        "bash": "ask",
        "webfetch": "ask"
      }
    }
  }
}
```

---

### General Subagent

**Mode**: `subagent`  
**Default**: Available for auto-spawning or @mention

General is a **full-featured subagent** with access to most tools (except `todowrite`/`todoread`). It can make changes, run commands, and execute complex multi-step tasks independently.

**When to use**:
- Complex research tasks
- Parallel work streams
- Delegated subtasks
- When primary agent needs help

**Invocation**:
```
@general Research AWS SDK best practices and create a summary document
```

**Configuration**:
```json
{
  "agent": {
    "general": {
      "mode": "subagent",
      "description": "General-purpose agent for complex research and multi-step tasks",
      "tools": {
        "todowrite": false,  // Disabled by default
        "todoread": false   // Disabled by default
      }
    }
  }
}
```

---

### Explore Subagent

**Mode**: `subagent`  
**Default**: Available for @mention

Explore is a **fast, read-only agent** optimized for codebase exploration. It cannot modify files or run commands that change state.

**When to use**:
- Finding files and symbols
- Understanding codebase structure
- Searching for patterns
- Answering questions about existing code

**Example**:
```
@explore Find all React components that use the ThemeContext
```

**Configuration**:
```json
{
  "agent": {
    "explore": {
      "mode": "subagent",
      "description": "Fast read-only agent for exploring codebases",
      "tools": {
        "write": false,
        "edit": false,
        "bash": false,
        "patch": false
      }
    }
  }
}
```

---

## Custom Agent Creation

You can create **unlimited custom agents** tailored to your workflows.

### Method 1: JSON Configuration

Define agents in `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "security-reviewer": {
      "description": "Reviews code for security vulnerabilities",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "temperature": 0.1,
      "prompt": "You are a security expert. Focus on: OWASP Top 10, input validation, authentication flaws, data exposure, injection vulnerabilities.",
      "tools": {
        "write": false,
        "edit": false,
        "bash": true
      },
      "permission": {
        "bash": {
          "*": "ask",
          "grep *": "allow",
          "find *": "allow"
        }
      },
      "color": "#ff4444"
    }
  }
}
```

---

### Method 2: Markdown Files

Create `.opencode/agents/` directory and add `.md` files:

**File**: `.opencode/agents/api-tester.md`

```
---
description: Tests and validates API endpoints
mode: subagent
model: openai/gpt-4-turbo
temperature: 0.2
tools:
  write: false
  edit: false
  bash: true
permission:
  bash:
    "*": "ask"
    "curl *": "allow"
    "nc *": "allow"
color: success
---
You are an API testing specialist. Your job is to:
1. Examine API endpoints
2. Test with various inputs
3. Validate responses
4. Report issues

Use curl or netcat to test endpoints. Always test edge cases.
```

**Filename becomes agent name**: `api-tester.md` → `api-tester` agent

---

### Method 3: CLI Creation

Use the interactive command:

```bash
opencode agent create
```

Interactive prompts:
```
? Where should this agent be saved? (Use arrow keys)
> Global (~/.config/opencode/agents/)
  Project (.opencode/agents/)

? Agent name: security-auditor

? Description: Performs security audits and identifies vulnerabilities

? Select tools for this agent: (Press space to select, a to toggle all)
> [x] read
  [x] grep
  [x] glob
  [ ] write
  [ ] edit
  [x] bash
  [ ] webfetch

? Model: (Use arrow keys)
> anthropic/claude-sonnet-4-20250514
  anthropic/claude-haiku-4-20250514
  openai/gpt-4-turbo
  openai/gpt-3.5-turbo

? Temperature (0.0-1.0): 0.1

? Save configuration as: Markdown (recommended) or JSON
```

Creates file automatically with your specifications.

---

## Agent Configuration Options

Agents support extensive configuration. Here's the complete reference:

### Core Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `description` | string | yes | - | Brief description of agent's purpose |
| `mode` | `"primary" \| "subagent" \| "all"` | no | `"all"` | How agent can be used |
| `disable` | boolean | no | `false` | Disable this agent |
| `prompt` | string (file path) | no | - | System prompt file |
| `model` | string (provider/model) | no | (uses default) | LLM model to use |
| `temperature` | number (0-1) | no | model default | Randomness control |
| `steps` | number | no | unlimited | Max agentic iterations |
| `hidden` | boolean | no | `false` | Hide from @autocomplete (subagents only) |
| `color` | string (hex/theme) | no | - | UI accent color |
| `top_p` | number (0-1) | no | - | Alternative to temperature |

### Tools & Permissions

| Option | Type | Description |
|--------|------|-------------|
| `tools` | object | Enable/disable specific tools (`write: true/false`) |
| `permission` | object | Granular permissions (`edit: "ask/allow/deny"`) |
| `task` | object | Subagent invocation permissions (glob patterns) |

### Additional Options

Any other fields are **passed through** to the LLM provider as model-specific parameters.

---

## Agent Permissions Deep Dive

Permissions control **what agents can do**. They're crucial for safety and cost control.

### Permission Levels

| Level | Meaning | Use Case |
|-------|---------|----------|
| `allow` | Always permitted | Safe operations (read, grep) |
| `ask` | Prompt for approval each time | Dangerous operations (edit, bash) |
| `deny` | Completely blocked | High-risk operations (delete, rm) |

---

### Tool-specific Permissions

```json
{
  "agent": {
    "safe-agent": {
      "permission": {
        "edit": "deny",
        "bash": "ask",
        "webfetch": "allow"
      }
    }
  }
}
```

**Tool permissions cascade**: Agent-specific overrides global config.

---

### Command-specific Permissions

For `bash` tool, you can whitelist/blacklist specific commands:

```json
{
  "agent": {
    "dev": {
      "permission": {
        "bash": {
          "*": "ask",              // Default: ask for all
          "git status": "allow",   // Git status always ok
          "git log": "allow",
          "npm test": "allow",     // Tests always ok
          "rm *": "deny",          // Never allow delete
          "dd *": "deny"           // Never allow destructive disk ops
        }
      }
    }
  }
}
```

**Pattern matching**: Commands are matched as strings (prefix or glob-like). `*` matches anything.

**Order matters**: Last matching rule wins.

```json
{
  "permission": {
    "bash": {
      "*": "deny",           // 1. Deny everything first
      "git status": "allow", // 2. Then allow specific safe commands
      "npm test": "allow"
    }
  }
}
```

---

### Task Permissions (Subagent Invocation)

Control which **subagents** an agent can invoke via the Task tool (@task or automatic delegation):

```json
{
  "agent": {
    "orchestrator": {
      "permission": {
        "task": {
          "*": "deny",                    // Deny all by default
          "orchestrator-*": "allow",     // Allow orchestrator's own subagents
          "code-reviewer": "ask"          // Ask before invoking reviewer
        }
      }
    }
  }
}
```

**Important**: Even if task permissions deny, users can **still manually @mention** any subagent. Task permissions only restrict **automatic** invocation by the agent.

---

## Using Agents via SDK

### List Available Agents

```typescript
const agents = await client.app.agents();
console.log(`Found ${agents.data.length} agents:`);

agents.data.forEach(agent => {
  console.log(`- ${agent.name} (${agent.mode})`);
  console.log(`  ${agent.description}`);
  console.log(`  Model: ${agent.model}`);
  console.log(`  Tools: ${Object.keys(agent.tools || {}).join(', ')}`);
});
```

---

### Switch Agents in Session

Change the active agent for a session:

```typescript
// Switch to Plan agent
await client.session.update({
  path: { id: sessionId },
  body: {
    agent: "plan"  // Agent name
  }
});

// Verify current agent
const session = await client.session.get({ path: { id: sessionId } });
console.log(`Current agent: ${session.data.agent}`);
```

---

### Invoke Subagents

Subagents are invoked inline within a prompt:

```typescript
// Direct subagent invocation
const response = await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [
      {
        type: "text",
        text: "@explore Find all components using Material-UI"
      }
    ]
  }
});
```

The primary agent will delegate to the `explore` subagent, which runs as a child session.

---

### Create Custom Agent Sessions

When creating a session, specify which agent to use:

```typescript
const session = await client.session.create({
  body: {
    title: "Security review",
    agent: "security-reviewer"  // Use custom agent
  }
});
```

If `agent` is not specified, uses the default (`build`).

---

### Monitor Agent Activity

Track which agents are doing what via events:

```typescript
const events = await client.event.subscribe({
  query: { eventType: "agent.created,agent.completed" }
});

for await (const event of events.stream) {
  if (event.type === 'agent.created') {
    console.log(`🤖 Agent ${event.properties.agent} spawned`);
    console.log(`   Parent session: ${event.sessionID}`);
  }
  if (event.type === 'agent.completed') {
    console.log(`✅ Agent ${event.properties.agentID} finished`);
    console.log(`   Messages: ${event.properties.messageCount}`);
  }
}
```

---

## Agent Patterns

### Pattern 1: Specialized Workflows

Create agents for specific tasks:

```json
{
  "agent": {
    "docs-writer": {
      "description": "Writes and maintains documentation",
      "mode": "subagent",
      "tools": {
        "write": true,
        "edit": true,
        "bash": false
      },
      "permission": {
        "edit": "ask"  // Require approval for doc changes
      }
    },
    "migration-helper": {
      "description": "Assists with database migrations",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4",  // Capable model
      "temperature": 0.2,  // Deterministic
      "tools": {
        "bash": true,
        "write": true,
        "edit": true
      },
      "permission": {
        "bash": {
          "*": "ask",
          "prisma migrate*": "allow",
          "knex migrate*": "allow"
        }
      }
    }
  }
}
```

---

### Pattern 2: Cost Optimization

Use cheaper models for simple tasks:

```json
{
  "agent": {
    "quick-fix": {
      "description": "Quick bug fixes and small changes",
      "mode": "primary",
      "model": "anthropic/claude-haiku-4-20250514",  // Cheaper
      "temperature": 0.1,
      "steps": 5  // Limit iterations
    },
    "complex-refactor": {
      "description": "Complex refactoring and architecture",
      "mode": "primary",
      "model": "anthropic/claude-sonnet-4-20250514",  // Premium
      "temperature": 0.3,
      "steps": 20
    }
  }
}
```

---

### Pattern 3: Quality Gates

Multi-agent review pipeline:

```typescript
// Orchestrate multiple agents for quality
async function qualityReview(sessionId: string, code: string) {
  const { client } = await createOpencode();
  
  // Step 1: Security review
  await client.session.prompt({
    path: { id: sessionId },
    body: {
      parts: [
        { type: "text", text: "@security-reviewer Review this code for vulnerabilities:\n\n" + code }
      ]
    }
  });
  
  // Step 2: Performance review
  await client.session.prompt({
    path: { id: sessionId },
    body: {
      parts: [
        { type: "text", text: "@performance-reviewer Analyze performance:\n\n" + code }
      ]
    }
  });
  
  // Step 3: General code quality
  await client.session.prompt({
    path: { id: sessionId },
    body: {
      parts: [
        { type: "text", text: "@general Check code quality and best practices:\n\n" + code }
      ]
    }
  });
  
  // Step 4: Build agent compiles feedback
  await client.session.prompt({
    path: { id: sessionId },
    body: {
      parts: [
        { type: "text", text: "Synthesize all reviews and provide final assessment" }
      ]
    }
  });
}
```

---

### Pattern 4: Hierarchical Delegation

Primary agents spawn subagents for subtasks:

```typescript
// User asks a complex question to primary agent
await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [
      { type: "text", text: "We need to add authentication. Please:" }
    ]
  }
});

// Primary agent might automatically:
// 1. Spawn @explore to investigate current auth setup
// 2. Spawn @general to design implementation
// 3. Spawn @docs-writer to update documentation
// All as child sessions, reporting back

// You can monitor this via events
const events = await client.event.subscribe({
  query: { eventType: "agent.created,agent.completed" }
});
```

---

### Pattern 5: Agent Pool

Reuse specialized agents across projects:

```typescript
class AgentPool {
  private agents: Map<string, string> = new Map(); // name → sessionId
  
  async getOrCreate(name: string, agentType: string): Promise<string> {
    if (this.agents.has(name)) {
      return this.agents.get(name)!;
    }
    
    const session = await client.session.create({
      body: {
        title: `Agent: ${name}`,
        agent: agentType  // Use specific agent
      }
    });
    
    this.agents.set(name, session.data.id);
    return session.data.id;
  }
  
  async invoke(agentName: string, prompt: string) {
    const sessionId = await this.getOrCreate(agentName, agentName);
    return await client.session.prompt({
      path: { id: sessionId },
      body: { parts: [{ type: "text", text: prompt }] }
    });
  }
}

// Usage
const pool = new AgentPool();
await pool.invoke("security-reviewer", "Review this login code...");
await pool.invoke("docs-writer", "Write docs for this API...");
```

---

## Agent vs Tool Distinction

**Common confusion**: Agents vs Tools

| Aspect | Agent | Tool |
|--------|-------|------|
| **What** | LLM with personality/role | Specific function the LLM can call |
| **Granularity** | Coarse (entire conversation) | Fine (individual action) |
| **State** | Maintains conversation | Stateless operation |
| **Example** | "Code reviewer" agent | `grep` tool for searching |
| **Configuration** | `opencode.json` → `agent` | `opencode.json` → `tools` |
| **Invocation** | Tab key, @mention, session creation | Automatically called by agent |

**Analogy**:
- **Agent** = Chef (Italian chef, pastry chef, sushi chef)
- **Tool** = Knife, stove, whisk (capabilities the chef uses)

An agent uses multiple tools to accomplish its mission.

---

## Security Considerations

### 1. Permission Management

Always restrict dangerous agents:

```json
{
  "agent": {
    "plan": {
      "permission": {
        "edit": "deny",
        "bash": "deny"
      }
    }
  }
}
```

### 2. Model Selection

Not all models are equal. Use appropriate models:

- **Critical tasks** (security, production): Use high-quality models (Sonnet, GPT-4)
- **Exploratory** (research, brainstorming): Can use cheaper models (Haiku, GPT-3.5)
- **Simple tasks** (formatting, linting): Even smaller models may suffice

### 3. Tool Whitelisting

Least privilege principle:

```json
{
  "tools": {
    "write": false,
    "edit": false,
    "bash": false,
    "webfetch": false
  },
  "agent": {
    "docs-writer": {
      "tools": {
        "write": true,
        "edit": true,
        "bash": false  // Docs writer doesn't need shell access
      }
    }
  }
}
```

### 4. Task Permissions

Prevent rogue agent spawning:

```json
{
  "agent": {
    "user-facing": {
      "permission": {
        "task": {
          "*": "deny",  // Don't let this agent spawn others
          "user-facing-*": "allow"  // Only its own subagents
        }
      }
    }
  }
}
```

---

## Performance Optimization

### 1. Model Selection

Faster models for simple tasks:

| Agent Type | Recommended Model | Reason |
|------------|------------------|--------|
| Explore | Claude Haiku | Fast read-only exploration |
| Plan | Claude Sonnet | Good reasoning, medium cost |
| Build | Claude Sonnet 4 | Capable for complex tasks |
| Docs | GPT-4 Turbo | Good writing quality |

---

### 2. Temperature Tuning

Lower temperature = more deterministic = faster (less retries):

```json
{
  "agent": {
    "explore": { "temperature": 0.0 },  // Exact matches
    "plan": { "temperature": 0.1 },     // Slight variation ok
    "creative": { "temperature": 0.7 } // Maximum creativity
  }
}
```

---

### 3. Step Limiting

Prevent infinite loops for cost control:

```json
{
  "agent": {
    "quick-check": {
      "steps": 5,  // Max 5 tool iterations
      "description": "Quick analysis, limited steps"
    }
  }
}
```

---

### 4. Caching Agent Sessions

Agent state is session-specific. For repeated tasks, reuse sessions:

```typescript
const agentSessionCache = new Map<string, string>();

async function getAgentSession(agentName: string): Promise<string> {
  if (agentSessionCache.has(agentName)) {
    return agentSessionCache.get(agentName)!;
  }
  
  const session = await client.session.create({
    body: { agent: agentName, title: `Agent ${agentName}` }
  });
  
  agentSessionCache.set(agentName, session.data.id);
  return session.data.id;
}
```

---

## Troubleshooting Agents

### Problem: Agent not appearing in @mention autocomplete

**Solution**:
- Check `mode: "subagent"` (only subagents are @mentionable)
- Check `hidden: true` (should be false or omitted)
- Verify agent file is in correct location:
  - Global: `~/.config/opencode/agents/`
  - Project: `.opencode/agents/`
- Restart OpenCode after adding agents

---

### Problem: Agent ignores tool restrictions

**Solution**:
- Verify permissions are set correctly
- Primary agents can often use tools even if subagents can't
- Check for conflicting global `tools` config that overrides
- Use `permission` field for finer control than `tools`

---

### Problem: Agent spawns wrong subagent

**Solution**:
- Check agent's `permission.task` settings
- Primary agent chooses subagent based on description and task
- You can force specific subagent with @mention

---

### Problem: Custom agent not loading

**Solution**:
- Validate JSON/YAML syntax in `opencode.json`
- For markdown agents, ensure frontmatter is valid (between `---` delimiters)
- Check file permissions (readable by OpenCode process)
- Look at OpenCode logs for parsing errors

---

### Problem: Agent uses wrong model

**Solution**:
- Check global `model` config
- Check agent-specific `model` config
- Agent model overrides global if specified
- Use `client.config.get()` to see effective config

---

## Best Practices

### ✅ DO

1. **Use Plan agent for analysis** - Safer, prevents accidental changes
2. **Give agents clear descriptions** - Helps model understand its role
3. **Restrict dangerous tools** - Use `permission: "ask"` for bash/edit
4. **Choose appropriate models** - Match model capability to task complexity
5. **Set temperature appropriately** - Lower for deterministic tasks
6. **Limit steps for cost control** - Prevent infinite tool loops
7. **Use custom agents for specialization** - Security reviewer, docs writer, etc.
8. **Monitor via events** - Track agent activity and performance
9. **Test agents in isolation** - Verify they behave as expected
10. **Share useful agents** - Contribute to community

---

### ❌ DON'T

1. **Don't give all agents full access** - Principle of least privilege
2. **Don't use very high temperature** (>0.8) for code tasks - Too random
3. **Don't ignore step limits** - Can lead to unexpected costs
4. **Don't hardcode secrets in prompts** - Use env vars or external tools
5. **Don't create circular agent delegations** - A calls B which calls A
6. **Don't forget to document custom agents** - Others need to understand them
7. **Don't use experimental features in production** - Test first
8. **Don't set `hidden: true` for user-facing agents** - Makes them inaccessible

---

## Common Questions

### Q: What's the difference between primary and subagent modes?

**Answer**:
- **Primary**: Direct user interaction, Tab key switchable, one active at a time
- **Subagent**: Invoked via @mention or auto-delegation, can run parallel as child sessions
- **Hidden primary**: Not shown but can be selected? Actually primary agents are always in the rotation unless hidden doesn't apply to them. Subagents with `hidden: true` don't appear in @autocomplete.

---

### Q: Can an agent change its own configuration?

**Answer**: No. Agent configuration is static (from `opencode.json` or markdown files). An agent cannot modify its own permissions, tools, or prompt. To change an agent, edit the config file and restart OpenCode.

---

### Q: Do agents share state?

**Answer**: No. Each session has its own agent instance with isolated conversation state. If you want to share context between agent sessions, you must:
- Use the same session and switch agents (shares history)
- Manually copy messages/information
- Use a shared knowledge base (files, database)

---

### Q: Can I use different providers for different agents?

**Answer**: Yes! Set `model` per agent:

```json
{
  "agent": {
    "build": {
      "model": "anthropic/claude-sonnet-4-20250514"
    },
    "plan": {
      "model": "anthropic/claude-haiku-4-20250514"
    },
    "creative": {
      "model": "openai/gpt-4-turbo"
    }
  }
}
```

Each agent can use a different provider/model combo.

---

### Q: How do agents decide which tools to use?

**Answer**: The LLM decides based on:
1. **System prompt** - Instruction about available tools
2. **Tool descriptions** - Each tool has a description explaining its purpose
3. **Permissions** - Only permitted tools are exposed to the model
4. **Context** - What you ask it to do

You can further constrain with `tools` config to only enable specific tools for an agent.

---

### Q: Can I create an agent that only reads files?

**Answer**: Yes:

```json
{
  "agent": {
    "reader": {
      "description": "Read-only agent for code review",
      "mode": "subagent",
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      }
    }
  }
}
```

Or use the built-in `explore` agent which is already read-only.

---

### Q: Do agents respect .gitignore?

**Answer**: Tools (grep, glob, list, read) respect `.gitignore` by default (via ripgrep). Agents cannot bypass this unless you configure `.opencode/.ignore` to include excluded files. This is a **safety feature** to prevent agents from reading generated files.

---

### Q: Can I use agents with the SDK?

**Answer**: Yes! Agents are configured server-side. The SDK just sends prompts. You can:
- Create sessions with specific agents (`session.create({ body: { agent: "my-agent" } })`)
- Switch agents mid-session (`session.update({ body: { agent: "plan" } })`)
- Invoke subagents with @mention in your prompt text

---

### Q: How do I debug why an agent made a certain decision?

**Answer**:
1. Check session messages: `client.session.messages()` to see full conversation
2. Check events: `client.event.subscribe()` to see tool calls
3. Look at agent configuration: `client.config.get()` to see active agents
4. Increase verbosity: Set `"debug": true` in config (if available)

---

### Q: Can agents be shared between projects?

**Answer**: Yes. Agents can be defined:
- **Globally**: `~/.config/opencode/agents/` - Available in all projects
- **Per-project**: `.opencode/agents/` - Only for that project

Use global for personal agents, project-specific for team standards.

---

### Q: What happens when an agent exceeds its step limit?

**Answer**: When `steps` limit is reached:
- Agent receives a system message: "You have reached the step limit. Provide a summary."
- Agent must respond with text only (no more tool calls)
- Session continues but agent becomes read-only for that turn
- User can continue conversation or delegate to another agent

---

### Q: Are there default agents in every OpenCode installation?

**Answer**: Yes, these are always available:
- `build` (primary)
- `plan` (primary)
- `general` (subagent)
- `explore` (subagent)
- `compaction`, `title`, `summary` (hidden system)

You can disable built-in agents but cannot delete them.

---

## Summary

**Agents are the "who" of OpenCode** - they define **who** is doing the work:

- **Primary agents**: Your main interface (Build, Plan, custom)
- **Subagents**: Specialized helpers (General, Explore, custom)  
- **System agents**: Hidden automation (compaction, title, summary)

**Create custom agents** for:
- Security review
- Documentation writing
- Testing/QA
- Database migrations
- Legacy code modernization
- Anything with a repeatable pattern!

**Configuration**:
- JSON: `opencode.json` → `agent` key
- Markdown: `.opencode/agents/*.md`
- CLI: `opencode agent create`

**Control**:
- Tools: Which actions agent can take
- Permissions: Ask/Allow/Deny for safety
- Model: Which LLM to use
- Temperature/Steps: Cost/quality tuning
- Task permissions: Which subagents can spawn

**Next**: Combine agents with [sessions](#sessions-deep-dive), [tools](#tools-deep-dive), and [events](#events-deep-dive) for sophisticated AI workflows!

---

## Resources

| Resource | URL |
|----------|-----|
| Agents Documentation | https://opencode.ai/docs/agents/ |
| SDK app.agents() | https://opencode.ai/docs/sdk#app |
| Agent Configuration | https://opencode.ai/docs/config/ |
| Permissions Guide | https://opencode.ai/docs/permissions/ |
| Custom Tools (for agent skills) | https://opencode.ai/docs/custom-tools/ |

---

## Next Steps

| Resource | URL |
|----------|-----|
| File API Reference | https://opencode.ai/docs/sdk#files |
| Built-in Tools: grep | https://opencode.ai/docs/tools#grep |
| Built-in Tools: glob | https://opencode.ai/docs/tools#glob |
| Built-in Tools: list | https://opencode.ai/docs/tools#list |
| Built-in Tools: read | https://opencode.ai/docs/tools#read |
| ripgrep patterns | https://docs.rs/ripgrep/12.0.0/ripgrep/ |

---

## Next Steps

| Resource | URL |
|----------|-----|
| Server API (Events) | https://opencode.ai/docs/server#events |
| SSE Specification | https://html.spec.whatwg.org/multipage/server-sent-events.html |
| SDK event.subscribe() | https://opencode.ai/docs/sdk#events |

---

## Next Steps

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