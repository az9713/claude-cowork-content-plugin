# Developer Guide

**How to Use Claude Opus 4.6 Agent Teams to Build a Claude Cowork Plugin**

---

## 1. Welcome and Overview

This guide is a comprehensive developer handbook for understanding, customizing, and building Claude Cowork plugins. It walks you through the content-repurposing plugin that six AI agents built in approximately five minutes, and then shows you how to modify it, extend it, and create your own plugins from scratch.

**Who this guide is for.** You are a developer with experience in C, C++, Java, or similar compiled/statically-typed languages. You may be completely new to AI tools, large language models (LLMs), plugin architectures based on Markdown, YAML configuration, and agent-based workflows. This guide assumes zero knowledge of those topics and explains every concept before using it.

**What you will be able to do after reading this.** You will understand how a Claude Cowork plugin is structured, how commands route to skills, how to modify existing skills, how to create entirely new skills and commands, how to test and package your plugin, and how to use agent teams to accelerate development.

**Time estimate.** Reading this guide takes approximately 1-2 hours. After that, hands-on practice with the exercises will take another 1-2 hours. By the end of the day, you will be comfortable building your own plugins.

---

## 2. Prerequisites and Setup

### 2.1 What You Need to Install

Before you can work with this plugin, you need a few tools. For each one, we explain what it IS, then how to install it.

---

#### Claude Code (CLI)

**What it is.** Claude Code is a command-line interface for Claude AI. If you are familiar with `javac` (the Java compiler) or `gcc` (the C compiler), think of Claude Code as a command-line tool that lets you interact with an AI directly from your terminal. Instead of compiling source code, it sends your instructions to Claude and returns the results.

**What it does.** It runs Claude in your terminal, supports loading plugins from local directories, and can orchestrate agent teams (multiple instances of Claude working together on a project).

**How to install it.**

