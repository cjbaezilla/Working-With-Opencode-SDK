# OpenCode Agents: A Complete Beginner's Guide

## What Are Agents?

Agents are specialized AI assistants within OpenCode that you can customize for specific tasks and workflows. Think of them as different "personalities" or "experts" you can summon to help with particular kinds of work.

For example:
- One agent could be a coding expert that writes and edits files
- Another could be a planner that analyzes code but doesn't make changes
- Another could be a security auditor that searches for vulnerabilities

Each agent has its own:
- **System prompt** (instructions that define its behavior)
- **AI model** (the underlying AI that powers it)
- **Tools it can use** (what actions it can perform)
- **Permissions** (what it's allowed to do)
- **Configuration settings** (temperature, max steps, etc.)

---

## Types of Agents

OpenCode has two types of agents, each serving different purposes:

### Primary Agents

Primary agents are the main assistants you interact with directly. They handle your main conversation and can perform a wide range of tasks.

**Key characteristics:**
- You interact with them directly
- You can switch between them using the **Tab** key
- They handle your primary workflow
- Built-in examples: `build`, `plan`

**When to use primary agents:**
- When you need to do the main work in your session
- When you want an agent with full capabilities
- When you're doing general development tasks

### Subagents

Subagents are specialized assistants that are invoked for specific tasks. They can be called automatically by primary agents or manually by you.

**Key characteristics:**
- Invoked using `@name` (e.g., `@explore find all test files`)
- Can be called by primary agents for specialized tasks
- Often have focused capabilities (e.g., read-only, fast searching)
- Built-in examples: `general`, `explore`

**When to use subagents:**
- When you need a specialized skill (fast codebase exploration, research)
- When you want to delegate a specific task
- When you need parallel work streams

---

## Built-in Agents

OpenCode comes with several pre-configured agents:

### Primary Agents

#### Build Agent (`build`)
- **Mode:** Primary
- **Default agent:** Yes
- **Capabilities:** Full tool access (write, edit, bash, etc.)
- **Use for:** General development work, creating files, making changes
- **Best for:** When you need complete freedom to modify your codebase

**Example usage:**
```
User: Create a new React component for a button
Build: [Creates the component file, adds styling, exports it]
```

#### Plan Agent (`plan`)
- **Mode:** Primary
- **Capabilities:** Read-only (no write/edit/bash by default)
- **Use for:** Planning, analysis, code review without making changes
- **Best for:** When you want suggestions but don't want accidental modifications

**Example usage:**
```
User: Analyze this code for potential improvements
Plan: [Reviews code, suggests refactorings, but doesn't implement them]
```

### Subagents

#### General Agent (`general`)
- **Mode:** Subagent
- **Capabilities:** Full tool access (except todo), can make changes
- **Use for:** Researching complex questions, multi-step tasks
- **Best for:** When you need a versatile helper that can both investigate and implement

**Example usage:**
```
User: @general Research best practices for API authentication and implement them
General: [Researches, reads files, and implements auth]
```

#### Explore Agent (`explore`)
- **Mode:** Subagent
- **Capabilities:** Read-only, fast codebase navigation
- **Use for:** Finding files, searching code, answering questions about the codebase
- **Best for:** Quick exploration without risk of modifications

**Example usage:**
```
User: @explore Find all places where database connections are created
Explore: [Searches codebase and returns results]
```

### Hidden System Agents

These run automatically and aren't meant for direct user interaction:

- **Compaction (`compaction`):** Summarizes long conversations
- **Title (`title`):** Generates session titles
- **Summary (`summary`):** Creates session summaries

---

## How Agents Work Together

A typical workflow might look like this:

1. You start with the `build` agent to implement a feature
2. You ask it to plan first, so it invokes the `plan` subagent
3. The `plan` agent analyzes without modifying
4. You review the plan and ask `build` to implement it
5. During implementation, `build` uses `explore` to find existing code
6. You switch to the `plan` agent to review the implementation

---

## Configuring Agents

You can customize agents in two ways:

### Method 1: JSON Configuration (opencode.json)

Edit your `opencode.json` file (usually in your project root or `~/.config/opencode/`):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": {
      "mode": "primary",
      "model": "anthropic/claude-sonnet-4-20250514",
      "temperature": 0.3,
      "tools": {
        "write": true,
        "edit": true,
        "bash": true
      }
    },
    "plan": {
      "mode": "primary",
      "model": "anthropic/claude-haiku-4-20250514",
      "temperature": 0.1,
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      }
    },
    "security-auditor": {
      "description": "Reviews code for security vulnerabilities",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "You are a security expert. Focus on finding vulnerabilities.",
      "tools": {
        "write": false,
        "edit": false,
        "bash": true
      }
    }
  }
}
```

### Method 2: Markdown Files

Create markdown files in:
- Global: `~/.config/opencode/agents/`
- Per-project: `.opencode/agents/`

**File naming:** The filename (without `.md`) becomes the agent name. For example, `security-auditor.md` creates a `security-auditor` agent.

**Markdown format:**
```markdown
---
description: Performs security audits and finds vulnerabilities
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
tools:
  write: false
  edit: false
  bash: true
