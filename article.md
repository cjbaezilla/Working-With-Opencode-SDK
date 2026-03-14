# Understanding OpenCode Agents: A Comprehensive Guide

## Executive Summary

Traditional AI assistants force you to choose between versatility and precision, often delivering generic answers that require extensive manual refinement. OpenCode agents eliminate this compromise by replacing a single general-purpose assistant with a coordinated team of specialists, each engineered for specific development tasks such as coding, testing, documentation, and deployment.

This approach mirrors the structure of high-performing development teams, where specialists collaborate seamlessly to deliver better results. With OpenCode, you gain granular control over your AI toolkit, assigning the right agent to each task based on expertise, permissions, and configuration.

In this guide, you will learn to build and customize your own agent team from the ground up. We cover the fundamentals of agent architecture, the distinction between primary agents and subagents, and when to use built-in options versus creating custom ones. You'll master configuration techniques, explore practical use cases across development scenarios, and adopt best practices for designing agents that integrate perfectly with your workflow.

By the end, you'll be equipped to transform your development process with a tailored AI team that executes tasks precisely, maintains clear boundaries, and dramatically improves your productivity.

## What Agents Actually Are

At their core, agents are specialized AI assistants that live inside OpenCode. I like to think of them as different personas or experts that we can summon depending on what kind of work we need done. One agent might be a coding specialist that writes and modifies files with precision. Another might be a careful planner that analyzes our code and suggests improvements without touching anything. A third could be a security focused auditor that hunts for vulnerabilities across our codebase.

Each agent carries its own set of defining characteristics. Every agent has a system prompt, which is a set of instructions that tells it how to behave and what to focus on. Each one can be powered by a different AI model, so we might use a fast lightweight model for quick analysis and a more capable model for complex implementation work. Agents also have distinct tool access, meaning some can write files while others are restricted to reading only. Permissions govern what actions an agent is allowed to take and whether it needs our approval first. Finally, configuration settings like temperature and maximum steps give us fine grained control over how the agent responds and how long it can work before stopping.

This separation is valuable because it gives us control. We never have to worry about an agent that is supposed to analyze our code accidentally rewriting something. We can trust that each agent operates within the boundaries we set for it.

## Primary Agents and Subagents

OpenCode organizes its agents into two fundamental categories, and understanding the distinction between them is important for making the most of the system.

### Primary Agents

Primary agents are the main assistants we interact with directly during a session. They handle our primary conversation and serve as the starting point for whatever work we want to do. I think of them as the captains of our development workflow. We switch between primary agents using the Tab key, which makes it quick and natural to change our approach mid conversation. The two built in primary agents that ship with OpenCode are Build and Plan. Build gives us full access to all tools for writing, editing, and executing code. Plan restricts us to read only operations, making it ideal for analysis and review without any risk of unintended changes.

### Subagents

Subagents work differently. They are specialized assistants that we invoke for specific tasks rather than interacting with them as our main conversation partner. We call them by typing an at sign followed by their name, like at explore or at general. They can also be triggered automatically by primary agents when the primary agent recognizes that a specialized task would benefit from focused attention. The built in subagents include General, which is a versatile research and task execution agent, and Explore, which is a fast read only agent designed for navigating codebases efficiently.

I find the subagent model particularly useful for parallel work. When I am in the middle of implementing something with the Build agent, I can ask it to delegate a research task to the General subagent while I continue working. This keeps momentum going and reduces the time I spend context switching.

## Built in Agents

OpenCode ships with several pre configured agents that cover the most common development scenarios.

The Build agent is the default primary agent. It has full access to every tool available, including writing files, editing existing code, executing shell commands, reading files, searching content, and fetching web resources. When I need to create a new feature, fix a bug, or make structural changes to a project, Build is the agent I reach for. It handles the complete development lifecycle from creation through testing.

