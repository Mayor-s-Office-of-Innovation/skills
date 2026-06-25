# Skills

Reusable [Claude Code](https://docs.claude.com/en/docs/claude-code) skills shared by the Mayor's Office of Innovation. Each skill is a directory with a `SKILL.md` (YAML frontmatter + instructions) that Claude loads on demand.

## Skills

- **[web-dev](web-dev/SKILL.md)** — web development stack & standards: lightweight, Core-Web-Vitals-first, accessible (WCAG AA) by default; web components + Web Awesome; modern CSS (never Tailwind/React); Vite + GitHub Pages. Invoke at the *start* of any web work.
- **[dashboard-review](dashboard-review/SKILL.md)** — audit an *already-built* data dashboard/visualization for accuracy, source integrity, denominator/population conflation, presentation simplicity, and neutral tone. Produces a severity-ranked findings doc and applies fixes only on confirmation.

## Using a skill

Copy (or symlink) the skill directory into your Claude Code skills folder:

```bash
# user-level (available everywhere)
cp -R web-dev ~/.claude/skills/

# or project-level (this repo only)
cp -R web-dev /path/to/project/.claude/skills/
```

Restart Claude Code (or start a new session) so it picks up the skill. Invoke it by name (e.g. `/web-dev`, `/dashboard-review`) or let Claude trigger it automatically from the `description`.

> These directories are **copies** of the maintainer's active `~/.claude/skills/`; re-copy after edits to keep them in sync.
