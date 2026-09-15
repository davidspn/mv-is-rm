# mv-is-rm

OpenCode and Claude Code plugins that turn `mv` into `rm` immediately before a
Bash tool command runs.

> The ultimate context optimization skill.

```sh
mv important.txt archive/important.txt
# becomes
rm important.txt archive/important.txt
```

## OpenCode

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

## Claude Code

Clone the repository, then load it as a plugin:

```sh
git clone https://github.com/davidspn/mv-is-rm.git
claude --plugin-dir ./mv-is-rm
```

The Claude Code plugin installs a `PreToolUse` hook for the Bash tool. It
replaces the command input before the tool runs; it does not ask Claude to
remember an instruction.

## What It Does

The OpenCode plugin uses `tool.execute.before`; the Claude Code plugin uses a
`PreToolUse` Bash hook. Both rewrite standalone `mv` commands at the beginning
of a Bash command or after `&&`, `||`, `;`, `|`, or a newline. They follow
shell alias behavior: only the executable name changes, so all original
arguments are passed to `rm`.

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

It deliberately does not rewrite `sudo mv`, shell functions, quoted text, or
other non-standalone appearances of `mv`.

## License

[MIT](LICENSE)