The Plan agent serves as the analytical counterpart to Build. By default, it cannot write files, edit code, or run commands. This makes it perfect for situations where I want feedback and suggestions without any risk of accidental modifications. I use Plan when I want to review code, analyze architecture decisions, or get recommendations before committing to an implementation approach. It provides the thinking without the doing.

The General subagent is a workhorse for research and multi step tasks. It has full tool access similar to Build, but it operates as a subagent rather than a primary agent. When I invoke General, I am typically asking it to investigate a complex question, gather information from multiple sources, and potentially implement changes based on what it finds. It is the agent I turn to when a task requires both investigation and action.

The Explore subagent is optimized for speed and safety. It operates in read only mode, which means it can search through files, find patterns, and answer questions about the codebase without any possibility of making changes. When I need to locate where a particular function is defined, find all instances of a specific pattern, or understand the structure of an unfamiliar codebase, Explore gets me answers quickly.

Beyond these visible agents, OpenCode also includes several hidden system agents that work behind the scenes. The Compaction agent automatically summarizes long conversations when context gets too large. The Title agent generates short descriptive titles for sessions. The Summary agent creates session summaries. These agents run without our direct involvement and help keep things organized.

## How Agents Work Together

What makes the agent system truly powerful is how the different agents collaborate. A typical workflow in my experience looks something like this. I start with the Build agent because that is my primary development assistant. Before diving into implementation, I might ask Build to invoke the Plan subagent to analyze the codebase and suggest an approach. The Plan agent does its analysis, reading files and examining the current state of things, then presents its findings without modifying anything. I review those suggestions and decide on a course of action. Once I am ready to implement, Build takes over and starts making changes. During implementation, Build might invoke the Explore subagent to find existing code patterns or locate files it needs to reference. After the implementation is complete, I can switch to the Plan agent myself using the Tab key to review what was done and verify it matches my expectations.

This back and forth between agents feels natural once you get used to it. Each agent plays to its strengths, and the transitions between them are smooth.

## Configuring Agents

One of the strengths of the OpenCode agent system is its flexibility in configuration. We can customize existing agents or create entirely new ones using two different approaches.

### JSON Configuration

The first approach uses the opencode.json file, which typically lives in our project root or in the global configuration directory. We define agents under the agent key, and each agent gets its own configuration object. The JSON format gives us access to every configuration option and is well suited for complex setups where we need fine grained control. We can specify the agent mode, choose which AI model it uses, set the temperature, control which tools it can access, define permission rules, and provide custom prompts. The JSON schema also supports comments if we use the JSONC variant, which helps keep our configuration readable as it grows.

### Markdown Configuration

The second approach uses markdown files, which I find more readable and easier to maintain for individual agents. We place these files in either the global agents directory or the project specific agents directory. The filename becomes the agent name, so a file called security auditor dot md creates an agent named security auditor. The markdown format uses a frontmatter section between triple dashes at the top of the file to specify configuration options. Everything below the frontmatter becomes the agent system prompt. This separation between configuration and prompt makes the markdown approach particularly clean for agents that need detailed instructions.

Both approaches work well, and the choice between them often comes down to personal preference and the complexity of what we are trying to configure. For a single agent with a long detailed prompt, I prefer the markdown approach. For managing multiple agents in one place or when I need programmatic access to the configuration, the JSON approach is more convenient.

## Configuration Options in Detail

Understanding the available configuration options is essential for creating agents that behave exactly the way we want.

The description option is a required field that provides a brief explanation of what the agent does and when to use it. This description appears in the user interface and helps us choose the right agent from the autocomplete menu. A good description is specific and actionable, like "Reviews code for security vulnerabilities and reports findings with severity ratings," rather than something vague like "Security agent."

The mode option determines how an agent can be used. Setting it to primary makes the agent available as a main assistant that we can switch to with the Tab key. Setting it to subagent means the agent can only be invoked with the at sign mention or by other agents. The default value is all, which allows the agent to function in both capacities. I recommend being explicit about mode to avoid confusion about how each agent should be used.

