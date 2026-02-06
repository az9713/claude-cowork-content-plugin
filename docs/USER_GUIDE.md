# Content Repurposing Plugin -- User Guide

**How to Use Claude Opus 4.6 Agent Teams to Build a Claude Cowork Plugin**

---

## Table of Contents

1. [Welcome](#1-welcome)
2. [What is Claude Cowork?](#2-what-is-claude-cowork)
3. [What are Plugins?](#3-what-are-plugins)
4. [Installing the Plugin](#4-installing-the-plugin)
5. [Understanding Commands vs Skills](#5-understanding-commands-vs-skills)
6. [Setting Up Your Workspace](#6-setting-up-your-workspace)
7. [Using Each Command](#7-using-each-command)
   - 7.1 [/content-repurposing:all](#71-content-repurposingall---see-everything-available)
   - 7.2 [/content-repurposing:extract](#72-content-repurposingextract---full-content-extraction)
   - 7.3 [/content-repurposing:linkedin](#73-content-repurposinglinkedin---linkedin-posts)
   - 7.4 [/content-repurposing:twitter](#74-content-repurposingtwitter---twitter-threads)
   - 7.5 [/content-repurposing:quotes](#75-content-repurposingquotes---quote-extraction)
   - 7.6 [/content-repurposing:titles](#76-content-repurposingtitles---title-generation)
8. [Working with Skills Directly](#8-working-with-skills-directly)
9. [Managing Your Output Files](#9-managing-your-output-files)
10. [Best Practices and Tips](#10-best-practices-and-tips)
11. [Frequently Asked Questions](#11-frequently-asked-questions)
12. [Next Steps](#12-next-steps)

---

## 1. Welcome

Welcome to the Content Repurposing Plugin for Claude Cowork. This guide will walk you through everything you need to know to turn your long-form content -- blog posts, video transcripts, articles, podcast notes -- into 20 or more platform-specific content pieces, all from a single source.

**Who is this guide for?** Anyone who creates content and wants to get more mileage from it. You might be a marketer, a blogger, a podcaster, a course creator, or someone who just writes a lot. You do not need any technical knowledge whatsoever. If you can type into a chat box and click a few buttons, you have all the skills required.

**What does this guide cover?** It walks you through installing the plugin, understanding how it works, using every command and skill, managing your output files, and getting the best results. By the end, you will be able to take any piece of content and generate LinkedIn posts, Twitter threads, Substack Notes, newsletter ideas, video scripts, quotable highlights, and compelling titles -- all in minutes.

**How long will this take?** About 15 to 20 minutes to read through. After that, you will be ready to try things out on your own content.

If you come across an unfamiliar term at any point, check the [Glossary](GLOSSARY.md) for a plain-English explanation.

---

## 2. What is Claude Cowork?

Claude Cowork is a desktop application where you collaborate with an AI assistant named Claude. If you have ever used a chat application like iMessage, WhatsApp, or Slack, you already understand the basic idea: you type a message, and Claude responds.

But Claude Cowork is more than a chatbot. Think of it as having a smart colleague sitting at the desk next to you. This colleague can read your documents, analyze your content, write new material, and save files to your computer -- all through a simple conversation.

Here is what the experience looks like in practice:

1. **You open the app.** You see a clean chat interface, similar to any messaging application.
2. **You type a message.** You might say "Here is my latest blog post" and paste your content, or you might point Claude to a file on your computer.
3. **Claude responds.** Claude reads what you shared, understands it, and replies with whatever you asked for -- a summary, social media posts, title ideas, or something else entirely.
4. **You can open folders.** Claude Cowork lets you open a folder on your computer. Once you do, Claude can read every file in that folder and save new files there. This is how you give Claude access to your content and how Claude delivers finished output back to you.

Without any plugins installed, Claude is a general-purpose assistant. It can answer questions, write drafts, help with research, and hold a conversation. It is helpful, but it is a generalist.

Plugins change that. They give Claude specialized skills and structured workflows, turning it from a general assistant into a focused specialist. That is where the Content Repurposing Plugin comes in.

---

## 3. What are Plugins?

Plugins are add-ons that teach Claude new skills. Without plugins, Claude is like a brand-new smartphone with no apps installed -- capable but limited. Installing a plugin is like installing an app: it unlocks specific capabilities that were not there before.

Here is an analogy. Imagine your computer without Microsoft Office. You can browse the web and manage files, but you cannot create spreadsheets or presentations. Install Office, and suddenly your computer can do all of that. Plugins work the same way for Claude.

The Content Repurposing Plugin turns Claude into a content creation specialist. Once installed, Claude gains structured workflows for extracting ideas from your content, generating platform-specific posts, pulling quotable statements, and creating compelling titles. Instead of giving Claude vague instructions and hoping for the best, the plugin provides Claude with proven frameworks, templates, and quality standards that produce consistent, professional results.

Every plugin comes with two things:

- **Commands**: Specific actions you can ask Claude to perform. You trigger these by typing a slash command in the chat box.
- **Skills**: Specialized knowledge that Claude uses behind the scenes. You do not trigger skills directly -- Claude draws on them automatically when they are relevant.

The next sections will explain both of these in detail.

---

## 4. Installing the Plugin

This section walks through every step of getting the plugin installed and running in Claude Cowork.

### Step 1: Get the Plugin File

The plugin is distributed as a folder (or `.zip` file) named `content-repurposing`.

- **If you downloaded this project from GitHub**: The `content-repurposing` folder is already in the project's root directory. You are ready to go.
- **If you received the plugin as a `.zip` file**: Save it somewhere you can find it easily, such as your Desktop or Downloads folder.
- **If you need to create the zip yourself**: See Section 9 of the [Developer Guide](DEVELOPER_GUIDE.md) for instructions on packaging the plugin.

### Step 2: Open Claude Cowork

Launch the Claude Cowork desktop application. If you have not installed it yet, download it from the official Claude Cowork website and follow the installation prompts for your operating system (Windows, Mac, or Linux).

Once the app opens, you should see the main chat interface -- a text input area at the bottom and a conversation area above it.

### Step 3: Open the Plugins Panel

1. Look at the **bottom-left corner** of the screen.
2. Click the **"Plugins"** tab. It may appear as an icon that looks like a puzzle piece or a plug.
3. The plugins panel opens on the left side of the screen. This panel shows all currently installed plugins.

### Step 4: Upload the Plugin

1. At the **top of the plugins panel**, click the **"+"** (plus) button.
2. A small menu appears. Select **"Upload Plugin"** (or "Add Plugin" depending on your version).
3. A file picker dialog opens -- this is the standard file browser for your operating system.
4. Navigate to the location where you saved the `content-repurposing` folder or `.zip` file.
5. Select the file and click **"Open"**.
6. Wait a few seconds while Claude Cowork processes the plugin.

### Step 5: Verify Installation

1. Look at the plugins panel on the left side. **"Content Repurposing"** should now appear in your list of installed plugins.
2. If it does not appear, close Claude Cowork completely and reopen it, then check the plugins panel again.
3. Click on **"Content Repurposing"** in the list.
4. You should see three things:
   - A **description** of the plugin
   - A **Commands** section listing all available commands
   - A **Skills** section listing all available skills

### Step 6: Enable the Plugin

1. Make sure the plugin is **enabled**. Look for a toggle switch next to the plugin name.
2. If the toggle is off (grayed out), click it to turn it on (it should change color, typically to blue or green).
3. Once enabled, Claude can access all of the plugin's commands and skills.

### Troubleshooting Installation

| Problem | Solution |
|---------|----------|
| Plugin does not appear after upload | Close and restart Claude Cowork, then check again |
| Zip file is rejected | Make sure the zip contains the correct folder structure -- see [Troubleshooting Guide](TROUBLESHOOTING.md) |
| Plugin appears but commands do not work | Make sure the plugin is enabled (toggle is on) |
| Error message during upload | Check that the zip file is not corrupted -- try downloading it again |

---

## 5. Understanding Commands vs Skills

Before you start using the plugin, it helps to understand the difference between commands and skills. They work together, but they play different roles.

### Commands: Things You Ask For

Commands are direct actions you trigger by typing in the chat box. Think of them as buttons on a TV remote -- you press a button, and something specific happens.

- Every command starts with `/content-repurposing:` followed by the command name.
- Example: Typing `/content-repurposing:linkedin` tells Claude "I want LinkedIn posts."
- You can see all available commands in the Commands tab of the plugin panel.
- **You** are always the one who triggers a command. Claude cannot run a command on its own.

### Skills: Knowledge Claude Uses

Skills are specialized knowledge and workflows that Claude draws on automatically. You do not invoke skills directly in most cases -- Claude uses them behind the scenes whenever they are relevant.

Think of it this way: Commands are the **"what"** (what you want), and Skills are the **"how"** (how Claude produces it). When you use the `/content-repurposing:linkedin` command, Claude automatically uses the `linkedin-post` skill behind the scenes. That skill contains detailed frameworks for hook patterns, post structures, tone guidelines, and quality standards.

You do not need to know which skill Claude is using. It happens automatically. But if you are curious, you can browse the Skills tab in the plugin panel to see what specialized knowledge is available.

### Quick Reference Table

| Feature | Commands | Skills |
|---------|----------|--------|
| **How to use** | Type `/content-repurposing:command` in chat | Claude uses them automatically |
| **Who triggers** | You (the user) | Claude (the AI assistant) |
| **When it happens** | When you type the command and press Enter | When Claude determines a skill is relevant |
| **Analogy** | TV remote buttons you press | TV's internal circuits that make the picture appear |
| **Where to find them** | Commands tab in plugin panel | Skills tab in plugin panel |
| **Number available** | 6 commands | 7 skills |

---

## 6. Setting Up Your Workspace

Claude Cowork uses a **workspace folder** -- a folder on your computer where Claude can read your content and save its output. Setting this up correctly is important.

### Why You Need a Workspace Folder

Claude needs to be able to:
- **Read** your source content (blog posts, transcripts, articles)
- **Save** the content it generates (LinkedIn posts, Twitter threads, title lists)

Both of these happen through a shared folder on your computer.

### Step-by-Step Setup

1. **Prepare your content folder.** Create a folder on your computer (or use an existing one) that contains the content you want to repurpose. For example, you might create a folder called `My Content` on your Desktop and put your blog posts, transcripts, and articles inside it.

2. **Open the folder in Claude Cowork.** In the Claude Cowork application, click **"Open Folder"** (usually in the top menu or sidebar). Navigate to your content folder and select it.

3. **Verify the connection.** After opening the folder, you can ask Claude: "What files do you see in my workspace?" Claude should list the files in your folder.

4. **Start working.** You are now ready to use the plugin commands. Claude will read files from this folder and save new output files here.

### Supported File Types

Claude can read content from:
- `.txt` files (plain text)
- `.md` files (Markdown)
- `.doc` / `.docx` files (Word documents)
- Content pasted directly into the chat box

### Where Output Files Go

All files that Claude generates are saved **in your workspace folder**. You will find them there after running any command. File names follow a consistent pattern so you can find them easily (more on this in Section 9).

### Tip: Organize Before You Start

If you have a lot of content, consider creating subfolders:
- `source-content/` for your original articles, transcripts, and blog posts
- `output/` for the content Claude generates

This keeps things tidy as you repurpose more material.

---

## 7. Using Each Command

This section walks through every command in the plugin with step-by-step instructions. For each command, you will learn what it does, when to use it, exactly how to use it, and what to expect.

### 7.1 /content-repurposing:all -- See Everything Available

**What it does:** Displays a complete list of all commands and skills available in the plugin.

**When to use it:** Use this command the first time you use the plugin, or anytime you forget what is available. It is a quick reference card you can pull up whenever you need it.

**How to use it:**

1. Click in the chat input box at the bottom of the screen.
2. Type exactly: `/content-repurposing:all`
3. Press **Enter** on your keyboard.
4. Claude responds with a formatted list of all commands and all skills, each with a short description.

**What you will see:** A clean, organized list divided into two sections -- **Commands (Quick Actions)** and **Skills (Auto-invoked by Claude)**. Each item includes its name and a brief description of what it does.

**Example output:**

```
Commands (Quick Actions)
- /content-repurposing:extract - Extract content ideas from any source
- /content-repurposing:linkedin - Generate LinkedIn posts
- /content-repurposing:twitter - Generate Twitter/X threads
- /content-repurposing:quotes - Extract quotable statements
- /content-repurposing:titles - Generate compelling titles

Skills (Auto-invoked by Claude)
- content-extraction - Full content idea extraction across all platforms
- key-takeaways - Summarize key takeaways
- linkedin-post - Create LinkedIn posts
- twitter-thread - Create Twitter/X threads
- quote-extractor - Pull quotable statements
- substack-note - Generate Substack Notes
- title-generator - Generate titles across platforms
```

This command does not require any content input -- it is purely informational.

---

### 7.2 /content-repurposing:extract -- Full Content Extraction

This is the **flagship command** of the plugin. It is the most powerful and comprehensive option, designed to squeeze the maximum number of content ideas from a single piece of source material.

**What it does:** Analyzes your content and generates 25 or more content ideas organized by platform, complete with titles, hooks, key points, priority ratings, and a week-by-week execution roadmap.

**When to use it:** Use this when you have a substantial piece of content (ideally 1,000 words or more) and you want a complete repurposing plan. This is the best starting point for any content repurposing session.

**How to use it:**

1. **Prepare your content.** Either have a file in your workspace folder, or copy your content to the clipboard.

2. **Enter the command.** Click in the chat box and type one of the following:
   - If your content is in a file: `/content-repurposing:extract my-blog-post.md`
   - If you want to paste content directly: `/content-repurposing:extract` followed by pasting your content into the chat

3. **Press Enter.**

4. **Answer Claude's questions.** Claude will ask you three things before generating output:

   - **"What platforms do you want content ideas for?"**
     - All platforms (Newsletter, Substack Notes, Twitter/X, LinkedIn, Short-form Video)
     - Social only (Twitter/X, LinkedIn, Substack Notes)
     - Newsletter/Long-form only
     - Custom selection (you pick which platforms)

   - **"What is the primary goal?"**
     - Brand awareness and reach
     - Engagement and community building
     - Lead generation and conversions
     - Thought leadership and authority
     - A mix of all

   - **"Any platforms or content types to skip?"**
     - Name anything you do not need (or say "none")

5. **Wait for Claude to generate.** This may take a minute depending on the length of your content. Claude is analyzing every angle, insight, story, and data point.

6. **Review the output.** Claude will display the results in the chat and save a report file to your workspace folder.

**Understanding the output:**

The extraction report is organized into platform-specific tables. Here is what each one contains:

- **Newsletter Ideas (5-7 ideas)**

  | Column | What It Means |
  |--------|---------------|
  | Title | A compelling subject line for the newsletter |
  | Angle/Hook | The unique perspective that makes this piece interesting |
  | Key Points | 3-4 bullet points that form the structure of the piece |
  | Est. Word Count | How long the finished newsletter would be |
  | Priority | High, Medium, or Low -- how urgently you should publish this |

- **Substack Notes Ideas (5-7 ideas)**

  | Column | What It Means |
  |--------|---------------|
  | Title | A short title for the Note |
  | Hook (first line) | The opening line that stops the scroll |
  | Key Insight | The main point of the Note |
  | CTA | A call-to-action or question to drive engagement |
  | Priority | High, Medium, or Low |

- **Twitter/X Thread Ideas (3-5 ideas)**

  | Column | What It Means |
  |--------|---------------|
  | Thread Title | A title describing the thread's topic |
  | Hook Tweet | The exact text of the first tweet (under 280 characters) |
  | # of Tweets | How many tweets the thread would contain |
  | Key Angle | The perspective or approach for the thread |
  | Priority | High, Medium, or Low |

- **LinkedIn Post Ideas (5-7 ideas)**

  | Column | What It Means |
  |--------|---------------|
  | Title | A title describing the post's topic |
  | Opening Hook | The first 1-2 lines visible before "see more" |
  | Format | Story, Listicle, Hot Take, How-To, Observation, Question, or Celebration |
  | Key Takeaway | The single insight the reader walks away with |
  | Priority | High, Medium, or Low |

- **Short-form Video Ideas (3-5 ideas)**

  | Column | What It Means |
  |--------|---------------|
  | Title | A title for the video |
  | Hook (first 3 sec) | What grabs attention in the first 3 seconds |
  | Key Point | The main message of the video |
  | Duration | 15s, 30s, 45s, or 60s |
  | Platform | TikTok, Instagram Reels, YouTube Shorts, or All |

- **Execution Roadmap**

  The final section is a 2-to-4-week publishing calendar:

  | Column | What It Means |
  |--------|---------------|
  | Week | Which week to publish |
  | Day | Which day of the week |
  | Platform | Where to post |
  | Content Piece | Which idea to publish |
  | Effort Level | Low (under 30 min), Medium (30-90 min), or High (90+ min) |
  | Status | Starts as "Planned" for all items |

**Output file:** Claude saves the complete report as `content-extraction-report-[source-name].md` in your workspace folder. Claude may also ask if you prefer a CSV format for use in spreadsheets.

---

### 7.3 /content-repurposing:linkedin -- LinkedIn Posts

**What it does:** Creates ready-to-post LinkedIn content from your source material. Each post follows proven LinkedIn engagement patterns with scroll-stopping hooks, structured bodies, and clear calls to action.

**When to use it:** When you need professional social media posts for LinkedIn. This is ideal for thought leadership content, industry insights, personal stories with business lessons, and tactical how-to advice.

**How to use it:**

1. **Prepare your content.** Have your source material ready -- either in a file or copied to your clipboard.

2. **Enter the command.** Click in the chat box and type:
   - With a file: `/content-repurposing:linkedin my-article.md`
   - With pasted content: `/content-repurposing:linkedin` then paste your content

3. **Press Enter.**

4. **Answer Claude's questions.** Claude will ask:
   - **"How many LinkedIn posts do you want?"** -- 1 to 5 is recommended. More posts means more variety.
   - **"What tone?"** -- Choose from: Thought Leader, Tactical, Storytelling, or Conversational.
   - **"Any specific angles to focus on?"** -- Optional. You can say "leadership," "practical tips," or just "no preference."

5. **Wait for Claude to generate.** Claude creates each post with a unique angle and style.

6. **Review the output.** Each post includes three parts:

   - **The Hook (first 2 lines):** The opening that appears before the "see more" button on LinkedIn. Claude uses one of five proven hook patterns:
     - Bold statement: "Most people get [topic] completely wrong."
     - Personal story: "Last week, I [experience] and it changed how I think about [topic]."
     - Question: "What if everything you knew about [topic] was backwards?"
     - Statistic: "[Number]% of [group] do [thing]. Here's why that matters."
     - Contrarian: "Unpopular opinion: [take]"

   - **The Body (150-300 words):** Short paragraphs with line breaks for easy reading. Includes concrete examples, a turning point or key insight, and builds toward a takeaway.

   - **The Close:** Ends with a question, call-to-action, memorable one-liner, or lesson summary. Includes 3-5 relevant hashtags on a separate line.

7. **Post styles vary across your posts.** Claude mixes these five styles to give you variety:
   - The Story (personal narrative leading to a lesson)
   - The Listicle ("X things I learned about Y")
   - The Hot Take (controversial opinion backed with evidence)
   - The How-To (step-by-step tactical advice)
   - The Reflection ("Looking back, here is what I would do differently")

**Output file:** Claude saves all posts as `linkedin-posts-[source-name].md` in your workspace folder, with each post separated by a horizontal rule.

**Tips:**
- Request 3 to 5 posts so you have variety to choose from.
- Try different tones to see which resonates with your audience.
- Copy and paste the posts directly into LinkedIn -- they are formatted and ready to go.
- Review and add your personal voice before posting. The posts are a strong starting point, not a final draft.

---

### 7.4 /content-repurposing:twitter -- Twitter Threads

**What it does:** Creates engaging Twitter/X threads that break down your content into tweet-sized insights. Each thread follows a proven structure: a hook tweet that demands attention, body tweets that deliver value, and a closing tweet with a call to action.

**When to use it:** When you want to share ideas on Twitter/X in a format that encourages engagement and followers. Threads work especially well for educational content, step-by-step processes, myth-busting, and storytelling.

**How to use it:**

1. **Prepare your content.** Have your source material ready.

2. **Enter the command.** Click in the chat box and type:
   - With a file: `/content-repurposing:twitter my-transcript.md`
   - With pasted content: `/content-repurposing:twitter` then paste your content

3. **Press Enter.**

4. **Answer Claude's questions.** Claude will ask:
   - **"How many threads do you want?"** -- 1 to 3 recommended per piece of source content.
   - **"Thread style?"** -- Choose from: Educational, Story, Listicle, or Breakdown.

5. **Wait for Claude to generate.**

6. **Review the output.** Each thread contains:

   - **Tweet 1 -- The Hook:** The most important tweet. It must create enough curiosity that readers click "Show thread." Claude uses patterns like:
     - "I spent [time] doing [thing]. Here's what I learned:"
     - "[Bold claim]. Here's why (and what to do about it):"
     - "Most people think [common belief]. They're wrong. Here's the truth:"
     - "[Number] [things] about [topic] that nobody talks about:"

   - **Tweets 2 through 8 -- The Body:** Each tweet delivers one clear point. Tweets are numbered (2/, 3/, etc.), include data or examples where possible, and sometimes end with a mini-hook to keep readers scrolling.

   - **Tweets 9 through 10 -- The Close:** A summary of the key insight, followed by a call to action ("Follow for more [topic]" or "RT the first tweet if this was helpful").

   - **Every single tweet is under 280 characters.** This is a strict rule with no exceptions.

   - **No thread exceeds 12 tweets.** Claude keeps threads tight and focused.

**Thread style variations:**
- **The Breakdown:** One big concept broken into its components, each explained in a tweet.
- **The Story Thread:** A chronological narrative with a lesson at the end.
- **The Listicle Thread:** "10 things about X" where each tweet is one numbered item.
- **The Myth-Buster:** "5 myths about X" where each myth is debunked in a tweet.

**Output file:** Claude saves all threads as `twitter-threads-[source-name].md` in your workspace folder. Each tweet is on its own line, numbered. Threads are separated by horizontal rules.

**Tips:**
- Post each tweet as a reply to the previous one to create the thread on Twitter/X.
- The hook tweet is the most important part. If it does not grab attention, nobody will read the rest.
- Save hashtags for the last tweet only -- mid-thread hashtags hurt engagement.

---

### 7.5 /content-repurposing:quotes -- Quote Extraction

**What it does:** Scans your content and pulls out the 10 to 15 most quotable, shareable statements. Each quote is scored by impact, categorized by type, and formatted for use on specific platforms.

**When to use it:** When you want pull quotes for social media graphics, tweet-worthy one-liners, presentation slides, email signatures, or any situation where you need a powerful standalone statement. This command is especially valuable for visual content -- think quote cards on Instagram, highlighted text in newsletters, or thumbnail text for YouTube.

**How to use it:**

1. **Prepare your content.** Have your source material ready.

2. **Enter the command.** Click in the chat box and type:
   - With a file: `/content-repurposing:quotes my-blog-post.md`
   - With pasted content: `/content-repurposing:quotes` then paste your content

3. **Press Enter.**

4. **Wait for Claude to generate.** Claude scans your content for statements that are surprising, concise, emotional, actionable, authoritative, contrarian, or universally resonant.

5. **Review the output.** The quotes are organized into four categories:

   - **Headline Quotes (2-3):** The absolute best statements -- strong enough to be a post headline, article title, or video thumbnail text.
   - **Insight Quotes (4-5):** Statements that convey a valuable insight or lesson learned.
   - **Story Quotes (2-3):** Memorable moments from anecdotes or personal experiences.
   - **Data Quotes (2-3):** Statistics, numbers, or factual claims that add credibility.

   Each quote includes:

   | Column | What It Means |
   |--------|---------------|
   | Quote | The exact quotable statement |
   | Context | Brief explanation of where this appeared in the content |
   | Impact Score (1-10) | How shareable and powerful the quote is |
   | Best Platform | Where this quote will perform best |
   | Suggested Visual | A recommendation for how to display it visually |

6. **Platform-specific formatting.** For the top 5 quotes, Claude provides ready-to-post versions:
   - **Twitter/X version:** Under 280 characters, optimized for engagement
   - **LinkedIn version:** With a brief context paragraph in a professional tone
   - **Instagram/Visual version:** Shortened for overlay on an image, with a suggested background style

**Output file:** Claude saves the quotes as `quotes-[source-name].md` in your workspace folder.

**Tips:**
- Impact scores are honest -- not everything gets a 10. Focus on the 8+ quotes for your best content.
- Use Headline Quotes for social media graphics and thumbnails.
- Use Data Quotes to add credibility to presentations and sales pages.
- Quotes are accurate to your source material. Claude does not paraphrase without noting it.

---

### 7.6 /content-repurposing:titles -- Title Generation

**What it does:** Generates 15 to 20 compelling title variations for your content, organized across five different styles. Includes a platform optimization table showing how each top title should be adapted for YouTube, blogs, newsletters, and social media. Also includes a title scorecard rating each title on clarity, curiosity, SEO potential, and click-worthiness.

**When to use it:** When you need a great headline for a blog post, video, newsletter, social media post, or any piece of content. A strong title can be the difference between content that gets read and content that gets ignored.

**How to use it:**

1. **Prepare your content.** Have your source material ready.

2. **Enter the command.** Click in the chat box and type:
   - With a file: `/content-repurposing:titles my-article.md`
   - With pasted content: `/content-repurposing:titles` then paste your content

3. **Press Enter.**

4. **Wait for Claude to generate.**

5. **Review the output.** Claude organizes titles into five style categories:

   - **Curiosity-Driven (3-4 titles):** Titles that create an information gap and make readers need to click.
     - Example pattern: "The [Adjective] [Topic] Secret That [Result]"
     - Example pattern: "What [Experts] Gets Wrong About [Topic]"
     - Example pattern: "I [Action] for [Time Period]. Here's What Happened."

   - **How-To / Tutorial (3-4 titles):** Titles that promise actionable, step-by-step value.
     - Example pattern: "How to [Achieve Result] in [Timeframe] (Step-by-Step)"
     - Example pattern: "The Complete Guide to [Topic] for [Audience]"

   - **Listicle (3-4 titles):** Numbered titles that promise organized value.
     - Example pattern: "[Number] [Things] That Will [Transform] Your [Area]"
     - Example pattern: "[Number] Mistakes [Audience] Make with [Topic]"

   - **Contrarian / Bold (2-3 titles):** Titles that challenge assumptions and provoke curiosity.
     - Example pattern: "Stop [Common Practice]: Why [Alternative] Works Better"
     - Example pattern: "The Case Against [Common Approach]"

   - **Question-Based (2-3 titles):** Titles that engage through direct questions.
     - Example pattern: "Is [Common Belief] Actually True?"
     - Example pattern: "What Would Happen If You [Bold Action]?"

6. **Platform Optimization Table.** For the top 5 titles, Claude provides adapted versions:

   | Column | What It Means |
   |--------|---------------|
   | Base Title | The original title |
   | YouTube Version | Under 60 characters, uses power words |
   | Blog/SEO Version | Includes primary keyword early, 50-60 characters |
   | Newsletter Subject Line | Creates urgency or curiosity, uses "you/your" |
   | Social Post Title | Shorter, punchier, more provocative |

7. **Title Scorecard.** Every title is rated on a 1-to-5 scale across four dimensions:

   | Dimension | What It Measures |
   |-----------|-----------------|
   | Clarity | How clearly the title communicates what the content is about |
   | Curiosity | How strongly the title creates a desire to click |
   | SEO Potential | How well the title would perform in search results |
   | Click-Worthiness | Overall likelihood someone clicks on this title |

**Output file:** Claude saves all titles as `titles-[source-name].md` in your workspace folder.

**Tips:**
- Sort by the "Total" column in the scorecard to find the highest-rated titles.
- Use different title styles for different platforms -- a curiosity-driven title might work on YouTube but a how-to title might work better for a blog.
- None of the titles are clickbait. Every title is deliverable by your actual content.

---

## 8. Working with Skills Directly

Most of the time, you will use commands (the `/content-repurposing:` instructions described above). But sometimes you want to access a skill without using a formal command. You can do this by simply asking Claude in natural language.

### key-takeaways

Ask Claude: "Summarize the key takeaways from this article" (and paste or reference your content).

Claude will use the key-takeaways skill to produce:
- An executive summary (2-3 sentences)
- 7 to 12 structured takeaways, each actionable and specific
- 3 to 5 deep-dive points with implementation advice
- A resources and references section
- A quick-reference summary suitable for SEO descriptions or show notes

### substack-note

Ask Claude: "Create Substack Notes from this blog post" (and paste or reference your content).

Claude will use the substack-note skill to generate 5 to 7 Notes, each:
- 100 to 300 words in length
- Written in a conversational tone (like talking to a smart friend)
- Structured as: Hook, Insight, Discussion prompt or CTA
- Free of hashtags (Substack Notes do not use them)

Each Note also includes a suggested posting time and expected engagement type.

### How Claude Knows Which Skill to Use

Claude is trained to recognize your intent. If you mention "LinkedIn posts," Claude uses the linkedin-post skill. If you say "find the best quotes," Claude uses the quote-extractor skill. You do not need to name the skill -- just describe what you want in plain language.

### Using Skills from the Plugin Panel

You can also access skills through the interface:
1. Click the **"+"** button in the chat area (or the plugin icon).
2. Navigate to **Plugins** then **Content Repurposing**.
3. Select a **skill** from the list.
4. Claude will guide you through the rest.

---

## 9. Managing Your Output Files

As you use the plugin, Claude saves output files to your workspace folder. Here is how to find, use, and organize them.

### Where Files Are Saved

All output files are saved in your **workspace folder** -- the folder you opened in Claude Cowork (see Section 6). Files appear at the top level of that folder unless you have created subfolders and asked Claude to use them.

### File Naming Pattern

Every output file follows a consistent naming pattern so you can find what you need:

| Command / Skill | File Name Pattern | Example |
|----------------|-------------------|---------|
| /extract | `content-extraction-report-[source-name].md` | `content-extraction-report-my-blog-post.md` |
| /linkedin | `linkedin-posts-[source-name].md` | `linkedin-posts-my-blog-post.md` |
| /twitter | `twitter-threads-[source-name].md` | `twitter-threads-my-blog-post.md` |
| /quotes | `quotes-[source-name].md` | `quotes-my-blog-post.md` |
| /titles | `titles-[source-name].md` | `titles-my-blog-post.md` |
| key-takeaways | `key-takeaways-[source-name].md` | `key-takeaways-my-blog-post.md` |
| substack-note | `substack-notes-[source-name].md` | `substack-notes-my-blog-post.md` |

### File Formats

- **Markdown (.md)** is the default format. Markdown files are plain text with simple formatting. You can open them in any text editor (Notepad, TextEdit, VS Code) or in a Markdown viewer.
- **CSV** is available for some commands (particularly /extract). CSV files can be opened in Excel, Google Sheets, or any spreadsheet application. Claude will ask which format you prefer.

### How to Use the Output

- **Copy and paste** directly from the markdown file to your social media platform, newsletter tool, or content management system.
- **Edit and personalize** before posting. Claude provides a strong starting point, but adding your personal voice makes the content better.
- **Import into scheduling tools** like Buffer, Hootsuite, or Later for batch scheduling.
- **Use as drafts** in your newsletter platform (Substack, ConvertKit, Mailchimp, etc.).

### Organizing Your Outputs

As you process more content, your workspace folder can fill up. Consider this organizational structure:

```
My Content/
  source-content/
    blog-post-january.md
    podcast-transcript-ep12.md
  output/
    2026-01/
      linkedin-posts-blog-post-january.md
      twitter-threads-blog-post-january.md
      quotes-blog-post-january.md
    2026-02/
      linkedin-posts-podcast-transcript-ep12.md
```

You can ask Claude to save files to a specific subfolder by including the path in your instructions.

---

## 10. Best Practices and Tips

These practical recommendations come from getting the best results out of the plugin across a wide range of content types.

1. **Start with /extract for a comprehensive overview.** Before drilling into any specific platform, run the full extraction command. It gives you a bird's-eye view of all the content ideas available and helps you prioritize what to create first.

2. **Longer content produces more ideas.** Aim for 1,000 words or more in your source material. A 500-word blog post might yield 10 to 15 ideas, but a 3,000-word transcript can easily produce 30 or more.

3. **Answer Claude's questions thoughtfully.** When Claude asks about platforms, goals, tone, and preferences, your answers directly shape the output. "All platforms, mix of goals" gives broad results. "LinkedIn only, thought leadership tone" gives focused results. Neither is wrong -- it depends on what you need.

4. **Use the execution roadmap to plan your posting schedule.** The roadmap from the /extract command organizes your ideas into a week-by-week calendar. Use it as a content calendar -- it saves hours of planning.

5. **Re-run commands with different preferences for variety.** Running `/content-repurposing:linkedin` twice with different tones (Storytelling the first time, Tactical the second) gives you two completely different sets of posts from the same source material.

6. **Review and personalize all output before posting.** Claude produces professional, ready-to-use content, but adding your personal voice, specific experiences, and individual style makes it authentically yours. Think of Claude's output as a strong first draft.

7. **Mix command types for a complete content strategy.** Use /quotes for visual content (Instagram graphics, presentation slides), /linkedin for text-based professional posts, /twitter for conversation-starting threads, and /titles for headlines across all your content.

8. **Batch your repurposing.** Process multiple pieces of content in one session. This is more efficient than repurposing one article at a time and helps you build a content backlog.

9. **Check the priority ratings.** Every idea in the /extract output has a priority rating (High, Medium, Low). Focus on the High-priority items first -- they represent the best combination of impact and effort.

10. **Save your favorite outputs as templates.** If Claude produces a LinkedIn post format you love, save it and reference it in future sessions. You can tell Claude: "Write LinkedIn posts in a similar style to this example" and paste your favorite output.

---

## 11. Frequently Asked Questions

**Do I need to know how to code?**
No. This plugin is designed for non-technical users. You type commands in a chat box and Claude does the rest. No programming, no configuration files, no command line.

**Can I use my own content?**
Yes. The plugin is designed to work with your content. Blog posts, video transcripts, podcast notes, articles, essays, newsletters -- any text-based content works.

**What types of content work best as source material?**
Long-form content works best. Blog posts, video transcripts, podcast transcripts, and articles of 1,000 words or more produce the most ideas. Shorter content (under 500 words) still works but yields fewer ideas.

**How many content ideas will I get?**
The /extract command generates 25 or more ideas across all platforms. Other commands produce varying amounts: /linkedin generates 1-5 posts, /twitter generates 1-3 threads, /quotes pulls 10-15 statements, and /titles creates 15-20 variations.

**Can I customize the output?**
Yes. Claude asks you about preferences (platforms, tone, number of pieces, goals) before generating content. Your answers shape the output. You can also ask Claude to revise any output after seeing it.

**Where do output files go?**
All output files are saved in your workspace folder -- the folder you opened in Claude Cowork. See Section 9 for file naming details.

**Is my content private?**
Claude processes your content within your session. Your content is not shared with other users or used to train future models through the plugin. Review Anthropic's privacy policy for details on how Claude handles data.

**What if Claude's output is not great?**
Re-run the command with different preferences. Ask Claude to revise specific pieces: "Make this LinkedIn post more conversational" or "Rewrite the hook to be more provocative." You can also provide an example of what you want and ask Claude to match that style.

**Can I use this for any platform, or only the ones listed?**
The plugin includes built-in support for LinkedIn, Twitter/X, Substack Notes, newsletters, and short-form video. For other platforms (Instagram captions, Facebook posts, YouTube descriptions), you can ask Claude directly and it will adapt -- the plugin's skills give Claude strong content creation foundations that transfer across platforms.

**Do I need an internet connection?**
Yes. Claude Cowork requires an internet connection to communicate with Claude.

**Can I use multiple plugins at the same time?**
Yes. Claude Cowork supports multiple plugins. They do not interfere with each other.

**How do I update the plugin?**
Upload a new version of the plugin zip file through the Plugins panel. The new version replaces the old one. Your previously generated output files are not affected.

**What if a command does not work?**
First, check that the plugin is enabled (the toggle in the plugin panel should be on). If it is enabled and the command still does not work, try restarting Claude Cowork. For persistent issues, see the [Troubleshooting Guide](TROUBLESHOOTING.md).

**Can I share this plugin with others?**
Yes. Share the plugin folder or zip file with anyone who has Claude Cowork. They follow the same installation steps in Section 4.

**What is the difference between /extract and the individual commands?**
The /extract command generates ideas across all platforms -- it tells you what to create and gives you a plan. The individual commands (/linkedin, /twitter, /quotes, /titles) generate the actual finished content for a specific platform. Think of /extract as the planning stage and the individual commands as the production stage.

**Can I process multiple pieces of content at once?**
You can process them one after another in the same session. Paste or reference one piece of content, run your commands, then move to the next. Each set of outputs will have its own file.

---

## 12. Next Steps

You now know everything you need to start repurposing your content with the Content Repurposing Plugin. Here is where to go from here:

- **[Quick Start Guide](QUICK_START_GUIDE.md)** -- Hands-on practice with guided examples. The fastest way to see the plugin in action.
- **[Developer Guide](DEVELOPER_GUIDE.md)** -- If you want to customize the plugin, add new commands, or modify existing skills.
- **[Troubleshooting Guide](TROUBLESHOOTING.md)** -- Solutions for common issues if something is not working as expected.
- **[Glossary](GLOSSARY.md)** -- Plain-English definitions for any technical terms you encounter.

The best way to learn is to try it. Open Claude Cowork, install the plugin, grab a piece of your content, and run `/content-repurposing:extract`. In a few minutes, you will have a complete content strategy ready to execute.
