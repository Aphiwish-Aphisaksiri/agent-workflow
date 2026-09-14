# Tools & External Services

How agents should interact with external tools and services in this repo.
**For any operation against an external tool or service — source control host,
CI, code quality, issue tracker, MCP server — consult this file first.**

Each entry lists: **purpose → preferred access method → fallback → gotchas.**

> **Credentials:** No secret values belong in this file, or anywhere else in the
> repo. See **Secrets & credentials** below for where they live instead.

<!-- Fill in the identifiers this repo needs. The point of this table is that an
agent never has to guess a project key, slug, or branch name. -->

**This repo's identifiers at a glance** *(verified <date>)*:

| | value |
|---|---|
| CI pipeline / project | <!-- e.g. my-service --> |
| Code-quality project key | <!-- e.g. my-service — watch for case sensitivity --> |
| Primary branch | <!-- e.g. main / master --> |
| PR base branch | <!-- e.g. main — may differ from the primary branch --> |

---

## Source control host (GitHub / GitLab / etc.)

- **Purpose:** Pull requests, reviews and review threads, issues, repo queries.
- **Access:** <!-- e.g. the `gh` CLI (`gh pr`, `gh api`, `gh api graphql`). Prefer
  a native CLI over an MCP server when both exist — it is scriptable and stable. -->
- **Fallback:** <!-- e.g. the web UI for anything the CLI cannot do -->
- **Gotchas:**
  <!-- - Run the CLI non-interactively so a pager cannot hijack the terminal
       (e.g. `GH_PAGER=cat`, redirect to a file, pass `< /dev/null` for writes).
     - Record the exact API shape for operations you had to look up once —
       replying to a review thread, resolving a thread, etc. -->

---

## CI

- **Purpose:** Check build/pipeline status and read job logs.
- **Access:** <!-- API endpoint + auth method, and where the token comes from
  (name the secret store entry, never the value) -->
- **Fallback:** <!-- e.g. the CI web UI -->
- **Gotchas:**
  <!-- - Note whether the token can *write* (trigger/retry) or is read-only.
     - Job logs can be enormous — grep or tail them, never dump them into context.
     - Note which other checks run inside this pipeline, so a failure here can be
       traced to its real source. -->

---

## Code quality / static analysis

- **Purpose:** Read the quality gate and analysis findings for a PR.
- **Access:** <!-- host, project key, auth method, token source -->
- **Fallback:** <!-- e.g. the dashboard UI -->
- **Gotchas:**
  <!-- - Record what the gate actually measures. Chasing a metric the gate does
       not check is a common waste of time.
     - Note where the project key is configured (or that it is *not* in a
       properties file), so nobody hunts for it in the wrong place. -->

---

## Issue tracker / knowledge base

- **Purpose:** Read tickets and documentation pages for planning and cross-referencing.
- **Access:** <!-- e.g. an MCP server, or a CLI/API -->
- **Site / IDs:** <!-- base URL and any required workspace/cloud identifier -->
- **Write policy:** <!-- Recommended default: read-only. State plainly whether the
  agent may create, update, transition, or comment without being asked. If the
  credentials technically allow writes, say so and reserve them for explicit requests. -->
- **Gotchas:**

---

## Secrets & credentials

**Never put a secret in any file in this repo — including files that are
git-ignored.** Two reasons, and the second is the one people miss:

1. Ignore rules are easy to lose. If `.agents/` is excluded via
   `.git/info/exclude`, that is local and unversioned — "untracked" is not a
   durable guarantee.
2. **Agents read context files on every interaction.** A token pasted here is
   re-transmitted every turn and lands in session logs.

**Store secrets outside the repo** — an OS keychain, a secrets manager, or your
shell environment. Prefer a store the agent sandbox *cannot* read, which makes
it a real trust boundary rather than a convention.

<!-- Record where this project's credentials live and what each is for — names
and purposes only, never values. Example:

| Store entry | What |
|---|---|
| `my-ci-api`   | CI API token, bearer auth, read-only |
| `my-sonar-api`| Code-quality token, basic auth, empty password |
-->

<!-- Document how to read and rotate a value, so nobody invents their own way. -->

**Gotchas — these have caused real leaks:**
- **Never `echo` a token.** Pipe it straight into the consuming command.
  Terminal scrollback is captured in session logs.
- **Never write an "is it set?" probe that can fall through to the value.** In
  a POSIX shell, `${tok:+yes}${tok:-NO}` prints the **token** when it is set,
  because `:-` only substitutes when the variable is *empty*. Use
  `[[ -n "$tok" ]] && echo yes || echo NO`.
- **Redaction patterns must cover the shape of your secrets.** A mask keyed on
  long unbroken alphanumeric runs silently misses hyphenated secrets such as
  UUIDs, because no single run is long enough. Safer still: print `file:line`
  and never the matched text.
- Any scan output or scratch script that touched a secret becomes a secret
  carrier itself — delete it once triaged.
- A secret store protects against at-rest and git/agent leakage, **not** against
  local code running as your user.

---

## Command reference

<!-- If you wrap these tools in shell helpers, aliases, or scripts, list them
here with their signatures so an agent can call them directly instead of
reconstructing raw API calls.

Recommendation: take the target project/pipeline as an **explicit argument**
rather than hardcoding per-repo wrappers. A command then cannot silently hit the
wrong project, and there is nothing to keep in sync when a repo is added.

| Helper | Purpose |
|---|---|
| | |

Also worth recording: a typical end-to-end flow, e.g. how to get from "CI is
red" to the failing job's log in three commands.
-->

---

## MCP servers

- Prefer a native CLI over an MCP server when both exist.
- <!-- List the MCP servers this project expects to be enabled, and any that are
  deliberately disabled — with the reason. Editors cap the number of active
  tools, so two large servers may not be able to run at once. An agent that
  knows a server is off will stop trying to use it. -->

---

## Cross-repo notes

<!-- If credentials or helpers are machine-wide and shared with sibling repos,
say so, and tabulate the identifiers that differ between them — case-sensitive
project keys and differing base branches are a common source of confusing 404s.

If this file is duplicated in another repo, note that the duplication is
deliberate (a file read every interaction should be self-contained) and that
**shared sections must be updated in both copies** when changed. -->
