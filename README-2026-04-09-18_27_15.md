# 📚 Topic to Notes — Auto Research & Save to Notion

> Drop a topic in a Google Sheet. Get fully researched, beautifully formatted notes in Notion. Automatically.

Ever sat down to study a new topic and spent more time *finding* good resources than actually learning? You open ten tabs, skim through articles, copy paste bits into a doc, and somehow your "notes" end up being a mess of half written bullet points. Yeah, same.

That's exactly why this workflow exists. It takes a topic you want to learn, researches it across the internet, writes detailed and well structured notes (with examples and analogies), and saves everything as a clean Notion page. All you have to do is type a topic into a Google Sheet. That's it.

---

## 🏗️ Architecture Overview

```
┌─────────────────┐
│  Google Sheet    │ ◄── You add topics here
│  (Input Source)  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Filter Engine   │ ◄── Skips topics already marked "Done"
│  (Code Node)     │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│         AI Research Agent               │
│  ┌───────────┐    ┌──────────────────┐  │
│  │  Internet  │───►│  Note Generation │  │
│  │  Search    │    │  (GPT-4.1)       │  │
│  └───────────┘    └──────────────────┘  │
│  Runs in parallel for each topic        │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────┐
│  Notion API      │ ◄── Creates a page per topic
│  (MCP Connector) │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Google Sheet    │ ◄── Marks topic as "Done"
│  (Status Update) │
└─────────────────┘
```

---

## 🔄 Workflow Pipeline

```
Manual Trigger
     │
     ▼
Read Google Sheet ──► Filter New Topics ──► Research & Write Notes (AI Agent)
                                                      │
                                                      ▼
                                Mark as Done ◄── Clean Data ◄── Save to Notion
```

| Step | Node Type | Run Mode | What It Does |
|------|-----------|----------|--------------|
| 1 | Manual Trigger | — | Starts the workflow |
| 2 | Google Sheets (Read) | Input | Reads all rows from the sheet |
| 3 | Code | Input | Filters out rows where Status = "Done" |
| 4 | AI Agent + Internet Search | Item (parallel) | Researches each topic and writes notes |
| 5 | AI Agent + Notion Tool | Item (parallel) | Creates a Notion page per topic |
| 6 | Code | Item | Extracts topic name and Notion URL |
| 7 | Google Sheets (Upsert) | Item | Marks topic as "Done" in the sheet |

---

## ⚙️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Workflow Engine | [Needle Workflows](https://needle.app) |
| AI Model | GPT 4.1 |
| Research Tool | Internet Search (built into Needle) |
| Note Storage | Notion (via MCP Connector) |
| Input/Tracking | Google Sheets (via Pipedream Connector) |
| Processing | Parallel item mode execution |

---

## 📋 Prerequisites

1. **Google Account** with access to Google Sheets
2. **Notion Account** connected via the Notion MCP connector in Needle
3. **A Google Sheet** with the following structure:

| Column A | Column B | Column C |
|----------|----------|----------|
| **Topic** | **Concepts to cover in that topic** | **Status** |
| React Hooks | useState, useEffect, custom hooks with examples | *(leave blank)* |
| Docker Basics | containers, images, volumes, docker compose | *(leave blank)* |
| REST APIs | HTTP methods, status codes, authentication, CRUD | *(leave blank)* |

> Leave the **Status** column empty for new topics. The workflow fills it in with "Done" after processing.

---

## 🧠 How It Works (Detailed)

### Step 1 — Manual Trigger
Kicks off the workflow whenever you're ready. Just hit "Test" or run it manually. You could also swap this out for a scheduled trigger if you want it to run daily or weekly on its own.

### Step 2 — Read Google Sheet
Pulls all the rows from your sheet, including the topic name, the concepts you want covered, and the current status. This is your master list of everything you want to learn.

### Step 3 — Filter New Topics
A code node that looks at the Status column and filters out any row already marked "Done." Only fresh topics move forward. You can keep adding new rows to the same sheet and rerun the workflow without worrying about duplicates.

```javascript
return input.rows.filter(row => !row.Status || row.Status.trim() === '');
```

### Step 4 — Research & Write Notes (AI Agent)
This is where the magic happens. For each topic, an AI agent:

1. **Searches the internet** to find detailed articles, tutorials, and examples
2. **Writes comprehensive notes** covering all the concepts you listed

Think of it like having a tutor who reads everything online and hands you a perfectly organized summary with real world analogies, code examples (if relevant), and clear explanations.

**Key details:**
| Property | Value |
|----------|-------|
| Model | GPT 4.1 |
| Temperature | 0.7 (creative but accurate) |
| Run Mode | Item (parallel processing) |
| Tools | Internet Search |
| Output | Structured JSON (title, content, originalTopic) |

### Step 5 — Save to Notion
Takes the notes generated in the previous step and creates a brand new Notion page for each topic. The page title matches your topic, and the full content is saved right there in Notion, formatted and ready to read. No more copying and pasting between apps.

### Step 6 — Clean Data
A small utility step that extracts the topic name and Notion URL so the next step knows exactly which row to update in your sheet.

### Step 7 — Mark as Done
Goes back to your Google Sheet, finds the row for the topic that was just processed, and updates the Status column to "Done." This closes the loop so the topic won't be picked up again on future runs.

---

## 📤 Output

For every topic in your Google Sheet, you get:

1. ✅ A **fully written Notion page** with detailed notes, examples, and explanations
2. ✅ Your **Google Sheet updated** with Status = "Done" for processed topics
3. ✅ **Parallel processing** so multiple topics are handled at the same time

---

## 💡 Tips & Best Practices

1. **Be specific with concepts.** Instead of just writing "React," try something like "React hooks, useEffect lifecycle, custom hooks with examples." The more detail you give, the better your notes will be.

2. **Keep adding rows anytime.** The workflow only picks up rows where the Status column is empty, so your sheet is a living document.

3. **Automate it fully.** Swap the Manual Trigger for a Scheduled Trigger (e.g., every morning at 8 AM) and you'll have fresh notes waiting for you without lifting a finger.

4. **Notes stay current.** The AI uses live internet search, so your notes include up to date information and resources.

---

## 🚀 Getting Started

1. Clone this workflow from the [Needle Templates Directory](https://needle.app)
2. Connect your **Google Sheets** connector (Pipedream)
3. Connect your **Notion** connector (MCP)
4. Point the Read node to your Google Sheet
5. Hit **Test** and watch the magic happen ✨

---

## 📝 Example

**Input (Google Sheet):**

| Topic | Concepts to cover | Status |
|-------|-------------------|--------|
| Python Decorators | function decorators, class decorators, real world use cases, @property | |

**Output (Notion Page):**
A fully formatted page titled "Python Decorators" with sections covering each concept, code snippets, analogies like "think of decorators as gift wrapping for your functions," and links to further reading.

**Updated Google Sheet:**

| Topic | Concepts to cover | Status |
|-------|-------------------|--------|
| Python Decorators | function decorators, class decorators, real world use cases, @property | Done |

---

## 👩‍💻 Built By

**Esha Agarwal** — built this because writing notes from scratch was taking forever and the results were never consistent. Now it's just: add topic → run → learn from clean notes.

Built with ❤️ on [Needle Workflows](https://needle.app)
