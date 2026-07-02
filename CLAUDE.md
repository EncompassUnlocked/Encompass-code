# Encompass Unlocked — repo guide

This repo contains (1) Claude skills for ICE Encompass admin work and (2) an MkDocs Material website published to GitHub Pages.

## Layout
- `skills/<skill-name>/SKILL.md` — skill source of truth. Folder names are lowercase-kebab-case and MUST match the `name:` in the SKILL.md frontmatter.
- `docs/` — website pages (Markdown). `docs/skills/*.md` are short landing pages per skill; they link to downloads, they do NOT duplicate skill content.
- `mkdocs.yml` — site nav. Add every new page here.
- `.claude-plugin/plugin.json` + `marketplace.json` — makes this repo installable via `/plugin marketplace add EncompassUnlocked/Encompass-code`.
- `.github/workflows/deploy.yml` — on push: zips each skill into `docs/skills/downloads/*.skill`, then deploys the site. Never commit `site/` or `docs/skills/downloads/`.

## Conventions
- Encompass web code: JavaScript, always async/await (never .then chains), loan object via `const loan = await elli.script.getObject("loan")`, plain-English comment above every block.
- Advanced Coding rules: VB.NET, field refs `[19]`, numeric `[#1109]`, date `[@field]`, custom fields `CX.*`.
- Never invent Encompass field IDs — ask the user or mark as placeholder.
- Docs pages follow: what it does → the code → how to use it → notes.

## Adding a new skill
1. Create `skills/<kebab-name>/SKILL.md` with `name:` (matching folder) and a `description:` full of trigger words.
2. Keep SKILL.md under ~500 lines; put long references in extra files in the same folder.
3. Add a landing page `docs/skills/<kebab-name>.md` and a nav entry in `mkdocs.yml`.
