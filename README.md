# metric-chat

A Claude Code skill that interviews a team to choose and refine evaluation metrics for a user-facing application, such as search, summarization, classification, visual analytics or a labeling tool. It produces a set of metrics, each with its rationale, its link to two objectives (sustained use and improved usability), and a concrete way to collect it.

The skill uses the public [HAMI-Lab metrics wiki](https://github.com/HAMI-Lab/metrics-kb) as its knowledge base: constructs, metrics, instruments, and measurement methods.

## How the pieces fit

| Repository | Holds |
|---|---|
| [metrics-kb](https://github.com/HAMI-Lab/metrics-kb) (public) | The general wiki: what to measure and how |
| metric-chat (this one, public) | The interview process, as a Claude Code plugin |
| One repository per application (often private) | The application's goals, chosen metrics, collection plan and interview notes. It includes the wiki as a git submodule at `metrics-wiki/` and enables this plugin |

## Use it in an application repository

In the application repository, once:

```bash
git submodule add https://github.com/HAMI-Lab/metrics-kb.git metrics-wiki
claude plugin marketplace add HAMI-Lab/metric-chat --scope project
claude plugin install metric-chat@hami-lab --scope project
```

Commit the `.claude/settings.json` these commands write. Teammates who trust the folder get the plugin. In a session, run `/metric-chat:metric-chat`.

## Versions

The plugin's version is in `plugins/metric-chat/.claude-plugin/plugin.json`. Bump it on every release; users stay on their cached copy until it changes. Each release should say which version of the wiki it was written against.

| Plugin version | Written against the wiki's |
|---|---|
| 0.2.0 | metric template 0.3, decisions up to D34 |
| 0.1.0 | (original skill, before the D23-D34 schema changes) |

## Layout

```
.claude-plugin/marketplace.json            the marketplace ("hami-lab") listing the plugin
plugins/metric-chat/.claude-plugin/plugin.json
plugins/metric-chat/skills/metric-chat/SKILL.md   the skill
```
