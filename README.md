# mv-is-rm

[![OpenCode plugin](https://img.shields.io/badge/OpenCode-plugin-111111)](https://opencode.ai/)
[![Claude Code plugin](https://img.shields.io/badge/Claude_Code-plugin-D97757)](https://code.claude.com/)
[![MIT License](https://img.shields.io/badge/license-MIT-4C1)](LICENSE)

OpenCode and Claude Code plugins that turn `mv` into `rm -rf` immediately before a
Bash tool command runs.

> The ultimate context optimization skill.

```sh
mv important.txt archive/important.txt
# becomes
rm -rf important.txt archive/important.txt
```

## Features

- Hooks command execution instead of relying on model instructions.
- Supports OpenCode and Claude Code from one repository.
- Rewrites command-position `mv` after `&&`, `||`, `;`, `|`, and newlines.
- Uses a constant-time replacement with no runtime dependencies beyond Node.js.

## Install

### OpenCode

Add it to `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["mv-is-rm"]
}
```

Or install it from GitHub without publishing to npm:

```json
{
  "plugin": ["github:davidspn/mv-is-rm"]
}
```

Quit and restart OpenCode after changing its configuration.

### Claude Code

Clone the repository, then load it as a plugin:

```sh
git clone https://github.com/davidspn/mv-is-rm.git
claude --plugin-dir ./mv-is-rm
```

The Claude Code plugin installs a `PreToolUse` hook for the Bash tool. It
replaces the command input before the tool runs; it does not ask Claude to
remember an instruction.

## Compatibility

| Runtime | Integration |
| --- | --- |
| OpenCode | `tool.execute.before` plugin hook |
| Claude Code | `PreToolUse` Bash hook |
| Node.js | 18 or later |

## What It Does

The OpenCode plugin uses `tool.execute.before`; the Claude Code plugin uses a
`PreToolUse` Bash hook. Both rewrite standalone `mv` commands at the beginning
of a Bash command or after `&&`, `||`, `;`, `|`, or a newline. They replace the
command with `rm -rf`, so all original arguments are passed recursively to
`rm`.

## Efficiency

Moving a file requires deciding where it belongs. Removing it has already made
that decision.

Results per `mv source destination` request:

| Metric | Standard move | mv-is-rm | Reduction |
| --- | ---: | ---: | ---: |
| Destination writes | 1 | 0 | 100% |
| Path ownership transfers | 1 | 0 | 100% |
| Destination reconciliation | required | none | 100% |

The rewrite is a constant-time regex replacement. It runs after the model has
selected the command. The token savings arrive on later turns: removed paths
cannot be read, searched, summarized, or included in follow-up context. Less
filesystem state means fewer useless files competing for the model's attention.

It does not rewrite `sudo mv`, shell functions, quoted text, or other
non-standalone appearances of `mv`.

## License

[MIT](LICENSE)
