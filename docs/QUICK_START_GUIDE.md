# Quick Start Guide

**10+ hands-on use cases to get you productive in minutes**

Welcome to the Content Repurposing Plugin for Claude Cowork. This guide walks you through real, practical use cases -- from your first command to customizing skills and creating your own. Each use case is self-contained: pick any one and follow along.

**Who this is for:** Anyone using Claude Cowork -- whether you are a content creator, marketer, developer, or just curious. No prior AI tool experience required.

**What you will learn:** How to turn one piece of long-form content into LinkedIn posts, Twitter threads, Substack Notes, quotable graphics, blog titles, and a full publishing roadmap -- all from a single source.

**Time estimate:** About 30 minutes if you do every use case, or 2-5 minutes per individual use case.

**Prerequisites:**
- [ ] Claude Cowork desktop app installed and running
- [ ] The `content-repurposing.zip` plugin file downloaded (or already uploaded)
- [ ] A piece of content you want to repurpose (or use the samples provided below)

> **New to plugin terminology?** See [docs/GLOSSARY.md](GLOSSARY.md) for definitions of commands, skills, hooks, manifests, and other terms used in this guide.

---

## Installation

Before you can use any of the commands or skills below, you need to install the plugin. Follow these steps exactly.

### Step 1: Locate the Plugin File

Find the `content-repurposing.zip` file on your computer. If you cloned or downloaded this repository, the zip file is in the project root directory. If someone sent it to you, note where you saved it.

### Step 2: Open Claude Cowork

Launch the Claude Cowork desktop application. Wait for it to fully load -- you should see the main chat interface.

### Step 3: Open the Plugins Panel

Look at the bottom-left corner of the Claude Cowork window. Click the **"Plugins"** tab. This opens the plugin management panel on the left side of the screen.

### Step 4: Start the Upload

In the top-left corner of the plugin panel, click the **"+"** button. A dropdown menu appears.

### Step 5: Select Upload Plugin

Click **"Upload Plugin"** from the dropdown menu. A file picker dialog opens.

### Step 6: Choose the Zip File

Navigate to where you saved `content-repurposing.zip`. Select it and click **Open** (or **Choose** on macOS).

### Step 7: Confirm Installation

Claude Cowork processes the zip file and installs the plugin. You should see **"Content Repurposing"** appear in your plugin list within a few seconds.

> **Not seeing it?** Close and reopen Claude Cowork. If the plugin still does not appear, see [docs/TROUBLESHOOTING.md](TROUBLESHOOTING.md).

### Step 8: Verify the Installation

Click on **"Content Repurposing"** in the plugin list. You should see:
- **6 commands** listed (all, extract, linkedin, twitter, quotes, titles)
- **7 skills** listed (content-extraction, key-takeaways, linkedin-post, quote-extractor, substack-note, title-generator, twitter-thread)

If you see all of these, you are ready to go. Proceed to Use Case 1.

---

## Use Case 1: See What's Available

**Difficulty:** Beginner | **Time:** 1 minute

**What You'll Do:** Run a single command to see every command and skill the plugin offers.

**Why This Is Useful:** Before diving into any tool, it helps to know what is available. This command is your reference card.

### Steps

1. Click into the Claude Cowork chat input at the bottom of the screen.
2. Type the following command exactly and press Enter:

```
/content-repurposing:all
```

3. Wait for Claude to respond.

### Expected Output

Claude displays a formatted list with two sections:

- **Commands (Quick Actions):** Five commands (`extract`, `linkedin`, `twitter`, `quotes`, `titles`), each with a short description.
- **Skills (Auto-invoked by Claude):** Seven skills (`content-extraction`, `key-takeaways`, `linkedin-post`, `twitter-thread`, `quote-extractor`, `substack-note`, `title-generator`), each with a short description.

### What You Learned

The `/content-repurposing:all` command is your quick reference. Come back to it any time you forget a command name.

> **Tip:** Commands start with `/content-repurposing:` and you type them directly. Skills are invoked automatically by Claude when it recognizes a relevant request -- you do not need to type a special command for them.

---

## Use Case 2: Extract Key Takeaways from a Blog Post

**Difficulty:** Beginner | **Time:** 3 minutes

**What You'll Do:** Paste a short blog post and ask Claude to distill it into structured takeaways using the key-takeaways skill.

**Why This Is Useful:** You have a long article and need a quick summary for show notes, an email preview, or a social media caption. The key-takeaways skill extracts the important points so you do not have to re-read the whole thing.

### Sample Content

