# Plugin Architecture Deep Dive

> **Who this is for:** Developers with C, C++, or Java experience who are new to AI plugins, LLM-driven architectures, and natural-language programming. Every concept is explained from scratch with analogies to traditional compiled-language ecosystems.

---

## 1. Overview: What Makes a Claude Cowork Plugin

A Claude Cowork plugin is a directory of plain-text files -- Markdown and JSON -- that tells Claude how to perform a set of related tasks. There are no compiled binaries, no bytecode, no shared libraries. The "source code" and the "executable" are the same thing: natural language instructions that Claude reads and follows at runtime.

This is the fundamental paradigm shift from C or Java development. In those languages you write algorithms that a compiler translates into machine instructions. Here you write *instructions for an AI* in structured English. The AI interprets those instructions every time a user invokes the plugin, much like an interpreter executes a script -- except the "interpreter" understands human language.

A plugin consists of exactly three things:

1. **A manifest** (`.claude-plugin/plugin.json`) -- metadata about the plugin (name, version, author).
2. **Commands** (`commands/*.md`) -- user-facing entry points, like endpoints in a REST API or `main()` functions.
3. **Skills** (`skills/<name>/SKILL.md`) -- reusable instruction sets that do the heavy lifting, like service classes behind a controller.

No build step. No compilation. No deployment pipeline. You write the files, zip the directory, and upload it.

---

## 2. Anatomy of a Plugin

Here is the full directory tree of the content-repurposing plugin built by the agent team:

```
content-repurposing/                    <-- Plugin root directory
  .claude-plugin/                       <-- Required marker directory
    plugin.json                         <-- Manifest (metadata)
  commands/                             <-- User-triggered slash commands
    all.md                              <-- Lists all commands and skills
    extract.md                          <-- Content extraction command
    linkedin.md                         <-- LinkedIn post command
    quotes.md                           <-- Quote extraction command
    titles.md                           <-- Title generation command
    twitter.md                          <-- Twitter thread command
  skills/                               <-- AI-invocable skill definitions
    content-extraction/SKILL.md         <-- Flagship skill (223 lines)
    key-takeaways/SKILL.md              <-- Summary generation (60 lines)
    linkedin-post/SKILL.md              <-- LinkedIn posts (68 lines)
    quote-extractor/SKILL.md            <-- Quote extraction (58 lines)
    substack-note/SKILL.md              <-- Substack Notes (54 lines)
    title-generator/SKILL.md            <-- Title generation (82 lines)
    twitter-thread/SKILL.md             <-- Twitter threads (67 lines)
```

### 2.1 The .claude-plugin Directory

The `.claude-plugin/` directory is a **required marker** that tells Claude Cowork "this is a plugin." Without it, the directory is just a folder of Markdown files.

**C/Java analogy:** This is like `META-INF/` in a Java JAR file or the `.git/` directory that marks a Git repository. Its presence signals "treat this directory as something special."

The directory must contain exactly one file: `plugin.json`.

### 2.2 The Plugin Manifest (plugin.json)

Every plugin needs a manifest. Here is the actual manifest from this plugin, with every field annotated:

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

| Field | Type | Required | Purpose | Analogy |
|-------|------|----------|---------|---------|
| `name` | string (kebab-case) | Yes | Unique identifier. Becomes the prefix for slash commands (`/content-repurposing:extract`). | `artifactId` in Maven `pom.xml` |
| `description` | string | Yes | User-facing description shown in the plugin gallery and autocomplete. | `<description>` in `pom.xml` |
| `version` | string (semver) | No | Semantic version number (major.minor.patch). | `<version>` in `pom.xml` |
| `author` | object | No | Object with a `name` field identifying the plugin creator. | `<developers>` in `pom.xml` |
| `keywords` | string[] | No | Array of tags for plugin discovery and search. | Maven `<tags>` or npm keywords |

**C analogy:** Think of `plugin.json` as a combination of a Makefile header and a `README` rolled into one machine-readable file. It tells the host system the plugin's identity without executing anything.

**Java analogy:** This is your `pom.xml` or `build.gradle` -- the project descriptor that package managers and IDEs use to understand your project.

### 2.3 The Commands Directory

Each `.md` file in the `commands/` directory defines one slash command. The file name (without `.md`) becomes the command identifier.

```
commands/extract.md  -->  /content-repurposing:extract
commands/linkedin.md -->  /content-repurposing:linkedin
commands/twitter.md  -->  /content-repurposing:twitter
```

