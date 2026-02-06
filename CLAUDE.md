# Content Repurposing Plugin for Claude Cowork

One-line description: A Claude Cowork plugin that turns long-form content into 20+ platform-specific pieces (Twitter, LinkedIn, Substack, newsletters, short-form video).

## Important

Do not use 'Alex McFarland' anywhere in code or content. Use generic attribution.

## File Structure

```
content-repurposing/
  .claude-plugin/plugin.json    # Plugin manifest (name, version, description, author, keywords)
  commands/                     # Slash commands (user-triggered actions)
    all.md, extract.md, linkedin.md, quotes.md, titles.md, twitter.md
  skills/                       # Skills (auto-invoked by Claude when relevant)
    content-extraction/SKILL.md   # Flagship skill - 25+ ideas across 5 platforms
    key-takeaways/SKILL.md        # Structured summaries and bullet lists
    linkedin-post/SKILL.md        # LinkedIn post generation
    quote-extractor/SKILL.md      # Quotable statements with impact scores
    substack-note/SKILL.md        # Substack Notes generation
    title-generator/SKILL.md      # 15-20 titles across style categories
    twitter-thread/SKILL.md       # Twitter/X thread generation
docs/                           # Project documentation
```

## Key Conventions

- Skills are defined in Markdown files named `SKILL.md` inside `skills/<skill-name>/`
- Commands are Markdown files in `commands/` with YAML frontmatter (`description`, `disable-model-invocation`)
- Commands use `$ARGUMENTS` to receive user input
- All directory and file names use kebab-case (e.g., `content-extraction`, `linkedin-post`)
- Plugin metadata lives in `.claude-plugin/plugin.json` (JSON format)
- Skills use YAML frontmatter with `name` and `description` fields

## Testing Locally

```bash
claude --plugin-dir ./content-repurposing
```

## Packaging for Upload

Zip the entire `content-repurposing/` directory (including `.claude-plugin/`). Upload the zip file to Claude Cowork.

## Documentation

All docs live in the `docs/` directory. See: QUICK_START_GUIDE.md, DEVELOPER_GUIDE.md, USER_GUIDE.md, PLUGIN_ARCHITECTURE.md, AGENT_TEAM_GUIDE.md, GLOSSARY.md, TROUBLESHOOTING.md, AGENT_TEAM_BUILD_REPORT.md.

## Git Workflow

- Descriptive commit messages referencing the component changed
- Separate commits for plugin files vs documentation; never commit credentials