The prompt option lets us specify a custom system prompt for the agent. In JSON configuration, we can reference an external file using the file syntax. In markdown configuration, the prompt goes directly below the frontmatter. The system prompt is where the real personality and behavior of the agent gets defined, so investing time in writing clear detailed prompts pays off significantly.

The model option allows us to override which AI model powers a particular agent. The format is provider slash model identifier. This is useful when we want to use different models for different tasks. A fast model like Haiku works well for planning and analysis where speed matters more than depth. A more capable model like Sonnet or GPT 4 is better for complex implementation tasks. If we do not specify a model, primary agents fall back to the globally configured model while subagents inherit the model of the primary agent that invoked them.

The temperature option controls how random and creative the agent responses are. Values range from zero to one. Lower temperatures between zero and two tenths produce focused deterministic responses that are ideal for code analysis and planning. Temperatures between three tenths and five tenths strike a good balance for general development work. Higher temperatures between six tenths and one produce more varied creative responses that work well for brainstorming and exploration. I tend to keep temperature low for agents that need precision and raise it slightly for agents where some creativity is welcome.

The top p option provides an alternative way to control response diversity. Like temperature, it ranges from zero to one, with lower values producing more focused responses. Some developers prefer top p over temperature because it can produce more consistent results in certain scenarios.

The steps option controls how many agentic iterations an agent can perform before it is forced to stop making tool calls and respond with text only. This is a useful cost control mechanism. When an agent reaches its step limit, it receives instructions to summarize its work and suggest remaining tasks. Setting steps to a reasonable number like five or ten prevents runaway execution while still giving the agent enough room to accomplish meaningful work.

The disable option lets us turn off an agent entirely by setting it to true. This is handy for temporarily removing experimental agents from the available list without deleting their configuration.

The hidden option applies only to subagents and removes them from the at sign autocomplete menu. Hidden agents can still be invoked programmatically by other agents through the Task tool. This is useful for internal helper agents that support other agents but should not be directly accessed by users.

The tools option gives us control over which tools an agent can use. We can enable or disable individual tools like write, edit, bash, read, grep, glob, task, and webfetch. Setting a tool to true allows the agent to use it, while setting it to false prevents access entirely. We can also use wildcards to control groups of tools, such as disabling all tools from a specific MCP server by using the server name prefix followed by an asterisk. Agent specific tool configuration overrides any global tool settings, giving us precise control at the individual agent level.

The permission option provides a more nuanced layer of control beyond simply enabling or disabling tools. For tools like edit, bash, and webfetch, we can set permissions to ask, allow, or deny. The ask setting prompts us for approval before the tool runs. The allow setting lets the tool execute without prompting. The deny setting blocks the tool entirely. For bash commands specifically, we can define granular rules using glob patterns. For example, we might allow git status and git diff without prompting but require approval for git push. The last matching rule takes precedence, so we typically place general rules first and specific overrides after them. Task permissions control which subagents an agent can invoke through the Task tool, also using glob patterns for flexible matching.

The color option lets us assign a visual identifier to each agent in the user interface. We can use hex color codes or named theme colors like primary, secondary, accent, success, warning, error, and info. Colors help us quickly distinguish between agents when switching or reviewing which agent is active.

Any additional options we specify beyond the standard ones are passed directly through to the AI provider as model specific parameters. This allows us to leverage provider specific features like OpenAI reasoning effort settings without waiting for explicit OpenCode support.

## Creating Custom Agents

When the built in agents do not cover our needs, creating custom agents is straightforward.

### Using the Interactive Wizard

OpenCode provides an interactive creation command that walks us through the process step by step. The wizard asks where we want to save the agent, either globally or for a specific project. It then prompts for a description of what the agent should do. Based on our description, it suggests an appropriate agent name and lets us select which tools the agent should have access to. Finally, it generates the configuration file with sensible defaults that we can refine later. I recommend this approach for anyone creating their first custom agent because it handles the structural details while we focus on describing what we want.

