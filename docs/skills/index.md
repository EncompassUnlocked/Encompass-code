# Skills

One of the most powerful things you can do with Claude AI is give it a 
**skill** — a set of instructions that tells Claude exactly how to behave for 
a specific type of task. Think of it like training Claude on your workflow so 
you don't have to re-explain it every time.

This section covers how to use skills in both **Claude Code** (the developer 
tool) and **Claude.ai Chat** (the browser-based version most of us use day to 
day).

---

## Skills in Claude Code

Claude Code is a CLI tool that runs Claude directly in your terminal or IDE. 
It supports custom skills through a file called `CLAUDE.md` stored in your 
project folder.

**How it works:**

When you open a project in Claude Code, it automatically reads your `CLAUDE.md` 
file and follows whatever instructions are in it — every single session, without 
you having to repeat yourself.

**How to use it:**

1. In your project folder, create a file called `CLAUDE.md`
2. Write your instructions in plain English — describe how Claude should behave, 
   what context it needs, what to avoid, preferred formats, etc.
3. Open Claude Code in that folder — it will read and apply the instructions 
   automatically

**Example `CLAUDE.md` for an Encompass project:**

```markdown
# Project Context
This project contains Encompass web form scripts written in JavaScript.

# How to write code here
- Always use async/await — never .then() chains
- Always retrieve the loan object with: const loan = await elli.script.getObject("loan")
- Field IDs that start with CX. are custom fields
- Add a plain-English comment above every code block explaining what it does

# Format
- Job aids follow this order: intro, how it works, the code, how to use it, notes
```

Now every time you open Claude Code in that folder, it already knows the rules.

---

## Skills in Claude.ai Chat

If you're using Claude through the browser at claude.ai, you can use 
**Projects** to set up persistent instructions that apply to every conversation 
in that project.

**How it works:**

Claude.ai Projects let you save a set of custom instructions that Claude 
remembers across all conversations inside that project. You don't need to 
re-explain your context every time you start a new chat.

**How to use it:**

1. Go to [claude.ai](https://claude.ai) and click **Projects** in the sidebar
2. Create a new project and give it a name (e.g. "Encompass Customizations")
3. Click into the project and find the **Project Instructions** section
4. Write your instructions in plain English — tell Claude who you are, what 
   you're building, how you like responses formatted, what to avoid, etc.
5. Start a new conversation inside the project — Claude will apply your 
   instructions automatically every time

**Example project instructions for Encompass work:**

```
I am an Encompass admin with 15+ years of experience. I am not a developer.

When I ask for code, write it for Encompass web forms using JavaScript and the 
elli.script API. Always use async/await. Always include plain-English comments 
in the code. Always give me a step-by-step explanation of how to use it.

Keep responses clear and practical. Avoid jargon I wouldn't know as an admin.
```

---

## Why This Matters

Without skills or project instructions, you start every Claude conversation 
from scratch. Claude doesn't know your environment, your field IDs, your 
preferences, or your skill level.

With a well-written skill or project instruction set, Claude already knows all 
of that before you type your first question. The result is faster, more 
accurate answers that fit your workflow right out of the box.

**The goal isn't to become a developer — it's to become a really effective 
Claude user. Skills get you there.**