permission:
  bash:
    "*": ask
    "git push": deny
color: "#ff5733"
---

You are a security expert. Focus on:
- Input validation issues
- Authentication flaws
- Data exposure risks
- Dependency vulnerabilities
```

The section between `---` lines is the configuration (called "frontmatter"). Everything after that is the system prompt.

---

## Configuration Options Explained

Here's a comprehensive list of all configuration options:

### Required Options

#### `description` (string)
A brief description of what the agent does and when to use it.

```json
{
  "agent": {
    "my-agent": {
      "description": "Writes high-quality documentation with examples"
    }
  }
}
```

**Purpose:** Helps you (and the AI) understand when to use this agent. Shown in the UI and agent autocomplete.

---

### Agent Behavior Options

#### `mode` (string)
How the agent can be used. Options:
- `"primary"`: Can be switched to with Tab key, handles main conversation
- `"subagent"`: Can only be invoked with `@name` or by other agents
- `"all"` (default): Works as both primary and subagent

```json
{
  "agent": {
    "explorer": {
      "mode": "subagent"
    }
  }
}
```

#### `prompt` (string)
Path to a custom system prompt file. Relative to the config file location.

```json
{
  "agent": {
    "code-reviewer": {
      "prompt": "{file:./prompts/review.txt}"
    }
  }
}
```

The `{file:...}` syntax tells OpenCode to read the prompt from that file. You can also put the prompt directly in markdown files (everything after the frontmatter).

---

### Model Options

#### `model` (string)
Override which AI model this agent uses.

```json
{
  "agent": {
    "fast-agent": {
      "model": "anthropic/claude-haiku-4-20250514"
    },
    "smart-agent": {
      "model": "anthropic/claude-sonnet-4-20250514"
    }
  }
}
```

**Format:** `provider/model-id`

**Common examples:**
- `anthropic/claude-sonnet-4-20250514`
- `anthropic/claude-haiku-4-20250514`
- `openai/gpt-4o`
- `openai/gpt-4-turbo`
- `opencode/gpt-5.1-codex` (for OpenCode Zen)

**Note:** If not specified, primary agents use the globally configured model, while subagents inherit the primary agent's model.

#### `temperature` (number)
Controls randomness and creativity (0.0 to 1.0):
- **0.0-0.2:** Very focused, deterministic. Ideal for code analysis, planning
- **0.3-0.5:** Balanced. Good for general development
- **0.6-1.0:** Creative, varied. Good for brainstorming

```json
{
  "agent": {
    "analyzer": {
      "temperature": 0.1
    },
    "brainstormer": {
      "temperature": 0.8
    }
  }
}
```

**Default:** Typically 0 for most models, 0.55 for Qwen models.

#### `top_p` (number)
Alternative to temperature for controlling diversity (0.0 to 1.0).

```json
{
  "agent": {
    "creative": {
      "top_p": 0.9
    }
  }
}
```

---

### Control Options

#### `steps` (number) or `maxSteps` (deprecated)
Maximum number of agentic iterations before forcing text-only response.

```json
{
  "agent": {
    "quick-thinker": {
      "steps": 5
    }
  }
}
```

**Purpose:** Control costs and limit unbounded execution. When the limit is reached, the agent summarizes its work.

#### `disable` (boolean)
Disable this agent entirely.

```json
{
  "agent": {
    "experimental-agent": {
      "disable": true
    }
  }
}
```

#### `hidden` (boolean)
Hide a subagent from the `@` autocomplete menu.

```json
{
  "agent": {
    "internal-helper": {
      "mode": "subagent",
      "hidden": true
    }
  }
}
```

**Use case:** For agents that should only be invoked programmatically by other agents.

---

### Tools & Permissions

#### `tools` (object)
Control which tools the agent can access.

```json
{
  "agent": {
    "read-only": {
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      }
    },
    "read-and-bash": {
      "tools": {
        "write": false,
        "edit": false,
        "bash": true
      }
    }
  }
}
```

**Available tools:**
- `write`: Create new files
- `edit`: Edit existing files
- `bash`: Run shell commands
- `read`: Read files (usually kept true)
- `grep`: Search in files
- `glob`: Find files by pattern
- `task`: Invoke subagents (for primary agents)
- `webfetch`: Fetch web content
- And any MCP server tools

**Wildcards:** You can use `*` to control tool groups.

```json
{
  "agent": {
    "no-mcp": {
      "tools": {
        "mymcp_*": false  // Disables all tools from "mymcp" MCP server
      }
    }
  }
}
```

**Note:** Agent-specific tool config overrides global config.

#### `permission` (object)
Control how tools require approval. Options: `"ask"`, `"allow"`, `"deny"`.

```json
{
  "agent": {
    "cautious": {
      "permission": {
        "edit": "ask",      // Ask before editing files
        "bash": "deny",     // Never allow bash commands
        "webfetch": "allow" // Always allow web fetching
      }
    }
  }
}
```

**Granular bash permissions:** Control specific commands.

```json
{
  "agent": {
    "safe-git": {
      "permission": {
        "bash": {
          "*": "ask",                    // Ask for all commands by default
          "git status": "allow",         // Always allow git status
          "git diff": "allow",           // Always allow git diff
          "git push": "deny",            // Never allow git push
          "git commit*": "ask"           // Ask for git commit commands
        }
      }
    }
  }
}
```

**Glob patterns supported:**
- `"git *"`: All git commands
- `"*"`: All commands
- `"npm test"`: Exact command match

**Rule precedence:** Last matching rule wins. Order matters!

```json
{
  "agent": {
    "smart-order": {
      "permission": {
        "bash": {
          "*": "ask",                    // Default: ask for everything
          "git status": "allow",         // Allow these specific ones
          "git diff": "allow"
        }
      }
    }
  }
}
```

**Task permissions:** Control which subagents can be invoked.

```json
{
  "agent": {
    "orchestrator": {
      "permission": {
        "task": {
          "*": "deny",                   // Deny all subagents by default
          "orchestrator-*": "allow",    // Allow ones with this prefix
          "code-reviewer": "ask"         // Ask for specific agent
        }
      }
    }
  }
}
```

**Note:** Users can always invoke subagents directly via `@` regardless of task permissions.

---

### Visual Options

#### `color` (string)
Customize the agent's color in the UI.

**Options:**
- Hex color: `"#FF5733"`, `"#00FF00"`
- Theme colors: `"primary"`, `"secondary"`, `"accent"`, `"success"`, `"warning"`, `"error"`, `"info"`

```json
{
  "agent": {
    "dangerous-ops": {
      "color": "#ff0000"
    },
    "review-agent": {
      "color": "accent"
    }
  }
}
```

---

### Advanced Options

#### `additional` options
Any other options you specify are passed directly to the AI provider as model-specific parameters.

```json
{
  "agent": {
    "deep-thinker": {
      "model": "openai/gpt-5",
      "reasoningEffort": "high",  // OpenAI-specific
      "textVerbosity": "low"      // OpenAI-specific
    }
  }
}
```

Check your provider's documentation for available parameters.

---

## Creating Custom Agents

### Interactive Creation

Use the built-in command:

```bash
opencode agent create
```

This interactive wizard will:
1. Ask where to save the agent (global or project-specific)
2. Prompt for a description of what the agent should do
3. Suggest an appropriate agent name
4. Let you select which tools to enable
5. Generate the configuration file

### Manual Creation

**JSON method:** Edit `opencode.json` and add your agent under the `agent` key.

**Markdown method:** Create a `.md` file in `.opencode/agents/` or `~/.config/opencode/agents/`.

**Minimal example:**
```markdown
---
description: A simple helper agent
mode: subagent
tools:
  write: false
  edit: false
  bash: false
