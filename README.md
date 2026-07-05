# Encompass Unlocked

A community library of ICE Encompass (Ellie Mae) code and Claude AI skills — business rules, custom calculations, web form logic, and automation examples. Built by an Encompass admin, for Encompass admins.

**📖 Browse the guides:** https://encompassunlocked.github.io/Encompass-code/

## Claude Skills

This repo doubles as a **Claude Code plugin marketplace**. Install every Encompass skill in one step:

```
/plugin marketplace add EncompassUnlocked/Encompass-code
/plugin install encompass-unlocked@encompass-unlocked
```

Or install individual skills in Claude.ai / Cowork by downloading the `.skill` files from the [Skills page](https://encompassunlocked.github.io/Encompass-code/skills/).

| Skill | What it does |
|---|---|
| `encompass-field-triggers` | Advanced Coding rules: field triggers, validation, milestone conditions (VB.NET) |
| `encompass-loan-custom-fields` | Loan Custom Field calculations: IIF logic, date math, safe operators |
| `encompass-scripting-framework` | Secure Scripting Framework: custom forms, tools, plugins (JavaScript) |
| `empkg` | Create and repair `.empkg` packages, manifest.xml, `.emfrm` forms |
| `product-self-knowledge` | Current Claude model/product reference |

## Repo layout

- `skills/` — Claude skill source (one folder per skill, each with a `SKILL.md`)
- `docs/` — MkDocs source for the website
- `.claude-plugin/` — plugin + marketplace manifests
- The site auto-deploys to GitHub Pages on every push (see `.github/workflows/deploy.yml`)

## License & disclaimer

All code is released under the [MIT License](LICENSE) — free to use, adapt, and share.
Everything is provided as-is: **always test in a sandbox before touching production**, and read the
[full disclaimer](https://encompassunlocked.github.io/Encompass-code/disclaimer/).
Encompass® is a registered trademark of ICE Mortgage Technology, Inc.; this project is an
independent community resource and is not affiliated with or endorsed by ICE.

## Connect

- 📘 Book: [*Micro Vibe Coding for Mortgage Professionals*](https://www.amazon.com/Micro-Vibe-Coding-Mortgage-Professionals-ebook/dp/B0GMMBL95B)
- 💼 LinkedIn: [Beth Bastian](https://www.linkedin.com/in/bethbastian/)

Contributions welcome — see [CONTRIBUTING.md](CONTRIBUTING.md).
