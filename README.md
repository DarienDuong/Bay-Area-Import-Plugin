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

## Install In Codex

From this repo root on each machine:

```bash
codex plugin marketplace add "$(pwd)"
codex plugin add bay-area-workflows@personal
```

## Update

Pull the latest repo changes:

```bash
git pull
```

Then refresh the installed plugin:

```bash
codex plugin add bay-area-workflows@personal
```

## Team Setup

Clone the repo:

```bash
git clone https://github.com/DarienDuong/Bay-Area-Import-Plugin.git
cd Bay-Area-Import-Plugin
```

Then install the plugin in Codex:

```bash
codex plugin marketplace add "$(pwd)"
codex plugin add bay-area-workflows@personal
```

## Add A Skill

Create a folder for the skill:

```text
plugins/bay-area-workflows/skills/<skill-name>/
```

Required file:

```text
plugins/bay-area-workflows/skills/<skill-name>/SKILL.md
```

Optional supporting files:

```text
agents/
scripts/
assets/
references/
```

## Validate

```bash
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py plugins/bay-area-workflows
```

## Release Workflow

After making changes:

```bash
git add .
git commit -m "Describe the workflow change"
git push
```

## Team Notes

Do not commit secrets, customer data, cookies, downloaded labels, local exports, or machine-specific config.