---

You are a helpful assistant. Answer questions clearly and concisely.
```

---

## Common Use Cases & Examples

### 1. Security Auditor

A subagent that searches for security issues without modifying files.

**File:** `~/.config/opencode/agents/security-auditor.md`

```markdown
---
description: Performs security audits and identifies vulnerabilities
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
tools:
  write: false
  edit: false
  bash: true
permission:
  bash:
    "*": ask
color: "#ff6b6b"
---

You are a security expert conducting code audits.

Focus on these vulnerability categories:
- **Input validation:** SQL injection, XSS, command injection
- **Authentication:** Weak passwords, missing MFA, session issues
- **Authorization:** Privilege escalation, IDOR
- **Data exposure:** Secrets in code, insecure storage
- **Dependencies:** Known CVEs, outdated packages
- **Configuration:** Insecure defaults, exposed ports

Procedure:
1. Examine authentication and authorization code
2. Check for input validation on all user inputs
3. Look for secrets, API keys, credentials
4. Review dependency versions against known vulnerabilities
5. Check configuration files for security misconfigurations

Provide:
- Specific vulnerable code locations (file:line)
- Severity rating (Critical/High/Medium/Low)
- Concrete remediation steps
- References to OWASP guidelines
```

**Usage:**
```
User: @security-auditor Check the authentication code for issues
Security Auditor: [Analyzes auth code, reports vulnerabilities]
```

---

### 2. Documentation Writer

A subagent focused on creating and maintaining documentation.

**File:** `.opencode/agents/docs-writer.md`

```markdown
---
description: Writes and maintains project documentation
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.4
tools:
  write: true
  edit: true
  bash: false
