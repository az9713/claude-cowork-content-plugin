# How to Use Claude Opus 4.6 Agent Teams to Build a Claude Cowork Plugin

**6 AI agents. 14 files. ~742 lines of code. ~5 minutes. One complete plugin.**

An educational deep-dive into how a team of specialized AI agents coordinated to build a fully functional Claude Cowork plugin from scratch.

---

## What This Project Is

This project is an educational showcase of Claude's **Agent Teams** feature. It documents how a single team lead (Claude Opus 4.6) designed an architecture, spawned six specialized builder agents, managed task dependencies, and produced a complete content-repurposing plugin in roughly five minutes.

The interesting story here is not the plugin itself -- it is **how six AI agents coordinated to build it**. If you have ever wondered what it looks like when multiple AI agents collaborate on a real software project, this repository walks you through every step: the planning, the parallel execution, the dependency management, and the final output.

This project is designed for two audiences. **Developers** with backgrounds in C, C++, Java, or similar languages who are new to AI tools, LLMs, and agent-based workflows will find step-by-step explanations of every concept. **Content creators and general users** who want to understand what Claude Cowork plugins can do will find practical guides and hands-on use cases.

---

## What You'll Learn

- **Agent Team Orchestration** -- How a team lead agent designs a build plan, assigns tasks, and coordinates multiple agents working simultaneously
- **Claude Cowork Plugin Architecture** -- The structure of a plugin: manifests, commands, skills, and how they fit together
- **Task Dependency Management** -- How agents handle build order (scaffold first, then commands and skills in parallel)
- **Parallel Agent Coordination** -- How independent tasks run concurrently while dependent tasks wait for prerequisites
- **Prompt Engineering for Skills** -- How to write effective system prompts that guide an LLM to produce structured output
- **Content Repurposing Patterns** -- Practical techniques for transforming long-form content into platform-specific formats (LinkedIn, Twitter/X, Substack, and more)
- **Plugin Development from Scratch** -- Everything you need to build, install, and customize your own Claude Cowork plugins

---

## The Story: How 6 AI Agents Built a Plugin in 5 Minutes

It started with a single instruction to a team lead running Claude Opus 4.6: build a content-repurposing plugin for Claude Cowork.

The team lead analyzed the requirements, designed the full plugin architecture, and then spawned six specialized builder agents -- each with a focused responsibility. The first agent, **scaffold-builder**, created the directory structure and plugin manifest. Once the scaffold was in place, the remaining five agents worked **in parallel**: one built the command files, while four others built different groups of skills simultaneously.

Here is how the team was organized:

```
                    ┌──────────────┐
                    │  TEAM LEAD   │
                    │  (Opus 4.6)  │
                    └──────┬───────┘
                           │
        ┌──────────┬───────┼──────────┬──────────┬──────────┐
        │          │       │          │          │          │
  scaffold-  command-  extraction-  takeaways-  social-   content-
  builder    builder   skill-       quotes-     skills-   skills-
                       builder      builder     builder   builder
```

**scaffold-builder** ran first, creating the directory layout and `plugin.json` manifest. Once it completed, the team lead launched the remaining five agents simultaneously:

- **command-builder** created all six command definitions (`all`, `extract`, `linkedin`, `quotes`, `titles`, `twitter`)
- **extraction-skill-builder** built the content extraction skill
- **takeaways-quotes-builder** built the key takeaways and quote extractor skills
- **social-skills-builder** built the LinkedIn post and Twitter thread skills
- **content-skills-builder** built the Substack note and title generator skills

The result: 14 files, approximately 742 lines of code, a fully functional plugin -- all in about 5 minutes.

For the complete build log with timestamps and task details, see [docs/AGENT_TEAM_BUILD_REPORT.md](docs/AGENT_TEAM_BUILD_REPORT.md).

---

## Project Structure

```
content-repurposing/
├── .claude-plugin/
│   └── plugin.json                  # Plugin manifest (name, version, description)
├── commands/
│   ├── all.md                       # Runs all repurposing skills at once
│   ├── extract.md                   # Extracts key content from source material
│   ├── linkedin.md                  # Generates a LinkedIn post
│   ├── quotes.md                    # Pulls notable quotes from the content
│   ├── titles.md                    # Generates title variations
│   └── twitter.md                   # Creates a Twitter/X thread
├── skills/
│   ├── content-extraction/
│   │   └── skill.md                 # Skill: parse and extract core content
│   ├── key-takeaways/
│   │   └── skill.md                 # Skill: identify key takeaways
│   ├── linkedin-post/
│   │   └── skill.md                 # Skill: format content for LinkedIn
│   ├── quote-extractor/
│   │   └── skill.md                 # Skill: extract notable quotes
│   ├── substack-note/
│   │   └── skill.md                 # Skill: create a Substack Note
│   ├── title-generator/
│   │   └── skill.md                 # Skill: generate title variations
│   └── twitter-thread/
│       └── skill.md                 # Skill: format content as a Twitter thread
docs/
├── QUICK_START_GUIDE.md             # 10+ hands-on use cases to try immediately
├── USER_GUIDE.md                    # Complete user manual
├── DEVELOPER_GUIDE.md               # Developer handbook for customization
├── PLUGIN_ARCHITECTURE.md           # Technical deep-dive into plugin structure
├── AGENT_TEAM_GUIDE.md              # Agent Teams explained from the ground up
├── AGENT_TEAM_BUILD_REPORT.md       # Detailed log of the 5-minute build process
├── GLOSSARY.md                      # 30+ terms defined for newcomers
└── TROUBLESHOOTING.md               # Common issues and fixes
```