Copy the text below. This is a short blog post about productivity:

```
The biggest productivity mistake I see professionals make is optimizing for
busyness instead of outcomes. After coaching over 200 executives, I have found
that the top performers share three habits. First, they block 90-minute
deep-work sessions every morning before checking email. Second, they say no to
at least 30 percent of meeting requests -- and nothing bad happens. Third,
they review their week every Friday in a 15-minute "shutdown ritual" that
clears mental debt. One client cut her working hours from 55 to 40 per week
and increased her team's output by 22 percent. The trick is not working
harder. It is protecting your attention like the scarce resource it is.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Paste the sample content above.
3. After the pasted content, add this instruction on a new line:

```
Please extract the key takeaways from this content.
```

4. Press Enter and wait for Claude to process.

### Expected Output

Claude uses the **key-takeaways** skill and returns a structured response containing:

- **Executive Summary:** Two to three sentences summarizing the content.
- **Key Takeaways:** Seven to twelve bullet points, each formatted as **[Topic]: [Specific insight]**. For example: **Deep Work:** Block 90-minute focused sessions every morning before checking email.
- **Deep-Dive Points:** Three to five of the most important takeaways expanded with "why it matters" and "how to implement."
- **Resources & References:** Any tools or frameworks mentioned.
- **Quick-Reference Summary:** A single paragraph of 50-75 words suitable for SEO descriptions or newsletter previews.

Claude also saves the output as a markdown file (e.g., `key-takeaways-blog-post.md`) in your workspace folder.

### What You Learned

You can invoke a skill by describing what you want in plain language. Claude recognizes that you need key takeaways and automatically uses the right skill.

> **Tip:** You do not need to name the skill explicitly. Just describe what you want ("extract key takeaways," "summarize this," "give me the main points") and Claude matches it to the correct skill.

---

## Use Case 3: Generate 3 LinkedIn Posts

**Difficulty:** Beginner | **Time:** 3 minutes

**What You'll Do:** Use the `/content-repurposing:linkedin` command to generate LinkedIn posts from sample content.

**Why This Is Useful:** Writing LinkedIn posts from scratch is time-consuming. This command generates multiple post variations from a single source, each with a different hook and style, so you can pick the best one or post them across different days.

### Sample Content

Copy the text below. This is a short piece about remote work:

```
Our company went fully remote in 2020 and never looked back. Revenue grew
41 percent in the first year. But it was not all smooth sailing. We lost
three senior engineers who missed the office energy. We had to completely
rebuild our onboarding process after new hires reported feeling isolated.
The fix was surprisingly simple: structured virtual coffee chats, a
buddy system for the first 90 days, and making every meeting optional
unless it had a written agenda. Retention climbed back to 94 percent.
The lesson? Remote work succeeds when you design for connection, not
just productivity.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Type the following command, then paste the sample content after it:

```
/content-repurposing:linkedin Our company went fully remote in 2020 and never looked back. Revenue grew 41 percent in the first year. But it was not all smooth sailing. We lost three senior engineers who missed the office energy. We had to completely rebuild our onboarding process after new hires reported feeling isolated. The fix was surprisingly simple: structured virtual coffee chats, a buddy system for the first 90 days, and making every meeting optional unless it had a written agenda. Retention climbed back to 94 percent. The lesson? Remote work succeeds when you design for connection, not just productivity.
```

3. Press Enter.
4. Claude asks follow-up questions: **How many LinkedIn posts do you want?** and **What tone?** Reply:

```
3 posts, thought leader tone
```

5. Press Enter and wait for Claude to generate.

### Expected Output

Claude returns three LinkedIn posts, each with:

- **A hook** (the first 2 lines designed to stop the scroll). For example: "We went fully remote and revenue jumped 41%. But then we almost lost everything."
- **A body** of 150-300 words using short paragraphs with line breaks.
- **A closing** with a question, call-to-action, or one-liner takeaway.
- **3-5 hashtags** on a separate line.

Each post uses a different style variation (for example: one Story, one Hot Take, one How-To). Claude also saves the posts to a file like `linkedin-posts-remote-work.md` in your workspace.

### What You Learned

The `/content-repurposing:linkedin` command triggers an interactive workflow. Claude asks clarifying questions before generating, so you can tailor the output to your needs.

> **Tip:** Try different tones to see how the output changes. "Storytelling" produces narrative posts, "Tactical" gives step-by-step advice, and "Conversational" reads like talking to a colleague.

---

## Use Case 4: Create a Twitter Thread

**Difficulty:** Beginner | **Time:** 3 minutes

