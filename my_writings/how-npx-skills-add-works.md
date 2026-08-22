# How does `npx skills add ...` work?

Pulling apart a single command, and what to check when it does not work on a locked-down machine.

```bash
npx skills add nutlope/hallmark -a claude-code -a opencode -g
```

The example is not arbitrary: [`nutlope/hallmark`](https://github.com/Nutlope/hallmark) is Together AI's design skill — *"a design skill for Claude Code, Cursor, and Codex that refuses to look AI-generated."* It ships 21 themes and can audit existing code for AI-design anti-patterns.

---

## What each part of the command does

```text
YOU
 │
 │ Windows CMD / PowerShell
 ↓
npx skills add nutlope/hallmark
 │
 ├── npx
 │     └── obtains/runs npm package "skills"
 │
 ├── skills
 │     └── Agent Skills installer CLI
 │
 ├── add
 │     └── installation command
 │
 └── nutlope/hallmark
       │
       ├── nutlope = GitHub owner
       └── hallmark = GitHub repository
                       │
                       ↓
                    SKILL.md
                       │
              ┌────────┼────────┐
              ↓        ↓        ↓
          Claude Code OpenCode  Codex
              │        │
              ↓        ↓
         agent reads Hallmark instructions
              │
              ↓
       uses them while working
```

The flags in the full command: `-a` selects which agent to install for (repeatable), `-g` installs globally rather than into the current project.

- **npm** — package manager for the Node JavaScript platform.
- **npx** — npm package executor. It fetches and runs a package without installing it permanently.

Check the CLI is reachable at all:

```bash
npx skills --version
```

---

## Where the packages come from

`npx` pulls the `skills` CLI from an npm registry; the CLI then pulls the skill itself from GitHub. Two different hops, two different things that can be blocked.

```bash
npm config get registry
```

On a normal home machine you will probably see `https://registry.npmjs.org/`. Anything else means your npm traffic is being redirected — which is the first thing worth knowing on a corporate box.

---

## Installing from a local directory

The `skills` CLI supports local repositories and directories, much like installing a Python package from a local path rather than PyPI:

```bash
npx skills add ./hallmark
```

So if the network path is blocked, you can bring the repository to the machine by whatever means you do have, put it wherever you like, and install from disk.

---

## Do you need npx to install Claude Code?

**No.** As of August 2026 the recommended installation is native:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

with platform package-manager alternatives available. The npm route also still works:

```bash
npm install -g @anthropic-ai/claude-code
```

Same story for OpenCode. `npx` is how you *install skills*, not how you must install the agents themselves.

---

## Diagnosing an isolated machine

A useful set to run before assuming anything is broken:

```bash
# Where are the programs?
command -v node
command -v npm
command -v npx
command -v skills
command -v claude
command -v opencode
command -v codex

node --version
npm --version
npx --version

# What registry does npm contact?
npm config get registry

# Where does npm cache packages?
npm config get cache

# What config files does npm use?
npm config get userconfig
npm config get globalconfig

npm config list

# Are the coding agents installed via npm?
npm list -g --depth=0

# Can git actually reach GitHub?
git ls-remote https://github.com/Nutlope/hallmark.git
```

That last one is the sharpest test: it separates "npm is blocked" from "GitHub is blocked", which need different workarounds.

---

## When a company redirects GitHub

If `npx skills add owner/repo` has been pointed at an internal GitHub Enterprise server, the redirection lives in one of a few places. For the current `skills` CLI the most important one is surprisingly simple — `GH_HOST`:

```bash
echo $GH_HOST
printenv GH_HOST
```

It can also be done through git URL rewriting, configured in `/etc/gitconfig` or `~/.gitconfig`:

```text
url.https://gitmirror.company.com/.insteadof https://github.com/
```

And check whether a proxy is in play:

```bash
echo $HTTPS_PROXY
echo $HTTP_PROXY
echo $NO_PROXY
```

---

[← My Writings](README.md) | [← Home](../README.md)