1. Install Node.js version 18 or later from [https://nodejs.org/](https://nodejs.org/). Node.js is a JavaScript runtime -- think of it as the JVM but for JavaScript. You need it because Claude Code is built on it.

2. Open your terminal and run:
   ```bash
   npm install -g @anthropic-ai/claude-code
   ```
   The `npm` command is Node's package manager, similar to Maven's `mvn` or Python's `pip`. The `-g` flag installs it globally (system-wide), like adding a program to your PATH.

3. Run `claude` once and follow the authentication prompts to link your Anthropic account.

**How to verify it works.**
```bash
claude --version
```
You should see a version number printed. If you get "command not found", Node.js may not be in your PATH.

---

#### Claude Cowork (Desktop App)

**What it is.** Claude Cowork is a graphical desktop application for working with Claude. If Claude Code is like running `javac` from the terminal, Claude Cowork is like using IntelliJ IDEA or Eclipse -- a GUI with menus, panels, and visual plugin management.

**What it does.** It provides a visual conversation interface with Claude, a file workspace panel, and a plugin management system where you can install, enable, and configure plugins.

**How to install it.** Download Claude Cowork from Anthropic's official website and follow the installer for your operating system.

**How it differs from Claude Code.** Both use the same underlying Claude AI. Claude Code is text-only in the terminal; Cowork is a GUI application. Cowork has a built-in plugin manager with a graphical interface for installing and managing plugins. Claude Code loads plugins via the `--plugin-dir` flag. You can develop with Claude Code and then deploy to Cowork, or work entirely in one or the other.

---

#### Text Editor

Any code editor works. VS Code is recommended because it has excellent Markdown preview, JSON validation, and YAML syntax highlighting. You will be editing `.md` (Markdown) and `.json` files.

If you use VS Code, consider installing the "YAML" extension by Red Hat for syntax checking in frontmatter sections.

---

#### Git (Optional but Recommended)

Git works on `.md` and `.json` files exactly the same way it works on `.c`, `.java`, or any other text file. Use it for version control as you would with any project. No special configuration is needed.

---

### 2.2 Concepts You Already Know (That Apply Here)

If you have experience with C, C++, or Java, many of the concepts in this plugin system have direct equivalents. This table maps what you already know to the new terminology.

| You Know (C/Java)            | New Equivalent          | What It Does                                                      |
|------------------------------|-------------------------|-------------------------------------------------------------------|
| Library / JAR / Package      | Plugin                  | A bundle of related functionality distributed as a unit           |
| `pom.xml` / `build.gradle`  | `plugin.json`           | Declares metadata: name, version, description, author             |
| Interface / API endpoint     | Command (`.md` file)    | An entry point that users invoke by name                          |
| Service / Implementation class | Skill (`SKILL.md`)   | Contains the actual logic (written as natural language instructions)|
| Method signature             | YAML frontmatter        | Declares name, description, and configuration flags               |
| Method body                  | Markdown body           | The actual instructions that Claude follows                       |
| Annotations (`@Override`)    | Frontmatter fields      | Metadata that controls behavior (e.g., `disable-model-invocation`)|
| Function parameters          | `$ARGUMENTS`            | User-provided input passed into the command                       |
| Build system (Maven/Gradle)  | `zip` + upload          | Packaging the plugin for distribution                             |
| Unit tests / local run       | `claude --plugin-dir`   | Local testing before deployment                                   |
| Project team / dev team      | Agent Team              | Multiple AI agents coordinating on a build task                   |

The biggest conceptual shift: in Java, your "code" is compiled and executed by a machine. In a Claude plugin, your "code" is Markdown text that Claude reads and follows as instructions. There is no compiler, no bytecode, no runtime in the traditional sense. The AI IS the runtime.

---

## 3. Understanding the Project Structure

### 3.1 Directory Layout

Here is the complete directory tree for this project, with annotations explaining what each file and folder does.

```
claude_cowork_alex_mcfarland/              <- Project root (the repository)
|
+-- CLAUDE.md                              <- Claude Code memory file (loaded every session)
+-- README.md                              <- Project overview and navigation hub
|
+-- content-repurposing/                   <- The plugin itself (this is what gets packaged)
|   +-- .claude-plugin/
|   |   +-- plugin.json                    <- Plugin manifest (like pom.xml)
|   +-- commands/                          <- User-triggered commands (like controller methods)
|   |   +-- all.md                         <- Lists all available commands and skills
|   |   +-- extract.md                     <- Extracts content ideas into a structured table
|   |   +-- linkedin.md                    <- Generates LinkedIn posts
|   |   +-- quotes.md                      <- Extracts quotable statements
|   |   +-- titles.md                      <- Generates title variations
|   |   +-- twitter.md                     <- Creates Twitter/X threads
|   +-- skills/                            <- AI behavior definitions (like service classes)
|       +-- content-extraction/
|       |   +-- SKILL.md                   <- Full content extraction across platforms
|       +-- key-takeaways/
|       |   +-- SKILL.md                   <- Summarizes key takeaways into bullet lists
|       +-- linkedin-post/
|       |   +-- SKILL.md                   <- Creates LinkedIn posts with hooks and CTAs
|       +-- quote-extractor/
|       |   +-- SKILL.md                   <- Finds and formats quotable statements
|       +-- substack-note/
|       |   +-- SKILL.md                   <- Generates Substack Notes for social feed
|       +-- title-generator/
|       |   +-- SKILL.md                   <- Generates titles across multiple styles
|       +-- twitter-thread/
|           +-- SKILL.md                   <- Creates Twitter/X threads with hooks
|
+-- docs/                                  <- Documentation (you are reading one of these)
    +-- AGENT_TEAM_BUILD_REPORT.md         <- Detailed log of the 5-minute build process
    +-- AGENT_TEAM_GUIDE.md               <- Agent Teams explained from the ground up
    +-- DEVELOPER_GUIDE.md                 <- This file
    +-- GLOSSARY.md                        <- 30+ terms defined for newcomers
    +-- PLUGIN_ARCHITECTURE.md             <- Technical deep-dive into plugin structure
    +-- QUICK_START_GUIDE.md               <- 10+ hands-on use cases
    +-- TROUBLESHOOTING.md                 <- Common issues and fixes
    +-- USER_GUIDE.md                      <- Complete user manual
```

### 3.2 What Each File Does

**Project root files:**

- **CLAUDE.md** -- This is a special file that Claude Code reads at the start of every session. Think of it like a `.bashrc` or `.profile` for AI -- it tells Claude about the project, conventions, and anything it should remember. If you work on this project with Claude Code, any notes stored here persist across sessions.

- **README.md** -- The project's front door. It describes the project, links to all documentation, explains the agent team build story, and provides quick-start instructions.

**The plugin directory (`content-repurposing/`):**

- **`.claude-plugin/plugin.json`** -- The plugin manifest. This is the only file that MUST exist for Claude to recognize a directory as a plugin. It declares the plugin's name, version, description, author, and keywords. Similar to `pom.xml` in Maven or `package.json` in Node.js.

- **`commands/all.md`** -- A reference command that lists all other commands and skills. When the user types `/content-repurposing:all`, Claude reads this file and displays the listing.

- **`commands/extract.md`** -- The extraction command. When invoked, it tells Claude to use the content-extraction skill to analyze source content and produce a structured table of 25+ content ideas.

- **`commands/linkedin.md`** -- Routes to the linkedin-post skill. Instructs Claude to read the source content, ask the user how many posts they want, and generate LinkedIn posts.

- **`commands/quotes.md`** -- Routes to the quote-extractor skill. Instructs Claude to find 10-15 quotable statements and rate them by impact.

- **`commands/titles.md`** -- Routes to the title-generator skill. Instructs Claude to generate 15-20 title variations across different styles.

- **`commands/twitter.md`** -- Routes to the twitter-thread skill. Instructs Claude to create Twitter/X threads with hook tweets, body, and calls to action.

- **`skills/content-extraction/SKILL.md`** -- The most comprehensive skill. Defines a content strategist persona, a 5-step process (Understand Source, Ask User, Extract by Platform, Create Roadmap, Save Output), and detailed quality standards. Produces a full extraction report with tables for newsletters, Substack Notes, Twitter threads, LinkedIn posts, and short-form video.

- **`skills/key-takeaways/SKILL.md`** -- Defines a content analyst persona that distills long-form content into executive summaries, bullet-point takeaways, deep-dive points, and quick-reference summaries.

- **`skills/linkedin-post/SKILL.md`** -- Defines a LinkedIn content strategist persona with specific hook patterns, body structure guidelines, post style variations, and formatting rules.

- **`skills/quote-extractor/SKILL.md`** -- Defines a content curator persona that identifies quotable moments, categorizes them (Headline, Insight, Story, Data), scores them, and formats them for multiple platforms.

- **`skills/substack-note/SKILL.md`** -- Defines a Substack strategist persona that creates 5-7 Notes in various formats (Quick Insight, Question, Hot Take, Teaser, and more).

- **`skills/title-generator/SKILL.md`** -- Defines a headline specialist persona that generates 15-20 titles across five style categories and rates them on clarity, curiosity, SEO potential, and click-worthiness.

- **`skills/twitter-thread/SKILL.md`** -- Defines a Twitter strategist persona with specific thread structures, tweet numbering conventions, the 280-character limit, and thread style variations.

**The docs directory:**

Each documentation file serves a specific audience and purpose. The GLOSSARY.md defines terminology. The TROUBLESHOOTING.md covers common errors. The AGENT_TEAM_GUIDE.md and AGENT_TEAM_BUILD_REPORT.md focus on the agent team build process. The PLUGIN_ARCHITECTURE.md provides a technical deep-dive. The QUICK_START_GUIDE.md gives hands-on exercises. The USER_GUIDE.md is the complete user manual.

### 3.3 The Plugin Directory Explained

The plugin lives inside the `content-repurposing/` directory. Three things make it a valid plugin:

1. **The `.claude-plugin/` directory.** The dot prefix is a convention borrowed from tools like `.git/`, `.vscode/`, and `.gradle/`. It signals "this is a configuration directory, not user content." Inside it, `plugin.json` is the manifest that tells Claude this folder is a plugin and provides its metadata.

2. **The `commands/` directory.** Every `.md` file in this directory becomes an invokable command. The filename (without `.md`) becomes the command name. So `extract.md` becomes the command `/content-repurposing:extract`.

3. **The `skills/` directory.** Each subdirectory contains a `SKILL.md` file that defines a skill. The subdirectory name becomes the skill name. So `linkedin-post/SKILL.md` defines the skill named `linkedin-post`.

Commands and skills are connected by name references in the command body. When `extract.md` says "Use the **content-extraction** skill", Claude looks for a skill directory named `content-extraction` and reads its `SKILL.md`. This is similar to how a Java controller method calls a service by its interface name.

---

## 4. Core Concepts Deep Dive

### 4.1 What is a Plugin?

If you come from Java, think of a plugin as a JAR file -- except instead of compiled bytecode, it contains Markdown files with natural language instructions, and instead of a JVM executing the code, Claude AI reads the instructions and follows them.

A plugin is a directory containing:
- A JSON manifest file (`plugin.json`) that declares metadata
- Markdown files that define commands (entry points) and skills (behavior)
- Nothing else. No compiled code, no dependencies, no build step.

This is the fundamental paradigm shift: **the code IS natural language**. In Java, you write `if (user.isActive()) { sendEmail(user); }` and a compiler turns it into machine instructions. In a Claude plugin, you write "If the user is active, send them an email" and Claude reads that sentence and carries out the action.

This means:
- There is no compiler. You edit a `.md` file, save it, and it is immediately "deployed."
- There is no type system. You describe what you want in plain English.
- There are no syntax errors in the traditional sense. But unclear instructions lead to incorrect behavior, just like a vague spec leads to buggy code.
- Debugging means reading Claude's output, comparing it to your instructions, and refining your wording.

### 4.2 The Plugin Manifest (plugin.json)

The manifest file lives at `.claude-plugin/plugin.json` inside your plugin directory. It is the ONLY required configuration file. Here is the manifest for this plugin, with line-by-line explanations:

```json
{
  "name": "content-repurposing",
  "description": "Extract and repurpose long-form content into platform-specific formats for Twitter/X, LinkedIn, Substack, newsletters, and more. Turn one piece of content into 20+ pieces across platforms.",
  "version": "1.0.0",
  "author": {
    "name": "Content Studio"
  },
  "keywords": ["content", "repurposing", "marketing", "social-media", "newsletter"]
}
```

**Field-by-field breakdown:**

- **`name`** (required) -- The plugin's unique identifier. Must be kebab-case (lowercase, hyphens between words). This is like `artifactId` in a Maven `pom.xml` or the package name in a `package.json`. Users will type this name when invoking commands: `/content-repurposing:extract`.

- **`description`** (required) -- A human-readable description of what the plugin does. This appears in the plugin browser and search results. Write it for humans, not machines.

- **`version`** (required) -- Follows semantic versioning: `major.minor.patch`. Increment `patch` for bug fixes, `minor` for new features that do not break existing behavior, `major` for breaking changes. Same convention as Maven versioning.

- **`author`** (required) -- An object with at least a `name` field. Identifies who built the plugin.

- **`keywords`** (optional) -- An array of strings for search and discovery. When users browse the plugin marketplace, these help them find your plugin.

**For developers who know XML but not JSON.** JSON uses curly braces `{}` for objects (like XML elements with attributes), square brackets `[]` for arrays (like repeated XML elements), and colons `:` to separate keys from values (like XML attribute="value"). Strings use double quotes. There are no closing tags. The equivalent of this JSON in XML would look something like:

```xml
<plugin name="content-repurposing" version="1.0.0">
  <description>Extract and repurpose...</description>
  <author name="Content Studio"/>
  <keywords>
    <keyword>content</keyword>
    <keyword>repurposing</keyword>
  </keywords>
</plugin>
```

### 4.3 Commands: The Entry Points

A command is a user-triggered action. When a user types `/content-repurposing:extract` in a Claude conversation, Claude finds the file `commands/extract.md` inside the plugin and follows its instructions.

**The analogy.** In a Java web application, a REST controller has endpoint methods annotated with `@GetMapping("/users")`. The annotation declares the route; the method body contains the logic. In a Claude plugin, the command filename declares the route and the file contents contain the logic.

#### Anatomy of a Command File

Every command file is a Markdown file with two parts: YAML frontmatter (the metadata) and the Markdown body (the instructions).

Here is `commands/extract.md` with detailed annotations:

```markdown
---
description: Quick content extraction from any source into a structured table of ideas
disable-model-invocation: true
---

Extract content ideas from the following source: $ARGUMENTS

Use the **content-extraction** skill to analyze this content. Follow these steps:

1. Read the source content thoroughly
2. Ask the user what platforms they want content ideas for (all platforms, social only, newsletter only, etc.)
3. Extract 25+ content ideas organized by platform
4. Output as a structured table with columns: Title, Angle/Hook, Key Points, Platform, Estimated Length
5. Include an execution roadmap at the end
6. Save the output to the workspace folder
```

**The frontmatter (between the `---` delimiters):**

- **`description`** -- A short explanation of what this command does. This text appears in the command menu when a user browses available commands. Think of it like a Javadoc `@summary` on a public method.

- **`disable-model-invocation: true`** -- This flag means that ONLY a human user can trigger this command. Claude will not invoke it autonomously during a conversation. If this were Java, it would be like marking a method as requiring explicit user action rather than being callable by the framework. All six commands in this plugin set this to `true`.

**The body (everything after the second `---`):**

- **`$ARGUMENTS`** -- This is a variable that gets replaced with whatever the user typed after the command name. If the user types `/content-repurposing:extract My blog post about AI safety`, then `$ARGUMENTS` becomes `My blog post about AI safety`. This is like a function parameter: `void extract(String arguments)`.

- **"Use the **content-extraction** skill"** -- This line tells Claude to look up the skill named `content-extraction` in the `skills/` directory and follow its instructions. This is the routing step -- the command delegates to a skill, like a controller calling a service.

- **The numbered steps** -- These are instructions Claude follows in order. They provide a high-level process. The skill file provides the detailed instructions for each step.

#### How Users Invoke Commands

Users type the command in a Claude conversation using this format:

```
/content-repurposing:extract [paste or describe your content here]
```

The format is: `/[plugin-name]:[command-name] [arguments]`

This is similar to calling a namespaced function: `ContentRepurposing.extract(arguments)`.

### 4.4 Skills: The Intelligence Layer

A skill is a detailed set of instructions that gives Claude expertise in a specific domain. If commands are REST controller methods (short, routing-focused), skills are service classes (detailed, logic-heavy).

**Where skills live.** Each skill is a directory inside `skills/` containing a file named `SKILL.md`. The directory name IS the skill name:

```
skills/
  linkedin-post/        <- Skill name: "linkedin-post"
    SKILL.md            <- Skill definition
  twitter-thread/       <- Skill name: "twitter-thread"
    SKILL.md            <- Skill definition
```

#### Anatomy of a SKILL.md File

Here is `skills/key-takeaways/SKILL.md` as an annotated example:

```markdown
---
name: key-takeaways
description: Summarizes key takeaways from long-form content into structured bullet
  lists and actionable insights. Use when creating show notes, content summaries,
  SEO descriptions, or quick reference guides.
---

# Key Takeaways Skill

You are a content analyst who distills long-form content into its most valuable
insights. Your summaries are concise, scannable, and actionable.

## Process

### Step 1: Analyze the Source
Read the entire source content. Identify:
- Main thesis and supporting arguments
- Actionable advice and steps
- Surprising or counterintuitive insights
- Data points and statistics
- Tools, resources, or references mentioned
- Frameworks or mental models presented

### Step 2: Create Structured Summary

#### Executive Summary (2-3 sentences)
A brief overview of what the content covers and why it matters.

#### Key Takeaways (7-12 bullet points)
Each takeaway should be:
- **Actionable**: The reader can do something with this information
- **Self-contained**: Makes sense without reading the original
- **Specific**: Includes concrete details, not vague generalities

...

### Step 3: Save Output
Save as a markdown file in the workspace folder.
Name: `key-takeaways-[source-name].md`

## Quality Standards
- Takeaways should be ordered by importance, not by appearance in the source
- Use active voice and imperative mood for actionable items
- Include specific numbers, names, and details rather than generalizations
- The summary should provide standalone value without the original content
```

**Breaking down the structure:**

1. **Frontmatter** -- The `name` and `description` fields identify the skill. The description helps Claude (and other skills) know when to invoke this skill.

2. **Persona definition** -- The line "You are a content analyst who..." sets Claude's role. This is like dependency injection for behavior: you tell the AI what kind of expert it should act as. Every skill in this plugin starts with a persona.

3. **Process steps** -- A numbered sequence of steps Claude follows. This is the core logic. In this plugin, the common pattern is:
   - Step 1: Analyze the source content
   - Step 2: Ask the user questions (interactive step)
   - Step 3: Generate the output
   - Step 4: Save to a file

4. **Quality standards** -- Rules that constrain the output. These are like assertions or validation rules. They tell Claude what "good" looks like and what to avoid.

5. **Output conventions** -- File naming patterns like `key-takeaways-[source-name].md` ensure consistent, predictable output.

#### How Skills Differ from Commands

| Aspect            | Command                        | Skill                              |
|-------------------|--------------------------------|------------------------------------|
| Triggered by      | User typing a `/command`       | Referenced from a command or auto-invoked by Claude |
| File location     | `commands/[name].md`           | `skills/[name]/SKILL.md`          |
| Length             | Short (5-15 lines typically)   | Long (50-200+ lines typically)    |
| Purpose            | Route and delegate             | Define detailed behavior           |
| `disable-model-invocation` | Usually `true`      | Not applicable (skills have no such flag) |
| Analogy            | Controller method              | Service class                      |

Skills can be invoked automatically by Claude when it determines they are relevant to the user's request -- Claude reads the skill's `description` to decide. Commands with `disable-model-invocation: true` can ONLY be triggered by the user typing the command.

### 4.5 How Commands and Skills Work Together

The flow is:

```
User types:  /content-repurposing:linkedin My blog post about AI

    |
    v

Claude finds: commands/linkedin.md
    - Reads the frontmatter (gets description, flags)
    - Reads the body (gets instructions)
    - Sees: "Use the **linkedin-post** skill"
    - Substitutes $ARGUMENTS with "My blog post about AI"

    |
    v

Claude finds: skills/linkedin-post/SKILL.md
    - Reads the persona ("You are a LinkedIn content strategist...")
    - Follows the process steps (Analyze, Ask User, Generate, Save)
    - Applies quality standards

    |
    v

Claude produces: LinkedIn posts saved to linkedin-posts-[source-name].md
```

This is the same pattern as MVC in a web application:

```
HTTP Request  -->  Controller  -->  Service  -->  Response
User Command  -->  Command.md  -->  SKILL.md -->  Output File
```

The command is thin (routing only). The skill is thick (all the logic). This separation means you can have multiple commands route to the same skill, or you can invoke a skill without any command at all (Claude does this automatically when it recognizes a relevant request).

### 4.6 YAML Frontmatter Explained (For Developers Who Know XML)

YAML (YAML Ain't Markup Language) is a data serialization format used for configuration. If you are used to XML or Java properties files, YAML serves the same purpose but with different syntax.

**The delimiters.** In a Markdown file, YAML frontmatter is enclosed between two lines of three dashes:

```yaml
---
key: value
another-key: another value
---
```

Everything between the `---` lines is YAML. Everything after the second `---` is regular Markdown.

**Key-value pairs.** YAML uses `key: value` syntax (note the space after the colon). This is equivalent to:

| YAML                               | XML                                      | Java Properties          |
|------------------------------------|------------------------------------------|--------------------------|
| `name: content-extraction`         | `<name>content-extraction</name>`        | `name=content-extraction`|
| `disable-model-invocation: true`   | `<disable-model-invocation>true</...>`   | `disable-model-invocation=true` |

**Common gotchas:**

- **Indentation matters.** YAML uses spaces (not tabs) for nesting. Using tabs will cause parse errors. If you are used to Java where indentation is cosmetic, this is a change.
- **Strings usually do not need quotes.** `name: key-takeaways` works without quotes. But if your string contains special characters like colons or hash symbols, wrap it in quotes: `description: "Step 1: Analyze"`.
- **Boolean values.** `true` and `false` are booleans, not strings. `disable-model-invocation: true` is a boolean true.
- **The `---` must be on its own line.** No spaces before or after.

---

## 5. How to Modify Existing Skills

### 5.1 Understanding What You Can Change

Every part of a SKILL.md file is editable. Here is what you can modify and the effect each change has:

- **The persona** -- Change the tone, expertise level, or domain focus. Making the persona "a casual social media manager" versus "a senior corporate communications director" will produce very different output.
- **The process steps** -- Add, remove, or reorder steps. If you do not want the skill to ask the user interactive questions, remove Step 2. If you want an additional review step, add one.
- **Platform-specific details** -- Change character limits, format rules, or platform conventions. For example, if Twitter increases its character limit, update the skill.
- **Quality standards** -- Add or modify the criteria that constrain output. This is your primary lever for controlling quality.
- **Output format** -- Change file naming conventions, file types, or the structure of the output.

### 5.2 Step-by-Step: Modifying the LinkedIn Post Skill

Let us walk through a concrete modification. We will add a new hook pattern and a new post style to the LinkedIn post skill.

**Step 1: Open the file in your editor.**

Open `content-repurposing/skills/linkedin-post/SKILL.md`.

**Step 2: Find the "Hook Patterns" section.**

Look for the section under "#### The Hook (First 2 lines)" that lists patterns like:
- Bold statement: "Most people get [topic] completely wrong."
- Personal story: "Last week, I [experience]..."
- Question: "What if everything you knew about [topic] was backwards?"
- Statistic: "[Number]% of [group] do [thing]..."
- Contrarian: "Unpopular opinion: [take]"

**Step 3: Add a new hook pattern.**

After the existing patterns, add:
```markdown
- Data-driven: "[Percentage]% of [group] do [thing]. Here's what the data says."
```

**Step 4: Find the "Post Style Variations" section.**

Look for the numbered list under "### Post Style Variations":
1. The Story
2. The Listicle
3. The Hot Take
4. The How-To
5. The Reflection

**Step 5: Add a new style.**

After item 5, add:
```markdown
6. **The Data Story**: Lead with a surprising statistic, explain the context, provide actionable insight
```

**Step 6: Save the file.**

**Step 7: Test your changes.**

```bash
claude --plugin-dir ./content-repurposing
```

This starts Claude with your modified plugin loaded. Now try the LinkedIn command:

```
/content-repurposing:linkedin [paste some sample content]
```

Verify that your new hook pattern ("Data-driven") and new post style ("The Data Story") appear in the generated output.

**Step 8: Iterate.**

If the output does not include your new additions, the most likely cause is that your instructions are not specific enough. Make them more explicit. For example, change "Lead with a surprising statistic" to "The first line MUST contain a specific statistic from the source material."

### 5.3 Common Modifications

Here are other modifications you might want to make, with guidance on where to edit:

**Adding a new platform to content-extraction.** Open `skills/content-extraction/SKILL.md`. Find the section after "#### Short-form Video Ideas". Add a new section following the same pattern (heading, table structure, format guidelines, quality criteria). For example, an "Email Marketing Ideas" section.

**Changing output file naming conventions.** Each skill's "Save Output" step specifies the filename pattern. For example, `key-takeaways-[source-name].md`. Change this to any pattern you prefer, like `[date]-key-takeaways-[source-name].md`.

**Adjusting character or word count limits.** Find the specific limit in the skill file (e.g., "280 characters" in the Twitter thread skill, "150-300 words" in the LinkedIn post skill) and change the number. Make sure you also update the quality standards section if it references the same limit.

**Adding new format types.** In the title-generator skill, there are five style categories (Curiosity-Driven, How-To, Listicle, Contrarian, Question-Based). To add a sixth, create a new subsection following the same pattern: category heading, 3-4 example templates, brief guidelines.

---

## 6. How to Create a New Skill from Scratch

### 6.1 Planning Your Skill

Before writing any Markdown, answer these four questions:

1. **What will it do?** Define the skill's purpose in one sentence. Example: "Generate Instagram captions from long-form content."
2. **What input does it need?** The source content, plus any user preferences (tone, number of captions, hashtag strategy).
3. **What output will it produce?** Captions with emojis, hashtags, and calls to action, saved to a Markdown file.
4. **What platforms or formats?** Instagram feed posts, Reels captions, Stories text.

### 6.2 Step-by-Step: Creating an "instagram-caption" Skill

We will create a complete new skill from scratch. Follow every step exactly.

**Step 1: Create the directory.**

```bash
mkdir content-repurposing/skills/instagram-caption
```

This creates the skill directory. The directory name `instagram-caption` becomes the skill name that commands and Claude will reference.

**Step 2: Create the SKILL.md file.**

Create the file `content-repurposing/skills/instagram-caption/SKILL.md` in your editor.

**Step 3: Write the complete file.**

Here is the full content. You can copy and paste this directly:

```markdown
---
name: instagram-caption
description: Generates engaging Instagram captions with emoji, hashtags, and calls-to-action from long-form content. Use when creating Instagram posts, carousels, or Reels captions.
---

# Instagram Caption Skill

You are a social media specialist who creates scroll-stopping Instagram captions. You understand the Instagram algorithm, visual storytelling, and how to drive engagement through compelling copy paired with strategic hashtag use.

## Process

### Step 1: Analyze the Source

Read the entire source content and identify:
- Visual moments that could pair with photos or graphics
- Emotional hooks and relatable experiences
- Quick tips or actionable advice
- Personal stories or behind-the-scenes moments
- Statistics or facts that surprise
- Quotes that resonate

### Step 2: Ask the User

Before generating captions, ask:
- **How many captions do you want?** (3-7 recommended)
- **What type of Instagram content?** (Feed post / Carousel / Reels)
- **What tone?** (Inspirational / Educational / Casual / Professional)
- **Hashtag strategy?** (Broad reach / Niche targeted / Minimal)

Wait for the user's response before proceeding.

### Step 3: Generate Captions

For each caption, provide:

#### The Hook (First Line)
The first line appears before "...more" in the feed. It must stop the scroll:
- Question: "Ever feel like [relatable struggle]?"
- Bold claim: "This changed everything about how I [topic]."
- Number: "[Number] things nobody tells you about [topic]."
- Story opener: "I was [situation] when [unexpected thing happened]."

#### The Body (2-4 short paragraphs)
- Use line breaks between paragraphs for readability
- Include 1-2 relevant emojis per paragraph (not excessive)
- Weave in the key insight or lesson
- Keep total length between 100-200 words (Instagram sweet spot)

#### The Call-to-Action
End every caption with ONE clear CTA:
- Engagement: "Double tap if you agree" or "Drop a [emoji] if this resonates"
- Save: "Save this for later"
- Share: "Share this with someone who needs to hear it"
- Comment: "What's your experience with [topic]? Tell me below"
- Profile: "Link in bio for the full [resource]"

#### Hashtags (Separate Block)
Provide 20-30 hashtags organized into three tiers:
- **High-volume (5-10)**: Broad hashtags with 1M+ posts
- **Medium-volume (10-15)**: Niche hashtags with 100K-1M posts
- **Low-volume (5-10)**: Specific hashtags with under 100K posts

Place hashtags in a separate block after the caption, not inline.

### Caption Style Variations

Vary the style across captions:
1. **The Storyteller**: Mini-narrative with a lesson at the end
2. **The Teacher**: Educational tip with clear takeaway
3. **The Questioner**: Lead with a question, build curiosity, answer at the end
4. **The List**: Numbered tips or lessons (works great for carousels)
5. **The Motivator**: Inspirational message with emotional resonance

### Step 4: Save Output

Save all captions to a markdown file in the workspace folder.
Name: `instagram-captions-[source-name].md`
Separate each caption with a horizontal rule (---).
Include the hashtag block after each caption.

## Quality Standards
- Every caption must stand alone without context from the original content
- Hooks must be compelling enough to make users tap "...more"
- Emojis should enhance readability, not clutter the text (2-5 per caption)
- Hashtags must be real, currently active tags (no made-up hashtags)
- Captions should feel authentic and conversational, not like ad copy
- Each caption must have a unique angle -- no repetition across the set
- Keep within Instagram's 2,200 character limit per caption
```

**Step 4: Test your new skill.**

Start Claude with the plugin directory:

```bash
claude --plugin-dir ./content-repurposing
```

Then test by asking Claude directly (skills can be auto-invoked):

```
I have a blog post about productivity tips. Can you create Instagram captions from it?
```

Claude should recognize the `instagram-caption` skill from its description and use it. If you paste or reference content, the skill's process steps will guide the interaction.

### 6.3 Skill Design Patterns

Looking across the seven skills in this plugin, four design patterns emerge. You can use these as templates for any new skill:

**The Interactive Pattern.** Used by: content-extraction, linkedin-post, twitter-thread. The skill asks the user questions before generating output. This pattern produces the most tailored results but requires user participation.

Structure:
1. Analyze source
2. Ask user preferences (number of items, tone, platforms)
3. Generate based on answers
4. Save output

**The Batch Pattern.** Used by: title-generator, quote-extractor. The skill generates a large number of items at once, then rates or scores them. Good for brainstorming-style output where quantity enables selection.

Structure:
1. Analyze source
2. Generate many items (15-20 titles, 10-15 quotes)
3. Score or rate each item
4. Save output with rankings

**The Structured Summary Pattern.** Used by: key-takeaways. The skill produces a single, organized document with multiple sections (executive summary, bullet points, deep-dive points, quick reference). Good for distillation tasks.

Structure:
1. Analyze source
2. Create multi-section summary
3. Save output

**The Platform-Adapted Pattern.** Used by: substack-note. The skill generates content specifically optimized for one platform's conventions and culture. Each note follows platform-specific rules (no hashtags on Substack, first-person voice, discussion prompts).

Structure:
1. Analyze source
2. Generate platform-specific items
3. Include platform metadata (posting time, engagement type)
4. Save output

---

## 7. How to Create a New Command

Now that we have an `instagram-caption` skill, let us create a command that routes to it. This gives users a quick way to invoke the skill by typing `/content-repurposing:instagram`.

### Step 1: Create the Command File

Create the file `content-repurposing/commands/instagram.md` in your editor.

### Step 2: Write the Frontmatter

```yaml
---
description: Generate Instagram captions from your content
disable-model-invocation: true
---
```

The `description` will appear in the command menu. Setting `disable-model-invocation: true` ensures only the user can trigger this command.

### Step 3: Write the Body

```markdown
Generate Instagram captions from the following content: $ARGUMENTS

Use the **instagram-caption** skill to create the captions. Follow these steps:

1. Read the source content thoroughly
2. Ask the user how many captions they want and what type (feed, carousel, reels)
3. Generate captions with hooks, body, CTAs, and hashtags
4. Save all captions to a markdown file in the workspace folder
```

### Step 4: Test

```bash
claude --plugin-dir ./content-repurposing
```

Then type:

```
/content-repurposing:instagram My latest blog post about remote work productivity
```

Claude should follow the command's instructions, invoke the instagram-caption skill, ask you the interactive questions from Step 2 of the skill, and generate captions.

### Step 5: Update the All Command

Open `content-repurposing/commands/all.md` and add the new command to the listing:

Under the "## Commands (Quick Actions)" section, add:
```markdown
- `/content-repurposing:instagram` - Generate Instagram captions from your content
```

Under the "## Skills (Auto-invoked by Claude)" section, add:
```markdown
- **instagram-caption** - Create Instagram captions with hooks, CTAs, and hashtags
```

Save the file. The `/content-repurposing:all` command will now list your new additions.

---

## 8. Testing Your Plugin

### 8.1 Local Testing with Claude Code CLI

The primary way to test during development is the `--plugin-dir` flag:

```bash
claude --plugin-dir ./content-repurposing
```

**What this does.** It starts a Claude session with your plugin loaded from the local directory. No installation or packaging is required. Every file is read directly from disk.

**The edit-test cycle.** This is your development loop:

1. Edit a skill or command file in your editor
2. Save the file
3. Start a new Claude session with `claude --plugin-dir ./content-repurposing`
4. Test the command or skill
5. Read the output, compare it to your expectations
6. Go back to step 1

You need to start a new Claude session each time because Claude reads the plugin files at startup. Changes to files are not picked up mid-session.

**Testing individual commands.** Type the full command in the Claude conversation:

```
/content-repurposing:extract [your test content]
/content-repurposing:linkedin [your test content]
/content-repurposing:twitter [your test content]
/content-repurposing:quotes [your test content]
/content-repurposing:titles [your test content]
/content-repurposing:all
```

**Testing skill auto-invocation.** Instead of using a command, describe what you want in natural language and see if Claude automatically invokes the right skill:

```
Can you extract the key takeaways from this article? [paste content]
```

Claude should recognize that the `key-takeaways` skill is relevant and use it.

### 8.2 Testing in Claude Cowork

Once you are satisfied with local testing, you can test in the full Cowork application:

1. Package the plugin as a zip file (see Section 9.1)
2. Open Claude Cowork
3. Navigate to plugin settings
4. Upload or install the zip file
5. Start a new conversation
6. Run each command and verify the output

The behavior should be identical to local testing with Claude Code. If it differs, check the Troubleshooting guide at `docs/TROUBLESHOOTING.md`.

### 8.3 Testing Checklist

Use this checklist before considering your plugin ready for distribution:

- [ ] All commands appear when you type `/content-repurposing:all`
- [ ] Each command invokes the correct skill (check that the right persona and process are used)
- [ ] Skills that have an "Ask the User" step actually pause and wait for input
- [ ] Output files are saved with the correct naming convention
- [ ] Output format matches the quality standards defined in each skill
- [ ] Character and word limits are respected (e.g., tweets under 280 characters)
- [ ] No skill produces generic or repetitive output -- each item has a unique angle
- [ ] The plugin works with different types of source content (blog posts, transcripts, articles)
- [ ] The `$ARGUMENTS` variable correctly passes user input to the skill

---

## 9. Packaging and Distribution

### 9.1 Creating the Zip File

To distribute your plugin, package the `content-repurposing/` directory as a zip file:

**On macOS or Linux:**
```bash
cd content-repurposing
zip -r ../content-repurposing-plugin.zip .
```

**On Windows (PowerShell):**
```powershell
Compress-Archive -Path content-repurposing\* -DestinationPath content-repurposing-plugin.zip
```

**What to include:**
- The `.claude-plugin/` directory with `plugin.json`
- The `commands/` directory with all `.md` files
- The `skills/` directory with all subdirectories and their `SKILL.md` files

**What to exclude:**
- The `docs/` directory (documentation is not part of the plugin)
- Any `.git/` directory
- README.md and other project-level files
- Any temporary or test files

The zip file should have this structure when extracted:
```
.claude-plugin/
  plugin.json
commands/
  all.md
  extract.md
  ...
skills/
  content-extraction/
    SKILL.md
  ...
```

### 9.2 Uploading to Claude Cowork

1. Open Claude Cowork
2. Navigate to Settings or Plugin Management (the exact menu location may vary by version)
3. Select "Install Plugin" or "Upload Plugin"
4. Choose your zip file
5. The plugin will appear in your installed plugins list
6. Enable it if it is not enabled by default
7. Start a new conversation to use the plugin

### 9.3 Sharing on GitHub

If you want to share your plugin with others:

1. **Create a repository** with the plugin directory as the root (or as a subdirectory of a larger project, as this repository demonstrates).

2. **Include a README** that explains what the plugin does, how to install it, and what commands are available.

3. **License your work.** This project uses the MIT License, which allows anyone to use, modify, and distribute the code. Choose a license that fits your needs.

4. **Use `.gitignore` if needed.** Markdown and JSON files are all text, so Git handles them well. No special configuration is required.

---

## 10. Using Agent Teams for Development

### 10.1 What Are Agent Teams?

Agent teams are a feature of Claude Code that allows a "team lead" instance of Claude to spawn and coordinate multiple "builder" instances of Claude, each working on a different task simultaneously. Think of it as multithreaded development -- instead of one developer working sequentially, you have multiple agents working in parallel.

For a complete explanation, see [AGENT_TEAM_GUIDE.md](AGENT_TEAM_GUIDE.md).

### 10.2 When to Use Agent Teams vs Single Agent

**Use a single agent when:**
- The task is small (creating one file, modifying one skill)
- Steps must be done sequentially (each step depends on the previous one)
- You need tight, interactive control over the process

**Use an agent team when:**
- The task has multiple independent sub-tasks that can run in parallel
- You are building something with many files (like an entire plugin)
- Different parts require different expertise or focus
- Speed matters and tasks can be parallelized

### 10.3 How This Plugin Was Built by Agents

This plugin was built by six agents coordinated by a team lead in approximately five minutes. Here is how the work was divided:

1. **scaffold-builder** ran first and created the directory structure and `plugin.json`. This had to complete before other agents could start (they needed directories to exist).

2. Once the scaffold was ready, five agents ran **in parallel**:
   - **command-builder** created all six command files
   - **extraction-skill-builder** built the content-extraction skill
   - **takeaways-quotes-builder** built the key-takeaways and quote-extractor skills
   - **social-skills-builder** built the linkedin-post and twitter-thread skills
   - **content-skills-builder** built the substack-note and title-generator skills

3. The team lead monitored progress, verified task completion, and handled any issues.

The result: 14 files, approximately 742 lines, produced in roughly 5 minutes. A single agent working sequentially would have taken significantly longer.

For the full build log with timestamps, see [AGENT_TEAM_BUILD_REPORT.md](AGENT_TEAM_BUILD_REPORT.md).

### 10.4 Quick Start: Creating Your Own Agent Team

Here is a condensed walkthrough of the agent team workflow. Each step references Claude Code CLI operations:

1. **Start Claude Code** in your project directory.

2. **Describe your build plan.** Tell Claude what you want to build and suggest how to divide the work. For example: "Build a social media analytics plugin with five skills. Have one agent create the scaffold, then four agents build skills in parallel."

3. **Claude creates the team.** The team lead agent uses `TeamCreate` to spawn builder agents with specific roles and instructions.

4. **Claude assigns tasks.** The team lead uses `TaskCreate` to define tasks and assigns them to specific agents.

5. **Agents work in parallel.** Each builder agent works on its assigned tasks independently. The team lead monitors progress.

6. **Agents report back.** Each agent sends completion messages to the team lead using `SendMessage`.

7. **Team lead verifies and cleans up.** The team lead reviews the output, verifies quality, and shuts down the builder agents.

The key insight: you interact with the team lead, and the team lead manages everything else. You do not need to interact with individual builder agents.

---

## 11. Development Workflow Checklist

Here is the complete workflow from idea to deployed plugin, as a numbered checklist:

1. Define your plugin's purpose and list the commands and skills you need
2. Create the directory structure: `.claude-plugin/`, `commands/`, `skills/`
3. Write `plugin.json` with name, description, version, author, and keywords
4. For each skill, create a directory in `skills/` and write `SKILL.md` with persona, process, and quality standards
5. For each command, create a `.md` file in `commands/` with frontmatter and body that routes to the appropriate skill
6. Create an `all.md` command that lists everything available
7. Test locally with `claude --plugin-dir ./your-plugin`
8. Test each command individually
9. Test skill auto-invocation with natural language requests
10. Iterate: edit files, restart Claude, re-test
11. Run through the testing checklist (Section 8.3)
12. Package as a zip file
13. Test the zip in Claude Cowork
14. Share on GitHub or upload to the plugin marketplace

---

## 12. FAQs for C, C++, and Java Developers

**"Where is the compiler?"**
There is no compiler. Markdown files are the source code AND the executable. You edit a `.md` file, save it, and Claude reads it directly. There is no build step, no compilation, no bytecode.

**"How do I debug?"**
Test with `claude --plugin-dir`, read Claude's output carefully, and compare it to your skill instructions. If the output is wrong, your instructions are either ambiguous or incomplete. Refine the Markdown. This is more like debugging a specification than debugging code.

**"Where are the types? How do I enforce structure?"**
YAML frontmatter provides basic structure (key-value metadata), but there is no type system. The "schema" of your output is defined in natural language within the skill's process steps and quality standards. If you want Claude to produce a table with specific columns, describe those columns explicitly in the skill.

**"How do I version control this?"**
Git works perfectly. Every file is plain text (`.md` and `.json`). Use Git exactly as you would for any project. Diffs, branches, merges, and pull requests all work normally.

**"Can I use conditionals? Where is the if/else?"**
Skills use natural language logic instead of formal control flow. Instead of `if (platform == "twitter") { limit = 280; }`, you write "If the user selects Twitter, keep each tweet under 280 characters." Claude interprets and follows these instructions. For complex branching, describe each scenario clearly.

**"How do I handle errors?"**
Include instructions for edge cases in your skill. For example: "If the source content is too short to extract 25 ideas, explain this to the user and extract as many as possible." This is analogous to writing try-catch blocks, but in natural language.

**"Can I call external APIs or databases?"**
Not directly from a skill file. Skills are instructions for Claude, not executable code. However, Claude Code has tool-use capabilities that can interact with files, run commands, and access certain services. The plugin's skills instruct Claude on WHAT to do; Claude's built-in capabilities determine HOW.

**"What is the equivalent of unit testing?"**
Run the command with known input and verify the output matches your expectations. There is no automated test framework for plugin skills. Your "test suite" is a set of sample inputs and expected outputs that you run manually.

**"Can I import or reuse code between skills?"**
There is no formal import system. If multiple skills need the same instruction (e.g., "always save output to the workspace folder"), you write that instruction in each skill. Duplication is acceptable and sometimes preferable for clarity, since each skill should be self-contained.

**"How do I handle concurrent users?"**
Each Claude session is independent. Two users running the same plugin simultaneously get separate Claude instances, each reading the same skill files but maintaining independent conversations. There is no shared state, no threading, no race conditions.

**"What about performance? How fast is this?"**
The "execution speed" depends on Claude's response time, which is typically a few seconds for short outputs and up to a minute for very long, structured outputs (like a 25-idea content extraction report). There is no way to "optimize" this in the traditional sense. Clearer, more concise instructions tend to produce faster responses.

**"What happens if I make a syntax error in the YAML frontmatter?"**
Claude may fail to parse the frontmatter, which means the command or skill will not load correctly. Common mistakes: using tabs instead of spaces, missing the space after the colon in `key: value`, or forgetting to close the `---` delimiters. Check `docs/TROUBLESHOOTING.md` for specific error messages.

---

## 13. Next Steps

You now have a solid understanding of how Claude Cowork plugins work, how to modify them, and how to build new ones from scratch. Here is where to go next:

- **Try the hands-on exercises** in [QUICK_START_GUIDE.md](QUICK_START_GUIDE.md) -- 10+ use cases you can run immediately
- **Read the full user manual** in [USER_GUIDE.md](USER_GUIDE.md) for complete command documentation
- **Study the technical architecture** in [PLUGIN_ARCHITECTURE.md](PLUGIN_ARCHITECTURE.md) for a deeper understanding of how plugins are loaded and executed
- **Learn about agent teams** in [AGENT_TEAM_GUIDE.md](AGENT_TEAM_GUIDE.md) if you want to use multiple agents to build your next plugin
- **Review the build log** in [AGENT_TEAM_BUILD_REPORT.md](AGENT_TEAM_BUILD_REPORT.md) to see exactly how six agents coordinated the build
- **Look up any unfamiliar terms** in [GLOSSARY.md](GLOSSARY.md) -- 30+ terms defined for newcomers
- **Troubleshoot issues** with [TROUBLESHOOTING.md](TROUBLESHOOTING.md) if something is not working as expected

Build something. Break something. Iterate. That is how plugins get better.
