# Shaping Skills

These skills originated as experiments with Claude Code. They have been adapted for Codex and GPT-6.1 Terra, but the original author described them as obsolete and had not recently used them. Validate the results against your own material and workflow.

Skills for shaping and breadboarding — the methodology from [Shape Up](https://basecamp.com/shapeup) adapted for working with an LLM.

**Case study:** [Shaping 0-1 with Claude Code](https://x.com/rjs/status/2020184079350563263) walks through the full process of building a project from scratch using these skills. The source for that project is at [rjs/tick](https://github.com/rjs/tick).

## Skills

### Document skills — for collaborative work

These turn transcripts of real conversations into structured shaping documents. They're useful on real production projects where you're working with other people and want to capture what was said in a format you can act on.

**These are extremely GIGO (garbage in, garbage out).** They don't evaluate whether the material makes sense or is reasonable. They format and distill — that's it. When your inputs are good conversations with good thinking, they save a ton of time. When your inputs are bad, you get a nicely formatted bad document.

**`/framing-doc`** — Turn conversation transcripts into a framing document that captures the problem worth solving and why it was chosen over alternatives.

**`/kickoff-doc`** — Turn a shaped project kickoff transcript into a reference document for the builder, capturing what was shaped and agreed.

### Solo skills — more experimental

These are for working with an agent on shaping and design. They're more experimental and less battle-tested than the document skills.

**`/shaping`** — Iterate on both the problem (requirements) and solution (shapes) before committing to implementation. Separates what you need from how you might build it, with fit checks to see what's solved and what isn't.

**`/breadboarding`** — Map a system into UI affordances, code affordances, and wiring. Shows what users can do and how it works underneath — in one view. Good for slicing into vertical scopes.

**`/breadboard-reflection`** — Compare an existing breadboard with its implementation and identify design issues.

## Install

### Codex

Copy the skill directories into the target project's `.agents/skills/` directory. Include the entire directories so the breadboarding references are available:

```bash
mkdir -p .agents/skills
cp -R /path/to/shaping-skills/{framing-doc,kickoff-doc,shaping,breadboarding,breadboard-reflection} .agents/skills/
```

To make them available across projects, use `~/.agents/skills/` instead. Start a new Codex session after installation. Copies do not update automatically; copy them again when you want to update them. Invoke a skill explicitly with its name, for example `$shaping` or `$breadboarding`.

Provide the relevant source material, desired outcome and constraints. For existing-system mapping or reflection, identify the workflow and code to inspect. Review the resulting claims against the sources and check breadboard wiring against the implementation. The skills guide the workflow; they do not establish that an output is correct.

### Claude Code

```bash
# Clone the repo, then symlink each skill into your Claude Code skills directory
git clone https://github.com/rjs/shaping-skills.git ~/.local/share/shaping-skills
ln -s ~/.local/share/shaping-skills/framing-doc ~/.claude/skills/framing-doc
ln -s ~/.local/share/shaping-skills/kickoff-doc ~/.claude/skills/kickoff-doc
ln -s ~/.local/share/shaping-skills/breadboarding ~/.claude/skills/breadboarding
ln -s ~/.local/share/shaping-skills/shaping ~/.claude/skills/shaping
ln -s ~/.local/share/shaping-skills/breadboard-reflection ~/.claude/skills/breadboard-reflection
```

Each skill must be a direct child of `~/.claude/skills/` so Claude Code can discover it. Symlinks keep them updatable with `git pull`.

## Claude Code hook: Ripple Check

The repo includes a hook that reminds Claude to check for ripple effects when editing shaping documents. When Claude writes or edits a `.md` file with `shaping: true` in its frontmatter, the hook prompts a checklist — update affordance tables, fit checks, work streams, etc.

### Setup

1. Symlink the hook script:

```bash
mkdir -p ~/.claude/hooks
ln -s ~/.local/share/shaping-skills/hooks/shaping-ripple.sh ~/.claude/hooks/shaping-ripple.sh
```

2. Add the hook to your `~/.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/shaping-ripple.sh",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

This fires after every `Write` or `Edit` tool call. It only activates for shaping documents (those with `shaping: true` frontmatter) — all other files pass through silently. This is a Claude Code hook; it is not configured by the Codex installation above. In Codex, follow the consistency checks in the skills themselves.

---

This README was written by [Claude Code](https://claude.com/claude-code).
