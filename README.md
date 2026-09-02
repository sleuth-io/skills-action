# Skills Actions

GitHub Actions for [sx](https://github.com/sleuth-io/sx) vaults.

## Available Actions

| Action                             | Description                                      |
|------------------------------------|--------------------------------------------------|
| [install-skills](./install-skills) | Install the sx CLI and sync skills from your vault |

## Quick Start

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  with:
    vault-url: git@github.com:your-org/ai-assets.git
    ssh-key: ${{ secrets.SX_VAULT_SSH_KEY }}
    clients: claude-code
```

## Inputs

| Input        | Required | Description                                                                                          |
|--------------|----------|------------------------------------------------------------------------------------------------------|
| `vault-url`  | yes*     | Git URL of the vault repository, e.g. `git@github.com:your-org/ai-assets.git`                        |
| `ssh-key`    | no       | Private SSH key with read access to the vault — a read-only deploy key stored as a secret            |
| `bot`        | no       | sx bot identity to install as (`SX_BOT`), so team- and bot-scoped assets resolve for this job         |
| `clients`    | yes      | Comma-separated clients to install for, e.g. `claude-code,cursor`                                    |
| `sx-version` | no       | sx release to install (defaults to latest)                                                           |
| `api-key`    | yes*     | Skills.new API key — legacy, only used when `vault-url` is not set                                    |

\* One of `vault-url` or `api-key` is required.

## Accessing a private vault repository

A workflow's built-in `GITHUB_TOKEN` can only read the repository the workflow
runs in. If your vault lives in a separate private repository, the job needs a
credential of its own. The recommended one is a **read-only deploy key**:

1. Generate a key: `ssh-keygen -t ed25519 -N "" -f sx_vault_key`
2. Add `sx_vault_key.pub` to the **vault** repository as a deploy key
   (Settings → Deploy keys, leave "Allow write access" unchecked), or:
   `gh repo deploy-key add sx_vault_key.pub -R your-org/ai-assets -t "GitHub Actions"`
3. Store the private key (`sx_vault_key`) as a secret named `SX_VAULT_SSH_KEY`
   on every repository whose workflows install skills — or as an organization
   secret shared with those repositories.
4. Pass it as `ssh-key` (above). sx hands it to git via `GIT_SSH_COMMAND`; it
   never lands in the sx config file.

The full explanation — why deploy keys, how sx uses the key, bot identities,
the GitHub App alternative, and troubleshooting — is in the sx docs:
[Using your vault from GitHub Actions](https://github.com/sleuth-io/sx/blob/main/docs/github-actions.md).

## Full Example: Claude PR Review

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    # Pull requests from forks run without secrets; skip them cleanly.
    if: github.event.pull_request.head.repo.fork == false
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: sleuth-io/skills-actions/install-skills@v1
        with:
          vault-url: git@github.com:your-org/ai-assets.git
          ssh-key: ${{ secrets.SX_VAULT_SSH_KEY }}
          bot: ci-reviewer          # optional: an sx bot in your vault
          clients: claude-code

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

## Skills.new (legacy)

Vaults hosted on app.skills.new still work with `api-key` instead of
`vault-url`/`ssh-key`. To move a skills.new library into a git vault, see the
[migration guide](https://github.com/sleuth-io/sx/blob/main/docs/migrate-from-skills-new.md).

## Supported Clients

See [sx documentation](https://github.com/sleuth-io/sx) for supported clients.

## License

MIT
