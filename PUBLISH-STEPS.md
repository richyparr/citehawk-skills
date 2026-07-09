# Publish steps — CiteHawk skill repo

Layout confirmed against the `skills` CLI (vercel-labs/skills, the `skills`
npm package): `npx skills add <owner>/<repo>` discovers skills in a top-level
`skills/` container directory as `skills/<name>/SKILL.md` (flat layout, walked
one level deep; SKILL.md needs `name` + `description` YAML frontmatter — both
present). No extra manifest is required.

Once owner/name is chosen (`<owner>/<repo>`, e.g. `richyparr/citehawk-skills`):

```bash
cd /Users/richardparr/.claude/jobs/190c3014/tmp/submissions-staging/skill-repo
# 1. Replace the placeholder in README.md:
sed -i '' 's|<owner>/<repo>|OWNER/REPO|g' README.md   # or edit by hand

# 2. Init + first commit
git init -b main
git add -A
git commit -m "feat: citehawk-geo Claude Code skill — agentic GEO via the CiteHawk MCP"

# 3. Create the PUBLIC repo and push
gh repo create <owner>/<repo> --public --source . --push \
  --description "Claude Code skill: close AI-visibility (GEO) gaps in your own repo using live CiteHawk data"

# 4. Verify install works
npx skills add <owner>/<repo> --list
```

Then propagate the final URL:
- `../awesome-mcp-servers/entry.md` — replace `<SKILL_REPO_URL>`.
- Optionally add `repository` to `../mcp-registry/server.json` and re-publish.
- Update `/Users/richardparr/geo/skills/README.md` install line
  (`npx skills add citehawk/skills` placeholder) — separate PR in the main repo.
