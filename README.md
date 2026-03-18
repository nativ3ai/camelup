# camelup

`camelup` is a small installer for the CaMeL-integrated Hermes build.

It supports two safe paths:

- clone the published `hermes-agent-camel` fork into a new directory
- attach the fork to an existing Hermes checkout and create a local `camel-main` branch from the fork without overwriting your current branch

## What it installs

Source repo:

- https://github.com/nativ3ai/hermes-agent-camel

The installer does not rewrite upstream Hermes history. On an existing checkout it adds a `camel` remote and creates a local branch from `camel/main`.

## Commands

### Doctor

```bash
./bin/camelup doctor
```

Checks for `git`, `python3`, and `curl`.

### Install into a fresh directory

```bash
./bin/camelup install --target ~/hermes-agent-camel
```

If `--target` does not exist or is empty, `camelup` clones the fork there.

### Apply to an existing Hermes checkout

```bash
./bin/camelup install --target ~/src/hermes-agent
```

If `--target` is an existing git repository, `camelup`:

1. verifies it looks like Hermes
2. adds remote `camel` -> `https://github.com/nativ3ai/hermes-agent-camel.git`
3. fetches `camel/main`
4. creates or resets local branch `camel-main` to `camel/main`
5. checks out `camel-main`

### Verify

```bash
./bin/camelup verify --target ~/src/hermes-agent
```

Confirms that the target checkout contains the CaMeL integration files.

## Examples

Fresh clone:

```bash
curl -fsSL https://raw.githubusercontent.com/nativ3ai/camelup/main/bin/camelup -o /tmp/camelup
chmod +x /tmp/camelup
/tmp/camelup install --target ~/hermes-agent-camel
```

Existing Hermes checkout:

```bash
curl -fsSL https://raw.githubusercontent.com/nativ3ai/camelup/main/bin/camelup -o /tmp/camelup
chmod +x /tmp/camelup
/tmp/camelup install --target ~/src/hermes-agent
```

## Notes

- This installer is intentionally non-destructive.
- It does not force-merge into your current branch.
- If your checkout has local modifications, `camelup` stops and tells you to clean or stash them first.