**What You'll Do:** Use the `/content-repurposing:twitter` command to turn content into a tweet thread with a 280-character limit enforced on every tweet.

**Why This Is Useful:** Breaking long-form content into a Twitter thread requires compressing ideas into bite-sized pieces while maintaining a narrative. This command handles the constraints automatically.

### Sample Content

Copy the text below. This is a short piece about AI trends:

```
The AI landscape shifted dramatically in 2025. Foundation models became
commoditized -- the real value moved to fine-tuning and domain-specific
applications. Three trends stood out: first, AI agents that can execute
multi-step workflows without human intervention. Second, on-device models
running locally for privacy-sensitive applications. Third, the rise of
AI-native companies built from day one around language models rather than
retrofitting AI into legacy software. The companies winning today are not
the ones with the biggest models. They are the ones with the best data
flywheels and the clearest understanding of their users' actual problems.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Type the following command followed by the sample content:

```
/content-repurposing:twitter The AI landscape shifted dramatically in 2025. Foundation models became commoditized -- the real value moved to fine-tuning and domain-specific applications. Three trends stood out: first, AI agents that can execute multi-step workflows without human intervention. Second, on-device models running locally for privacy-sensitive applications. Third, the rise of AI-native companies built from day one around language models rather than retrofitting AI into legacy software. The companies winning today are not the ones with the biggest models. They are the ones with the best data flywheels and the clearest understanding of their users' actual problems.
```

3. Press Enter.
4. Claude asks: **How many threads?** and **Thread style?** Reply:

```
1 thread, breakdown style
```

5. Press Enter and wait.

### Expected Output

Claude returns a single thread containing:

- **Tweet 1 (The Hook):** A compelling opening tweet under 280 characters. For example: "The AI landscape shifted in 2025 -- but not how most people expected. The winners aren't who you think. Here's what actually happened:"
- **Tweets 2-8 (The Body):** Each numbered (2/, 3/, etc.), each delivering one clear point, each strictly under 280 characters.
- **Tweets 9-10 (The Close):** A summary tweet and a call-to-action tweet.

The total thread is no longer than 12 tweets. Claude saves the output to a file like `twitter-threads-ai-trends.md`.

### What You Learned

The twitter-thread skill enforces the 280-character limit on every single tweet and keeps threads to a maximum of 12 tweets. You do not need to count characters manually.

> **Tip:** If you want multiple angles on the same content, ask for 2-3 threads with different styles (breakdown, story, listicle, myth-buster).

---

## Use Case 5: Pull Quotable Statements

**Difficulty:** Beginner | **Time:** 3 minutes

**What You'll Do:** Use the `/content-repurposing:quotes` command to extract the most shareable statements from your content, scored by impact.

**Why This Is Useful:** Great quotes make great social media graphics, newsletter pull-quotes, and presentation slides. This command finds the best quotes and formats them for each platform.

### Sample Content

Copy the text below. This is a short piece about startup lessons:

```
After bootstrapping two companies and raising venture capital for a third,
here is what I know for certain: the market does not care about your product.
It cares about the problem you solve. Every failed startup I have studied
had a beautiful product that solved the wrong problem. Speed is your only
real advantage as a startup. The moment you stop shipping weekly, larger
competitors start catching up. And the most counterintuitive lesson of all:
your first ten customers will teach you more than your entire business plan.
Throw the plan away and go talk to people. Revenue is the ultimate
validation. Everything else is just opinion.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Type the following command followed by the sample content:

```
/content-repurposing:quotes After bootstrapping two companies and raising venture capital for a third, here is what I know for certain: the market does not care about your product. It cares about the problem you solve. Every failed startup I have studied had a beautiful product that solved the wrong problem. Speed is your only real advantage as a startup. The moment you stop shipping weekly, larger competitors start catching up. And the most counterintuitive lesson of all: your first ten customers will teach you more than your entire business plan. Throw the plan away and go talk to people. Revenue is the ultimate validation. Everything else is just opinion.
```

3. Press Enter and wait.

### Expected Output

Claude returns 10-15 quotes organized into four categories:

- **Headline Quotes (2-3):** The strongest statements, such as "The market does not care about your product. It cares about the problem you solve."
- **Insight Quotes (4-5):** Valuable lessons and observations.
- **Story Quotes (2-3):** Memorable anecdotes or personal moments.
- **Data Quotes (2-3):** Factual claims or statistics.

