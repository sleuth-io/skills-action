# Install Skills Action

Install the sx CLI and sync skills from [Skills.new](https://skills.new).

## Usage

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  with:
    api-key: ${{ secrets.SKILLS_API_KEY }}
    clients: claude-code
```

## Inputs

| Input        | Required | Default  | Description                                          |
|--------------|----------|----------|------------------------------------------------------|
| `api-key`    | Yes      | -        | Your Skills.new API key                              |
| `clients`    | Yes      | -        | LLM clients to install skills for (comma-separated)  |
| `sx-version` | No       | latest   | sx CLI version to install (minimum v0.14.2 required) |

See [sx documentation](https://github.com/sleuth-io/sx) for supported clients.

## Outputs

| Output   | Description                                |
|----------|--------------------------------------------|
| `skills` | Comma-separated list of installed skills   |

## Examples

### Single client

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  with:
    api-key: ${{ secrets.SKILLS_API_KEY }}
    clients: claude-code
```

### Multiple clients

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  with:
    api-key: ${{ secrets.SKILLS_API_KEY }}
    clients: claude-code,cursor
```

### Pin sx version

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  with:
    api-key: ${{ secrets.SKILLS_API_KEY }}
    clients: claude-code
    sx-version: v0.14.2
```

### Use skills output

```yaml
- uses: sleuth-io/skills-actions/install-skills@v1
  id: skills
  with:
    api-key: ${{ secrets.SKILLS_API_KEY }}
    clients: claude-code

- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      Installed skills: ${{ steps.skills.outputs.skills }}
```
