# Contributing

This repo is a **public mirror**. The source of truth lives in the private Heltar monorepo (`heltarchat`) and is synced here on every change. To change anything in this repo, send the change to the monorepo:

- **Editing skill content** (agent instructions, examples, gotchas) → edit the matching `SKILL.md` in `public/skills/skills/heltar-*/SKILL.md` in the monorepo. The plugin picks up changes on the next sync (its `skills/` is a symlink to `../../../skills/skills`).
- **Editing the API spec** (request/response shapes, fields, status values) → edit the source doc in `public/docs/api-reference/` in the monorepo. The skill's `references/api-reference.md` symlink picks it up automatically.
- **Adding a slash command** → add a markdown file under `public/heltar-plugins/plugins/heltar-claude-plugin/commands/` in the monorepo. The filename (without extension) becomes the command, e.g. `heltar-foo.md` → `/heltar-foo`.
- **Adding a new skill** → create the skill in `public/skills/skills/heltar-<name>/` in the monorepo. The plugin picks it up via the symlink — no plugin-side change needed.

PRs should be opened against the monorepo, not this mirror. Direct PRs here will be closed.

## Validation

Before opening a PR, run from the bundled-repo root:

```bash
./skills/scripts/sync-check.sh
```

This validates:

- Every skill has a `SKILL.md` with frontmatter, and `name:` matches the folder slug.
- Every `references/api-reference.md` symlink resolves into `../../docs/`.
- Every `uses:` references a real skill folder.
- The plugin's `skills/` symlink resolves to `../../../skills/skills`.
- `marketplace.json` and `plugin.json` parse cleanly.

## Versioning

Bump the version in:

- `.claude-plugin/marketplace.json` — `plugins[0].version`
- `plugins/heltar-claude-plugin/.claude-plugin/plugin.json` — `version`
- `../skills/CHANGELOG.md` — note user-visible changes

## License

Contributions are licensed under Apache-2.0, matching the repo.