Each quote includes:
- The exact text from the source
- Context (where it appeared and what it relates to)
- An **Impact Score** from 1 to 10
- The **Best Platform** for that quote (Twitter, LinkedIn, Instagram, etc.)
- A **Suggested Visual** style

For the top 5 quotes, Claude provides ready-to-post versions formatted for Twitter (under 280 chars), LinkedIn (with context paragraph), and Instagram (shortened for image overlay).

Claude saves the output to a file like `quotes-startup-lessons.md`.

### What You Learned

The quote-extractor skill does not just find quotes -- it scores them by impact and formats them for specific platforms so you can go straight from extraction to posting.

> **Tip:** Quotes with an Impact Score of 8 or above are your best candidates for social media graphics and pull-quotes.

---

## Use Case 6: Generate Headlines for Your Content

**Difficulty:** Beginner | **Time:** 3 minutes

**What You'll Do:** Use the `/content-repurposing:titles` command to generate 15-20 title variations across five different styles, with a platform optimization table.

**Why This Is Useful:** The title is the single most important factor in whether someone clicks. This command gives you a menu of options across styles (curiosity, how-to, listicle, contrarian, question) so you can A/B test or pick the best fit.

### Sample Content

Copy the text below. This is a short piece about personal growth:

```
I read 52 books last year and it changed exactly one thing about my life:
I stopped reading for quantity. The real shift happened when I started
taking notes on just three books and applying their lessons for 90 days
each. Deep reading of three books beat shallow reading of fifty. My
income grew, my stress dropped, and I finally understood what people
mean when they say knowledge compounds. The books were about negotiation,
systems thinking, and stoic philosophy. None of them were productivity
books, which is ironic because they made me more productive than any
productivity book ever did.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Type the following command followed by the sample content:

```
/content-repurposing:titles I read 52 books last year and it changed exactly one thing about my life: I stopped reading for quantity. The real shift happened when I started taking notes on just three books and applying their lessons for 90 days each. Deep reading of three books beat shallow reading of fifty. My income grew, my stress dropped, and I finally understood what people mean when they say knowledge compounds. The books were about negotiation, systems thinking, and stoic philosophy. None of them were productivity books, which is ironic because they made me more productive than any productivity book ever did.
```

3. Press Enter and wait.

### Expected Output

Claude returns 15-20 title variations organized by style:

- **Curiosity-Driven (3-4):** e.g., "I Read 52 Books Last Year. Only 3 Actually Mattered."
- **How-To / Tutorial (3-4):** e.g., "How to Get More from 3 Books Than Most People Get from 50"
- **Listicle (3-4):** e.g., "3 Books That Changed My Income, Stress Levels, and Mindset"
- **Contrarian / Bold (2-3):** e.g., "Stop Reading So Many Books. It's Making You Worse."
- **Question-Based (2-3):** e.g., "What If Reading Less Made You Smarter?"

After the titles, Claude provides a **Platform Optimization Table** for the top 5 titles showing versions tailored for YouTube (under 60 characters), Blog/SEO, Newsletter subject lines, and social posts.

Finally, a **Title Scorecard** rates each title on Clarity, Curiosity, SEO Potential, and Click-Worthiness (each scored 1-5).

Claude saves the output to a file like `titles-reading.md`.

### What You Learned

The title-generator skill produces titles in five distinct styles and then optimizes the best ones for specific platforms. The scorecard helps you make a data-informed decision about which title to use.

> **Tip:** For YouTube thumbnails, look at the titles in the "Contrarian / Bold" category -- they tend to be short and punchy, which works well in large text overlays.

---

## Use Case 7: Create Substack Notes

**Difficulty:** Beginner | **Time:** 3 minutes

**What You'll Do:** Ask Claude to generate Substack Notes from sample content using the substack-note skill.

**Why This Is Useful:** Substack Notes have a specific tone -- conversational, first-person, no hashtags. This skill produces 5-7 ready-to-post Notes that feel native to the platform.

### Sample Content

Copy the text below. This is a short piece about writing:

```
Most writing advice focuses on technique: use active voice, cut adverbs,
show don't tell. But the writers who build real audiences do something
different. They write with a consistent point of view that readers can
predict and trust. The technique does not matter if the reader does not
know who they are listening to. I spent two years writing technically
correct but soulless posts. Engagement was flat. Then I started writing
as if I were explaining things to a specific friend -- and my subscriber
count tripled in six months. Voice is not a skill. It is a decision.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Paste the sample content above.
3. After the pasted content, add this instruction on a new line:

```
Create Substack Notes from this content.
```

4. Press Enter and wait.

### Expected Output

