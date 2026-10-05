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
