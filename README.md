# license-to-code

A personal collection of Claude Code plugins.

## Add this marketplace

```
/plugin marketplace add theelims/license-to-code
```

(Or, if cloning locally:)

```
/plugin marketplace add ./path/to/this/repo
```

## Browse plugins

```
/plugin marketplace list license-to-code
```

## Available plugins

### `uat-agents`

User Acceptance Testing workflow for web apps via the Chrome extension. A planning skill authors and auto-reviews test plans; a tester skill executes them, logs structured bug reports, and accumulates UX observations across sessions.

**Install:**
```
/plugin install uat-agents@license-to-code
```

See [`plugins/uat-agents/README.md`](./plugins/uat-agents/README.md) for full documentation.

---

## Repo layout

```
.
├── .claude-plugin/
│   └── marketplace.json            # marketplace manifest
├── plugins/
│   └── uat-agents/                 # one plugin per subdirectory
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── README.md
│       ├── skills/
│       ├── agents/
│       └── templates/
├── README.md                        # this file
└── LICENSE
```

To add a new plugin: create `plugins/<plugin-name>/` with its own `.claude-plugin/plugin.json`, then add an entry to `.claude-plugin/marketplace.json`.

## License

MIT (see `LICENSE`)
