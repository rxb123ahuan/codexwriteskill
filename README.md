# oh-story-codex-skills

Codex adaptation of the Chinese web-novel writing skill set from
[`worldwonderer/oh-story-claudecode`](https://github.com/worldwonderer/oh-story-claudecode).

This repository keeps the original multi-skill structure and adapts the workflows for Codex:

- Slash commands are treated as natural-language invocation hints.
- Story skills run in the main Codex thread by default.
- Codex subagents are used only when the user explicitly asks for parallel/subagent work.
- `story-setup` creates Codex-readable `STORY.md`, `.codex-story/rules/`, and tracking files instead of Claude Code hooks/agents.
- `story-cover` prefers Codex image generation tools instead of requiring a separate image API key.

## Skills

| Skill | Purpose |
| --- | --- |
| `story` | Router entry for Chinese web-novel tasks |
| `story-long-write` | Long-form web-novel planning, outlining, drafting, and revision |
| `story-short-write` | Short story ideation, drafting, and polishing |
| `story-long-analyze` | Long-form novel deconstruction |
| `story-short-analyze` | Short story deconstruction |
| `story-long-scan` | Long-form market and ranking research |
| `story-short-scan` | Short-form market and ranking research |
| `story-deslop` | Remove obvious AI-writing traces from prose |
| `story-review` | Review drafts against Chinese web-novel platform expectations |
| `story-import` | Convert existing manuscripts into a structured writing project |
| `story-cover` | Generate or plan Chinese web-novel cover concepts |
| `story-setup` | Initialize a structured writing project |
| `browser-cdp` | Browser CDP helper for market data collection |

## Install From GitHub

After publishing this repository, install one or more skills with Codex's skill installer:

```bash
python "$CODEX_HOME/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo <your-github-user>/oh-story-codex-skills \
  --path skills/story \
  --path skills/story-long-write \
  --path skills/story-short-write \
  --path skills/story-deslop \
  --path skills/story-review
```

To install the complete set, pass every folder under `skills/` as a `--path`.

Restart Codex after installation so the new skills are picked up.

## Attribution

Original project: <https://github.com/worldwonderer/oh-story-claudecode>

The original project is licensed under the license included in this repository.
