# BCQF Website: Deployment Guide

How the BCQF one-pager gets from your laptop to a live URL, and how every later edit ships. The site's content is in `content_markdown.md` and its design is in `design_markdown.md`.

**The pipeline:**

```
Claude Code (your laptop) ──git push──▶ GitHub repo ──auto-deploy──▶ Vercel ──▶ bcqf-website.vercel.app
        │                                    ▲
        └────── GitHub MCP server ───────────┘
                (pull requests, issues, repo info)
```

Two connections to GitHub, each with one job:

- **git** pushes the actual files, including the PNG logos.
- **The GitHub MCP server** lets Claude Code work with GitHub itself: open and merge pull requests, read the repo, file issues. It can't replace git for pushing, because its file-push tool only accepts text content, not images.

---

## Part 0: One-time installs

You need these on your laptop. Check each with the command in the last column.

| Tool | Why | Check |
|---|---|---|
| **Node.js 24 LTS** (22.12+ also works) | Astro needs Node 22.12 or newer; odd versions like 23 aren't supported | `node -v` |
| **Git** | Version control | `git --version` |
| **GitHub CLI (`gh`)** | Signs git into GitHub and creates the repo in one command | `gh --version` |
| **Claude Code** | Builds and edits the site | `claude --version` |

Also make sure you have:

- A GitHub account (yours: `jett-takazawa`).
- A Vercel account. Sign up at vercel.com with **Continue with GitHub**, so the two are linked from the start.

Set your git identity once, using the email on your GitHub account:

```bash
git config --global user.name "Jett Takazawa"
git config --global user.email "the-email-on-your-github-account"
```

These commands assume macOS or Linux. On Windows, run them in WSL or Git Bash.

---

## Part 1: Sign git into GitHub

```bash
gh auth login
```

Answer the prompts:

1. **Where do you use GitHub?** GitHub.com
2. **Preferred protocol for Git operations?** HTTPS
3. **Authenticate Git with your GitHub credentials?** Yes
4. **How would you like to authenticate?** Login with a web browser

After this, `git push` works without asking for a password, and Claude Code can push when you approve it.

---

## Part 2: Set up the project

Make a folder for the site and put the three spec files and the logos in it:

```
bcqf-website/
├── docs/
│   ├── content_markdown.md
│   ├── design_markdown.md
│   └── deployment_markdown.md
└── public/
    └── logos/
        ├── bcqf-black.png   ← Elegant_BCQF_Finance_Logo.png
        ├── bcqf-white.png   ← Elegant_BCQF_Burgundy_and_Gold_Logo.png
        └── bcqf-full.png    ← Boston_College_Quantitative_Finance_Logo.png
```

Use lowercase, hyphenated file names. Your Mac ignores capitalization in file names, but Vercel's build servers don't, so a link to `BCQF-White.png` that works locally will 404 in production.

Then open Claude Code in that folder (`cd bcqf-website && claude`) and paste this prompt:

> Scaffold an Astro site in this folder (minimal template, TypeScript, static output, no Vercel adapter) without overwriting `docs/` or `public/logos/`. Build the one-page site from `docs/content_markdown.md` and `docs/design_markdown.md`: keep the page copy in a content file under `src/content/` rather than hardcoded in components, and put the design tokens in a global stylesheet. Crop the logos as described in the design spec. Add `"engines": { "node": "24.x" }` to package.json, set `site` in astro.config to `https://bcqf-website.vercel.app` for now, and write a `CLAUDE.md` using the template in `docs/deployment_markdown.md`. Then run `npm run build` and fix anything that fails.

Check it locally:

```bash
npm run dev      # opens at http://localhost:4321
npm run build    # must finish with no errors before you push
```

### What makes Vercel read the project cleanly

Vercel deploys a static Astro site with zero configuration. Keep it that way:

- **Static output, no adapter.** Only add `@astrojs/vercel` later if you want Vercel Web Analytics or image optimization.
- **No `vercel.json`.** It isn't needed. Vercel specifically warns against using `vercel.json` rewrites with Astro.
- **Commit `package-lock.json`.** Vercel installs exactly the versions you tested with.
- **Pin Node with `engines`.** Vercel reads `"engines": { "node": "24.x" }` from package.json and uses it over the dashboard setting, so your laptop and the build server match.
- **Keep `.gitignore` covering:** `node_modules/`, `dist/`, `.astro/`, `.vercel/`, `.env`, `.DS_Store`. The Astro template includes most of these; check that `.env` is there.

### CLAUDE.md template

`CLAUDE.md` sits in the project root and Claude Code reads it at the start of every session. It keeps edits consistent.

