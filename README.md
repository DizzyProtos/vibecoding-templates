# Vibecoding Workshop Templates

This repository contains **seven core project ideas** plus a set of **meme & fun bonus projects**, along with Cursor configuration (rules + skills) for building **2–3 hour MVPs** in a vibecoding workshop. Each project is a Node.js/TypeScript API with clear architecture, domain guidance, and optional MCP integrations.

---

## What’s in this repo

| Item | Purpose |
|------|--------|
| **`RULE.md`** | Project-wide Cursor rules: shared conventions, folder layout, and per-project architecture (entities, endpoints, testing). |
| **`skills/`** | One Cursor skill per project. Each skill encodes domain knowledge, API design, and test guidance so the agent stays on-spec. |

### The 7 core project ideas

1. **Team Mood & Check-In API** — Daily check-ins, mood scores, team summaries  
2. **Minimal Kanban/Task Board Backend** — Projects, columns, tasks, workflow (todo → doing → done)  
3. **Micro-Journal Prompt Generator API** — Stored prompts, random/themed retrieval, user preferences  
4. **AI-Powered Habit Tracker API** — Habits, completions, streaks, daily summary  
5. **Content Curation & Summarization Service** — Submit URL, fetch, summarize, tag  
6. **Personal Finance Snapshot API** — Transactions, categories, monthly snapshot  
7. **Event RSVP & Waitlist Service** — Events, capacity, registration, waitlist, promote  

Details for each (entities, endpoints, testing) are in **`RULE.md`** and in the matching skill under **`skills/`**.

### Meme & fun bonus projects

These are extra, playful APIs you can use for late-night vibecoding or as light-hearted workshop options:

1. **Waifu Image Generator API** — Define your “ideal waifu” and generate image prompts/metadata via OpenAI or another image MCP  
2. **Wasted Time Tracker API** — Start/stop “wasted time” sessions and get playful suggestions for what you could have done instead  
3. **Mortality Countdown API** — Input age or birthdate and get a countdown until age 80 (dark humor, technically correct date math)  

Architecture, entities, endpoints, and test expectations for these are in the **“Meme & Fun Projects”** section of `RULE.md`, with matching skills under `skills/`.

---

## How to work with this repository

### 1. Clone and open in Cursor

```bash
git clone <this-repo-url>
cd vibecoding-templates
cursor .
```

### 2. Use the rules

- **Option A — Single project:** Copy the **Shared Tech & Conventions** plus **one project section** from `RULE.md` into your project’s `.cursor/rules/` (e.g. as `vibecoding-api.mdc`) or use a root `AGENTS.md` / `RULE.md` in the app you’re building.
- **Option B — This repo as reference:** Keep this repo open in a separate window and tell the agent to follow the conventions and project section from `RULE.md` for the idea you chose (e.g. “We’re building the Team Mood API; follow the Team Mood section in RULE.md”).

Rules are applied automatically (if in `.cursor/rules/`) or when you @-mention the rule file in chat.

### 3. Use the skills

Skills live in **`skills/<project-name>/SKILL.md`**. Cursor discovers skills from:

- **Project-level:** `.cursor/skills/` or `.agents/skills/`  
- **User-level:** `~/.cursor/skills/`  

To use a skill for a workshop project:

- **Copy the skill** into your app: e.g. `your-app/.cursor/skills/team-mood-facilitator/` with the same `SKILL.md` content. Cursor will load it for that project.
- **Or reference this repo:** Open this repo in Cursor and type **`/team-mood-facilitator`** (or the skill folder name) in Agent chat when working on the Team Mood API. The agent will use that skill’s guidance.

Skill names match the folder names, e.g. `team-mood-facilitator`, `kanban-domain-assistant`, `micro-journal-prompt-curator`, `habit-domain-coach`, `content-curation-summarization`, `finance-domain-helper`, `event-rsvp-domain`, plus meme/fun skills like `waifu-image-generator`, `wasted-time-tracker`, and `mortality-countdown`.

### 4. Optional: MCP