### Manual Creation

For more control, we can create agents manually using either the JSON or markdown approach described earlier. With the JSON method, we add a new entry to the agent object in our opencode.json file. With the markdown method, we create a new dot md file in the agents directory. Either way, we need to provide at minimum a description and decide on the mode, tools, and permissions. The manual approach is faster once we are familiar with the configuration options and gives us complete control from the start.

## Practical Use Cases

The agent system really shows its value when we look at practical applications. Here are several scenarios where custom agents make a meaningful difference in development workflow.

### Security Auditing

A security auditor agent operates as a subagent with read only access plus bash capabilities for running diagnostic commands. It cannot write or edit files, which ensures it never accidentally modifies code during an audit. The system prompt instructs it to focus on common vulnerability categories like injection attacks, authentication weaknesses, authorization flaws, data exposure, dependency vulnerabilities, and configuration issues. When invoked, it systematically examines the codebase, identifies potential problems, rates them by severity, and provides specific remediation guidance. I have found this kind of focused security review invaluable for catching issues that general purpose agents might overlook.

### Documentation Writing

A documentation writer agent has the ability to create and edit files but typically does not need bash access. Its system prompt focuses on documentation best practices including audience awareness, clear structure, practical examples, progressive disclosure, and user friendly language. It can create README files, API documentation, architecture overviews, tutorials, code comments, and contributing guidelines. The slightly higher temperature setting gives it enough flexibility to write naturally while the focused prompt keeps it on topic. When I need to document a new feature or update existing documentation, this agent handles the heavy lifting while I review and refine the output.

### Code Review

A code reviewer agent operates in read only mode with carefully controlled bash permissions that allow it to run git diff and git log commands for reviewing changes. It analyzes code across multiple dimensions including correctness, readability, performance, maintainability, and security. Its output follows a structured format that categorizes issues by severity, provides specific file and line references, suggests concrete improvements, and highlights positive aspects of the code. I appreciate that a good code reviewer agent balances criticism with encouragement, making the review process constructive rather than discouraging.

### Debugging

A debug assistant agent can read files and run diagnostic commands but cannot modify code. Its methodology follows a systematic approach starting with symptom gathering, moving through log analysis and reproduction attempts, forming hypotheses about root causes, testing those hypotheses, and finally proposing fixes. The agent keeps us informed of its investigation steps, which helps us learn from the debugging process and builds confidence in its findings. When I encounter an unfamiliar error, having a methodical debugging partner saves considerable time compared to random searching.

### Database Management

A database specialist agent focuses on query optimization, schema design, migration planning, and performance tuning. It understands the nuances of different database systems and can write efficient queries, suggest appropriate indexes, review execution plans, and plan safe migrations with rollback strategies. The agent enforces best practices like using parameterized queries, considering performance at scale, and testing migrations before deployment. For projects with complex data layers, having a dedicated database expert agent reduces the likelihood of performance problems and data integrity issues.

## Best Practices for Agent Design

Creating effective agents requires thoughtful design. Over time, I have developed several principles that lead to better results.

Starting simple is important. It is tempting to build an agent that does everything, but focused agents perform better. Begin with a narrow purpose and add complexity only when the need becomes clear. A simple code review agent that checks for basic issues is more useful than an overly complex one that tries to address every possible concern and ends up doing none of them well.

Choosing appropriate models matters more than people expect. Fast cheap models work well for tasks that require quick responses like exploration and simple analysis. More capable models are worth the extra cost for complex reasoning tasks like security auditing and detailed code review. Matching the model to the task improves both quality and efficiency.