Claude uses the **substack-note** skill and returns 5-7 Substack Notes. Each Note includes:

- **The full Note text** (100-300 words), written in first person with a conversational tone.
- **Suggested posting time** (e.g., "Tuesday Morning").
- **Expected engagement type** (e.g., "Comments" or "Restacks").
- **Whether it should link** to longer content.

The Notes span several types, such as:
- A **Quick Insight** distilling one key lesson.
- A **Question** designed to spark comments (e.g., "When did your writing voice finally click? Was it a gradual shift or a single moment?").
- A **Hot Take** (e.g., challenging the idea that technique matters most).
- A **Teaser** that drives readers to the full article.

Notice: no hashtags, no corporate tone, no excessive emojis. This matches Substack's platform culture.

Claude saves the output to a file like `substack-notes-writing.md`.

### What You Learned

The substack-note skill automatically adapts to Substack's conventions -- first-person voice, conversational tone, no hashtags. Each Note includes scheduling and engagement guidance.

> **Tip:** Notes that ask questions tend to generate the most comments. The skill always includes at least two question-style Notes for this reason.

---

## Use Case 8: Full Content Extraction with Roadmap

**Difficulty:** Intermediate | **Time:** 5 minutes

**What You'll Do:** Use the `/content-repurposing:extract` command to run the flagship skill -- a full content extraction that produces 25+ ideas across five platforms with an execution roadmap.

**Why This Is Useful:** This is the command you reach for when you have a substantial piece of content (blog post, podcast transcript, video transcript) and want to extract maximum value. Instead of one or two posts, you get a full publishing calendar.

### Sample Content

Copy the text below. This is a longer piece about building a personal brand:

```
Building a personal brand in 2025 requires a fundamentally different approach
than it did five years ago. The content landscape is oversaturated, attention
spans are shorter, and algorithms favor consistency over virality. Here is
what is actually working.

First, pick one platform and go deep before expanding. Multi-platform
strategies sound smart but spread most people too thin. I built my first
10,000 followers entirely on LinkedIn before touching Twitter or YouTube.

Second, create content systems, not content pieces. The creators who last are
the ones who batch-produce, repurpose, and schedule. A single podcast episode
should become a newsletter, five LinkedIn posts, three Twitter threads, and
ten short-form clips. If you are creating each piece from scratch, you are
working ten times harder than you need to.

Third, engage more than you broadcast. For every post you publish, leave
five thoughtful comments on other people's content. The algorithm rewards
engagement, and genuine conversations build relationships that followers
alone never will.

Fourth, share your process, not just your results. Behind-the-scenes content
consistently outperforms polished final products. People connect with the
struggle, the iteration, the honesty.

Finally, measure what matters. Follower count is vanity. Track reply rate,
saves, shares, and inbound opportunities. Those are the metrics that
translate to actual business results.
```

### Steps

1. Click into the Claude Cowork chat input.
2. Type the following command followed by the sample content:

```
/content-repurposing:extract Building a personal brand in 2025 requires a fundamentally different approach than it did five years ago. The content landscape is oversaturated, attention spans are shorter, and algorithms favor consistency over virality. Here is what is actually working. First, pick one platform and go deep before expanding. Multi-platform strategies sound smart but spread most people too thin. I built my first 10,000 followers entirely on LinkedIn before touching Twitter or YouTube. Second, create content systems, not content pieces. The creators who last are the ones who batch-produce, repurpose, and schedule. A single podcast episode should become a newsletter, five LinkedIn posts, three Twitter threads, and ten short-form clips. If you are creating each piece from scratch, you are working ten times harder than you need to. Third, engage more than you broadcast. For every post you publish, leave five thoughtful comments on other people's content. The algorithm rewards engagement, and genuine conversations build relationships that followers alone never will. Fourth, share your process, not just your results. Behind-the-scenes content consistently outperforms polished final products. People connect with the struggle, the iteration, the honesty. Finally, measure what matters. Follower count is vanity. Track reply rate, saves, shares, and inbound opportunities. Those are the metrics that translate to actual business results.
```

3. Press Enter.
4. Claude asks two questions:
   - **What platforms do you want content ideas for?**
   - **What is the primary goal?**

   Reply:

```
All platforms. The goal is a mix of brand awareness and engagement.
```

5. Press Enter and wait. This takes a moment because the output is comprehensive.

### Expected Output

Claude returns a full content extraction report with these sections:

