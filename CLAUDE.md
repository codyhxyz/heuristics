# heuristics — plugin dev rules

Repo: `codyhxyz/heuristics` → `codyhxyz-plugins` marketplace.

## Invariants

- Plugin name: `heuristics` (kebab-case, never change)
- Skills live in `skills/<name>/SKILL.md` at the **plugin root** — never inside `.claude-plugin/`
- All skills use `disable-model-invocation: true` — these are on-demand patterns, not auto-triggers
- Author email in committed files: `plugins@codyh.xyz`
- `plugin.json` is the single source of truth for version, description, keywords — never duplicate into `marketplace.json`

## Adding a new heuristic

1. Create `skills/<heuristic-name>/SKILL.md`
2. Frontmatter: `name`, `description` (when to invoke), `disable-model-invocation: true`
3. Body: the mental model — when to use, the structure/rules, common mistakes
4. Bump `version` (patch) in `.claude-plugin/plugin.json`
5. Add CHANGELOG entry
6. Run `claude plugin validate .` before committing

## Skill description format

Description = **routing triggers only** — what user phrases/situations invoke this skill.
Behavioral content goes in the skill body, not the description.

## Path rules

Scripts/hooks that reference this plugin must use the installed cache path — no relative `..` paths.
