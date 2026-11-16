# Install svg-bundle-pipeline

The skill is one folder: `svg-bundle-pipeline`. Copy it into your agent's skills folder and the agent loads it whenever a task matches its description.

## Claude Code

```bash
mkdir -p ~/.claude/skills
cp -r svg-bundle-pipeline ~/.claude/skills/
```

Restart Claude Code, or run `/skills` to confirm it is listed.

## Codex CLI

```bash
mkdir -p ~/.codex/skills
cp -r svg-bundle-pipeline ~/.codex/skills/
```

## Cursor

```bash
mkdir -p .cursor/skills
cp -r svg-bundle-pipeline .cursor/skills/
```

Cursor reads project skills from `.cursor/skills/` in the project root. For a global install, use `~/.cursor/skills/` instead.

## Claude.ai

Go to Settings, then Capabilities, then Skills, and upload the `svg-bundle-pipeline` folder.

## Check it works

Ask your agent: "Build me a cut-ready SVG star, following the svg-bundle-pipeline skill."

The result should be a closed-path SVG with viewBox `0 0 1024 1024`, fill-rule evenodd, no strokes, and no dust specks. If the agent leaves a stroke attribute in, point it at the cut-safety rules in SKILL.md.

## Updating

The free skill is updated on GitHub. Replace the folder with the new version. Your own palettes and project rules live in your project, not in the skill, so an update will not overwrite them.
