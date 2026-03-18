# camelup

`camelup` is a non-destructive installer for the CaMeL-integrated Hermes build.

It is built for one goal:

- wire CaMeL trust boundaries into Hermes without replacing or breaking a user's existing Hermes checkout

`camelup` supports two deployment modes:

1. fresh install: clone the published `hermes-agent-camel` fork into a new directory
2. existing checkout: attach the fork to an already-installed Hermes repo and create a local `camel-main` branch from it

In the existing-checkout path, `camelup` does **not** overwrite the user's current branch, rewrite history, or merge over their existing setup.

## What CaMeL Adds To Hermes

The integrated build adds runtime trust boundaries inside Hermes itself.

Main behavior:

- separates trusted control from untrusted data
- derives a trusted operator plan from real user turns
- treats tool outputs, retrieved content, browser content, files, recall, and MCP results as untrusted evidence by default
- injects a per-turn security envelope into the effective runtime context
- gates sensitive tools against the trusted operator plan
- strips internal CaMeL metadata before provider API calls

Sensitive capabilities gated by the integrated build include:

- terminal / command execution
- file mutation
- persistent memory writes
- external messaging
- scheduled actions
- skill mutation
- delegation / subagents
- browser interaction and selected external side effects

Read-only actions such as list/status queries remain allowed.

This is designed to make indirect prompt injection materially harder without blocking explicitly authorized operator actions.

## Source Repos

- CaMeL-integrated Hermes fork: https://github.com/nativ3ai/hermes-agent-camel
- Original Hermes upstream: https://github.com/NousResearch/hermes-agent
- CaMeL paper: https://arxiv.org/abs/2503.18813
- CaMeL reference repo: https://github.com/google-research/camel-prompt-injection

## Install Modes

### Mode 1: Fresh directory

Use this if you want a dedicated Hermes checkout that already includes the CaMeL integration.

```bash
curl -fsSL https://raw.githubusercontent.com/nativ3ai/camelup/main/bin/camelup -o /tmp/camelup
chmod +x /tmp/camelup
/tmp/camelup install --target ~/hermes-agent-camel
```

Result:

- clones `nativ3ai/hermes-agent-camel` into `~/hermes-agent-camel`
- leaves any existing Hermes install elsewhere untouched

### Mode 2: Existing Hermes checkout

Use this if you already have Hermes installed or cloned and want the CaMeL build available there without replacing your current branch.

```bash
curl -fsSL https://raw.githubusercontent.com/nativ3ai/camelup/main/bin/camelup -o /tmp/camelup
chmod +x /tmp/camelup
/tmp/camelup install --target ~/src/hermes-agent
```

Result:

1. verifies the target looks like a Hermes repo
2. requires a clean working tree
3. adds remote `camel -> https://github.com/nativ3ai/hermes-agent-camel.git`
4. fetches `camel/main`
5. creates or resets local branch `camel-main`
6. checks out `camel-main`

This means the user keeps their original Hermes repo, remotes, and branches. `camelup` just adds a parallel guarded branch they can switch to.

## Commands

### `doctor`

```bash
./bin/camelup doctor
```

Checks host prerequisites:

- `git`
- `python3`
- `curl`

### `install`

```bash
./bin/camelup install --target <path>
```

Behavior:

- empty or missing path: clone the CaMeL fork there
- existing Hermes repo: wire in `camel/main` as local branch `camel-main`

### `verify`

```bash
./bin/camelup verify --target <path>
```

Confirms the target checkout contains the expected CaMeL integration files:

- `agent/camel_guard.py`
- `docs/camel-benchmark.md`
- `tests/agent/test_camel_guard.py`

### `status`

```bash
./bin/camelup status --target <path>
```

Reports:

- current branch
- current revision
- whether the `camel` remote is present
- whether the checkout is currently on `camel-main`

Use this when you want to inspect a Hermes repo without mutating it.

## Why This Does Not Replace Existing Hermes

That is deliberate.

For users who already run Hermes, the safest integration model is:

- keep their checkout
- keep their branch history
- keep their current setup usable
- make the guarded build available as a separate branch

That is exactly what `camelup` does.

If they want the original setup back:

```bash
cd ~/src/hermes-agent
git checkout main
```

Or any other branch they were using before.

## Validation

`camelup` itself was tested in both modes:

1. fresh clone path
2. existing Hermes checkout path

Verified outcomes:

- fresh clone lands on fork `main`
- existing Hermes checkout lands on local `camel-main`
- expected CaMeL files are present

## Notes

- `camelup` is intentionally non-destructive
- it does not force-merge into a user's current branch
- it stops on dirty working trees rather than guessing
- it is an installer/wiring tool, not a replacement for Hermes' own dependency/bootstrap process
