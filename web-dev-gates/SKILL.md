---
name: web-dev
description: Decides the shape of a web project before any code is written, and keeps it there. Three gates, climbed in order, stop at the first that holds — static site, static plus a scheduled job, then and only then a server. GitHub is the default infrastructure; city-provided AWS is the only step beyond it. Frameworks only when named; Svelte, Astro, or Preact when one is — never React or Next.js. Use on ANY request to build, scaffold, prototype, or add a feature to a website, web app, dashboard, form, tool, or internal page, and whenever a framework, database, login, or hosting service is about to be introduced.
license: MIT
---

# Web-dev gates

Two people to take care of: the person using this, and the person maintaining it. The user needs it fast and accessible. The maintainer needs the fewest things that can break, or cost money. Shape the project before writing code.

## Persistence

Active every response. Run the gates at the start of a project, and again whenever a request would change a gate's answer. A static site does not turn into a database because a later feature request sounded reasonable.

## The gates (for the maintainer)

1. **Infrastructure** — no server unless input persists or a secret is kept
2. **Build and framework** — no build unless it does something; no framework unless you can say why.
3. **Least power** — for every feature, HTML before CSS before JS before a dependency. Runs on every feature.

Climb in order. Stop at the first rung that holds.

### Gate 1 — Infrastructure

A server exists only if at least one of these is true:

- input from users must be saved
- a secret must be kept off the client
- someone must log in

None true → static site. Host it here, in this order, stopping at the first
that works:

1. **GitHub Pages.** Files in a repo. Nothing to run.
2. **GitHub Actions on a schedule.** Fetch, build, commit. Covers anything
   that copies data published somewhere else.
3. **One serverless function.** For the one thing a static site can't do.
4. **City-provided AWS.** The SF Department of Technology can set up an
   environment for your app. Say why the rungs above can't cover it. Expect
   an account, an owner, and a security review to come with it.

Vercel, Supabase, Netlify, and other outside hosts are not on this ladder.
They put city data with an outside party. If one seems necessary, that's a
conversation, not a default.

"Internal only" does not need a server. It means the site sits behind
something that already exists — [FILL IN: the office's answer, e.g.
enterprise Pages, SSO in front of a static host]. Ask before building a login.

### Gate 2 — Build and framework

The web platform now does what frameworks were invented to do: modules,
templates, custom elements, scoped styles, `fetch`, layout. Frameworks were
built for large products with large teams. Assume this isn't one until shown
otherwise. Climb in order, stop at the first that holds:

1. **No build.** HTML, CSS, and JS files, split however reads best. ES
   modules to organize them. Open the file, it works.
2. **Vite.** Only when a build does something — bundling many modules, an
   asset pipeline. Runs in GitHub Actions, deploys to Pages. Still no
   framework.
3. **11ty.** Many pages that share headers, navigation, and layout, driven
   by data that rarely changes. Templates plus data files, built to plain
   HTML in Actions, served from Pages. Still a static site. (Rungs 2 and 3
   solve different problems — many modules vs. many pages. Pick the one that
   fits. You don't pass through Vite to reach 11ty.)
4. **A framework.** Only for one specific thing it does that you can name.
   Not "it's well known." Not "for later." If you can't name it, stop at
   rung 3. If you can, it's Svelte, Astro, or Preact. Never React — not
   Next.js, not under any wrapper. If someone asks for React, stop and
   confirm before going on.

### Gate 3 — Least power

For each feature, use the least powerful tool that does the job. Less power
means less to break, and anyone can read it. Climb per feature, stop at the
first that holds:

1. **HTML.** `<details>` to expand and collapse, `<dialog>` for modals,
   `<input type="date">` for dates, `<datalist>` for suggestions, `required`
   and `pattern` for validation, a `<form>` that submits.
2. **CSS.** Layout, responsiveness, hover and focus states, transitions,
   `:has`, `@media`, scroll-snap, sticky positioning. If CSS can do it, JS
   doesn't.
3. **JS.** Fetching data, responding to events, state that HTML and CSS
   can't hold. `fetch`, `IntersectionObserver`, `Intl` — platform APIs before
   custom code.
4. **A dependency.** Name the rung it replaces and why that rung isn't
   enough. Modern CSS over Tailwind. `<dialog>` over a modal library. If you
   can't name it, stop at rung 3.

## Output

First response of a project, before any code:

```
Shape: [static site / static + scheduled job / one function / server]
Hosting: [where]
Build: [none / Vite / 11ty / framework, and why]
Not included: [database, login, framework, ...]
Add when: [the specific condition that would change this]
```

Plain language. No jargon the requester didn't use. Then code. Put the block
in the README too, so the next maintainer finds it.

When a later request would flip a gate, show the block again with what
changes — a new vendor, a security review, a monthly bill, someone who has
to own it — and ask. If they say yes, build it.

## The floor (for the user)

Never cut, whatever the shape:

- **Fast.** Core Web Vitals pass. Ship the least code.
- **Usable by everyone.** WCAG 2.2 AA, checked before release. Semantic
  HTML. Keyboard access with visible focus. A text alternative for every
  image and chart. Meaning never carried by color alone.
  `prefers-reduced-motion` honored. Zoom never disabled.
- **Safe.** Validate anything that leaves the browser. No secrets in the
  client. No `innerHTML` from user input. If residents' data is collected,
  decide where it lives and for how long at Gate 1.
- **Checked.** A keyboard-only pass and a contrast check before shipping.
- **Theirs.** Anything the requester asks for. Their call, always.

## The handoff (for the maintainer)

- Deployment runs from GitHub Actions and is set up as part of the work.
  Never left manual.
- One check per flow that matters, sized to the shape. Static site: the
  build passes, and a link check and accessibility check run in Actions.
  Anything with a server: one end-to-end test per flow (submit, book,
  cancel), and no more. A check that fails when the flow breaks, not a
  suite.
- Print `npm install` commands for the requester to run. Don't run them.
  Before they run one, ask them to look the package up on npmx.dev and check
  three things: lots of other people use it, it has no security warnings,
  and the version isn't brand new. A version published in the last few days
  can be an attacker who took over a maintainer's account; those are usually
  caught within a week, so install the version before it. Pin the version
  you install.

## Boundaries

These gates decide the shape. For the code itself, pair with a per-task
simplicity skill (ponytail or equivalent).

The right shape is the one with the fewest things that can break.