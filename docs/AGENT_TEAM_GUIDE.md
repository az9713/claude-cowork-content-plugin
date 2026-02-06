# Agent Teams: How 6 AI Agents Built This Plugin

> **Who this is for:** Developers with C, C++, or Java experience who are new to AI agent workflows. This guide explains agent teams from the ground up, walks through how two separate agent teams built this project — one team of 6 agents built the plugin in 5 minutes, a second team of 6 agents wrote all the documentation in 4 minutes — and teaches you how to create your own agent teams.

---

## 1. What Are Agent Teams?

### The Concept

Agent Teams are groups of AI agents that work together on complex tasks, coordinated by a team lead. Each agent is an independent Claude session -- its own context window, its own tools, its own assigned work -- that runs in parallel with the others.

Think of it this way: instead of one developer writing 14 files sequentially, you have 6 developers working simultaneously, each writing their assigned files while a project manager keeps everything on track.

The team lead (also a Claude instance, running on the most capable model) handles the coordination: it designs the plan, creates tasks, spawns worker agents, manages dependencies, monitors progress, and shuts everyone down when the work is done.

### Analogy for Traditional Developers

If you come from C, C++, or Java, you already understand the core concepts. Agent teams map directly to patterns you know.

**Multi-threaded program analogy:**
- **Team lead** = main thread (spawns workers, coordinates, joins)
- **Worker agents** = worker threads (each handles one task independently)
- **Task list** = thread-safe work queue (tracks what needs to be done)
- **SendMessage** = inter-thread communication (message passing between threads)
- **Task dependencies** = synchronization barriers (thread B waits until thread A signals completion)
- **shutdown_request** = thread cancellation / join (tell a thread to terminate, wait for confirmation)

**Build system analogy:**
- **Team lead** = build tool (Make, Maven, Gradle)
- **Tasks** = build targets (compile this, generate that)
- **Dependencies** = build dependencies (`javac` before `jar`, `mkdir` before `cp`)
- **Parallel agents** = parallel build (`make -j6`)
- **Completion** = build success (all targets built)

