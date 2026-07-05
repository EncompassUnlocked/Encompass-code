# Encompass Unlocked — Update Summary (July 2026)

## What changed in these files

**Bug fixes**
- **Fixed broken skill downloads (live-site bug):** every "download the .skill file" link pointed to `docs/skills/downloads/`, which didn't exist — all five download links were 404ing. The `.skill` files are now built and included at `docs/skills/downloads/`. (Note: when you update a skill's SKILL.md in the future, re-zip its folder into this location so the download stays current.)
- Fixed the duplicated "## Notes" heading in the button hard-stop guide.
- Normalized mixed line endings (CRLF → LF) across all docs.

**SEO**
- Rewrote every guide's H1 to match how admins actually search (e.g., "How to Update a Field Value with a Button Click in Encompass Web Forms" instead of "Button: Field update"). Nav labels stay short; page titles now carry the search keywords.
- Added a meta `description` to every guide page (this is the text Google shows under your link).
- Expanded the site description in `mkdocs.yml`.

**Brand connection**
- Homepage now has a "Get More Encompass Unlocked" section linking your book and LinkedIn, with placeholders (marked `TODO`) for the ICE blog/video links and your newsletter signup embed.
- README now has Connect and License sections.

**Legal / trust**
- Added an MIT `LICENSE` — without one, nobody had the legal right to reuse the code, which defeats "community library."
- Added `docs/disclaimer.md` (linked in nav and homepage): as-is warning, test-in-sandbox guidance, and the ICE trademark / non-affiliation notice.
- Added `CONTRIBUTING.md` with sanitization ground rules for community submissions.

**Housekeeping**
- Commented out the empty "YouTube Notes" nav section — un-comment it in `mkdocs.yml` when the channel is live (the episode-1 page is still in the repo, just hidden from nav).
- Added `docs/CNAME.example` for the custom domain (see checklist below).

## Your to-do checklist (things only you can do)

**In these files (search for `TODO`)**
- [ ] Paste the ICE blog interview and video URLs into `docs/index.md`
- [ ] Replace the newsletter placeholder in `docs/index.md` with your Kit/Beehiiv signup embed once it exists

**Custom domain (encompassunlocked.com)**
1. [ ] At your registrar, add a `CNAME` record: `www` → `encompassunlocked.github.io`, and either an `ALIAS`/`ANAME` for the apex domain or four `A` records pointing to GitHub Pages IPs (185.199.108.153, .109.153, .110.153, .111.153)
2. [ ] In the repo: Settings → Pages → Custom domain → enter `encompassunlocked.com`, and check "Enforce HTTPS" once the certificate provisions
3. [ ] Rename `docs/CNAME.example` to `docs/CNAME` and push (this keeps the domain set across deploys)
4. [ ] Update `site_url` in `mkdocs.yml` to `https://encompassunlocked.com/`

**GitHub settings (10 minutes)**
- [ ] Repo → About (gear icon): add topics `encompass`, `mortgage`, `ice-mortgage-technology`, `claude`, `claude-skills`, `ai`, `loan-origination`
- [ ] Repo → Settings → Social preview: upload a 1280×640 branded image (this is the card people see when the link is shared on LinkedIn)
- [ ] Org profile: add an avatar/logo and a profile README for the EncompassUnlocked org

**Search visibility**
- [ ] Set up Google Search Console for the domain and submit the sitemap (`/sitemap.xml` — Material generates it automatically)
- [ ] Link to the site from your LinkedIn Featured section, webinar slides, and book page — inbound links are what get a new site indexed