Controlling permissions carefully protects us from unintended consequences. Starting with the ask setting for potentially destructive tools gives us visibility into what agents are doing. As we gain confidence in an agent behavior, we can relax permissions to allow for more autonomous operation. Using glob patterns for bash permissions lets us allow safe commands automatically while requiring approval for risky ones.

Writing clear descriptions helps us and other team members choose the right agent. A description like "Writes API documentation with OpenAPI specifications and usage examples" is immediately understandable, while something like "Documentation agent" leaves us guessing about its actual capabilities.

Crafting focused prompts is where the real work happens. A good prompt tells the agent exactly what role to play, what areas to focus on, what output format to use, and what principles to follow. Vague prompts produce vague results. Investing time in prompt engineering pays dividends in agent performance.

Using colors for visual distinction sounds minor but makes a real difference in daily use. When we can glance at the interface and immediately see which agent is active, we make fewer mistakes and work more confidently.

Testing agents in different scenarios reveals edge cases and unexpected behaviors before they cause problems in real work. Trying an agent on a small safe task first builds understanding of its capabilities and limitations.

## Workflow Tips

Getting the most out of the agent system requires developing good habits around how we use agents in practice.

Switching between agents during a session is as simple as pressing the Tab key for primary agents. For subagents, typing the at sign followed by the agent name brings up an autocomplete menu that makes selection easy. When subagents create child sessions, we navigate between them using arrow keys, with down or a leader combination entering the first child session, left and right cycling between siblings, and up returning to the parent. This navigation pattern becomes second nature after a few sessions.

Understanding permission prompts helps us make good decisions quickly. When an agent needs approval for an action, the prompt shows exactly what it wants to do. We can choose to allow once, always allow, deny once, or always deny. Our choices are remembered based on the permission rules, so we do not have to answer the same question repeatedly. If we find ourselves approving the same action frequently, that is a signal to update our permission configuration.

Configuring permissions thoughtfully from the start reduces interruptions later. If we want completely hands off operation, we set permissions to allow. If we want to review every action, we use ask. If we want to prevent certain actions entirely, we use deny. Most developers find a middle ground where common safe operations are allowed automatically while potentially risky operations require approval.

## Troubleshooting Common Issues

When things do not work as expected, there are a few common causes worth checking.

If an agent does not appear in the available list, the mode setting is the first thing to verify. A subagent will not show up as a primary agent and vice versa. For subagents, checking that hidden is not set to true unless that is intentional is also important. Verifying that the agent file is in the correct directory, either the project specific agents folder or the global agents folder, rules out path issues.

If an agent is not using the expected AI model, the configuration chain is worth examining. Primary agents use the globally configured model when no agent specific model is set. Subagents inherit the model from the primary agent that invoked them unless they have their own model specified. Running the models command confirms which models are actually available and helps catch typos in model identifiers.

If tools that should be available are not working, the tools configuration at both the agent and global level should be checked. Agent specific tool settings override global settings, so an agent might have a tool disabled even if it is enabled globally. Ensuring tool names are spelled correctly and that the tools actually exist in the current setup resolves most of these issues.

Permission issues usually come down to understanding the three permission levels. The ask setting will always prompt, which might feel like the tool is not working if we expect automatic execution. The deny setting blocks completely with no prompt. The allow setting runs without asking. For bash permissions, remembering that the last matching rule wins helps us debug unexpected permission behavior. Checking the order of our rules often reveals the cause.

## Conclusion

The OpenCode agent system offers a flexible and powerful way to customize our development workflow. By understanding the distinction between primary agents and subagents, learning the available configuration options, and following best practices for agent design, we can create a personalized toolkit that matches how we actually work. The built in agents cover common scenarios well, and the ability to create custom agents means we are never limited by what comes out of the box. Starting with the built in agents, experimenting with configuration, and gradually building custom agents as specific needs emerge is a practical path that leads to real productivity gains. The investment in learning the agent system pays off quickly in the form of faster development cycles, fewer mistakes, and more confident code review and analysis.
