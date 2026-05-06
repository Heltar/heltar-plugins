# Heltar Plugins

Marketplace for the Heltar Claude Code plugin. Bundles the [Heltar Skills](https://github.com/Heltar/skills) for one-click install in Claude Code, with a built-in `/heltar-help` command.

## What's inside

```
.
├── .claude-plugin/
│   └── marketplace.json              ← marketplace listing
└── plugins/
    └── heltar-claude-plugin/
        ├── .claude-plugin/
        │   └── plugin.json           ← plugin manifest
        ├── commands/
        │   └── heltar-help.md        ← /heltar-help slash command
        └── skills/                   → ../../../skills/skills (symlink — DRY)
```

## Install in Claude Code

From the marketplace:

```bash
/plugin install Heltar/heltar-plugins
```

Or directly from a local clone:

```bash
git clone https://github.com/Heltar/heltar-plugins.git
claude plugin add ./heltar-plugins
```

## What you get

- All 11 [Heltar skills](./plugins/heltar-claude-plugin/skills/) — authentication, messaging, templates, campaigns, chatbots, webhooks, contacts, calls, groups, code-editor, schedule. Bundled into the plugin at publish time so the install is self-contained.
- `/heltar-help` — a slash command that lists every skill, what it covers, and when to load it.

## Plugin vs skill

- **Skill** = one `SKILL.md` file with structured agent knowledge for one API surface. Standalone.
- **Plugin** = a wrapper that bundles skills + slash commands + manifest, so Claude Code can install it as one unit.

If you only want the knowledge files, install [`Heltar/skills`](https://github.com/Heltar/skills) directly. Use this plugin when you also want the `/heltar-help` shortcut.

This repo is a public mirror — source of truth is the private Heltar monorepo and is synced here on every change.

## License

Apache-2.0
