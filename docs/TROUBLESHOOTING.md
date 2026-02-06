# Troubleshooting Guide

This guide covers common issues you may encounter when installing, using, developing, or testing the content-repurposing plugin. Each issue lists the symptom, likely cause, fix, and a prevention tip.

If you are new to the terminology used here, see the [Glossary](GLOSSARY.md) for definitions of all technical terms.

---

## Table of Contents

1. [Installation Issues](#1-installation-issues)
2. [Command Issues](#2-command-issues)
3. [Skill Issues](#3-skill-issues)
4. [Agent Team Issues (For Developers)](#4-agent-team-issues-for-developers)
5. [Output Issues](#5-output-issues)
6. [Testing Issues](#6-testing-issues)
7. [Getting Help](#7-getting-help)

---

## 1. Installation Issues

### 1.1 Plugin Not Showing Up After Upload

**Symptom:** You uploaded the zip file to Claude Cowork, the upload appeared to succeed, but the plugin does not appear in the plugin list or command menu.

**Likely Cause:** Claude Cowork caches the plugin list and may not refresh automatically after an upload.

**Fix:**
1. Close the Claude Cowork session (close the browser tab or desktop app window).
2. Reopen Claude Cowork and start a new session.
3. Check the plugin list -- your plugin should now appear.
4. If it still does not appear, re-upload the zip file. The previous upload may have silently failed due to a network interruption.

**Prevention:** Always verify the plugin appears in the list immediately after upload. If building a habit of frequent uploads during development, keep a browser tab open to the plugin list and refresh it after each upload.

---

### 1.2 Zip File Rejected on Upload

**Symptom:** Claude Cowork rejects the zip file with an error message, or the upload fails immediately.

**Likely Cause:** The directory structure inside the zip is incorrect. The most common mistake is zipping the parent directory so the zip contains a nested folder (e.g., `my-folder/content-repurposing/...` instead of `content-repurposing/...`), or missing the `.claude-plugin/` directory entirely.

**Fix:**
1. Unzip your file into a temporary folder and inspect the structure. It must look like this:
   ```
   content-repurposing/
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
2. The top-level item inside the zip must be the `content-repurposing/` directory itself.
3. Make sure `.claude-plugin/plugin.json` exists and contains valid JSON (no trailing commas, all strings in double quotes).
4. Re-zip correctly. On most systems, navigate *into* the parent folder, select the `content-repurposing/` folder, and compress it.

**Prevention:** Always zip by right-clicking the `content-repurposing/` folder itself, not its parent. After zipping, unzip into a temp location to verify structure before uploading.

---

### 1.3 Plugin Appears but No Commands or Skills Visible

**Symptom:** The plugin shows up in the plugin list with its name and description, but typing `/content-repurposing:` does not show any commands, and skills are not being invoked.

**Likely Cause:** The `commands/` or `skills/` directories are missing from the zip, or the Markdown files inside them have syntax errors in their YAML frontmatter.

**Fix:**
1. Verify that the `commands/` directory is present inside the zip and contains `.md` files.
2. Verify that the `skills/` directory has subdirectories, each containing a `SKILL.md` file.
3. Open each command file and check that the YAML frontmatter is valid:
   - Starts with `---` on its own line
   - Ends with `---` on its own line
   - Contains at minimum a `description` field
   - No tabs (use spaces only in YAML)
4. Open each `SKILL.md` and check the same frontmatter rules, ensuring `name` and `description` fields are present.
5. Re-zip and re-upload.

**Prevention:** After creating or editing any command or skill file, validate the YAML frontmatter by checking that the opening and closing `---` markers are present and that values are properly formatted.

---

## 2. Command Issues

### 2.1 Slash Commands Not Appearing in Command Menu

**Symptom:** You type `/content-repurposing:` in Claude Cowork and no autocomplete suggestions appear.

**Likely Cause:** The plugin is either not installed or the command files have invalid frontmatter that prevents Claude Cowork from parsing them.

**Fix:**
1. Check that the plugin is listed in your installed plugins. If not, see [Section 1.1](#11-plugin-not-showing-up-after-upload).
2. Open each file in the `commands/` directory and verify the YAML frontmatter is valid:
   ```yaml
   ---
   description: A short description of this command
   disable-model-invocation: true
   ---
   ```
3. Ensure the `description` field is present -- commands without a description may not appear in the menu.
4. Ensure there are no invisible characters or encoding issues in the files (save as UTF-8 without BOM).
5. Re-upload the plugin.

**Prevention:** Use a text editor that shows invisible characters (such as VS Code with "Render Whitespace" enabled). Always include a `description` field in command frontmatter.

---

### 2.2 Command Runs but Does Not Invoke Skill

**Symptom:** You run a slash command (e.g., `/content-repurposing:extract`), Claude acknowledges the command, but does not follow the skill's detailed process. The output is generic rather than following the structured steps defined in the skill.

**Likely Cause:** The command body does not reference the skill by its exact name, or the skill's `name` field in its frontmatter does not match what the command references.

**Fix:**
1. Open the command file (e.g., `commands/extract.md`) and check that it references the skill by name:
   ```
   Use the **content-extraction** skill to analyze this content.
   ```
2. Open the corresponding skill file (e.g., `skills/content-extraction/SKILL.md`) and verify the `name` field in its frontmatter matches exactly:
   ```yaml
   ---
   name: content-extraction
   ---
   ```
3. Names are case-sensitive and must match exactly, including hyphens.
4. Re-upload after fixing any mismatches.

**Prevention:** Establish a naming convention and stick to it. The skill directory name, the `name` field in SKILL.md frontmatter, and the reference in the command file should all use the exact same string (kebab-case).

---

### 2.3 $ARGUMENTS Not Being Passed

**Symptom:** You run a command with additional text (e.g., `/content-repurposing:extract my-article.md`) but the skill does not receive or process the content reference. Claude asks what content you want to analyze as if you provided nothing.

**Likely Cause:** The command body does not include the `$ARGUMENTS` variable, or it is misspelled.

**Fix:**
1. Open the command file and verify it contains `$ARGUMENTS` (exact spelling, with the dollar sign and all caps):
   ```
   Extract content ideas from the following source: $ARGUMENTS
   ```
2. Make sure `$ARGUMENTS` is not inside a code block (backticks), which would prevent substitution.
3. Re-upload after fixing.

**Prevention:** Always include `$ARGUMENTS` in the command body where user input should be substituted. Test with sample input after uploading.

---

## 3. Skill Issues

### 3.1 Skill Not Recognized by Claude

**Symptom:** Claude does not invoke the skill when you expect it to. For example, you ask Claude to "extract content ideas" and it responds with a generic answer instead of following the content-extraction skill's detailed process.

**Likely Cause:** The skill's `description` field in its YAML frontmatter is missing, vague, or does not match the user's request closely enough for Claude to select it.

**Fix:**
1. Open the `SKILL.md` file and check that both `name` and `description` are present in the frontmatter.
2. Make the `description` more specific and keyword-rich. Claude uses the description to decide when a skill is relevant:
   ```yaml
   ---
   name: content-extraction
   description: Extracts and organizes content ideas from long-form content into detailed platform-specific tables. Use when repurposing transcripts, articles, blog posts, or any long-form content into multiple pieces across platforms.
   ---
   ```
3. Alternatively, use the corresponding slash command to explicitly trigger the skill rather than relying on auto-invocation.

**Prevention:** Write descriptions that include the key verbs and nouns a user would likely use when requesting that functionality. Include a "Use when..." phrase that lists specific trigger scenarios.

---

### 3.2 YAML Frontmatter Parse Errors

**Symptom:** The skill or command file causes errors when the plugin is loaded, or Claude ignores the file entirely.

**Likely Cause:** The YAML frontmatter contains a syntax error. Common YAML mistakes include:
- Using tabs instead of spaces for indentation
- Missing the closing `---` marker
- Unquoted strings that contain special characters (colons, brackets, etc.)
- Trailing whitespace after the `---` markers

**Fix:**
1. Open the file in a text editor and check the frontmatter block carefully.
2. Ensure it starts and ends with exactly `---` (three hyphens, nothing else on the line).
3. Replace any tab characters with spaces.
4. If a value contains a colon, wrap it in quotes:
   ```yaml
   # Wrong - the colon breaks parsing
   description: Extract ideas: from content to posts

   # Correct - quoted string
   description: "Extract ideas: from content to posts"
   ```
5. Validate your YAML using an online YAML validator or a VS Code extension.

**Prevention:** Use a code editor with YAML syntax highlighting (VS Code, Sublime Text). Configure your editor to insert spaces instead of tabs. Keep frontmatter values simple -- avoid special characters when possible.

---

### 3.3 Skill Produces Unexpected Output

**Symptom:** The skill runs but produces output that does not match the expected format (missing tables, wrong structure, incomplete sections).

**Likely Cause:** The skill instructions in SKILL.md are ambiguous, or the source content provided is too short or in an unexpected format for the skill to process effectively.

**Fix:**
1. Review the SKILL.md file and ensure the instructions are specific about expected output format (table columns, section headings, number of items).
2. If the source content is very short (under 500 words), the skill may not have enough material to generate the expected number of ideas. Provide longer, more detailed content.
3. If specific sections are missing, add explicit instructions in the skill file like "You MUST include all of the following sections" followed by the section list.
4. Try running the command again -- LLM outputs can vary between runs.

**Prevention:** Include explicit output format requirements in every skill file. Use phrases like "Always include," "You must produce," and "Required sections" to make expectations clear to the model.

---

## 4. Agent Team Issues (For Developers)

These issues apply if you are using Claude Code agent teams to build or extend the plugin.

### 4.1 Agents Failing to Create Files

**Symptom:** A worker agent reports that it cannot create a file, or the file does not appear after the agent claims to have created it.

**Likely Cause:** The target directory does not exist. Agents cannot create files in directories that have not been created yet. This typically happens when the directory-creation task has not completed before file-writing tasks begin.

**Fix:**
1. Ensure the directory structure is created before any file-writing tasks start.
2. Set up a task dependency: make the scaffold/directory-creation task a blocker for all file-writing tasks.
3. Do not unblock file-writing agents until the scaffold agent has confirmed completion.
4. If working without agent teams, manually create directories first:
   ```bash
   mkdir -p content-repurposing/.claude-plugin
   mkdir -p content-repurposing/commands
   mkdir -p content-repurposing/skills/content-extraction
   ```

**Prevention:** Always define a scaffold task as Task #1 and set all other tasks to be blocked by it. Verify directory creation before sending unblock messages.

---

### 4.2 Task Dependency Deadlocks

**Symptom:** Agents are stuck waiting and no progress is being made. The TaskList shows tasks that are blocked, but the blocking tasks are also blocked or in an unexpected state.

**Likely Cause:** A circular dependency was created (Task A blocks Task B, and Task B blocks Task A), or a blocking task failed without being marked as completed.

**Fix:**
1. Run TaskList to see the current state of all tasks.
2. Look for circular dependency chains (A blocks B blocks A).
3. If a blocking task failed, manually mark it as completed or delete it and create a new unblocked version.
4. If there is a circular dependency, delete one of the dependency relationships using TaskUpdate.
5. As a last resort, create new tasks without dependencies and reassign the work.

**Prevention:** Design your task dependency graph as a DAG (Directed Acyclic Graph) -- dependencies should flow in one direction only. Draw out the dependency graph before creating tasks. Keep it simple: a single scaffold task blocking all others (as in this project) is a reliable pattern.

---

### 4.3 Agent Communication Failures

**Symptom:** An agent does not respond to messages, does not pick up its task, or seems to be stuck without making progress.

**Likely Cause:** The agent may have encountered an error, exceeded its context window, or its process may have timed out.

**Fix:**
1. Send a status-check message to the agent asking it to report its current state.
2. If the agent does not respond after a reasonable wait, send a shutdown request and spawn a replacement agent with the same task.
3. Check the agent's output for error messages that might indicate what went wrong.
4. If the issue is context window exhaustion (the task was too large), split it into smaller subtasks.

**Prevention:** Keep individual agent tasks focused and reasonably scoped. A single agent should handle 1-3 files, not an entire project. Monitor agents and intervene early if one appears stuck.

---

### 4.4 Permission Denied Errors

**Symptom:** An agent reports "permission denied" when trying to create or edit a file.

**Likely Cause:** The agent's permission mode does not allow the requested operation, or the file system itself restricts access to the target path.

**Fix:**
1. Check the agent's permission mode configuration. For file creation, the agent needs write permissions.
2. If using Claude Code, consider using `bypassPermissions` mode for builder agents that only create new files in known directories. This eliminates interactive approval prompts that block parallel workflows.
3. If the file system restricts access (e.g., read-only directories, OS-level permissions), change the target output directory to one where the agent has write access.
4. On Windows, ensure the path is not inside a protected system directory.

**Prevention:** Decide on a permission strategy before spawning agents. For trusted tasks that only create new files in a project directory, `bypassPermissions` is safe and efficient. For tasks that modify existing files or run system commands, use more restrictive modes.

---

## 5. Output Issues

### 5.1 Files Not Saving to Workspace

**Symptom:** A skill runs and produces output in the conversation, but no file is saved to the workspace folder.

**Likely Cause:** The skill instructions tell Claude to "save to the workspace folder," but Claude may not have file-write access in the current session, or the workspace path is ambiguous.

**Fix:**
1. In Claude Cowork, ensure you have granted Claude permission to write files when prompted.
2. If Claude asks where to save, provide an explicit path or confirm the default workspace location.
3. If the file was generated in the conversation but not saved, you can copy-paste the output into a file manually.
4. Check if the skill instructions include a clear "Save Output" step. If missing, add one.

**Prevention:** Every skill that produces output should have an explicit "Save Output" step with a specific file naming convention (e.g., `content-extraction-report-[source-name].md`). Grant file-write permissions at the start of your session.

---

### 5.2 Wrong File Format

**Symptom:** The output file is saved but in the wrong format (e.g., you expected a `.csv` but got a `.md`, or the Markdown renders incorrectly).

**Likely Cause:** The skill defaults to Markdown output, and the user did not specify a preference. Or the Markdown contains syntax issues (broken tables, missing blank lines before lists).

**Fix:**
1. If you need a different format, re-run the command and explicitly state the format: "Save as CSV" or "Save as Markdown."
2. For broken Markdown tables, check that:
   - There is a blank line before and after each table
   - The header separator row uses `|---|` format
   - All rows have the same number of `|` pipe characters
3. Open the file in a Markdown preview tool (VS Code, GitHub, etc.) to verify rendering.

**Prevention:** When requesting output, always specify your preferred format. If a skill supports multiple formats, the "Save Output" step should ask the user which one they want.

---

### 5.3 Missing Tables or Sections

**Symptom:** The output file is missing expected tables, sections, or data. For example, the content extraction report has Newsletter and LinkedIn sections but is missing Twitter and Video sections.

**Likely Cause:** The source content was too short to generate ideas for all platforms. Or the skill instructions were not explicit enough about requiring all sections. Or the LLM's response was cut off due to length limits.

**Fix:**
1. Re-run the skill with a longer or more detailed source.
2. If specific platforms were skipped, tell Claude explicitly: "Include ideas for all 5 platforms: Newsletter, Substack Notes, Twitter/X, LinkedIn, and Short-form Video."
3. If the output appears cut off mid-sentence, the response hit a length limit. Ask Claude to "continue" or to regenerate the missing sections.

**Prevention:** Provide source content of at least 1,000 words for best results. When running the extraction skill, confirm "all platforms" when asked. For very long outputs, request that Claude save sections incrementally.

---

## 6. Testing Issues

### 6.1 --plugin-dir Not Loading Plugin

**Symptom:** You run `claude --plugin-dir ./content-repurposing` but the plugin does not appear in your Claude Code session.

**Likely Cause:** The path is incorrect, the `.claude-plugin/plugin.json` manifest is missing or invalid, or you are running the command from the wrong directory.

**Fix:**
1. Verify you are in the correct directory. The `content-repurposing/` folder must be a direct child of your current working directory when using `./content-repurposing`:
   ```bash
   ls ./content-repurposing/.claude-plugin/plugin.json
   ```
   If this command fails, you are in the wrong directory or the path is wrong.
2. Try using an absolute path instead:
   ```bash
   claude --plugin-dir /full/path/to/content-repurposing
   ```
3. Verify `plugin.json` is valid JSON. A trailing comma or missing quote will cause a silent parse failure.
4. Make sure you are running a version of Claude Code that supports the `--plugin-dir` flag.

**Prevention:** Always use absolute paths during testing to eliminate ambiguity. Keep a terminal open in the project root directory and use the path from there.

---

### 6.2 Changes Not Reflecting (Caching)

**Symptom:** You edited a skill or command file, re-ran Claude Code with `--plugin-dir`, but Claude still uses the old version of the skill.

**Likely Cause:** Claude Code or the underlying system may cache plugin files. The previous session's loaded plugin data persists.

**Fix:**
1. Fully exit your Claude Code session (not just close the conversation -- exit the process).
2. Restart Claude Code with the `--plugin-dir` flag.
3. Verify the file was actually saved by opening it in a text editor and confirming your changes are present.
4. If using a copy of the plugin elsewhere, make sure you are editing the right copy (not a stale duplicate).

**Prevention:** During active development, always restart Claude Code after making changes to plugin files. Keep only one copy of the plugin directory to avoid editing the wrong files.

---

### 6.3 Local Testing vs. Uploaded Behavior Differences

**Symptom:** The plugin works correctly when tested locally with `--plugin-dir`, but behaves differently after being zipped and uploaded to Claude Cowork.

**Likely Cause:** The zip file may contain an older version of the files, extra files, or a different directory structure than what you tested locally. Additionally, Claude Cowork and Claude Code may handle certain features differently.

**Fix:**
1. After zipping, unzip into a fresh temporary folder and compare with your source directory:
   - Same number of files?
   - Same file contents? (check a few key files)
   - Same directory structure?
2. If files differ, re-zip from the correct source directory.
3. If behavior differs despite identical files, note the specific difference and check whether it relates to:
   - File permissions (Claude Cowork may have different write access than local Claude Code)
   - Path handling (absolute vs. relative paths)
   - Features supported in one environment but not the other
4. Test the unzipped copy locally with `--plugin-dir` to verify it matches the uploaded behavior.

**Prevention:** Automate your zip process with a script that always zips from the same source. After zipping, do a verification unzip and local test before uploading. Keep a change log so you know exactly what differs between versions.

---

## 7. Getting Help

### Claude Code GitHub Issues

If you encounter a bug in Claude Code itself (not the plugin), report it at:

**https://github.com/anthropics/claude-code/issues**

When filing an issue, include:
- The exact error message or unexpected behavior
- Your operating system and Claude Code version
- Steps to reproduce the problem
- The relevant plugin files (sanitized of any private content)

### Community Resources

- **Claude Code Documentation:** The official documentation for Claude Code covers plugin development, agent teams, and CLI usage. Check the Claude Code docs for the latest features and configuration options.
- **Claude Cowork Help:** Within Claude Cowork, use the built-in help features for questions about the interface, plugin management, and session configuration.

### Self-Diagnosis Checklist

Before seeking help, run through this quick checklist:

- [ ] Is `plugin.json` valid JSON? (Paste it into a JSON validator)
- [ ] Do all `.md` files have valid YAML frontmatter? (Opening and closing `---` present, no tabs)
- [ ] Is the directory structure correct? (`.claude-plugin/`, `commands/`, `skills/` all present)
- [ ] Are skill names consistent? (Directory name matches `name` field in SKILL.md frontmatter)
- [ ] Did you restart Claude Code / Claude Cowork after making changes?
- [ ] Are you using the correct path with `--plugin-dir`?
- [ ] Is the zip file structure correct? (Unzip and verify)

---

*See also: [Quick Start Guide](QUICK_START_GUIDE.md) for setup instructions, [Developer Guide](DEVELOPER_GUIDE.md) for plugin development details, and [Glossary](GLOSSARY.md) for term definitions.*
