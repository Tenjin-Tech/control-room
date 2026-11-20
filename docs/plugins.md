# Writing a Control Room plugin

A plugin is a folder named after its `id`, with two files:

- `manifest.json`: `id` (lowercase letters, digits and `-`, the same as the folder's name), `name`, `version` (`x.y.z`), `access: "full"`, and an optional `description`.
- `main.js`: a function that receives the plugin API.

```js
module.exports = (api) => {
  api.prompt('session', 'Always answer in French.')
  api.on('thread-state', (thread, state) => console.log(thread, state, api.workingAgents()))
  api.onUnload(() => { /* undo what api.on and api.prompt do not */ })
}
```

[control-room-sample-plugin](https://github.com/Tenjin-Tech/control-room-sample-plugin) is a template to start from.

## Where plugins live

Your plugins go in `~/.nightshift/control-room/plugins/<id>/`, by **Install** under **Settings › Community plugins › Available**, or by copying or cloning the folder there. A plugin never runs until you turn it on there.

Turning it on asks for your approval and records a hash of every file in its folder (`.git` aside). If any file changes, it stops starting until you turn it on again. Only its own folder is covered, so keep everything it needs inside it.

## The API

| Member | What it does |
|---|---|
| `api.prompt(moment, text)` | Gives agents an instruction, removed when the plugin stops. `text` is a string, or a function of `{ agent, repo }` that returns one (or nothing, to say nothing that time). |
| `api.on(event, fn)` | Listens to an app event. Removed when the plugin stops. |
| `api.onUnload(fn)` | Runs `fn` when the plugin stops: undo here what `api.on` and `api.prompt` don't. |
| `api.workingAgents()` | How many agents are working right now. |
| `api.id` | The plugin's id. |
| `api.ctx` | The app's internals (main process). Not stable: it can change with any update. |

`api.prompt`, `api.on`, `api.onUnload`, `api.workingAgents` and `api.id` stay stable across updates.

### Prompt moments

- `session`: added after Control Room's own instructions for every chat agent (Claude's system prompt, Codex's developer instructions), read again each time the agent starts. Terminal threads don't get it.
- `task`: once, after the first message of a new task, terminal threads included, and shown under it ("Told by plugins").

An unknown moment stops the plugin. A prompt function that throws stops the plugin too, and the agent starts without its instruction.

### Events

| Event | Arguments |
|---|---|
| `thread-state` | thread key, its new state (`working`, `idle`, …) |
| `thread-starting` | thread key |
| `session-closed` | thread key |
| `created`, `removing`, `removed` | the workspace |
| `page-loaded` | none |

## Access and safety

`access: "full"` is the only level: the plugin runs in the app's main process and can do anything you can on this Mac, including reading your tokens and files. There is no sandboxed level yet, and no renderer code.

A plugin that throws stops on its own, without affecting the app or other plugins, and Settings says why. A plugin that takes the app down while it starts (before its first `await`) stays off at the next launch.

## Publishing

1. Put the plugin in its own public repository, with a `README.md` and a `LICENSE`. The README says what the plugin sends over the network and why, and whether it needs an account or payment.
2. Create a GitHub release whose tag is the manifest's `version`, and attach every file of the plugin to it, flat (no folders).
3. Open a pull request here that adds an entry to [`plugins.json`](../plugins.json): `id`, `name`, `author`, `description`, `repo` (`owner/name`).

Control Room then lists it under **Available**, installs the files of the release matching the version at the head of your repository, and offers each newer version as an update.