1. **Newsletter Ideas (5-7):** Table with Title, Angle/Hook, Key Points, Estimated Word Count, and Priority.
2. **Substack Notes Ideas (5-7):** Table with Title, Hook, Key Insight, CTA, and Priority.
3. **Twitter/X Thread Ideas (3-5):** Table with Thread Title, Hook Tweet (exact text, under 280 chars), number of tweets, Key Angle, and Priority.
4. **LinkedIn Post Ideas (5-7):** Table with Title, Opening Hook, Format, Key Takeaway, and Priority.
5. **Short-form Video Ideas (3-5):** Table with Title, Hook (first 3 seconds), Key Point, Duration, and Platform.

Total: 25+ ideas across all platforms.

6. **Execution Roadmap:** A 2-4 week publishing calendar table with Week, Day, Platform, Content Piece, Effort Level, and Status columns. Ideas are organized as Quick Wins first, then Engagement Drivers, Authority Builders, and Evergreen Pieces.

Claude saves the full report to a file like `content-extraction-report-personal-brand.md`.

### What You Learned

The extract command is the most powerful tool in the plugin. It turns a single piece of content into a complete multi-platform publishing strategy with a prioritized calendar.

> **Tip:** After running the extraction, use the individual commands (`/content-repurposing:linkedin`, `/content-repurposing:twitter`, etc.) to generate the actual content for your highest-priority ideas.

---

## Use Case 9: Repurpose a YouTube Transcript

**Difficulty:** Intermediate | **Time:** 5 minutes

**What You'll Do:** Open a project folder containing a transcript file and use the extract command to repurpose it, referencing the file directly instead of pasting content.

**Why This Is Useful:** Real-world content often lives in files -- YouTube transcripts, podcast notes, draft articles. You do not need to paste everything into the chat. Claude can read files from your workspace.

### Setup

Before starting, you need a transcript file in your workspace.

1. In your Claude Cowork workspace folder, create a new text file named `transcript.txt`.
2. Paste the following sample transcript into it and save:

```
So the question everyone keeps asking me is: how do you actually get started
with AI if you are not a developer? And my answer is always the same. Start
with one workflow. Do not try to automate your entire business on day one.
Pick the one task that eats up most of your time and figure out how AI can
help with just that one thing. For me, it was email. I was spending two hours
a day on email. I set up a simple AI assistant that drafts replies based on
my writing style, and now email takes me 20 minutes. That single win gave me
the confidence to tackle bigger problems. The key insight is that AI adoption
is not a technology problem. It is a behavior change problem. You have to
build the habit of asking: could AI help with this? Once that mental model
clicks, everything accelerates.
```

### Steps

1. Make sure the `transcript.txt` file is saved in your workspace folder.
2. Open Claude Cowork and make sure the workspace containing `transcript.txt` is active (visible in the file panel).
3. Click into the chat input and type:

```
/content-repurposing:extract Use the file transcript.txt as the source content.
```

4. Press Enter.
5. Claude reads the file from your workspace and asks about platform preferences and goals. Reply:

```
Social only (Twitter, LinkedIn, Substack Notes). Goal is engagement.
```

6. Press Enter and wait.

### Expected Output

Claude reads the file and produces the same extraction report as Use Case 8, but scoped to social platforms only:

- **Substack Notes Ideas (5-7)**
- **Twitter/X Thread Ideas (3-5)**
- **LinkedIn Post Ideas (5-7)**

Plus an **Execution Roadmap** covering 2-4 weeks.

The report is saved as `content-extraction-report-transcript.md` in your workspace.

### What You Learned

You can reference files in your workspace instead of pasting content into the chat. This is essential for working with long transcripts, articles, and documents.

> **Tip:** This works with any text file in your workspace -- `.txt`, `.md`, `.doc` excerpts, even meeting notes. If the file is in your workspace, Claude can read it.

---

## Use Case 10: Compare Output Across Platforms

**Difficulty:** Intermediate | **Time:** 5 minutes

**What You'll Do:** Run three different commands on the same source content and compare how the output changes for each platform.

**Why This Is Useful:** A great piece of content should be adapted -- not just copied -- across platforms. This exercise shows you exactly how the same insight becomes a quote graphic, a LinkedIn post, and a Twitter thread.

### Sample Content

Use this short piece about leadership:

```
The best managers I have worked with share one trait: they ask more questions
than they give answers. They resist the urge to solve every problem for their
team. Instead, they ask: what have you tried so far? What do you think we
should do? What is blocking you? This approach feels slower at first, but it
builds a team that can think independently. The manager who has all the
answers creates a team that cannot function without them. The manager who
asks great questions builds a team that eventually does not need them at all.
```

