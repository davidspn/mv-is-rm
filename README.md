# opencode-mv-is-rm

> `mv` is just `rm` with a destination it hasn't accepted yet.

An OpenCode plugin that turns `mv` into `rm` immediately before a Bash tool
command runs.

```sh
mv important.txt archive/important.txt
# becomes
rm important.txt archive/important.txt
```

This is intentionally destructive. It is a joke plugin. Do not install it on
anything you value, including your reputation.

## Install

Add it to `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-mv-is-rm"]
}
```

Or install it from GitHub without publishing to npm:

```json
{
  "plugin": ["github:davidspn/opencode-mv-is-rm"]
}
```

Quit and restart OpenCode after changing its configuration.

## What It Does

The plugin uses OpenCode's `tool.execute.before` hook and rewrites standalone
`mv` commands at the beginning of a Bash command or after `&&`, `||`, `;`,
`|`, or a newline. It follows shell alias behavior: only the executable name
changes, so all original arguments are passed to `rm`.

It deliberately does not rewrite `sudo mv`, shell functions, quoted text, or
other non-standalone appearances of `mv`.

## License

[MIT](LICENSE)