permission:
  edit: ask
  write: ask
color: "#4ecdc4"
---

You are a technical writer specializing in clear, comprehensive documentation.

Documentation Principles:
1. **Audience awareness:** Write for the intended audience (devs, users, admins)
2. **Clear structure:** Use headings, lists, and logical flow
3. **Examples:** Include practical code examples
4. **Progressive disclosure:** Start simple, add complexity gradually
5. **User-friendly language:** Avoid jargon when possible, explain when necessary

Types of documentation you create:
- README files with setup instructions
- API documentation with examples
- Architecture overviews
- Tutorials and guides
- Code comments (clear, concise)
- CHANGELOG entries
- Contributing guidelines

Format guidelines:
- Use Markdown formatting
- Include code blocks with language syntax highlighting
- Add tables for structured data
- Use callout boxes for notes, warnings, tips
- Include diagrams when helpful (describe them)

When updating existing docs:
- Preserve existing structure unless it needs improvement
- Update examples to match current codebase
- Fix broken links and outdated information
- Maintain consistent style and tone

Process:
1. Understand the purpose and audience
2. Gather information from code comments, READMEs, and existing docs
3. Create an outline
4. Write clear, concise content
5. Add practical examples
6. Review for clarity and completeness
```

**Usage:**
```
User: @docs-writer Write documentation for the new API endpoint
Docs Writer: [Researches the code, writes comprehensive docs with examples]
```

---

### 3. Code Reviewer

A subagent that reviews code quality and best practices.

**File:** `~/.config/opencode/agents/code-reviewer.md`

```markdown
---
description: Reviews code for quality, bugs, and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
tools:
  write: false
  edit: false
  bash: true
permission:
  edit: deny
  bash:
    "*": ask
    "git diff": allow
    "git log*": allow
color: "#ffd93d"
---

You are a senior code reviewer with expertise in multiple languages and frameworks.

Review Focus Areas:

