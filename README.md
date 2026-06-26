# Skills

Reusable [Claude Code](https://docs.claude.com/en/docs/claude-code) skills shared by the Mayor's Office of Innovation. Each skill is a directory containing a `SKILL.md` (YAML frontmatter + instructions) that Claude loads on demand.

## [web-dev](web-dev/SKILL.md)

Web development stack & standards — lightweight, Core-Web-Vitals-first, and accessible (WCAG AA) by default; web components + Web Awesome; modern CSS (never Tailwind, never React); Vite + GitHub Pages. Invoke at the *start* of any web work, before choosing tools or scaffolding.

## [dashboard-review](dashboard-review/SKILL.md)

Audit an *already-built* data dashboard or visualization for accuracy, source integrity, denominator/population conflation, presentation simplicity, and neutral tone. Produces a severity-ranked findings doc and applies fixes only on confirmation. Run it *after* a dashboard exists (use `web-dev` to build one).

## Installing a skill

Copy (or symlink) the skill directory into your Claude Code skills folder:

```bash
# user-level (available everywhere)
cp -R web-dev ~/.claude/skills/

# or project-level (this repo only)
cp -R web-dev /path/to/project/.claude/skills/
```

Restart Claude Code (or start a new session) so it picks up the skill. Invoke it by name (e.g. `/web-dev`, `/dashboard-review`) or let Claude trigger it automatically from the `description`.

> These directories are **copies** of the maintainer's active `~/.claude/skills/`; re-copy after edits to keep them in sync.

## License

[MIT](LICENSE) © City and County of San Francisco