```markdown
# BCQF website

One-page site for the Boston College Quantitative Finance Club. Astro, static output, deployed on Vercel from the `main` branch of github.com/jett-takazawa/bcqf-website.

## Source of truth
- Copy: src/content/ (originally from docs/content_markdown.md). Never hardcode copy in components.
- Design: docs/design_markdown.md. Colors and type come from the tokens in the global stylesheet; never add new hex values inline.
- Logos: public/logos/. Lowercase, hyphenated file names only.

## Workflow
- Make changes on a branch, not on main: `git switch -c <short-description>`.
- Run `npm run build` before every commit. Don't push a failing build.
- Small commits with clear messages ("Update member list", not "changes").
- Push the branch, then open a pull request with the GitHub MCP server. Vercel posts a preview link on the PR.
- Merging to main deploys to production. Only merge when asked.

## Never
- Commit .env files, tokens, or anything from ~/.claude.json.
- Add a vercel.json or the Vercel adapter unless asked.
- Use the Boston College seal, wordmark, or eagle.
```

---

## Part 3: Create the GitHub repo and push

From the project folder (or ask Claude Code to run it):

```bash
git init -b main
git add .
git commit -m "Initial BCQF site"
gh repo create bcqf-website --public --source=. --remote=origin --push
```

That last command creates `github.com/jett-takazawa/bcqf-website`, connects your folder to it, and pushes.

**Public or private?** Either works on Vercel's free plan for a repo on your personal account. Public is fine here: everything in it is already on the live site, and it doubles as a portfolio piece. Just never commit secrets.

**Keep the repo on your personal account for now, not a GitHub organization.** On Vercel's free Hobby plan, commits pushed to a repo owned by a GitHub organization only deploy if they're authored by the Vercel account owner, and private organization repos can't deploy at all. On a personal repo, collaborators' commits deploy normally. See Part 7 for handing the site off later.

---

## Part 4: Connect Claude Code to the GitHub MCP server

This lets Claude Code open pull requests, merge them, read the repo and manage issues for you. GitHub hosts the server at `https://api.githubcopilot.com/mcp/`. It's available to all GitHub users; no Copilot subscription needed.

### Step 1: Create a fine-grained personal access token

1. Go to **github.com/settings/personal-access-tokens/new**.
2. **Token name:** `claude-code-bcqf`
3. **Expiration:** 90 days. Put a reminder in your calendar to rotate it.
4. **Resource owner:** `jett-takazawa`
5. **Repository access:** Only select repositories → `bcqf-website`
6. **Repository permissions:**
   | Permission | Access |
   |---|---|
   | Contents | Read and write |
   | Pull requests | Read and write |
   | Issues | Read and write |
   | Metadata | Read-only (added automatically) |
7. **Generate token** and copy it. GitHub shows it only once.

Scoping the token to one repo means Claude Code can't touch your other projects through it.

### Step 2: Add the server to Claude Code

Run this in your terminal (not inside a Claude Code session). The first line reads the token without printing it or saving it to your shell history.

```bash
read -rs GITHUB_PAT        # paste the token, press Enter (nothing will appear)
claude mcp add --transport http --scope user github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer $GITHUB_PAT"
unset GITHUB_PAT
```

- `--scope user` makes the server available in every project and stores the token in `~/.claude.json` on your laptop, outside the repo.
- **Don't use `--scope project`.** That writes the config, token included, into `.mcp.json` in the project folder, where it would get committed and pushed to GitHub.

### Step 3: Verify

```bash
claude mcp list
```

You should see `github` with `✔ Connected`. Inside a Claude Code session, `/mcp` shows the same status. Then try:

> Using the GitHub MCP server, show me the latest commit on main in jett-takazawa/bcqf-website.

Claude Code asks your permission the first time it uses each GitHub tool. Approve the ones you're comfortable with.

### When the token expires

Create a new token with the same settings, then:

```bash
claude mcp remove github
read -rs GITHUB_PAT
claude mcp add --transport http --scope user github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer $GITHUB_PAT"
unset GITHUB_PAT
```

---

## Part 5: Deploy on Vercel

1. Go to **vercel.com/new**.
2. Under **Import Git Repository**, connect GitHub if prompted. When GitHub asks which repositories the Vercel app can access, choose **Only select repositories** → `bcqf-website`.
3. Click **Import** next to `bcqf-website`.
4. On the configure screen:
   | Setting | Value |
   |---|---|
   | Project name | `bcqf-website` |
   | Framework Preset | **Astro** (detected automatically) |
   | Root Directory | `./` |
   | Build and Output Settings | Leave the defaults (build: `npm run build`, output: `dist`) |
   | Environment Variables | None |
5. Click **Deploy**.

In about a minute the site is live at `bcqf-website.vercel.app` (Vercel adds a suffix if that name is taken). If the URL differs, update `site` in `astro.config` to match.

From now on:

- **Every push to `main`** deploys to production automatically.
- **Every other branch and every pull request** gets its own preview URL. Vercel's bot comments the link on the PR.

### Optional: a custom domain

If the club buys a domain (for example `bcqf.org`):

1. Vercel project → **Settings** → **Domains** → **Add Domain**. Add both the bare domain and the `www` version when Vercel offers.
2. At your domain registrar, add the DNS records Vercel shows on that page: an **A** record for the bare domain and a **CNAME** for `www`. Use the exact values on your Domains page; the CNAME target is unique to your project.
3. Wait for the Domains page to show the domain as valid (minutes to a few hours).
4. Update `site` in `astro.config` to the new domain and push.

### Plan note

