# Glossary

> **How to Use This Glossary:** This glossary defines every specialized term used throughout the project documentation. Terms are listed alphabetically. When you encounter an unfamiliar term in any of the other docs (README, Quick Start Guide, Developer Guide, User Guide, Plugin Architecture, Agent Team Guide, Troubleshooting, or Build Report), look it up here. Where helpful, C or Java analogies are provided in parentheses to bridge your existing programming knowledge to these new concepts. Terms that appear **in bold within a definition** have their own entries in this glossary.

---

## Terms

**Agent Team**
A group of AI **worker agents** coordinated by a **team lead** agent to complete a complex task collaboratively. Each agent has a specialized role and works on an assigned portion of the work, similar to threads in a multi-threaded program where each thread handles a different subtask. In this project, a team of 6 agents built the entire plugin in parallel.

**API (Application Programming Interface)**
A defined set of rules that allows one piece of software to communicate with another. In the context of **LLMs**, an API lets your code send text prompts to a model and receive generated text back. (Similar to calling a library function in C/Java -- you provide input parameters and get a return value, except the "function" runs on a remote server.)

**Artifact**
Any file or output produced by a **skill** or **command** during execution. For this plugin, artifacts are typically Markdown files (content reports, post drafts, title lists) saved to the user's workspace. (Analogous to build artifacts in Java -- the compiled outputs produced by a build process.)