---

## Prerequisites

You need one of the following installed:

### Option A: Claude Code CLI (Command Line)

Claude Code is Anthropic's command-line interface for Claude. It runs in your terminal.

1. Install Node.js (version 18 or later) from [nodejs.org](https://nodejs.org/)
2. Install Claude Code:
   ```bash
   npm install -g @anthropic-ai/claude-code
   ```
3. Authenticate by running `claude` and following the prompts

### Option B: Claude Cowork Desktop App

Claude Cowork is the desktop application that supports plugins natively.

1. Download Claude Cowork from Anthropic's official site
2. Install and sign in with your Anthropic account
3. Plugins are managed through the app's settings

For detailed setup instructions, see [docs/USER_GUIDE.md](docs/USER_GUIDE.md).

---

## Quick Start

Get up and running in five steps. For the full guide with 10+ hands-on use cases, see [docs/QUICK_START_GUIDE.md](docs/QUICK_START_GUIDE.md).

1. **Clone this repository** to your local machine
2. **Copy** the `content-repurposing/` folder into your Claude Cowork plugins directory
3. **Restart** Claude Cowork (or reload plugins)
4. **Open a conversation** and paste or reference any long-form content
5. **Run a command** -- try `/all` to generate all content types at once, or start with `/extract` to see the extraction step

That is it. The plugin will transform your content into LinkedIn posts, Twitter threads, Substack Notes, titles, key takeaways, and notable quotes.

---

## Documentation Guide

| Document | Description |
|----------|-------------|
| [Quick Start Guide](docs/QUICK_START_GUIDE.md) | 10+ hands-on use cases you can try right now. Start here. |
| [User Guide](docs/USER_GUIDE.md) | Complete user manual covering installation, usage, and all commands. |
| [Developer Guide](docs/DEVELOPER_GUIDE.md) | Developer handbook for customizing skills, adding new commands, and extending the plugin. |
| [Plugin Architecture](docs/PLUGIN_ARCHITECTURE.md) | Technical deep-dive into how the plugin is structured: manifests, commands, skills, and data flow. |
| [Agent Team Guide](docs/AGENT_TEAM_GUIDE.md) | Agent Teams explained from the ground up. How team leads, builders, and task dependencies work. |
| [Agent Team Build Report](docs/AGENT_TEAM_BUILD_REPORT.md) | Detailed log of the 5-minute build process, including timestamps, agent assignments, and task ordering. |
| [Glossary](docs/GLOSSARY.md) | 30+ terms defined -- LLM, prompt, skill, agent, plugin, and more. Written for newcomers. |
| [Troubleshooting](docs/TROUBLESHOOTING.md) | Common issues and fixes for installation, plugin loading, and command execution. |

---

## Credits and Attribution

This project was inspired by the YouTube video **"Plugins Are Here for Claude Cowork (Full Walkthrough)"**:

[https://www.youtube.com/watch?v=tTlfUgZOL_0](https://www.youtube.com/watch?v=tTlfUgZOL_0)

We recommend watching the original video for a complete walkthrough of the plugin concept and capabilities.

**This project is an educational demonstration of Claude's Agent Teams feature. It is not a replication of any paid product. For the full content repurposing plugin experience, please refer to the original video and support the creator.**

The purpose of this repository is to show developers and content creators how multiple AI agents can coordinate to build software -- using a Claude Cowork plugin as the concrete example. The plugin code produced by the agent team is included so readers can study the output, but the real value is in understanding the build process itself.

---

## License

This project is released under the [MIT License](LICENSE).

---

## Contributing

Contributions are welcome. Here is how you can help:

1. **Report issues** -- If you find bugs, unclear documentation, or missing explanations, open an issue.
2. **Improve documentation** -- This project is designed for newcomers. If something confused you, it will confuse others. Pull requests that clarify language or add examples are especially valuable.
3. **Add new skills** -- Want to add an Instagram caption generator or a YouTube description skill? Follow the patterns in [docs/DEVELOPER_GUIDE.md](docs/DEVELOPER_GUIDE.md) and submit a pull request.
4. **Share your experience** -- If you use Agent Teams to build something, consider documenting your process and linking it here.

Please keep pull requests focused on a single change. For large changes, open an issue first to discuss the approach.