For database or external services (email, Slack, HTTP, etc.), configure MCP in **`.cursor/mcp.json`** (project) or **`~/.cursor/mcp.json`** (user). `RULE.md` suggests per-project MCP ideas (e.g. DB + email for Team Mood, DB + fetch for Content Curation). See [Cursor MCP docs](#cursor-documentation-and-screenshots) below.

---

## Cursor usage with multiple agents

Cursor gives you different “agents” and surfaces; using them in a consistent way keeps vibecoding clean.

### Chat (Agent) vs Composer

| Surface | Best for |
|--------|----------|
| **Chat (Agent)** | Questions, small edits, “explain this”, “add a test for X”. Uses **User** and **Project rules** and **Skills**. Good for exploring and refining domain logic. |
| **Composer** | Multi-file changes, “scaffold the full API”, “add all endpoints from RULE.md”. Also uses project rules and skills; better for larger, structured tasks. |

**Practical split for workshops:**

- Use **Chat** to design entities, validate API shape with the skill, and iterate on one endpoint or one test file.
- Use **Composer** to generate routes + controllers + services for a whole project section from `RULE.md`, or to apply a skill across many files.

### Rules vs skills

| Mechanism | Use for |
|-----------|--------|
| **Rules** (e.g. `.cursor/rules/*.mdc`, `AGENTS.md`, or `RULE.md`) | Global constraints: folder structure, “no business logic in controllers”, error mapping, where MCP code lives. Applied per chat/composer session (always, by glob, or when @-mentioned). |
| **Skills** (`skills/*/SKILL.md`) | Domain-specific guidance: entity names, validation rules, example endpoints, test scenarios. Invoked when relevant or via `/skill-name`. |

Use **rules** for “how we build” and **skills** for “what this project is”.

### Multiple projects in one workspace

If you have several workshop projects in one repo (monorepo) or one workspace:

- Put **shared** conventions in a single rule (e.g. “use `src/http`, `src/domain`, `src/infrastructure`”).
- Use **one skill per project** and invoke the right skill when working in that project (e.g. `/habit-domain-coach` when in the habit tracker folder).
- Optionally use **path-scoped rules** (globs in `.mdc` frontmatter) so a rule applies only under `apps/team-mood/` or `apps/kanban/`.

### Keeping agents consistent

- **Same rules:** Ensure the project that you’re building has the right rule file (or `RULE.md` content) in scope so both Chat and Composer see it.
- **Same skill:** When starting a task, mention the skill or the project name (“we’re building the Event RSVP API; use the event-rsvp-domain skill”).
- **MCP:** Once configured in `mcp.json`, both Chat and Composer can use MCP tools; no extra per-agent setup.

---

## Cursor documentation and screenshots

Screenshots, UI walkthroughs, and detailed descriptions of Rules, Skills, MCP, and Agent behavior are in the official Cursor docs. Use these links for accurate, up-to-date visuals and steps.

### Rules

- **Rules overview and UI**  
  [https://cursor.com/docs/context/rules](https://cursor.com/docs/context/rules)  
  Covers: Project / User / Team rules, `AGENTS.md`, rule types (Always / Intelligently / File-specific / Manual), creating rules from Settings or `/create-rule`, and best practices. Use this for screenshots of the Rules UI and rule configuration.

### Skills

- **Agent Skills**  
  [https://cursor.com/docs/skills](https://cursor.com/docs/skills)  
  Covers: What skills are, skill directories (`.cursor/skills/`, `.agents/skills/`, user-level), `SKILL.md` format and frontmatter, optional `scripts/` and `references/`, and how to view and invoke skills. Use this for screenshots of the Skills UI and folder layout.

### MCP (Model Context Protocol)

- **Model Context Protocol**  
  [https://cursor.com/docs/context/mcp](https://cursor.com/docs/context/mcp)  
  Covers: What MCP is, `mcp.json` location and format, local vs remote servers, and how tools appear in Cursor. Use this for MCP setup and any MCP-related screenshots.

- **MCP integrations and marketplace**  
  [https://cursor.com/help/customization/mcp](https://cursor.com/help/customization/mcp)  
  Covers: One-click MCP installs and manual configuration. Use this for marketplace and integration screenshots.

### Agent and chat

- **Agent overview**  
  [https://docs.cursor.com/agent/overview](https://docs.cursor.com/agent/overview)  
  Use for high-level Agent/Composer behavior and any overview screenshots.

- **Chat (tabs, history, etc.)**  
  [https://docs.cursor.com/agent/chat/tabs](https://docs.cursor.com/agent/chat/tabs)  
  Use for Chat UI and multi-conversation screenshots.

For the latest screenshots and UI, always refer to these official pages; they are the canonical source.

---

## Quick reference

- **Pick a project** → Read its section in `RULE.md` and the matching skill in `skills/<name>/SKILL.md`.
- **Apply rules** → Copy shared + project block into your app’s `.cursor/rules/` or use `RULE.md` in your app; or keep this repo open and reference it in chat.
- **Apply skills** → Copy the skill folder into your app’s `.cursor/skills/` or `.agents/skills/`, or open this repo and type `/skill-name` in Agent.
- **Screenshots and UI** → See the official Cursor docs linked above.