**Microservices analogy:**
- **Team lead** = orchestration service (Kubernetes orchestrator, workflow engine)
- **Worker agents** = microservices (each handles a specific domain)
- **Task list** = message queue (Kafka, RabbitMQ)
- **SendMessage** = API calls between services
- **Task dependencies** = service dependencies (service B depends on service A's output)

### Key Components

1. **Team**: A named group that provides the organizational context. Created once, deleted when all work is done. (Like a thread pool or a project workspace.)

2. **Team Lead**: The orchestrating agent. It creates the plan, spawns workers, manages the lifecycle. Runs on the most capable model (Opus 4.6 in this project). (Like the main thread in a multi-threaded program.)

3. **Workers / Teammates**: Agents that do the actual work. Each is an independent Claude session with its own tools and instructions. (Like worker threads or microservice instances.)

4. **Tasks**: Units of work with a subject, description, status, owner, and dependency relationships. Tasks progress through `pending` -> `in_progress` -> `completed`. (Like work items in a task queue or build targets in a Makefile.)

5. **Messages**: Communication channel between agents. Used for coordination, status updates, unblock notifications, and shutdown requests. (Like message passing in concurrent systems.)

6. **Task Dependencies**: Relationships that enforce ordering -- "Task B cannot start until Task A completes." (Like build dependencies or synchronization barriers.)

---

## 2. How Our Team Worked

### 2.1 The Team Structure

```
                      +----------------+
                      |   TEAM LEAD    |
                      | (Claude Opus   |
                      |     4.6)       |
                      +-------+--------+
                              |
         +----------+---------+----------+----------+----------+
         |          |         |          |          |          |
   +-----v----+ +--v------+ +v--------+ +v-------+ +v-------+ +v-------+
   | scaffold- | | command-| |extract- | |takeaw- | |social- | |content-|
   | builder   | | builder | |ion-skill| |ays-    | |skills- | |skills- |
   |           | |         | |-builder | |quotes- | |builder | |builder |
   | Task #1   | | Task #2 | | Task #3 | |builder | | Task #5| | Task #6|
   | (BLOCKING)| |         | |         | |Task #4 | |        | |        |
   +-----+-----+ +----+----+ +----+----+ +---+----+ +---+----+ +---+----+
         |             |          |           |          |          |
     plugin.json    6 command   content-   key-take-  linkedin-  substack-
     + 10 dirs      files       extraction aways +    post +     note +
                                SKILL.md   quote-ext  twitter-   title-gen
                                (223 lines) SKILL.md  thread     SKILL.md
                                           (60+58 ln) SKILL.md   (54+82 ln)
                                                      (68+67 ln)
```

### 2.2 What Each Agent Did

#### scaffold-builder (Task #1) -- The Foundation Layer

**Role:** Create the plugin manifest and all directories.

**Files created:**
- `.claude-plugin/plugin.json` (10 lines) -- plugin manifest with name, description, version, author, keywords
- 10 directories: `.claude-plugin/`, `commands/`, `skills/`, and 7 skill subdirectories

**Why it mattered:** Every other agent needed directories to exist before writing files into them. This is the same reason you run `mkdir -p` before `cp` in a shell script, or why a build system creates `target/classes/` before running `javac`. Without the scaffold, all other agents would fail with "directory not found" errors.

**Time:** ~30 seconds. Fastest task because it only created one file and empty directories.

#### command-builder (Task #2) -- The Entry Points

**Role:** Create all 6 command files in `commands/`.

**Files created:**

| File | Lines | Purpose |
|------|-------|---------|
| `all.md` | 27 | Master list of all commands and skills |
| `extract.md` | 16 | Content extraction, routes to content-extraction skill |
| `linkedin.md` | 15 | LinkedIn posts, routes to linkedin-post skill |
| `quotes.md` | 15 | Quote extraction, routes to quote-extractor skill |
| `titles.md` | 15 | Title generation, routes to title-generator skill |
| `twitter.md` | 15 | Twitter threads, routes to twitter-thread skill |

**Design pattern:** All commands follow the same structure: YAML frontmatter with `description` and `disable-model-invocation: true`, then a body that routes to a skill. This consistency was ensured by the team lead providing exact file content in the agent's prompt.

**Time:** ~1 minute after unblock.

#### extraction-skill-builder (Task #3) -- The Flagship Skill

**Role:** Create the content-extraction SKILL.md, the most complex file in the plugin.

**Files created:**
- `skills/content-extraction/SKILL.md` (223 lines)

**Why it took longest:** This skill covers 5 platforms (Newsletter, Substack Notes, Twitter/X, LinkedIn, Short-form Video), each with its own table schema, 6-7 format types per platform, and a complete 2-4 week execution roadmap. It is the largest single file in the plugin by a factor of 2.7x.

**Time:** ~3.5 minutes after unblock. Last agent to finish.

#### takeaways-quotes-builder (Task #4) -- Analysis Skills

**Role:** Create key-takeaways and quote-extractor SKILL.md files.

**Files created:**
- `skills/key-takeaways/SKILL.md` (60 lines) -- content analyst persona, structured summary output
- `skills/quote-extractor/SKILL.md` (58 lines) -- content curator persona, 10-15 quotes with impact scores

**Time:** ~1.25 minutes after unblock.

#### social-skills-builder (Task #5) -- Social Platform Skills

**Role:** Create linkedin-post and twitter-thread SKILL.md files.

**Files created:**
- `skills/linkedin-post/SKILL.md` (68 lines) -- LinkedIn strategist persona, 5 hook patterns, 5 style variations
- `skills/twitter-thread/SKILL.md` (67 lines) -- Twitter strategist persona, 280-char limit enforcement, 4 thread styles

**Time:** ~2 minutes after unblock.

#### content-skills-builder (Task #6) -- Content Platform Skills

**Role:** Create substack-note and title-generator SKILL.md files.

**Files created:**
- `skills/substack-note/SKILL.md` (54 lines) -- Substack strategist persona, 7 note types, no-hashtags rule
- `skills/title-generator/SKILL.md` (82 lines) -- headline specialist persona, 15-20 titles, scorecard system

**Time:** ~2.25 minutes after unblock.

### 2.3 The Dependency Graph

```
    Task #1 (scaffold-builder)
       |
       | BLOCKS all other tasks
       |
    +--+--+--+--+--+
    |  |  |  |  |  |
    v  v  v  v  v  v
   #2 #3 #4 #5 #6     <-- All 5 run in PARALLEL after #1 completes
```

This is the simplest possible dependency graph: one blocking task, then everything else runs in parallel. There are no dependencies between Tasks #2-#6 -- the command-builder does not need to wait for any skill-builder, and no skill-builder depends on another.

**Why this works:** Each agent writes to its own files in its own directories. There are no shared resources, no race conditions, no need for locking. This is embarrassingly parallel work.

**Why Task #1 was blocking:** Directories must exist before files can be written into them. If the command-builder tried to write `commands/extract.md` before `commands/` existed, the write would fail. The scaffold-builder creates all directories first, then all writers start simultaneously.

**C analogy:** This is like calling `mkdir -p` before `gcc -o output.o`. The compile step depends on the output directory existing, but once it exists, all source files can compile in parallel (`make -j6`).

**Java analogy:** Like running `mvn initialize` (which creates `target/classes/`) before `mvn compile`. The compile phase cannot start until the directory structure exists.

### 2.4 The Timeline

```
T+0:00  Team lead creates team "content-repurposing-plugin" and 6 tasks
        Team lead defines dependency graph: Tasks #2-#6 blocked by Task #1
T+0:01  scaffold-builder spawned (only unblocked task)
T+0:02  5 remaining agents spawned (blocked, waiting for Task #1)
T+0:30  scaffold-builder completes Task #1 (plugin.json + 10 directories)
T+0:31  Team lead marks Task #1 complete, sends unblock messages to 5 agents
T+0:32  All 5 agents begin working in parallel
        |  command-builder starts writing 6 command files
        |  extraction-skill-builder starts writing content-extraction SKILL.md
        |  takeaways-quotes-builder starts writing 2 skill files
        |  social-skills-builder starts writing 2 skill files
        |  content-skills-builder starts writing 2 skill files
T+1:30  command-builder completes Task #2 (6 command files)
T+1:45  takeaways-quotes-builder completes Task #4 (2 skill files, 118 lines)
T+2:30  social-skills-builder completes Task #5 (2 skill files, 135 lines)
T+2:45  content-skills-builder completes Task #6 (2 skill files, 136 lines)
T+4:00  extraction-skill-builder completes Task #3 (1 skill file, 223 lines)
T+4:30  Team lead shuts down all agents and deletes team
```

**Total wall-clock time:** ~5 minutes
**Parallelism achieved:** 5 agents working simultaneously
**Sequential bottleneck:** Only Task #1 (30 seconds of directory creation)
**Estimated sequential time:** If one agent did everything: ~15-20 minutes

The parallelism saved roughly 10-15 minutes. The 30-second sequential bottleneck was unavoidable but minimal.

### 2.5 Communication Flow

The team used five types of messages during the build:

**1. Task assignment (team lead -> agents):**
When the team lead spawned each agent, it included a detailed prompt with the exact file content to write. This is the most important message -- it defines everything the agent needs to do.

```
Team lead spawns scaffold-builder with prompt:
  "Create the following files and directories:
   .claude-plugin/plugin.json containing: { ... exact content ... }
   commands/ (empty directory)
   skills/content-extraction/ (empty directory)
   ..."
```

**2. Unblock notifications (team lead -> agents):**
After Task #1 completed, the team lead sent messages to all 5 blocked agents telling them to begin.

```
Team lead -> command-builder: "Task #1 is complete. You are unblocked.
  Begin writing command files."
```

**3. Completion reports (agents -> team lead):**
Each agent reported back when finished, listing the files created and their sizes.

```
command-builder -> Team lead: "Task #2 complete.
  Created 6 files: all.md (27 lines), extract.md (16 lines), ..."
```

**4. Status checks (team lead -> agents):**
The team lead monitored progress and checked on the last running agent.

```
Team lead -> extraction-skill-builder: "You are the last agent
  still working. How is progress on the content-extraction skill?"
```

**5. Shutdown sequence (team lead -> agents -> team lead):**

```
Team lead -> command-builder: shutdown_request("Task complete, wrapping up")
command-builder -> Team lead: shutdown_approved (agent terminates)
```

Additionally, all agents used the shared task system (TaskGet to read their task description, TaskUpdate to mark tasks as `in_progress` and then `completed`).

---

## 3. The Documentation Team: A Second Agent Team Build

This project used **two separate agent teams**, not one. After the plugin team finished and was deleted, a second team was created to write all the documentation you are reading right now. This section documents that second build and compares the two teams.

### 3.1 Why a Second Team?

The plugin build produced 14 files of code. But code without documentation has limited educational value. The project needed 10 comprehensive documentation files totaling thousands of lines — CLAUDE.md, README.md, a glossary, a troubleshooting guide, a quick start guide with 12 use cases, a developer guide, a user guide, a plugin architecture deep-dive, and this very agent team guide.

Writing 10 detailed documents sequentially would take a single agent 20-30 minutes. A 6-agent team could do it in roughly 4 minutes.

### 3.2 Team Structure

```
Team: "docs-team"
Team Lead: Claude Opus 4.6 (same session that led the plugin build)

                    ┌──────────────────┐
                    │    TEAM LEAD     │
                    │  (Claude Opus    │
                    │      4.6)        │
                    └────────┬─────────┘
                             │
       ┌──────────┬──────────┼──────────┬──────────┬──────────┐
       │          │          │          │          │          │
 ┌─────▼─────┐ ┌─▼────────┐ ┌▼────────┐ ┌▼───────┐ ┌▼───────┐ ┌▼──────────┐
 │foundation-│ │ readme-   │ │quickst- │ │develop-│ │ user-  │ │architectu-│
 │ writer    │ │ writer    │ │art-     │ │er-guide│ │ guide- │ │re-writer  │
 │           │ │           │ │writer   │ │-writer │ │ writer │ │           │
 │ 3 files   │ │ 1 file    │ │ 1 file  │ │ 1 file │ │ 1 file │ │ 2 files   │
 └───────────┘ └───────────┘ └─────────┘ └────────┘ └────────┘ └───────────┘
   Task #1       Task #2      Task #3     Task #4    Task #5     Task #6
```

### 3.3 What Each Agent Did

| Agent | Task | Files Created | Lines | Time |
|---|---|---|---|---|
| foundation-writer | #1 | CLAUDE.md (53), GLOSSARY.md (127), TROUBLESHOOTING.md (444) | 624 | ~1.5 min |
| readme-writer | #2 | README.md (192) | 192 | ~1 min |
| quickstart-writer | #3 | QUICK_START_GUIDE.md (878) | 878 | ~2.5 min |
| developer-guide-writer | #4 | DEVELOPER_GUIDE.md (1,084) | 1,084 | ~3 min |
| user-guide-writer | #5 | USER_GUIDE.md (781) | 781 | ~2.5 min |
| architecture-writer | #6 | PLUGIN_ARCHITECTURE.md (665), AGENT_TEAM_GUIDE.md (796) | 1,461 | ~4 min |
| **TOTAL** | **6 tasks** | **10 files** | **~5,298** | **~4 min** |

#### foundation-writer (Task #1) — Reference Documents

Created three foundational reference files:
- **CLAUDE.md** (53 lines): Project memory file loaded into every Claude Code session. Contains file structure, conventions, testing commands, and the "do not use Alex McFarland" rule.
- **GLOSSARY.md** (127 lines): 30 terms defined alphabetically with C/Java analogies. Every term used across all documentation is defined here.
- **TROUBLESHOOTING.md** (444 lines): 7 categories of common issues (installation, commands, skills, agent teams, output, testing, getting help) with symptom/cause/fix format.

This was the fastest agent because the three files, while numerous, are shorter and more formulaic than narrative documentation.

#### readme-writer (Task #2) — Project Landing Page

Created the main **README.md** (192 lines): project branding, the "6 agents in 5 minutes" story, project structure tree, prerequisites, quick start teaser, documentation guide table, and attribution/credits section.

This agent needed to know the names and paths of all other documentation files (even though they did not exist yet) so it could link to them. The team lead provided this list in the agent's prompt.

#### quickstart-writer (Task #3) — 12 Use Cases

Created **QUICK_START_GUIDE.md** (878 lines): installation walkthrough, then 12 self-contained use cases ranging from "See What's Available" (1 minute, beginner) to "Create Your First Custom Skill" (10 minutes, intermediate). Each use case includes sample content that users can paste directly.

This agent had to invent sample content for each use case (productivity tips, remote work advice, AI industry trends) to make the examples realistic and educational.

#### developer-guide-writer (Task #4) — Developer Handbook

Created **DEVELOPER_GUIDE.md** (1,084 lines): the largest single documentation file. Covers prerequisites with C/Java analogies, a concept mapping table (Plugin = JAR, Skill = Service class, etc.), complete project structure walkthrough, deep dives on manifests, commands, skills, frontmatter, step-by-step skill modification, creating new skills from scratch, creating new commands, testing, packaging, agent teams for development, and a developer FAQ.

This agent received the most detailed prompt — it needed to understand every plugin file to describe them accurately.

#### user-guide-writer (Task #5) — End-User Manual

Created **USER_GUIDE.md** (781 lines): written for non-technical users who may have never used an AI tool beyond basic ChatGPT. Covers what Claude Cowork is, what plugins are (with phone app analogy), step-by-step installation with every click described, commands vs skills comparison table, detailed walkthrough of each command, output file management, best practices, and 15+ FAQs.

#### architecture-writer (Task #6) — Technical Deep-Dives

Created two files:
- **PLUGIN_ARCHITECTURE.md** (665 lines): technical anatomy of a plugin, manifest schema, command deep-dive, skill deep-dive with design patterns, data flow diagram, and a Java/Spring comparison table.
- **AGENT_TEAM_GUIDE.md** (796 lines): this file. Agent team concepts, the plugin build walkthrough, step-by-step recreation guide, best practices, API reference, and FAQ.

This agent had the heaviest workload (2 large files, 1,461 lines total) and was the last to finish — taking approximately 4 minutes. This mirrors the plugin build where the agent with the most content (extraction-skill-builder, 223 lines) was also the last to finish.

### 3.4 Dependency Graph: No Dependencies

```
   Task #1   Task #2   Task #3   Task #4   Task #5   Task #6
     │         │         │         │         │         │
     ▼         ▼         ▼         ▼         ▼         ▼
  foundation readme  quickstart dev-guide user-guide architecture
   writer     writer    writer    writer    writer     writer
     │         │         │         │         │         │
     ▼         ▼         ▼         ▼         ▼         ▼
  (3 files)  (1 file)  (1 file)  (1 file)  (1 file)  (2 files)

  ALL 6 AGENTS RUN IN PARALLEL FROM THE START
  No blocking dependencies. No scaffold phase needed.
```

**Why no dependencies?** Unlike the plugin build where directories had to exist before files could be written into them, all documentation files are written to directories that already existed (`docs/` and the project root). No agent needed the output of another agent — the README could reference `docs/GLOSSARY.md` even before the glossary existed, because it was linking by file path, not by content.

**Contrast with the plugin team:** The plugin team had a fan-out dependency pattern (1 blocking scaffold → 5 parallel workers). The docs team had a flat pattern (6 fully parallel workers, zero dependencies). This made the docs team simpler to coordinate — no unblock messages were needed.

### 3.5 Timeline

```
T+0:00  Team lead creates team "docs-team" and 6 tasks
T+0:00  All 6 tasks created with NO dependencies
T+0:01  All 6 agents spawned simultaneously (all unblocked)
        │
        │  ┌── readme-writer ────────── DONE at T+1:00
        │  ├── foundation-writer ────── DONE at T+1:30
        │  ├── user-guide-writer ────── DONE at T+2:30
        │  ├── quickstart-writer ────── DONE at T+2:30
        │  ├── developer-guide-writer ─ DONE at T+3:00
        │  └── architecture-writer ──── DONE at T+4:00 (2 large files)
        │
T+4:00  Last agent completes
T+4:15  Team lead shuts down all agents and deletes team

TOTAL WALL-CLOCK TIME: ~4 minutes
TOTAL LINES WRITTEN: 5,298
PARALLELISM: 6 agents from start (no sequential phase)
ESTIMATED SEQUENTIAL TIME: ~20-25 minutes
SPEEDUP: ~5-6x
```

### 3.6 Communication Flow

The docs team used a simpler communication pattern than the plugin team because there were no dependencies to manage:

**1. Task assignment (team lead → agents at spawn time):**
Each agent received a single comprehensive prompt containing:
- The project branding and context
- The target audience description
- The exact files to create with detailed content specifications
- Cross-reference paths to other docs (so agents could link to files being written by other agents)
- Quality requirements (step-by-step hand-holding, C/Java analogies, zero assumed knowledge)

**2. Completion reports (agents → team lead):**
Each agent messaged the team lead when finished, reporting files created and line counts.

**3. Status check (team lead → architecture-writer):**
When the architecture-writer was the last agent running, the team lead sent a status check. The architecture-writer was writing AGENT_TEAM_GUIDE.md (796 lines) — the second-largest file — and needed extra time.

**4. Shutdown sequence (team lead → agents → team lead):**
Standard shutdown_request / shutdown_approved exchange, same as the plugin team.

**What was NOT needed:** No unblock messages (no dependencies), no broadcast messages (no team-wide announcements), no error recovery (all agents completed successfully).

### 3.7 Comparing the Two Teams

This project demonstrates two different agent team patterns. Here is a side-by-side comparison:

| Aspect | Plugin Team | Documentation Team |
|---|---|---|
| **Team name** | `content-repurposing-plugin` | `docs-team` |
| **Team lead** | Claude Opus 4.6 | Claude Opus 4.6 (same session) |
| **Number of agents** | 6 | 6 |
| **Agent type** | All `builder` | All `builder` |
| **Permission mode** | `bypassPermissions` | `bypassPermissions` |
| **Tasks** | 6 | 6 |
| **Dependencies** | 1 blocking (scaffold) → 5 parallel | None (all 6 parallel from start) |
| **Files created** | 14 | 10 |
| **Total lines** | ~742 | ~5,298 |
| **Wall-clock time** | ~5 minutes | ~4 minutes |
| **Sequential bottleneck** | 30 seconds (scaffold) | None |
| **Largest single file** | 223 lines (content-extraction) | 1,084 lines (DEVELOPER_GUIDE) |
| **Slowest agent** | extraction-skill-builder (3.5 min) | architecture-writer (4 min) |
| **Unblock messages sent** | 5 (one per blocked agent) | 0 |
| **Inter-team communication** | None | None |

### 3.8 Inter-Team vs. Intra-Team Communication

**Intra-team communication** (within a single team) is the standard pattern used in both builds:
- Team lead sends prompts to agents at spawn time
- Team lead sends unblock messages when blocking tasks complete (plugin team only)
- Agents send completion reports to the team lead
- Team lead sends status checks to slow agents
- Team lead sends shutdown requests; agents approve and terminate
- All agents share the same task list and use TaskUpdate to track progress

**Inter-team communication** (between the plugin team and the docs team) did not occur, because the two teams existed at different times:

```
Timeline:
  ┌──────────────────────┐
  │  Plugin Team          │  ← Created, did work, shut down, DELETED
  │  (5 minutes)          │
  └──────────┬───────────┘
             │
             │  (team deleted, then...)
             │
  ┌──────────▼───────────┐
  │  Documentation Team   │  ← Created fresh, did work, shut down, DELETED
  │  (4 minutes)          │
  └──────────────────────┘
```

The two teams were **sequential, not concurrent**. The plugin team was completely finished and deleted before the docs team was created. This means:

- No shared task list between teams
- No messages between plugin agents and docs agents
- No dependency between a plugin task and a docs task
- The only connection is the **team lead** — the same Claude Opus 4.6 session led both teams, carrying forward the knowledge of what was built

**Why sequential?** The docs team needed to document the plugin. It needed to read the actual plugin files (commands, skills, plugin.json) to write accurate documentation. If the two teams ran concurrently, docs agents might try to read plugin files that had not been written yet. Running them sequentially eliminated this risk entirely.

**Could they have been concurrent?** In theory, yes — with careful dependency management. You could create a single team with 12 agents where docs tasks depend on plugin tasks. But this adds complexity for little benefit. The sequential approach is simpler and the total time (5 + 4 = 9 minutes) is still dramatically faster than a single agent doing everything (~40+ minutes).

### 3.9 Lessons Learned from the Docs Build

1. **Agents writing 2+ large files take the longest.** The architecture-writer (2 files, 1,461 lines) was the bottleneck. Consider splitting large workloads across more agents for better parallelism.

2. **No-dependency teams are simpler to manage.** Without blocking tasks, there is no need for unblock messages, no risk of deadlock, and no coordination overhead beyond spawning and shutdown.

3. **Cross-referencing works even with parallel writes.** Agents can link to files being written by other agents (e.g., README linking to GLOSSARY.md) as long as the team lead specifies all file paths in advance. The links work because they reference paths, not content.

4. **Documentation agents need more context than code agents.** Plugin agents received exact file content in their prompts. Documentation agents needed broader context: the project branding, the target audience profile, C/Java analogy requirements, the full plugin file structure, and the list of all other docs for cross-referencing.

5. **The same team lead can orchestrate multiple sequential teams.** The Opus 4.6 session that led the plugin build accumulated knowledge about the project structure. This context made it more effective as docs team lead — it already understood every plugin file and could write better prompts for the docs agents.

---

## 4. Step-by-Step: How to Create Your Own Agent Team

This section walks through every step of creating an agent team, using a hypothetical "meeting-notes" plugin as an example. Follow along to build your own.

### Step 1: Design Your Architecture (Planning Phase)

Before creating any agents or tasks, answer these questions on paper (or in your head):

**What is the final output?**
List every file and directory the team needs to create:
```
meeting-notes/
  .claude-plugin/plugin.json
  commands/summarize.md
  commands/action-items.md
  commands/decisions.md
  skills/meeting-summary/SKILL.md
  skills/action-tracker/SKILL.md
```

**How can the work be split into independent tasks?**
Look for natural boundaries:
- Task A: Create manifest + directories (must be first)
- Task B: Create 3 command files (independent of skills)
- Task C: Create meeting-summary skill (independent of other skills)
- Task D: Create action-tracker skill (independent of other skills)

**What are the dependencies?**
Draw the dependency graph:
```
  Task A (scaffold)
  +--+--+
  |  |  |
  v  v  v
  B  C  D    (all parallel after A)
```

**How many agents do you need?**
One per independent task. In this case: 4 agents (1 scaffold + 3 content).

**Rule of thumb:** If two tasks write to different files and do not need each other's output, they can run in parallel. If task B needs the output of task A (beyond just directory existence), B must be blocked by A.

### Step 2: Create the Team

The team lead uses the TeamCreate API to establish the team:

```
TeamCreate:
  team_name: "meeting-notes-plugin"
  description: "Building a meeting-notes plugin for Claude Cowork"
```

**What this does:** Creates team configuration and an empty task list. This is like initializing a project workspace -- no work happens yet, but the infrastructure for tracking work is in place.

**C analogy:** `pthread_create()` for the thread pool, before any threads are spawned.

**Java analogy:** `ExecutorService pool = Executors.newFixedThreadPool(4)` -- creates the pool, but no tasks are submitted yet.

### Step 3: Create Tasks with Dependencies

Create each task with a subject, description, and activeForm:

```
TaskCreate:
  subject: "Create plugin scaffold"
  description: "Create .claude-plugin/plugin.json with the following content:
    { \"name\": \"meeting-notes\", \"version\": \"1.0.0\", ... }
    Create directories: commands/, skills/meeting-summary/, skills/action-tracker/"
  activeForm: "Creating plugin scaffold"

TaskCreate:
  subject: "Create command files"
  description: "Create commands/summarize.md, commands/action-items.md,
    commands/decisions.md. Each command should use $ARGUMENTS and route
    to the appropriate skill..."
  activeForm: "Writing command files"

TaskCreate:
  subject: "Create meeting-summary skill"
  description: "Create skills/meeting-summary/SKILL.md with name: meeting-summary,
    description: 'Summarizes meeting recordings...', persona: 'You are a meeting
    analyst...', process steps: [1] Identify participants, [2] Extract topics..."
  activeForm: "Writing meeting-summary skill"

TaskCreate:
  subject: "Create action-tracker skill"
  description: "Create skills/action-tracker/SKILL.md with name: action-tracker..."
  activeForm: "Writing action-tracker skill"
```

Then set dependencies so Tasks #2-#4 wait for Task #1:

```
TaskUpdate:
  taskId: "2"
  addBlockedBy: ["1"]

TaskUpdate:
  taskId: "3"
  addBlockedBy: ["1"]

TaskUpdate:
  taskId: "4"
  addBlockedBy: ["1"]
```

**Key fields explained:**

| Field | Purpose | Analogy |
|-------|---------|---------|
| `subject` | Brief title shown in task list (imperative form: "Create X") | Makefile target name |
| `description` | Detailed instructions for the agent. **Be extremely specific.** | The recipe body of a Makefile target |
| `activeForm` | Present-tense label shown while the task runs ("Creating X") | Build progress message |
| `addBlockedBy` | List of task IDs that must complete before this task can start | Makefile dependencies (`target: dep1 dep2`) |

### Step 4: Spawn Agents

Use the Task tool to create each agent:

```
Task:
  description: "Create the plugin scaffold for the meeting-notes plugin"
  subagent_type: builder
  mode: bypassPermissions
  name: scaffold-builder
  team_name: meeting-notes-plugin
  prompt: "You are scaffold-builder on the meeting-notes-plugin team.
    Your task: Create the following files and directories...
    [Include EXACT file content here]"
```

**Key parameters explained:**

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `subagent_type` | `builder` | Gives the agent Read, Write, Edit, Bash, Glob, Grep tools. This is the type for agents that create/modify files. |
| `mode` | `bypassPermissions` | Lets the agent write files without prompting the user for approval on each write. Use only in controlled environments. |
| `name` | `scaffold-builder` | Human-readable identifier. Other agents use this name to send messages. |
| `team_name` | `meeting-notes-plugin` | Links the agent to the team so it can access the shared task list. |
| `prompt` | (detailed text) | The full instructions for the agent. **This is the most important parameter.** |

**Available subagent_type values:**

| Type | Tools | Use For |
|------|-------|---------|
| `builder` | Read, Write, Edit, Bash, Glob, Grep | Creating and modifying files (used in this project) |
| `Explore` | Read, Glob, Grep (read-only) | Researching code without making changes |
| `Plan` | Read, Glob, Grep (read-only) | Creating plans without implementing |
| `general-purpose` | All tools | Flexible agents that can do anything |

**Available mode values:**

| Mode | Behavior | When to Use |
|------|----------|-------------|
| `bypassPermissions` | Agent acts without asking user approval | Trusted, controlled environments. Used in this project. |
| `default` | Agent asks user before sensitive actions | Most real-world scenarios |
| `plan` | Agent can only read and plan, not write | Safety-critical planning phases |

**Critical lesson from this project:** The team lead provided **exact file content** in each agent's prompt. Rather than telling an agent "write a LinkedIn post skill," the team lead wrote out the complete 68-line SKILL.md content and told the agent to write exactly that. This eliminates ambiguity and ensures consistency across agents.

### Step 5: Monitor and Coordinate

While agents work, the team lead:

1. **Watches for completion messages.** Each agent sends a message when it finishes its task, listing the files created.

2. **Sends unblock notifications.** When the blocking task (scaffold) completes, the team lead notifies all blocked agents:

```
SendMessage:
  type: message
  recipient: command-builder
  content: "Task #1 (scaffold) is complete. All directories exist. You are
    unblocked -- begin writing your command files."
  summary: "Scaffold done, you are unblocked"
```

3. **Handles errors.** If an agent reports a problem (file write failed, directory not found), the team lead diagnoses and resolves:
   - Is the agent trying to write before its dependency completed? Send an unblock message.
   - Did the scaffold miss a directory? Ask scaffold-builder to create it, or send the missing directory path to the blocked agent.

4. **Checks progress on long-running tasks.** The team lead can ping agents:

```
SendMessage:
  type: message
  recipient: extraction-skill-builder
  content: "You are the last agent still working. Status update?"
  summary: "Checking progress on extraction skill"
```

### Step 6: Shut Down Cleanly

When an agent completes its task, the team lead sends a shutdown request:

```
SendMessage:
  type: shutdown_request
  recipient: scaffold-builder
  content: "Your task is complete. Shutting down."
```

The agent processes the request and responds:

```
SendMessage:
  type: shutdown_response
  request_id: "abc-123"
  approve: true
```

The agent then terminates. The team lead repeats this for each agent.

**C analogy:** `shutdown_request` is like `pthread_cancel()` followed by `pthread_join()`. You signal the thread to stop, then wait for it to confirm termination.

**Java analogy:** Like calling `future.cancel(true)` or `executorService.shutdownNow()` and then waiting for `awaitTermination()`.

After all agents are shut down, clean up the team:

```
TeamDelete
```

### Step 7: Verify Results

After the team is deleted, verify all files were created:

1. Check the directory structure: `ls -R meeting-notes/`
2. Verify each file has content (not empty)
3. Test the plugin locally: `claude --plugin-dir ./meeting-notes`
4. Try each command to confirm it works

---

## 5. Best Practices

### 4.1 Prompt Design for Agents

The quality of your agent team's output is almost entirely determined by the quality of the prompts you give each agent. Here are the lessons from this build:

**Be extremely specific.** Do not tell an agent "create a skill for LinkedIn posts." Instead, provide the complete file content -- every line, every heading, every table template. The agent's job is to write the file, not design it.

**Include exact file paths.** Do not say "create a skill file." Say "create the file at `skills/linkedin-post/SKILL.md` with the following content."

**Specify naming conventions.** Tell agents to use kebab-case for directories, UPPERCASE for `SKILL.md`, and the exact frontmatter fields to include.

**Provide file content in the prompt.** The team lead in this project wrote the complete content of every file and included it in each agent's prompt. This is why all 14 files are consistent in style and conventions -- the agents did not have to make design decisions.

**Do not let agents "decide."** Every decision an agent makes is a decision that could diverge from the plan. If you have a clear vision, encode it in the prompt. Save creative freedom for tasks where you genuinely want the agent to explore options.

### 4.2 Task Dependency Management

**Minimize blocking dependencies.** Every dependency is a point where parallelism stops. This project achieved maximum parallelism with just one blocking dependency (scaffold before everything else).

**Use a single scaffold task.** Rather than having each agent create its own directories (which risks race conditions when two agents try to create the same parent directory), designate one agent to create the entire directory structure.

**Avoid complex DAGs.** Keep your dependency graph simple. A fan-out pattern (one blocking task, then N parallel tasks) is ideal. If you need task C to depend on both A and B, make sure A and B are truly prerequisites.

**Do not create circular dependencies.** If Task A is blocked by Task B and Task B is blocked by Task A, neither will ever start. The task system does not detect cycles -- it will simply deadlock.

### 4.3 When to Use Agent Teams vs. Single Agent

| Scenario | Single Agent | Agent Team | Why |
|----------|-------------|------------|-----|
| 1-3 files to create | Yes | No | Team overhead exceeds time savings |
| 10+ independent files | Maybe | Yes | Parallelism saves significant time |
| Files that depend on each other | Yes | No | Sequential dependencies negate parallelism |
| Tasks requiring shared context | Yes | No | Agents cannot share context windows |
| Speed is critical | - | Yes | Parallel execution is faster |
| Cost is critical | Yes | - | Each agent session costs tokens |
| Files are large and complex | - | Yes | Each agent can focus on one file |
| Strict consistency required | Yes | - | One agent maintains consistent style |
| Consistency via detailed prompts | - | Yes | Detailed prompts ensure consistency |

**The break-even point:** Agent teams become worthwhile when you have 4+ independent tasks that would each take more than a minute for a single agent. Below that, the overhead of creating the team, spawning agents, managing communication, and shutting down exceeds the time saved by parallelism.

### 4.4 Error Handling

**Agent fails to write a file:**
- Check if the directory exists (scaffold task may not have completed)
- Check if the agent received the unblock notification
- Retry by sending the agent a corrected prompt via SendMessage

**Agent produces wrong content:**
- You cannot "undo" what an agent wrote. Instead, spawn a new agent (or use the team lead directly) to overwrite the file with correct content.
- Prevent this by providing exact file content in the original prompt.

**Agent crashes or becomes unresponsive:**
- Spawn a replacement agent with the same task
- Use TaskUpdate to reassign the task to the new agent

**Scaffold task fails:**
- All other tasks remain blocked
- Fix the scaffold issue and re-run, or have the team lead create directories directly

**Partial completion:**
- If an agent completes some files but not all, create a new task for the remaining files and spawn a new agent
- Use TaskUpdate to mark the original task's status appropriately

### 4.5 Communication Patterns

**Use SendMessage "message" for targeted communication.** Every message between the team lead and a specific agent should use the `message` type with an explicit `recipient`. This is the most common pattern.

**Use "broadcast" sparingly.** Broadcasting sends a separate copy of the message to every agent on the team. Each delivery costs tokens. In a team of 6, a broadcast costs 6x the tokens of a single message. Reserve broadcasts for truly urgent, team-wide announcements (e.g., "stop all work, critical issue found").

**Keep messages concise and actionable.** Agents do not need pleasantries. A good message is: "Task #1 complete. You are unblocked. Begin writing command files." A bad message is: "Hey team, great news! The scaffold agent has finished its work, and I'm happy to report that all directories are now in place. Please go ahead and start working on your assigned tasks whenever you're ready."

**Include context in unblock messages.** When unblocking an agent, remind it of what it needs to do: "Task #1 complete. Directories exist. Write commands/summarize.md with the content from your original prompt."

---

## 6. Agent Team API Reference

Quick reference for every tool used in agent team workflows.

### TeamCreate

Creates a new team with an empty task list.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `team_name` | string | Yes | Unique identifier for the team (kebab-case). |
| `description` | string | No | Human-readable description of the team's purpose. |

**Example:**
```
TeamCreate:
  team_name: "content-repurposing-plugin"
  description: "Building a content repurposing plugin for Claude Cowork"
```

### TaskCreate

Creates a new task in the team's task list. Status starts as `pending`.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `subject` | string | Yes | Brief title in imperative form ("Create scaffold"). |
| `description` | string | Yes | Detailed instructions for the agent. |
| `activeForm` | string | Recommended | Present-continuous label shown during execution ("Creating scaffold"). |

**Example:**
```
TaskCreate:
  subject: "Create plugin scaffold"
  description: "Create .claude-plugin/plugin.json and 10 directories..."
  activeForm: "Creating plugin scaffold"
```

### TaskUpdate

Updates a task's status, owner, or dependencies.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `taskId` | string | Yes | The task ID to update. |
| `status` | string | No | `pending`, `in_progress`, `completed`, or `deleted`. |
| `owner` | string | No | Agent name to assign the task to. |
| `subject` | string | No | Update the task title. |
| `description` | string | No | Update the task description. |
| `activeForm` | string | No | Update the spinner label. |
| `addBlockedBy` | string[] | No | Task IDs that must complete before this task can start. |
| `addBlocks` | string[] | No | Task IDs that this task blocks (cannot start until this completes). |

**Status workflow:** `pending` -> `in_progress` -> `completed`

Use `deleted` to permanently remove a task.

**Example (set dependency):**
```
TaskUpdate:
  taskId: "2"
  addBlockedBy: ["1"]
```

**Example (mark complete):**
```
TaskUpdate:
  taskId: "1"
  status: "completed"
```

### TaskList

Lists all tasks with summary information. Returns each task's id, subject, status, owner, and blockedBy list. Use this to check overall progress or find available tasks.

**Example output:**
```
#1 [completed]   Create plugin scaffold         (owner: scaffold-builder)
#2 [in_progress] Create command files           (owner: command-builder, blocked by: [])
#3 [in_progress] Create content-extraction skill (owner: extraction-skill-builder, blocked by: [])
#4 [pending]     Create takeaways + quotes      (no owner, blocked by: [1])
```

### TaskGet

Retrieves full details of a specific task, including the complete description.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `taskId` | string | Yes | The task ID to retrieve. |

Use this when an agent needs to read its full task description, or when the team lead needs to review a task's details.

### SendMessage

Sends messages between agents. This is the primary communication mechanism.

**Message types:**

| Type | Purpose | Key Parameters |
|------|---------|---------------|
| `message` | Send a DM to one specific agent | `recipient` (required), `content`, `summary` |
| `broadcast` | Send to all agents at once | `content`, `summary` -- USE SPARINGLY |
| `shutdown_request` | Ask an agent to shut down | `recipient`, `content` |
| `shutdown_response` | Agent's reply to shutdown | `request_id`, `approve` (true/false), `content` |

**Message example:**
```
SendMessage:
  type: message
  recipient: command-builder
  content: "Task #1 complete. You are unblocked."
  summary: "Scaffold done, unblocking command-builder"
```

**Shutdown example:**
```
SendMessage:
  type: shutdown_request
  recipient: scaffold-builder
  content: "All work complete. Please shut down."
```

**Agent responds:**
```
SendMessage:
  type: shutdown_response
  request_id: "abc-123"
  approve: true
```

### Task (Spawn Agent)

Spawns a new agent (worker) to perform work.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `description` | string | Yes | Brief description of the agent's purpose. |
| `subagent_type` | string | Yes | Agent type: `builder`, `Explore`, `Plan`, `general-purpose`. |
| `name` | string | Yes | Unique agent name (used in SendMessage `recipient`). |
| `team_name` | string | Yes | Team to join. Gives access to shared task list. |
| `mode` | string | No | Permission mode: `bypassPermissions`, `default`, `plan`. |
| `prompt` | string | Yes | Full instructions for the agent. **Be extremely detailed.** |
| `run_in_background` | boolean | No | If `true`, agent runs without blocking the team lead. |

**Example:**
```
Task:
  description: "Build plugin scaffold"
  subagent_type: builder
  name: scaffold-builder
  team_name: content-repurposing-plugin
  mode: bypassPermissions
  prompt: "You are scaffold-builder. Create the following:
    1. Directory: .claude-plugin/
    2. File: .claude-plugin/plugin.json with content:
       { \"name\": \"content-repurposing\", ... }
    3. Directory: commands/
    4. Directory: skills/content-extraction/
    ..."
```

### TeamDelete

Removes the team and its task list. All agents must be shut down first.

**Prerequisite:** Send `shutdown_request` to every agent and wait for confirmation before calling TeamDelete.

---

## 7. Frequently Asked Questions

**How many agents can I run at once?**
There is no hard-coded limit, but practical limits include: your machine's memory (each agent has a context window), API rate limits, and token costs. The content-repurposing build used 6 agents comfortably. For most projects, 4-8 agents is a practical sweet spot.

**What happens if an agent crashes?**
The task remains in its last reported status (usually `in_progress`). You can spawn a replacement agent, assign it the same task, and have it pick up where the crashed agent left off -- or start fresh. The task system is persistent, so no tracking information is lost.

**Can agents communicate with each other (not just with the team lead)?**
Yes. Any agent can use SendMessage with another agent's name as the `recipient`. However, in practice, most communication goes through the team lead (hub-and-spoke pattern). Direct agent-to-agent communication is useful for tightly coupled tasks but adds complexity.

**How much does an agent team cost (in API tokens)?**
Each agent consumes tokens for its prompt (including the detailed task description), its tool calls, and its output. A team of 6 agents costs roughly 6x what a single agent costs for the same volume of work, but completes in a fraction of the wall-clock time. The team lead's token usage is additional overhead for coordination. Detailed prompts (providing exact file content) increase token usage but dramatically improve output quality.

**Can I mix different model types in a team?**
Yes. The team lead should run on the most capable model (Opus 4.6) for complex coordination. Worker agents can run on faster, cheaper models (Sonnet 4.5 or Haiku 4.5) if their tasks are straightforward. Use the `model` parameter when spawning agents to override the default.

**What is the difference between builder and general-purpose agents?**
`builder` agents have Read, Write, Edit, Bash, Glob, and Grep tools -- everything needed for file creation and modification. `general-purpose` agents have all available tools, which may include additional capabilities beyond file operations. For plugin building, `builder` is sufficient and appropriate.

**How do I see what an agent is doing?**
The `activeForm` field on each task shows a live spinner label (e.g., "Writing command files"). Agents also send messages to the team lead with progress updates. You can monitor the task list with TaskList to see overall status at any time.

**Can agents read files created by other agents?**
Yes. All agents share the same filesystem. Once scaffold-builder creates a directory, command-builder can write files into it. Once command-builder writes a file, any other agent can read it. This is why task dependencies matter -- they ensure files exist before other agents try to access them.

**What is the maximum prompt size for an agent?**
There is no strict maximum, but very long prompts (tens of thousands of tokens) can slow agent startup and consume a significant portion of the context window. For this project, the team lead included complete file content (up to 223 lines) in agent prompts without issues. If your prompt is extremely long, consider breaking the task into smaller sub-tasks.

**How do I debug agent team issues?**
1. Check TaskList for stuck tasks (status `in_progress` for too long)
2. Check for blocked tasks with unresolved dependencies
3. Send a status-check message to unresponsive agents
4. Review agent messages for error reports
5. Verify the directory structure exists (common cause of write failures)
6. Test file paths manually to ensure they are valid

---

## 8. Further Reading

- **[Agent Team Build Report](AGENT_TEAM_BUILD_REPORT.md)** -- The detailed log of how the 6 agents built the content-repurposing plugin, including exact timelines, file sizes, and design decisions.
- **[Plugin Architecture](PLUGIN_ARCHITECTURE.md)** -- Deep dive into the plugin file format, commands, skills, and how they work together.
- **[Developer Guide](DEVELOPER_GUIDE.md)** -- Hands-on guide to building your own plugins.
- **[Glossary](GLOSSARY.md)** -- Definitions of every technical term used in these docs.
- **[Troubleshooting](TROUBLESHOOTING.md)** -- Solutions to common problems.

---

*This guide was itself written by an AI agent team, demonstrating the same workflow it documents.*