Claude discovers commands by scanning this directory. You do not need to register commands anywhere else -- placing a properly formatted `.md` file in `commands/` is enough.

**C analogy:** Each command file is like a separate `main()` entry point. The file name is the function name.

**Java analogy:** Each command file is like a Spring `@RestController` method annotated with `@GetMapping("/extract")`. The file name determines the route.

### 2.4 The Skills Directory

Each subdirectory under `skills/` defines one skill. The subdirectory must contain a file named `SKILL.md`. The subdirectory name becomes the skill identifier.

```
skills/linkedin-post/SKILL.md  -->  skill name: "linkedin-post"
skills/twitter-thread/SKILL.md -->  skill name: "twitter-thread"
```

Skills differ from commands in an important way: commands are triggered explicitly by the user (via a slash command), while skills can be invoked automatically by Claude when it determines the skill is relevant to the conversation.

**Java analogy:** If commands are controllers, skills are service classes. A controller is the entry point; the service contains the business logic. Claude can also call a service directly without going through a controller, just as application code might call a service from multiple places.

---

## 3. Commands Deep Dive

### 3.1 Command File Format

Here is the complete `commands/extract.md` file with every line annotated:

```yaml
---                                                          # [1] Frontmatter start delimiter
description: Quick content extraction from any source        # [2] Metadata: shows in autocomplete menu
    into a structured table of ideas
disable-model-invocation: true                               # [3] Metadata: only users can trigger this
---                                                          # [4] Frontmatter end delimiter
                                                             #
Extract content ideas from the following source: $ARGUMENTS  # [5] Body: instruction + parameter
                                                             #
Use the **content-extraction** skill to analyze this         # [6] Body: routes to a skill
content. Follow these steps:                                 #
                                                             #
1. Read the source content thoroughly                        # [7] Body: step-by-step workflow
2. Ask the user what platforms they want content ideas for   #
3. Extract 25+ content ideas organized by platform           #
4. Output as a structured table with columns: Title,         #
   Angle/Hook, Key Points, Platform, Estimated Length        #
5. Include an execution roadmap at the end                   #
6. Save the output to the workspace folder                   #
```

**Line-by-line breakdown:**

- **[1] and [4]:** The `---` delimiters mark the start and end of the YAML frontmatter block. Everything between them is metadata, not instructions. This is identical to frontmatter in static site generators like Jekyll or Hugo.

- **[2] `description`:** A short string displayed in the Claude Cowork autocomplete menu when the user types `/`. This is how users find and select your command. (Like a Javadoc `@summary` on a public method.)

- **[3] `disable-model-invocation: true`:** A boolean flag that prevents Claude from calling this command on its own. Only the human user can trigger it by typing the slash command. More detail in Section 3.5.