**$ARGUMENTS**
A built-in variable available inside **command** Markdown files. When a user runs a slash command and types additional text after it, that text is substituted into `$ARGUMENTS`. (Similar to `argv` in C or `String[] args` in Java's `main` method -- it captures what the user passed in.)

**Claude**
An AI assistant built by Anthropic. Claude is the **LLM** that powers Claude Code and Claude Cowork. It processes natural language instructions, generates text, writes code, and follows structured **skill** definitions. (Think of it as a very advanced text-processing engine that understands and generates human language.)

**Claude Code**
A command-line interface (**CLI**) tool that lets developers interact with **Claude** directly from a terminal. Claude Code can read files, write code, run commands, and operate as an agent team. It is the tool used to build this plugin. (Similar to a compiler's CLI -- you invoke it from your terminal, pass it instructions, and it produces output.)

**Claude Cowork**
A collaborative workspace application where users interact with **Claude** through a graphical interface. Claude Cowork supports **plugins** that extend Claude's capabilities with custom **commands** and **skills**. This is where the content-repurposing plugin is installed and used.

**CLI (Command Line Interface)**
A text-based interface where you type commands and see text output, as opposed to a graphical interface with buttons and windows. If you have used `gcc`, `javac`, `make`, or `git` from a terminal, you have used a CLI. **Claude Code** is a CLI tool.

**Command (Plugin Command)**
A user-triggered action defined in a Markdown file inside the `commands/` directory of a **plugin**. Commands appear as **slash commands** in the Claude Cowork interface. Each command file contains YAML **frontmatter** (metadata) and a body of instructions that tell **Claude** what to do. (Analogous to a function that the user can call by name -- like typing a function signature to invoke it.)

**Connector**
A component that links one system to another, enabling data to flow between them. In plugin architectures, connectors might link Claude to external services, databases, or APIs. (Similar to database connectors or JDBC drivers in Java -- they bridge two systems that do not natively communicate.)

**Frontmatter**
A block of metadata at the top of a Markdown file, enclosed between two lines of three dashes (`---`). Written in **YAML** format, frontmatter defines properties like `name`, `description`, and `disable-model-invocation`. The rest of the file after the frontmatter is the actual content. (Similar to header comments or annotations in Java that provide metadata about a class without being part of the executable logic.)

```yaml
---
name: my-skill
description: A short explanation of what this skill does
---
# The actual content starts here
```

**JSON (JavaScript Object Notation)**
A lightweight text format for storing structured data using key-value pairs, arrays, and nested objects. The plugin **manifest** (`plugin.json`) is written in JSON. (Similar to a struct in C or a HashMap in Java, but stored as a text file. Curly braces `{}` denote objects, square brackets `[]` denote arrays.)

```json
{
  "name": "content-repurposing",
  "version": "1.0.0"
}
```

**kebab-case**
A naming convention where words are lowercase and separated by hyphens. Example: `content-extraction`, `linkedin-post`, `twitter-thread`. This project uses kebab-case for all directory names and file names. (Contrast with camelCase used in Java variables or snake_case used in C.)

**LLM (Large Language Model)**
A type of AI trained on vast amounts of text that can understand and generate human language. **Claude** is an LLM. LLMs work by predicting what text should come next given an input, but modern LLMs like Claude can follow complex instructions, reason about problems, and produce structured output. (There is no direct C/Java analogy -- LLMs are a fundamentally new category of software that processes language probabilistically rather than following deterministic logic.)

**Manifest**
A metadata file that declares a plugin's identity and properties. For Claude Cowork plugins, the manifest is `.claude-plugin/plugin.json` and contains the plugin's name, version, description, author, and keywords. (Similar to a `pom.xml` in Maven or a `package.json` in Node.js -- it tells the host system what this package is and how to handle it.)

**Markdown**
A plain-text formatting language that uses simple symbols to indicate structure: `#` for headings, `**` for bold, `-` for bullet lists, `|` for tables. All **commands** and **skills** in this plugin are written in Markdown. Markdown files use the `.md` extension. (Similar to writing HTML but much simpler -- `# Heading` instead of `<h1>Heading</h1>`.)

**MCP (Model Context Protocol)**
An open protocol that standardizes how AI models connect to external tools and data sources. MCP allows **Claude** to interact with file systems, APIs, databases, and other services through a common interface. (Analogous to JDBC in Java -- a standard protocol that lets your code talk to many different databases through one consistent interface.)

**Model**
In the AI context, a model is the trained neural network that processes input and generates output. When people say "Claude Opus 4.6", they are referring to a specific model. Different models have different capabilities, speeds, and costs. (Loosely analogous to different versions of a compiler -- each version may optimize differently or support different features, but they all compile code.)

**Opus / Sonnet / Haiku**
The three tiers of Claude models, each offering a different balance of capability and speed. **Opus** is the most capable (used as the team lead in this project). **Sonnet** balances capability with speed. **Haiku** is the fastest and most lightweight. (Analogous to compiler optimization levels: `-O0` for fast compilation, `-O2` for balanced, `-O3` for maximum optimization.)

**Permission Mode**
A security setting in **Claude Code** that controls what actions agents can take without asking the user for approval. Options range from requiring approval for every action to `bypassPermissions` (used in this project's agent build) which lets agents act freely. (Similar to file permissions in Unix/Linux -- read, write, execute -- but applied to AI agent actions.)

**Plugin**
A self-contained package of **commands** and **skills** that extends Claude Cowork's functionality. A plugin has a specific directory structure: a `.claude-plugin/` folder with a **manifest**, a `commands/` folder, and a `skills/` folder. Plugins are distributed as zip files. (Similar to a shared library or .jar file in Java -- a packaged unit of functionality that plugs into a host application.)

**Prompt**
The text input given to an **LLM** to instruct it on what to do. A prompt can be a question, a set of instructions, or a document to analyze. In this plugin, **skill** files are essentially detailed prompts that tell Claude how to perform a specific task. (Similar to command-line arguments or configuration files that tell a program how to behave, but written in natural language.)

**Skill**
A reusable set of instructions that **Claude** can automatically invoke when relevant. Skills are defined in `SKILL.md` files inside `skills/<skill-name>/` directories. Unlike **commands** (which users trigger explicitly), skills are selected by Claude based on context. Each skill defines a persona, a step-by-step process, output format, and quality standards. (Analogous to a class with methods in Java -- it encapsulates a specific capability that the system can call when needed.)

**SKILL.md**
The specific filename used for skill definition files in a Claude Cowork **plugin**. Each skill lives in its own directory (e.g., `skills/linkedin-post/SKILL.md`). The file contains **YAML frontmatter** (with `name` and `description`) followed by the full skill instructions in **Markdown**.

**Slash Command**
A command triggered by the user by typing a forward slash (`/`) followed by the plugin name and command name, for example: `/content-repurposing:extract`. Slash commands appear in an autocomplete menu in Claude Cowork. (Similar to CLI commands -- you type a specific name to invoke a specific action.)

**Sub-agent**
A **worker agent** spawned by **Claude Code** to handle a specific subtask within an **agent team**. Sub-agents operate independently but coordinate through the task system and messaging. In this project, 6 sub-agents were spawned by the team lead. (Analogous to child threads spawned by a main thread in a multi-threaded C/Java program.)

**Task (Agent Team Task)**
A discrete unit of work assigned to a **worker agent** within an **agent team**. Tasks have an ID, a description, a status (pending, in_progress, completed), an owner (the assigned agent), and dependency relationships with other tasks. (Similar to a work item in a task queue or a Runnable in Java's thread pool -- a self-contained unit of work that can be tracked and managed.)

**Task Dependency**
A relationship between two **tasks** where one task cannot start until another task finishes. In this project, Tasks #2 through #6 all depended on Task #1 (directory creation). The team lead defines dependencies when creating the task list. (Analogous to build dependencies in Makefiles or Maven -- target B cannot compile until target A finishes.)

**Team Lead**
The coordinating **agent** in an **agent team** that designs the plan, creates **tasks**, assigns work to **worker agents**, manages **task dependencies**, monitors progress, and shuts down agents when work is complete. In this project, the team lead ran on Claude **Opus** 4.6. (Analogous to the main thread in a multi-threaded program that spawns worker threads and joins them when done.)

**Worker Agent**
A **sub-agent** in an **agent team** that performs a specific assigned **task**. Worker agents have access to tools (Read, Write, Edit, Bash, Glob, Grep) and communicate with the **team lead** via messages. In this project, all 6 workers were "builder" type agents focused on creating files. (Analogous to worker threads in a thread pool -- each picks up a task and executes it independently.)

**YAML (YAML Ain't Markup Language)**
A human-readable data format used for configuration and metadata. YAML uses indentation and colons to represent structure. In this plugin, YAML appears inside **frontmatter** blocks at the top of Markdown files. (Similar to properties files or `.ini` files in C/Java projects, but supports nested structures.)

```yaml
name: content-extraction
description: Extracts content ideas from long-form content
```

**Zip (Plugin Packaging)**
The distribution format for Claude Cowork **plugins**. To share or upload a plugin, you compress the entire plugin directory (including `.claude-plugin/`, `commands/`, and `skills/`) into a single `.zip` archive. (Analogous to a `.jar` file in Java or a `.tar.gz` archive in C projects -- a compressed package containing all files needed to use the software.)

---

*See also: [Plugin Architecture](PLUGIN_ARCHITECTURE.md) for how these concepts fit together, and [Developer Guide](DEVELOPER_GUIDE.md) for hands-on instructions.*