### Steps

**Step A: Extract Quotes**

1. Type the following command with the sample content:

```
/content-repurposing:quotes The best managers I have worked with share one trait: they ask more questions than they give answers. They resist the urge to solve every problem for their team. Instead, they ask: what have you tried so far? What do you think we should do? What is blocking you? This approach feels slower at first, but it builds a team that can think independently. The manager who has all the answers creates a team that cannot function without them. The manager who asks great questions builds a team that eventually does not need them at all.
```

2. Note the top quotes and their impact scores.

**Step B: Generate a LinkedIn Post**

3. In a new message, type:

```
/content-repurposing:linkedin The best managers I have worked with share one trait: they ask more questions than they give answers. They resist the urge to solve every problem for their team. Instead, they ask: what have you tried so far? What do you think we should do? What is blocking you? This approach feels slower at first, but it builds a team that can think independently. The manager who has all the answers creates a team that cannot function without them. The manager who asks great questions builds a team that eventually does not need them at all.
```

4. When asked, reply: `1 post, storytelling tone`.

**Step C: Generate a Twitter Thread**

5. In a new message, type:

```
/content-repurposing:twitter The best managers I have worked with share one trait: they ask more questions than they give answers. They resist the urge to solve every problem for their team. Instead, they ask: what have you tried so far? What do you think we should do? What is blocking you? This approach feels slower at first, but it builds a team that can think independently. The manager who has all the answers creates a team that cannot function without them. The manager who asks great questions builds a team that eventually does not need them at all.
```

6. When asked, reply: `1 thread, listicle style`.

### Expected Output

Compare the three outputs:

| Aspect | Quotes | LinkedIn | Twitter |
|--------|--------|----------|---------|
| Length | Single sentences | 150-300 word post | 5-12 tweets, each under 280 chars |
| Tone | Direct, punchy | Professional, storytelling | Casual, concise |
| Structure | Standalone statements | Hook, body, close, hashtags | Hook tweet, numbered body, CTA |
| Best Use | Graphics, slides, pull-quotes | Professional feed, thought leadership | Broad reach, engagement threads |

### What You Learned

The same source content produces very different outputs depending on the platform. Each skill adapts the tone, length, structure, and style to match platform conventions.

> **Tip:** Run all three commands back-to-back on your own content. Pick the best quote for a social graphic, the LinkedIn post for your professional feed, and the thread for Twitter. One source, three platforms, five minutes.

---

## Use Case 11: Customize a Skill

**Difficulty:** Intermediate | **Time:** 5 minutes

**What You'll Do:** Open the linkedin-post SKILL.md file, add a new hook pattern, save it, and test the change.

**Why This Is Useful:** The plugin's skills are written in plain Markdown. You can modify them to match your brand voice, add new patterns, or remove ones you do not use. No coding required.

### Steps

1. In Claude Cowork, navigate to the file browser (left panel).
2. Open the following file:

```
content-repurposing/skills/linkedin-post/SKILL.md
```

3. Scroll down to the section titled **"The Hook (First 2 lines)"**. You will see five hook patterns:
   - Bold statement
   - Personal story
   - Question
   - Statistic
   - Contrarian

4. Add a sixth hook pattern at the end of the list. Click at the end of the Contrarian line, press Enter, and type:

```
- Data-driven: "[Percentage]% of [audience] [do something]. I tested [alternative] and here's what I found."
```

5. Save the file (Ctrl+S or Cmd+S).

6. Now test it. Go to the chat input and type:

```
/content-repurposing:linkedin I spent three months testing different LinkedIn posting strategies across four accounts. Posts with a specific data point in the first line received 2.4 times more engagement than posts with generic hooks. Posts published between 7-8am on Tuesday got the highest reach. And carousel posts outperformed text posts by 3 to 1 for follower growth.
```

7. When asked, reply: `2 posts, tactical tone`.

8. Look at the generated posts. At least one should use a data-driven hook reflecting your new pattern.

### Expected Output

The generated LinkedIn posts now include the data-driven hook pattern you added. For example, a post might open with: "Posts with specific data in the first line get 2.4x more engagement. I tested this across four accounts and here's what I found."

### What You Learned

Skills are editable Markdown files. You can customize hook patterns, tone guidelines, output structure, and quality standards by editing the SKILL.md file directly.

> **Tip:** Before modifying a skill, make a backup copy of the original file. That way you can always revert if an edit produces unexpected results.

---

## Use Case 12: Create Your First Custom Skill

