# Control Room

Multi-agent coordination for your Mac: Claude Code and Codex agents, each in its own worktree and thread.

**[Get Control Room →](https://nightshiftfactory.com/controlroom/)** Free for macOS (Apple silicon): sign in with GitHub to download.

## Community plugins

[`plugins.json`](plugins.json) lists the community plugins Control Room can install, in **Settings › Community plugins**.

| Plugin | What it does |
|---|---|
| [PR Lens](https://github.com/Tenjin-Tech/control-room-pr-lens) | Agents draw each code change they make as one picture in the thread. |

A plugin installs off. It runs only after you turn it on, and any change to its files turns it off until you approve it again.

### Add yours

1. Start from [control-room-sample-plugin](https://github.com/Tenjin-Tech/control-room-sample-plugin). The plugin API is in [docs/plugins.md](docs/plugins.md).
2. Publish a GitHub release whose tag is the manifest's `version` (for example `0.1.0`), with the plugin's files attached. Control Room installs the files of that release.
3. Open a pull request that adds an entry to `plugins.json`: `id` (the manifest's), `name`, `author`, `description`, `repo` (`owner/name`).

Your README must say what the plugin sends over the network and why, and whether it needs an account or payment.

## License

MIT