- **[5] `$ARGUMENTS`:** A special placeholder that gets replaced with whatever the user types after the command name. If the user types `/content-repurposing:extract my blog post about AI`, then `$ARGUMENTS` becomes `my blog post about AI`. (Like `argv` in C or `args[]` in Java's `main`.)

- **[6] Skill routing:** The line `Use the **content-extraction** skill` tells Claude to find and follow the instructions in `skills/content-extraction/SKILL.md`. This is the command-to-skill delegation pattern.

- **[7] Workflow steps:** Numbered steps that guide Claude through the process. These provide a high-level roadmap; the skill provides the detailed instructions.

### 3.2 Frontmatter Fields Reference

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `description` | string | (none) | Shown in the command picker / autocomplete menu. Required for discoverability. |
| `disable-model-invocation` | boolean | `false` | If `true`, only the user can trigger this command. Claude cannot invoke it autonomously. |
| `allowed-tools` | string[] | (all) | Restricts which tools (Read, Write, Bash, etc.) Claude can use while executing this command. |
| `model` | string | (default) | Override the Claude model used for this command (e.g., `claude-sonnet-4-5-20250929` for speed). |
| `context` | string[] | (none) | Additional files or URLs to include as context when executing the command. |

All six commands in this plugin use the same two frontmatter fields: `description` and `disable-model-invocation: true`.

### 3.3 $ARGUMENTS: Parameter Passing

`$ARGUMENTS` is the mechanism for passing user input into a command. It works through simple text substitution.

**How it works:**

1. User types: `/content-repurposing:linkedin my blog post about AI agents`
2. Claude reads `commands/linkedin.md`
3. The string `my blog post about AI agents` replaces every occurrence of `$ARGUMENTS` in the file body
4. Claude executes the resulting instructions

**Indexed access:**

You can also access individual arguments by position:
- `$ARGUMENTS[0]` -- the first word/token
- `$ARGUMENTS[1]` -- the second word/token

This is useful when a command needs structured input, like `/my-plugin:create project-name template-name` where you want to separate the two values.

**Empty $ARGUMENTS:**

If the user types just `/content-repurposing:extract` with no additional text, `$ARGUMENTS` is empty. Well-designed commands should handle this gracefully -- for example, by prompting the user: "Please provide the source content or a file path."

**C analogy:** `$ARGUMENTS` is exactly like `argv` in C. `$ARGUMENTS[0]` is `argv[1]` (the first user-supplied argument), and the full `$ARGUMENTS` string is like concatenating all of `argv[1..argc-1]`.

**Java analogy:** This maps to `String[] args` in `public static void main(String[] args)`. The full `$ARGUMENTS` is `String.join(" ", args)`.

### 3.4 The Command-Routes-to-Skill Pattern

Every command in this plugin follows the same architectural pattern:

```
Command (thin wrapper)  -->  Skill (full implementation)
  extract.md            -->  content-extraction/SKILL.md
  linkedin.md           -->  linkedin-post/SKILL.md
  twitter.md            -->  twitter-thread/SKILL.md
  quotes.md             -->  quote-extractor/SKILL.md
  titles.md             -->  title-generator/SKILL.md
```

The command receives the user's input, provides a brief workflow outline, and delegates to a skill for the detailed work. The skill contains the persona, process steps, output templates, and quality standards.

**Why this pattern?**

1. **Separation of concerns.** The command handles the "what" (user input, high-level steps). The skill handles the "how" (detailed instructions, output formats).

2. **Reusability.** A skill can be invoked from multiple commands, or Claude can invoke it directly without any command at all. For example, Claude might use the `key-takeaways` skill while processing a document even if the user did not type a slash command.

3. **Maintainability.** To change how LinkedIn posts are generated, you edit one file (`skills/linkedin-post/SKILL.md`). The command file does not need to change.

**Traditional analogy:**

| Role | Java/Spring | This Plugin |
|------|------------|-------------|
| Entry point | `@RestController` | Command `.md` file |
| Business logic | `@Service` class | Skill `SKILL.md` file |
| Delegation | Controller calls `service.method()` | Command says "Use the **skill-name** skill" |

### 3.5 disable-model-invocation Explained

This frontmatter flag controls who can trigger the command:

| Value | Who can trigger | When to use |
|-------|----------------|-------------|
| `true` | Only the human user (by typing the slash command) | Default for most commands. Use when the command should be an explicit, intentional action. |
| `false` or omitted | Both the user and Claude (Claude can decide to invoke it during a conversation) | Use for commands that Claude should be able to call proactively, like a "summarize this" command that Claude might invoke when it detects a long document. |

All six commands in this plugin use `disable-model-invocation: true` because content repurposing should be a deliberate user action -- you do not want Claude autonomously generating LinkedIn posts during an unrelated conversation.

**C analogy:** Setting `disable-model-invocation: true` is like making a function `static` in C so it is only callable from within its own translation unit (in this case, only by the user). Omitting it is like making the function `extern` -- accessible from anywhere.

---

## 4. Skills Deep Dive

### 4.1 SKILL.md File Format

Here is a simplified, annotated example based on the `key-takeaways` skill (the shortest skill in the plugin at 60 lines):

```yaml
---
name: key-takeaways                                        # [1] Skill identifier
description: Summarizes key takeaways from long-form       # [2] How Claude decides when to invoke
  content into structured bullet lists and actionable
  insights. Use when creating show notes, content
  summaries, SEO descriptions, or quick reference guides.
---

# Key Takeaways Skill                                      # [3] Human-readable title

You are a content analyst who distills long-form content   # [4] The PERSONA
into its most valuable insights. Your summaries are
concise, scannable, and actionable.

## Process                                                  # [5] Step-by-step instructions

### Step 1: Analyze the Source                              # [6] Individual process steps
Read the entire source content. Identify:
- Main thesis and supporting arguments
- Actionable advice and steps
- Surprising or counterintuitive insights
- Data points and statistics
- Tools, resources, or references mentioned
- Frameworks or mental models presented

### Step 2: Create Structured Summary

#### Executive Summary (2-3 sentences)                      # [7] Output structure/template
A brief overview of what the content covers and why
it matters.

#### Key Takeaways (7-12 bullet points)
Each takeaway should be:
- **Actionable**: The reader can do something with this
- **Self-contained**: Makes sense without reading the original
- **Specific**: Includes concrete details, not vague generalities

Format each as:
- **[Topic]**: [Specific insight or action item]

#### Deep-Dive Points (3-5 expanded takeaways)              # [8] Extended output format
...

### Step 3: Save Output                                     # [9] File output instructions
Save as a markdown file in the workspace folder.
Name: `key-takeaways-[source-name].md`

## Quality Standards                                        # [10] Acceptance criteria
- Takeaways should be ordered by importance
- Use active voice and imperative mood
- Include specific numbers, names, and details
- The summary should provide standalone value
```

**Line-by-line breakdown:**

- **[1] `name`:** The skill identifier. Must match the directory name (`skills/key-takeaways/`). Claude uses this to find and reference the skill.

- **[2] `description`:** A detailed description that Claude reads to decide whether this skill is relevant. The more specific you are (e.g., "Use when creating show notes, content summaries, SEO descriptions"), the better Claude can match the right skill to the right situation.

- **[3] Title:** A Markdown heading that serves as the human-readable name. Not used programmatically.

- **[4] Persona:** The most important line in the file. "You are a content analyst who..." tells Claude to adopt a specific role with specific capabilities. This shapes all of Claude's subsequent behavior.

- **[5]-[6] Process steps:** Numbered steps that Claude follows in order. Each step is a heading with detailed instructions underneath.

- **[7]-[8] Output templates:** Define the exact structure of what Claude should produce. Tables, bullet lists, and section headings constrain Claude's output to a predictable format.

- **[9] Save instructions:** Tell Claude where and how to save the results.

- **[10] Quality standards:** Acceptance criteria that Claude checks its output against before presenting it to the user.

### 4.2 Frontmatter Fields Reference

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `name` | string | (required) | Skill identifier. Must match the parent directory name. |
| `description` | string | (required) | Detailed description used by Claude to decide when to invoke this skill. Longer, more specific descriptions lead to better skill matching. |
| `disable-model-invocation` | boolean | `false` | If `true`, Claude cannot invoke this skill autonomously -- it must be triggered via a command. |
| `user-invocable` | boolean | `false` | If `true`, the skill appears as a slash command the user can call directly (e.g., `/skill-name`). |
| `allowed-tools` | string[] | (all) | Restricts which tools are available while executing this skill. |
| `model` | string | (default) | Override the model for this skill. |
| `context` | string[] | (none) | Additional files or resources to load as context. |
| `agent` | boolean | `false` | If `true`, the skill runs as a sub-agent with its own context window. |
| `hooks` | object | (none) | Pre/post processing hooks that run before or after the skill. |

In this plugin, all seven skills use only the two required fields: `name` and `description`.

### 4.3 The Skill Body: Writing Instructions for AI

This is where the paradigm shift happens. In C or Java, you write algorithms -- sequences of operations that manipulate data. In a SKILL.md file, you write *instructions* -- structured natural language that tells Claude what to do, step by step.

**The Persona Pattern**

Every skill in this plugin starts with a persona statement:

| Skill | Persona |
|-------|---------|
| content-extraction | "You are a content strategist specializing in repurposing long-form content into platform-specific pieces." |
| key-takeaways | "You are a content analyst who distills long-form content into its most valuable insights." |
| linkedin-post | "You are a LinkedIn content strategist who creates scroll-stopping professional posts that drive engagement." |
| quote-extractor | "You are a content curator with an eye for statements that stop the scroll." |
| twitter-thread | "You are a Twitter/X content strategist who creates viral-worthy threads." |
| substack-note | "You are a Substack content strategist who creates engaging Notes that build community." |
| title-generator | "You are a headline specialist who creates titles that demand clicks while delivering on their promise." |

The persona tells Claude to behave as a domain expert. It shapes tone, vocabulary, judgment calls, and output quality. Without a persona, Claude responds as a general-purpose assistant; with a persona, it responds as a specialist.

**C/Java analogy:** The persona is like a class constructor that initializes the object's state. Before any method runs, the object knows what it is and what capabilities it has. The persona "initializes" Claude's behavior before the process steps execute.

**The Process Pattern**

After the persona, every skill defines a numbered process:

```markdown
## Process
### Step 1: Analyze the Source
### Step 2: Ask the User (optional - interactive skills only)
### Step 3: Generate Output
### Step 4: Save Output
```

Claude follows these steps in order. You can think of each step as a method call in a sequential program. Steps can include sub-steps, bullet lists of criteria, table templates, and format specifications.

**Table Templates**

Skills define the exact structure of output tables:

```markdown
| # | Title | Angle/Hook | Key Points | Platform | Estimated Length |
|---|-------|-----------|------------|----------|-----------------|
```

Claude will fill in this table following the column definitions. This is one of the most powerful patterns: by defining a table schema, you constrain Claude's output to a structured, predictable format.

**C analogy:** Table templates are like `struct` definitions. They define the shape of the data without populating it:
```c
struct ContentIdea {
    char title[100];
    char angle[200];
    char key_points[500];
    char platform[50];
    int estimated_length;
};
```

**Quality Standards**

Every skill ends with a Quality Standards section:

```markdown
## Quality Standards
- Every idea must be specific enough to write immediately
- No generic filler ideas
- Priority ratings should be justified
- Each idea should stand alone
```

These are acceptance criteria that Claude checks its output against. If Claude's output fails a standard, it should revise before presenting the result.

**Java analogy:** Quality standards are like JUnit assertions at the end of a test. They define what "correct" looks like so you can verify the output meets expectations.

### 4.4 Skill Design Patterns (From This Plugin)

Across the seven skills in this plugin, five distinct design patterns emerge:

#### Pattern 1: Interactive (Ask Before Generating)

**Used by:** content-extraction, linkedin-post, twitter-thread

The skill includes a "Step 2: Ask the User" section that gathers preferences before generating output:

```markdown
### Step 2: Ask the User
- **How many LinkedIn posts do you want?** (1-5 recommended)
- **What tone?** (Thought leader / Tactical / Storytelling / Conversational)
- **Any specific angles to focus on?**
```

**When to use:** When the output is highly customizable and user preferences significantly affect the result. Asking first avoids generating content that the user will reject.

**Traditional analogy:** Like a constructor that requires parameters -- you cannot create the object without providing configuration first.

#### Pattern 2: Batch Generation (Create Multiple Items)

**Used by:** quote-extractor (10-15 quotes), title-generator (15-20 titles), substack-note (5-7 notes)

The skill generates a large set of items in a single invocation:

```markdown
Extract 10-15 quotes total, organized by type:

#### Headline Quotes (2-3)
#### Insight Quotes (4-5)
#### Story Quotes (2-3)
#### Data Quotes (2-3)
```

**When to use:** When the user needs multiple options to choose from, or when the task naturally produces a collection of items.

**Traditional analogy:** Like a factory method that returns a `List<T>` instead of a single object.

#### Pattern 3: Scored Output (Rate Items by Quality)

**Used by:** quote-extractor (impact scores 1-10), title-generator (scorecard with four dimensions)

The skill rates each generated item on one or more quality dimensions:

```markdown
| Title | Clarity (1-5) | Curiosity (1-5) | SEO Potential (1-5) | Click-Worthiness (1-5) | Total |
|-------|--------------|-----------------|--------------------|-----------------------|-------|
```

**When to use:** When the user needs to quickly identify the best items from a large set. Scores provide a shortcut for decision-making.

**Traditional analogy:** Like implementing `Comparable<T>` on objects so they can be sorted by quality.

#### Pattern 4: Platform-Adapted (Same Content, Different Formats)

**Used by:** content-extraction (5 platforms), title-generator (YouTube, Blog, Newsletter, Social versions)

The skill takes a single input and adapts it across multiple platforms, respecting each platform's conventions:

```markdown
#### Newsletter Ideas (5-7 ideas)
#### Substack Notes Ideas (5-7 ideas)
#### Twitter/X Thread Ideas (3-5 ideas)
#### LinkedIn Post Ideas (5-7 ideas)
#### Short-form Video Ideas (3-5 ideas)
```

**When to use:** When the same core content needs to appear on multiple platforms with different format requirements (character limits, tone, structure).

**Traditional analogy:** Like the Adapter pattern in object-oriented design, or like having a `serialize()` method that outputs JSON, XML, and CSV from the same data.

#### Pattern 5: Save-to-Workspace (Persist Output Files)

**Used by:** All seven skills

Every skill ends with a Save Output step that writes results to a file:

```markdown
### Step 3: Save Output
Save as a markdown file in the workspace folder.
Name: `key-takeaways-[source-name].md`
```

File naming follows a consistent convention: `[skill-name]-[source-identifier].[format]`

**When to use:** Always. Users expect to find their generated content saved as files they can reference, edit, and share.

**Traditional analogy:** Like writing output to a file stream at the end of a program (`fwrite` in C, `FileWriter` in Java).

### 4.5 How Claude Processes a Skill

Here is the complete internal sequence of events when a skill runs:

1. **Trigger.** The user invokes a command (e.g., `/content-repurposing:linkedin "my blog post"`) or Claude autonomously determines a skill is relevant to the conversation.

2. **Command resolution.** Claude reads the command file (`commands/linkedin.md`), substitutes `$ARGUMENTS`, and processes the body.

3. **Skill discovery.** The command body says "Use the **linkedin-post** skill." Claude locates `skills/linkedin-post/SKILL.md`.

4. **Skill loading.** Claude reads the entire SKILL.md file, parsing the frontmatter for metadata and the body for instructions.

5. **Persona adoption.** Claude internalizes the persona statement: "You are a LinkedIn content strategist..." This shapes all subsequent behavior.

6. **Process execution.** Claude follows the numbered steps:
   - Step 1: Analyzes the source content
   - Step 2: Asks the user clarifying questions (if the skill is interactive)
   - Step 3: Generates output following the templates and format specifications
   - Step 4: Saves the output file

7. **Quality validation.** Claude checks its output against the Quality Standards section. If output fails a standard, Claude revises.

8. **Output delivery.** Claude presents the results to the user and confirms the file was saved.

---

## 5. Data Flow Diagram

Here is the complete flow when a user invokes the LinkedIn command:

```
User types: /content-repurposing:linkedin "my blog post about AI"
         |
         v
Claude reads commands/linkedin.md
         |
         v
$ARGUMENTS = "my blog post about AI"
         |
         v
Command body: "Generate LinkedIn posts from the following
content: my blog post about AI"
         |
         v
Command body: "Use the linkedin-post skill to create the posts"
         |
         v
Claude reads skills/linkedin-post/SKILL.md
         |
         v
Claude adopts persona: "You are a LinkedIn content strategist..."
         |
         v
Step 1: Claude analyzes "my blog post about AI"
         |
         v
Step 2: Claude asks user:
  - "How many LinkedIn posts do you want? (1-5 recommended)"
  - "What tone? (Thought leader / Tactical / Storytelling)"
  - "Any specific angles to focus on?"
         |
         v
User responds: "3 posts, thought leader tone, focus on practical tips"
         |
         v
Step 3: Claude generates 3 LinkedIn posts following:
  - 5 hook patterns (bold statement, personal story, question, stat, contrarian)
  - 5 style variations (Story, Listicle, Hot Take, How-To, Reflection)
  - Format rules (short paragraphs, 150-300 words, 3-5 hashtags)
         |
         v
Quality check: Unique angles? Hooks that compel "see more"?
  No clickbait? Professional but human tone?
         |
         v
Step 4: Claude saves linkedin-posts-my-blog-post.md
         |
         v
Claude presents all 3 posts to the user with file path confirmation
```

---

## 6. Comparison with Traditional Architectures

If you are coming from Java/Spring or C/C++, this table maps every concept you know to its plugin equivalent:

| Concept | Java / Spring | C / C++ | This Plugin |
|---------|--------------|---------|-------------|
| Application | Spring Boot app | Compiled binary | Plugin directory (folder of .md/.json files) |
| Project descriptor | `pom.xml` / `build.gradle` | `Makefile` | `.claude-plugin/plugin.json` |
| Entry points | `@RestController` methods | `main()` functions | `commands/*.md` files |
| Business logic | `@Service` classes | Library modules (.c/.h) | `skills/*/SKILL.md` files |
| Method parameters | `@RequestParam`, `@PathVariable` | `argc`/`argv` | `$ARGUMENTS` |
| Annotations / metadata | `@GetMapping`, `@PostMapping` | `#pragma`, compiler flags | YAML frontmatter (`---` blocks) |
| Business rules | Java code (if/else, loops) | C code (algorithms) | Natural language instructions |
| Return type | `ResponseEntity<T>` | `int` / `struct*` | Markdown tables + saved files |
| Dependency injection | `@Autowired` | `#include` + linker | "Use the **skill-name** skill" |
| Build step | `mvn package` / `gradle build` | `make` / `gcc` | None (interpreted at runtime) |
| Package format | `.jar` / `.war` | `.so` / `.dll` / executable | `.zip` file |
| Deployment | Docker / Tomcat / Kubernetes | Install binary | Upload zip to Claude Cowork |
| Testing | JUnit / Mockito | CUnit / Google Test | `claude --plugin-dir ./my-plugin` |
| Configuration | `application.yml` | `.ini` / `.cfg` files | YAML frontmatter fields |

**Key differences from compiled-language architectures:**

1. **No type system.** There are no declared types, interfaces, or contracts. Quality standards in the skill file serve a similar purpose: they define "correct output" without formal types.

2. **No compilation.** Changes take effect immediately. Edit a SKILL.md file, invoke the command, and see the new behavior. There is no build-test-deploy cycle.

3. **Non-deterministic output.** Unlike a compiled function that always returns the same result for the same input, skills produce different output each time because Claude interprets instructions probabilistically. Quality standards help constrain this variability.

4. **Human-readable "code."** Anyone who can read English can understand what a skill does. This eliminates the gap between specification documents and implementation.

---

## 7. Extension Points

The plugin architecture supports several extension mechanisms beyond commands and skills:

### Connectors (MCP Servers)

MCP (Model Context Protocol) servers let your plugin interact with external systems -- APIs, databases, file services. For example, you could add an MCP connector so the content-extraction skill writes directly to a Notion database or posts to a Slack channel.

**C/Java analogy:** MCP connectors are like JDBC drivers or REST client libraries -- they provide a standardized interface between your code and an external system.

### Hooks (Pre/Post Processing)

Hooks are shell commands that execute before or after a command or skill runs. For example, a pre-hook could fetch the latest content from an RSS feed before the extraction skill processes it, and a post-hook could push the generated files to a Git repository.

**C/Java analogy:** Hooks are like Makefile targets that run before or after the main build, or like `@Before` and `@After` annotations in JUnit.

### Sub-agents (Specialized Agents Within Skills)

By setting `agent: true` in a skill's frontmatter, the skill runs as an independent sub-agent with its own context window. This is useful for skills that need to process very large documents or maintain separate state.

**C/Java analogy:** Running a skill as a sub-agent is like spawning a child process instead of calling a function in the same process -- the sub-agent has its own memory space and lifecycle.

### Future Possibilities

- **Skill chaining:** One skill invokes another skill, creating multi-step pipelines (extract content, then generate titles, then create social posts).
- **Conditional routing:** Commands that route to different skills based on user input or content type.
- **Shared state:** Skills that read from and write to a shared data store within a conversation.

---

## 8. Security Considerations

Claude Cowork plugins operate within a security sandbox:

1. **No arbitrary code execution.** Skills are natural language instructions, not executable code. There is no `eval()`, no shell access, no way to run arbitrary programs from within a skill. Claude interprets instructions, but it does so within its safety constraints.

2. **Tool restrictions.** The `allowed-tools` frontmatter field can restrict which tools (Read, Write, Bash, Glob, Grep) a skill can use. A skill that only generates text can be restricted to no tools at all.

3. **Permission modes.** When running plugins locally with Claude Code, the permission mode controls what Claude can do without asking for user approval. The default mode prompts the user before sensitive operations (writing files, running commands). The `bypassPermissions` mode used during the agent build is only appropriate for trusted, controlled environments.

4. **User approval for sensitive actions.** Even with tool access, Claude will prompt the user before performing potentially destructive operations -- deleting files, executing system commands, or accessing external services.

5. **No network access from skills.** Skills cannot make HTTP requests or access the internet directly. External access requires MCP connectors, which must be explicitly configured and approved.

**For C/Java developers:** This security model is more restrictive than running a compiled program, which has full OS access. Think of it as running code in a Java Security Manager sandbox where permissions must be explicitly granted.

---

*See also: [Agent Team Guide](AGENT_TEAM_GUIDE.md) for how the agent team built this plugin, [Developer Guide](DEVELOPER_GUIDE.md) for hands-on plugin development, and [Glossary](GLOSSARY.md) for term definitions.*