Vercel's free Hobby plan is for non-commercial use. A club site with no payments, ads or paid placements fits. If sponsors ever pay for placement on the site, ask Vercel support whether that changes things.

---

## Part 6: Everyday editing workflow

Once set up, every change follows the same loop. You can drive the whole thing from Claude Code:

```
branch → edit → build → push → pull request → check preview → merge → live
```

Example prompt:

> Add a new member, [Name] ([Major]), to the members list. Make the change on a new branch, run the build, push it, and open a pull request with the GitHub MCP server. Give me the PR link.

Then:

1. Open the PR. Vercel's bot comments a preview URL within a minute or two.
2. Check the preview on your laptop and your phone.
3. Merge, or ask Claude Code: *"Merge PR #3 with the GitHub MCP server."*
4. Vercel deploys `main` to production automatically.

For a one-word typo you can commit straight to `main`, but the branch-and-PR loop is what keeps things safe once other board members start editing.

### Undoing a bad change

- **Preferred:** ask Claude Code to `git revert` the bad commit and push it. The site redeploys with the fix and auto-deploys keep working.
- **Emergency:** in Vercel, the production deployment tile has **Instant Rollback**. On the free plan it only goes back one deployment, and it pauses auto-deploys until you click **Undo Rollback**, so use it to stop the bleeding, then fix forward with a revert.

### Adding other board members

1. GitHub → `bcqf-website` → **Settings** → **Collaborators** → **Add people**.
2. Each editor runs Parts 0, 1 and 4 on their own laptop with their own token, then `git clone https://github.com/jett-takazawa/bcqf-website`.

Because the repo is on a personal account, their pushes deploy on the free plan.

---

## Part 7: Handing the site off

Before you graduate, move ownership to whoever runs the club next so the site doesn't depend on your accounts:

1. **GitHub:** repo **Settings** → **Transfer ownership** to the next maintainer's account. GitHub redirects the old URL.
2. **Vercel:** project **Settings** → transfer the project to their Vercel account, then reconnect the Git repository under the project's **Git** settings if the link breaks.
3. **Domain:** if the club bought one, make sure it's registered to the club email (`bcquantitativefinance@gmail.com`), not a personal account.
4. **Tokens:** the new owner creates their own token (Part 4). Delete yours at github.com/settings/personal-access-tokens.

If the club later wants a shared GitHub organization with several people deploying, that requires Vercel's paid Pro plan (see the note in Part 3).

---

## Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| `claude mcp list` shows `✘ Failed to connect` or an auth error | Token expired, mistyped, or missing a permission | Make a new token (Part 4, Step 1) and re-add the server |
| Claude says it has no GitHub tools | Server added in a different scope or session started before adding | Run `claude mcp list`; restart Claude Code |
| `git push` asks for a password or is rejected | git isn't signed in | `gh auth login`, or `gh auth setup-git` |
| Vercel build fails, works locally | Node version mismatch or missing lockfile | Check `engines` in package.json and that `package-lock.json` is committed; read the build log in Vercel → Deployments |
| Logo shows locally, 404 on Vercel | File name capitalization | Rename to lowercase and match the path exactly |
| Push to `main` didn't go live | Project is in a rolled-back state | Vercel → project overview → **Undo Rollback** |
| No preview link on a PR | Vercel GitHub app lacks access to the repo | GitHub → Settings → Applications → Vercel → Configure → add `bcqf-website` |

---

## Security checklist

- [ ] Token scoped to `bcqf-website` only, with an expiration date
- [ ] MCP server added with `--scope user`, never `--scope project`
- [ ] No `.mcp.json` containing a token in the repo
- [ ] `.env` listed in `.gitignore`
- [ ] Vercel GitHub app limited to `bcqf-website`
- [ ] Calendar reminder to rotate the token before it expires

---

## Sources

- [Claude Code: Connect to tools via MCP](https://code.claude.com/docs/en/mcp)
- [GitHub MCP server: Install in Claude Code](https://github.com/github/github-mcp-server/blob/main/docs/installation-guides/install-claude.md)
- [GitHub MCP server repository](https://github.com/github/github-mcp-server)
- [GitHub Docs: Using the GitHub MCP server](https://docs.github.com/en/copilot/how-tos/context/model-context-protocol/using-the-github-mcp-server)
- [Astro: Install and set up](https://docs.astro.build/en/install-and-setup/)
- [Vercel: Astro on Vercel](https://vercel.com/docs/frameworks/frontend/astro)
- [Vercel: Deploying Git repositories](https://vercel.com/docs/git)
- [Vercel: Supported Node.js versions](https://vercel.com/docs/functions/runtimes/node-js/node-js-versions)
- [Vercel: Adding a custom domain](https://vercel.com/docs/domains/working-with-domains/add-a-domain)
- [Vercel: Instant Rollback](https://vercel.com/docs/instant-rollback)
- [Vercel: Fair use guidelines](https://vercel.com/docs/limits/fair-use-guidelines)
- [Vercel Community: Hobby accounts and GitHub organizations](https://community.vercel.com/t/why-cant-hobby-accounts-deploy-from-organizations/10015)