**Code Quality:**
- Clear, readable code
- Proper naming conventions
- Single responsibility principle
- DRY (Don't Repeat Yourself)
- Appropriate comments (explain why, not what)

**Potential Bugs:**
- Edge cases not handled
- Null/undefined dereferences
- Off-by-one errors
- Race conditions
- Resource leaks

**Performance:**
- Unnecessary computations in loops
- Inefficient algorithms or data structures
- N+1 query problems
- Unnecessary re-renders (frontend)
- Memory leaks

**Security:**
- Input validation
- Authentication/authorization checks
- Secrets in code
- SQL injection vectors
- XSS vulnerabilities

**Best Practices:**
- Framework conventions followed
- Consistent code style
- Error handling
- Logging appropriate information
- Tests present and meaningful

Review Process:
1. Understand the change (check git diff if available)
2. Read the changed code carefully
3. Identify issues in each category above
4. Suggest specific improvements
5. Highlight positive aspects (be encouraging)
6. Prioritize issues by severity

Output Format:
```
Summary: [High-level summary]

Issues Found:
- [Severity: Critical/High/Medium/Low] [Category]
  Location: file:line
  Issue: [Description]
  Suggestion: [Specific fix]

Positive Notes:
- [What was done well]

Overall Assessment: [Approve/Request Changes/Need Major Revision]
```

Be constructive, specific, and actionable. Never just say "this is bad" — always explain why and how to fix.
```

**Usage:**
```
User: @code-reviewer Review the changes in the last commit
Code Reviewer: [Checks git diff, provides structured review]
```

---

### 4. Debug Assistant

A subagent specialized in debugging and troubleshooting.

**File:** `.opencode/agents/debugger.md`

```markdown
---
description: Helps debug issues and investigate problems
mode: subagent
model: anthropic/claude-haiku-4-20250514
temperature: 0.2
tools:
  write: false
  edit: false
  bash: true
permission:
  bash:
    "*": ask
    "tail*": allow
    "grep*": allow
    "cat*": allow
color: "#ff9f43"
---

You are a debugging expert. Help users investigate and resolve issues.

Debugging Methodology:
1. **Gather symptoms:** What's happening? What should happen?
2. **Check logs:** Error messages, stack traces, application logs
3. **Reproduce:** Can the issue be consistently reproduced?
4. **Isolate:** Find the minimal code that triggers the issue
5. **Hypothesize:** Form theories about the root cause
6. **Test:** Verify hypotheses with targeted checks
7. **Solution:** Propose fixes with explanation

Tools you use:
- Read log files
- Search for error patterns
- Check recent changes (git log, git diff)
- Examine configurations
- Run diagnostic commands
- Check system resources

Your approach:
- Systematic, methodical investigation
- Ask clarifying questions when needed
- Consider recent changes (code, dependencies, config)
- Think about environmental factors
- Check for race conditions, timing issues
- Look for patterns and correlations

When you find the issue:
- Explain the root cause clearly
- Provide a concrete fix
- Suggest prevention strategies
- Reference similar issues if applicable

Keep the user informed of your investigation steps.
```

---

### 5. Database Specialist

A subagent for database-related tasks.

**File:** `.opencode/agents/db-expert.md`

```markdown
---
description: Database expert for queries, migrations, and optimization
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.2
tools:
  write: false
  edit: false
  bash: true
  # Your database tools here if available
permission:
  edit: deny
  bash:
    "*": ask
    "*sql*": allow
    "*db*": allow
color: "#5f27cd"
---

You are a database administrator and optimization expert.

Expertise areas:
- SQL query writing and optimization
- Schema design and migrations
- Index strategies
- Performance tuning
- Database debugging
- Backup and recovery planning
- Replication and scaling

When helping:
1. Understand the data model and requirements
2. Write efficient queries (proper joins, avoiding N+1)
3. Suggest appropriate indexes
4. Review query execution plans
5. Consider data volume and growth
6. Think about transaction isolation levels
7. Plan safe migrations with rollback strategies

Best practices you enforce:
- Use parameterized queries (prevent SQL injection)
- Consider query performance at scale
- Add appropriate indexes but don't over-index
- Use transactions for data integrity
- Write idempotent migrations
- Test migrations on staging first
- Have rollback plans

Database-specific knowledge:
- PostgreSQL: CTEs, window functions, partitioning
- MySQL: Storage engines, optimization hints
- SQLite: Limitations and best uses
- NoSQL: When to use, data modeling patterns

Always explain the reasoning behind your recommendations.
```

---

## Complete Configuration Example

Here's a comprehensive `opencode.json` with multiple custom agents:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "agent": {
    "build": {
      "mode": "primary",
      "temperature": 0.3
    },
    "plan": {
      "mode": "primary",
      "temperature": 0.1,
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      }
    },
    "security-auditor": {
      "description": "Performs security audits and finds vulnerabilities",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "temperature": 0.1,
      "tools": {
        "write": false,
        "edit": false,
        "bash": true
      },
      "permission": {
        "bash": {
          "*": "ask"
        }
      },
      "color": "#ff6b6b"
    },
    "docs-writer": {
      "description": "Writes and maintains project documentation",
      "mode": "subagent",
      "temperature": 0.4,
      "tools": {
        "write": true,
        "edit": true,
        "bash": false
      },
      "permission": {
        "edit": "ask",
        "write": "ask"
      },
      "color": "#4ecdc4"
    },
    "debugger": {
      "description": "Helps debug issues and investigate problems",
      "mode": "subagent",
      "temperature": 0.2,
      "tools": {
        "write": false,
        "edit": false,
        "bash": true
      },
      "permission": {
        "bash": {
          "*": "ask",
          "tail*": "allow",
          "grep*": "allow",
          "cat*": "allow"
        }
      },
      "color": "#ff9f43"
    }
  }
}
```

---

## Best Practices

### 1. Start Simple
Begin with simple agents and add complexity as needed.

```json
{
  "agent": {
    "simple-reviewer": {
      "description": "Basic code review",
      "mode": "subagent",
      "tools": {
        "write": false,
        "edit": false
      }
    }
  }
}
```

### 2. Use Appropriate Models
- **Fast, cheap tasks:** Use Haiku or fast models
- **Complex analysis:** Use Sonnet or GPT-4
- **Simple Q&A:** Use minimal temperature, cheaper models

### 3. Control Permissions Carefully
- Start with `"ask"` for potentially destructive tools
- Use glob patterns for command-level control
- Structure rules with general rules first, specific overrides last

### 4. Provide Clear Descriptions
The `description` field helps you choose the right agent in the UI.

```json
{
  "description": "Writes API documentation with OpenAPI specs"  // Good!
  // vs
  "description": "Docs agent"  // Less helpful
}
```

### 5. Write Focused Prompts
Each agent should have a clear, narrow purpose.

**Good:**
```markdown
You are a React expert. Review React code for:
- Hook usage patterns
- Component design
- State management
- Performance issues
```

**Less focused:**
```markdown
You review code.
```

### 6. Use Colors for Visual Distinction
Colors help you quickly identify agents in the UI.

### 7. Test Your Agents
Try them out in different scenarios to ensure they behave as expected.

---

## Workflow Tips

### Switching Between Agents

In a session:
1. Press **Tab** to cycle through primary agents
2. Type `@agent-name` to invoke a subagent
3. Use `↑`/`↓` to navigate between parent/child sessions when subagents create them

### Permission Prompts

When an agent needs to perform an action requiring permission:
1. OpenCode shows a prompt with the command/action
2. Options: Allow Once, Always, Deny Once, Deny Always
3. Choices are remembered based on permission rules

### Debt Configuration

If a permission is set to `"ask"`, you'll be prompted. If you want to completely prevent an action, use `"deny"`. If you want hands-off operation, use `"allow"`.

---

## Troubleshooting

### Agent Not Showing Up
- Check that `mode` includes `subagent` or `primary` as appropriate
- For subagents, ensure `hidden: true` isn't set (unless intended)
- Verify the agent file is in the correct directory (.opencode/agents or ~/.config/opencode/agents)

### Agent Not Using Expected Model
- Primary agents: Check global `model` setting if not specified
- Subagents: They inherit the primary agent's model unless explicitly set
- Verify the model ID is correct and available (`opencode models`)

### Tools Not Available
- Check the `tools` configuration
- Check global tool settings (agent-specific overrides)
- Ensure the tool exists and is correctly named

### Permission Issues
- Remember: `"ask"` will prompt, `"deny"` blocks, `"allow"` auto-approves
- Check rule order for bash permissions (last match wins)
- Use `opencode permission` command to view current settings

---

## Summary

Agents are a powerful way to customize OpenCode for your workflow:

- **Primary agents:** Main assistants you switch between with Tab
- **Subagents:** Specialized helpers invoked with `@name`
- **Configuration:** JSON in opencode.json or markdown files
- **Key settings:** model, temperature, tools, permissions, prompt
- **Create with:** `opencode agent create` or manual file creation
- **Use:**
  - Tab to switch primary agents
  - `@subagent` to invoke subagents
  - Navigate sessions with arrow keys

Start with the built-in agents, then create custom ones for your specific needs. Keep agents focused, set appropriate permissions, and provide clear prompts.

---

## Next Steps

1. Try the built-in agents (`build`, `plan`, `explore`, `general`)
2. Use `opencode agent create` to make your first custom agent
3. Experiment with different models and temperatures
4. Fine-tune permissions based on your comfort level
5. Create agent-specific prompts for specialized tasks

Remember: Agents are your customizable workflow tools. Tailor them to match how you work!