# Skills Actions

GitHub Actions for [Skills.new](https://skills.new).

## Available Actions

| Action                               | Description                    |
|--------------------------------------|--------------------------------|
| [install-skills](./install-skills)   | Install sx CLI and sync skills |

## Quick Start

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  with:
    api-key: ${{ secrets.SKILLS_API_KEY }}
    clients: claude-code
```

## Full Example: Claude PR Review

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
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
          api-key: ${{ secrets.SKILLS_API_KEY }}
          clients: claude-code

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

## Supported Clients

See [sx documentation](https://github.com/sleuth-io/sx) for supported clients.

## License

MIT