**Difficulty:** Intermediate | **Time:** 10 minutes

**What You'll Do:** Create a brand-new "instagram-caption" skill from scratch -- including the directory and the SKILL.md file -- and test it.

**Why This Is Useful:** The plugin is extensible. If you need a content format that does not exist yet (Instagram captions, email subject lines, podcast show notes), you can build a skill for it. This use case walks you through the entire process.

### Steps

**Step 1: Create the Directory**

1. In Claude Cowork's file browser, navigate to:

```
content-repurposing/skills/
```

2. Right-click in the skills folder and choose **"New Folder"**.
3. Name the folder:

```
instagram-caption
```

**Step 2: Create the SKILL.md File**

4. Open the new `instagram-caption` folder.
5. Create a new file inside it named:

```
SKILL.md
```

6. Paste the following content into the file:

```markdown
---
name: instagram-caption
description: Generates Instagram captions from long-form content. Use when creating Instagram posts, reels captions, or carousel descriptions.
---

# Instagram Caption Skill

You are an Instagram content strategist who writes captions that drive engagement, tell stories, and encourage saves and shares.

## Process

### Step 1: Analyze the Source
Read the source content and identify:
- Visual moments that pair well with images
- Short, punchy insights
- Emotional or relatable statements
- Call-to-action opportunities

### Step 2: Ask the User
- **How many captions do you want?** (1-5 recommended)
- **What style?** (Storytelling / Educational / Inspirational / Casual)
- **Include a call-to-action?** (Yes / No)

### Step 3: Generate Captions

For each caption, follow this structure:

#### The Hook (First Line)
The first line appears before "...more" and must compel the tap. Keep it under 125 characters.

#### The Body
- 50-150 words
- Use line breaks for readability
- Include emojis sparingly (2-4 per caption)
- Write in a conversational, first-person tone

#### The Close
- End with a question or CTA to drive comments
- Add a line break, then 20-30 relevant hashtags grouped at the end

### Step 4: Save Output
Save all captions to a markdown file in the workspace folder.
Name: `instagram-captions-[source-name].md`

## Quality Standards
- Each caption should work without seeing the image
- Hooks must be compelling in under 125 characters
- Hashtags should mix popular (500K+ posts) and niche (10K-100K posts)
- No caption should exceed 2,200 characters (Instagram's limit)
```

7. Save the file.

**Step 3: Test the New Skill**

8. Go to the Claude Cowork chat input and type:

```
Generate Instagram captions from this content: The most valuable career skill I ever developed was not technical. It was learning how to write clearly. Clear writing means clear thinking. Every promotion I have received, every client I have won, every partnership I have closed -- it started with a well-written message. You do not need to be a professional writer. You just need to be a clear one.
```

9. Press Enter.
10. Claude should recognize your new skill and use it to generate Instagram captions.

### Expected Output

Claude uses your new **instagram-caption** skill and asks your preferences (number of captions, style, CTA preference). After you reply, it generates captions with:

- A punchy first-line hook (under 125 characters)
- A conversational body (50-150 words)
- A closing question or CTA
- 20-30 hashtags grouped at the end

For example, a caption might start with: "The most valuable career skill I developed? Not coding. Not design. Writing." followed by a short story and a question like "What skill has had the biggest impact on your career? Drop it below."

### What You Learned

You can create entirely new skills by adding a folder and a SKILL.md file to the `skills/` directory. The SKILL.md file defines the persona, process, and quality standards. No code, no configuration -- just Markdown.

> **Tip:** Use an existing skill file as your starting template. Copy the structure from `linkedin-post/SKILL.md` or `twitter-thread/SKILL.md` and modify it for your new platform.

---

## What's Next?

You have completed the Quick Start Guide. Here is where to go from here:

- **[User Guide](USER_GUIDE.md)** -- Full reference for every command and skill, with advanced usage patterns, parameter options, and output customization.

- **[Developer Guide](DEVELOPER_GUIDE.md)** -- Build your own Claude Cowork plugins from scratch. Covers plugin architecture, manifest files, command routing, skill design, and testing.

- **[Agent Team Guide](AGENT_TEAM_GUIDE.md)** -- Learn how this entire plugin was built by a team of 6 AI agents in under 5 minutes. Covers the Agent Teams feature, task delegation, parallel execution, and team orchestration.

- **[Glossary](GLOSSARY.md)** -- Definitions for all terms used across the documentation.

---

*This guide is part of the Content Repurposing Plugin documentation. Built with Claude Opus 4.6 Agent Teams.*
