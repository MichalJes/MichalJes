# GitHub Profile README Refresh — Design

## Problem

The profile README's "📊 GitHub Stats" section depends on three third-party widgets:

- `github-readme-stats` (self-hosted fork on Vercel) — currently broken, returning an error
  card ("Maximum retries exceeded... add PAT_1"). This is a recurring failure: the same
  `PAT_1` error was already fixed once in January 2023 and has since broken again, because
  the GitHub PAT it depends on needs periodic renewal. The upstream project is effectively
  unmaintained.
- `github-readme-streak-stats` (Heroku) — still responding today, but running on Heroku's
  free dyno tier, which Heroku discontinued in November 2022. Alive on borrowed time.
- `top-langs` card — same broken Vercel deployment as the first widget.

Separately, these stats only reflect **public** repo activity. Most of the account's
strongest recent work (QuestTourDashboard, event-production-stack, the MeasuringCard
thesis, audio-router, various TrueNAS apps) lives in **private** repos, so even a working
stats widget would understate actual output.

## Goals

- Eliminate the recurring maintenance burden of fragile third-party widgets.
- Give the profile a portfolio function (recruiter/professional visibility) alongside its
  existing personal-expression role, without exposing private repos publicly.

## Non-goals

- Making any private repos public.
- Redesigning the intro/bio or tech-stack sections (both are current and unaffected).

## Design

### Remove

The entire "📊 GitHub Stats" section — all three widgets.

### Add

A new "🚀 Highlights" section, placed between "💻 Tech Stack" and where "📊 GitHub Stats"
used to be. Format: compact bullet list — bold project name, one-line description, small
tech-badge row underneath each entry (matches the existing badge-driven visual style of the
rest of the README). Chosen over a table (renders poorly on narrow/mobile GitHub views) and
collapsible `<details>` blocks (added complexity not needed here) after comparing rendered
mockups of all three.

Since the featured projects are private repos, their names are **not** hyperlinked (no
public page exists to link to — a link would just 404/access-denied for visitors). Plain
bold text + description instead.

Five highlights, chosen to span infra, embedded/hardware, shipped software with real users,
and academic work:

1. **event-production-stack** — live-event AV/network system design (Ansible, MikroTik)
2. **QuestTourDashboard** — live tournament tooling used at a real Disney Lorcana
   championship (Python, TypeScript)
3. **audio-router** — Raspberry Pi tournament-intercom appliance with a web dashboard
   (Shell, Raspberry Pi)
4. **MeasuringCard** — master's thesis: CAN bus EMI measurement card for a Formula Student
   EV (C, KiCad)
5. **TrueNAS_ShelfarrProwlarrBridge** — homelab automation bridging two apps around a
   broken upstream query builder (Python, Docker)

### Unchanged

- Intro/bio ("💫 About Me")
- Socials (LinkedIn, X)
- Tech Stack badge wall

## Testing / verification

This is a single static Markdown file with no build step. Verification is visual: render
the updated `README.md` (e.g. via `gh repo view` preview or GitHub's own page after push)
and confirm the Highlights section renders as intended and no broken image links remain.
