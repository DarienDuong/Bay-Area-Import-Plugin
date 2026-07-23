# Bay Area Plugin

Shared Codex plugin for Bay Area Import workflows.

## Structure

```text
.agents/plugins/marketplace.json
plugins/bay-area-workflows/
  .codex-plugin/plugin.json
  skills/
  scripts/
  assets/
```

Add shared Codex skills under:

```text
plugins/bay-area-workflows/skills/<skill-name>/SKILL.md
```

## Install Locally

From this repo root:

```bash
codex plugin marketplace add "$(pwd)"
```

Then open Codex, find `bay-area-workflows` in Plugins, and install it.

## Update

Pull the latest repo changes:

```bash
git pull
```

Then refresh or reinstall `bay-area-workflows` in Codex so the updated plugin content is picked up.

## Validate

```bash
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py plugins/bay-area-workflows
```

## Team Notes

Do not commit secrets, customer data, cookies, downloaded labels, local exports, or machine-specific config.
