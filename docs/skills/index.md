# Skills

A **skill** is a folder containing a `SKILL.md` file — expert instructions Claude loads automatically whenever your request matches the skill's description. Give Claude the Encompass skills below and it already knows the syntax rules, safe operators, and pitfalls before you type your first question.

All skills on this site are free. Each one comes as a `.skill` file (a zip of the skill folder).

---

## Installing in Claude.ai / Claude Desktop

1. Download the `.skill` file from the skill's page (links below)
2. In Claude, go to **Settings → Capabilities → Skills**
3. Click **Upload skill** and select the file
4. That's it — the skill triggers automatically when you ask a matching question

## Installing in Claude Code

Install everything at once from this site's GitHub repo:

```
/plugin marketplace add EncompassUnlocked/Encompass-code
/plugin install encompass-unlocked@encompass-unlocked
```

Or manually: unzip any `.skill` file into `~/.claude/skills/` (personal) or `.claude/skills/` inside your project.

---

## Available skills

| Skill | Use it for |
|---|---|
| [Field Triggers (Advanced Coding)](encompass-field-triggers.md) | VB.NET business rules: triggers, validation, milestone conditions |
| [Loan Custom Field Code](encompass-loan-custom-fields.md) | CX. field calculations, IIF logic, date math |
| [Scripting Framework](encompass-scripting-framework.md) | JavaScript for custom forms, tools, and plugins |
| [EMPKG Package Editor](empkg.md) | Building and repairing .empkg / .emfrm packages |
| [Product Self-Knowledge](product-self-knowledge.md) | Up-to-date Claude model and product reference |

---

## Skills vs. CLAUDE.md vs. Projects

These three often get mixed up:

- **Skills** — reusable expertise that loads only when relevant. Best for "how to write Encompass code correctly." Works in Claude.ai, Claude Code, and Cowork.
- **CLAUDE.md** — a file in a project folder that Claude Code reads every session. Best for project-specific context ("this repo uses async/await everywhere").
- **Projects (claude.ai)** — persistent instructions for a workspace of conversations. Best for personal preferences ("I'm an admin, not a developer — explain plainly").

Use skills for the Encompass knowledge, and CLAUDE.md/Projects for *your* environment and preferences. They stack.
