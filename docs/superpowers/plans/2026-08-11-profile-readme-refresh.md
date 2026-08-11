# Profile README Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the broken/fragile "GitHub Stats" widget section in `README.md` with a hand-written "Highlights" section covering five private-repo projects.

**Architecture:** Single-file Markdown edit. No build step, no test framework — verification is visual (render locally + confirm on GitHub after push, confirm no broken image links).

**Tech Stack:** Markdown, shields.io static badges.

## Global Constraints

- Highlight project names are plain bold text, NOT hyperlinked (the projects are private repos with no public page to link to — see spec, "Add" section).
- New badges must use `style=for-the-badge` to match the existing Tech Stack section's visual style (see spec, "Unchanged" section — Tech Stack is not being redesigned, so new badges must fit it).
- Every new badge URL must be verified to return HTTP 200 before being written into `README.md` (this whole project exists because of broken embeds — don't introduce a new one).

---

### Task 1: Replace GitHub Stats section with Highlights section

**Files:**
- Modify: `/home/esus/Repos/MichalJes/README.md:11-15`

**Interfaces:** None — this is a single self-contained file edit, no other tasks depend on it.

- [ ] **Step 1: Verify all new badge URLs return HTTP 200**

The Highlights section introduces four badges not already present in the Tech Stack section (Python, Shell Script, C, C++, Docker, Raspberry Pi are already verified-working and reused as-is). Run:

```bash
for url in \
  "https://img.shields.io/badge/ansible-%23EE0000.svg?style=for-the-badge&logo=ansible&logoColor=white" \
  "https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge&logo=typescript&logoColor=white" \
  "https://img.shields.io/badge/MikroTik-293239?style=for-the-badge" \
  "https://img.shields.io/badge/KiCad-314CB0?style=for-the-badge" \
  ; do
  code=$(curl -s -o /dev/null -w "%{http_code}" --max-time 10 "$url")
  echo "$code  $url"
done
```

Expected: all four lines show `200`. If any line shows a non-200 code, stop and pick a
replacement badge (drop the `logo=` param and use a plain color badge like the MikroTik/KiCad
ones above, which don't depend on an external icon existing) before proceeding.

- [ ] **Step 2: Replace lines 11-15 of README.md**

Current content (lines 11-15):

```markdown
# 📊 GitHub Stats:
![](https://github-readme-stats-sigma-five.vercel.app/api?username=MichalJes&theme=react&hide_border=true&include_all_commits=false&count_private=false)<br/>
![](https://github-readme-streak-stats.herokuapp.com/?user=MichalJes&theme=react&hide_border=true)<br/>
![](https://github-readme-stats-sigma-five.vercel.app/api/top-langs/?username=MichalJes&theme=react&hide_border=true&include_all_commits=false&count_private=false&layout=compact)
```

Replace with:

```markdown
# 🚀 Highlights:
**event-production-stack** — Live-event AV/network system design: MikroTik routing, Ansible-managed rack, camera/audio pipeline for tournament broadcasts.
![Ansible](https://img.shields.io/badge/ansible-%23EE0000.svg?style=for-the-badge&logo=ansible&logoColor=white) ![MikroTik](https://img.shields.io/badge/MikroTik-293239?style=for-the-badge)

**QuestTourDashboard** — Live tournament tooling for a real Disney Lorcana team championship: bracket projection, roster/check-in, OBS overlays.
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) ![TypeScript](https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge&logo=typescript&logoColor=white)

**audio-router** — Raspberry Pi appliance routing judge/player audio for a tournament intercom, with a web dashboard.
![Shell Script](https://img.shields.io/badge/shell_script-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white) ![Raspberry Pi](https://img.shields.io/badge/-RaspberryPi-C51A4A?style=for-the-badge&logo=Raspberry-Pi)

**MeasuringCard** — Master's thesis: CAN bus EMI measurement card for a Formula Student electric race car (STM32, KiCad, MATLAB).
![C](https://img.shields.io/badge/c-%2300599C.svg?style=for-the-badge&logo=c&logoColor=white) ![KiCad](https://img.shields.io/badge/KiCad-314CB0?style=for-the-badge)

**TrueNAS_ShelfarrProwlarrBridge** — Custom acquisition-provider bridge working around a broken upstream query builder, running in my homelab.
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
```

Note: no trailing blank line needed after — keep the file ending with a single newline as it
currently does.

- [ ] **Step 3: Visually verify the rendered result**

Run:

```bash
cd /home/esus/Repos/MichalJes && gh api repos/MichalJes/MichalJes/readme --jq '.content' | base64 -d
```

This won't reflect the local edit until pushed — instead, preview locally with `glow` if
installed, or just re-read the file:

```bash
cat /home/esus/Repos/MichalJes/README.md
```

Confirm: the "📊 GitHub Stats" heading and all three old widget `![]()` embeds are gone; the
new "🚀 Highlights" section with five bolded project names, descriptions, and badge rows is
present; the "💫 About Me", "🌐 Socials", and "💻 Tech Stack" sections are byte-for-byte
unchanged.

- [ ] **Step 4: Commit**

```bash
cd /home/esus/Repos/MichalJes
git add README.md
git commit -m "$(cat <<'EOF'
Replace broken GitHub Stats widgets with a curated Highlights section

github-readme-stats (self-hosted Vercel fork) and github-readme-streak-stats
(Heroku free tier, discontinued 2022) are both unreliable/broken and only
reflect public-repo activity anyway, which undersells actual output since
recent work lives in private repos. Replaced with five hand-written project
highlights.

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
EOF
)"
git log --oneline -3
```

Expected: commit succeeds, `git log` shows the new commit on top.

- [ ] **Step 5: Push and confirm live render**

```bash
cd /home/esus/Repos/MichalJes && git push
```

Then open `https://github.com/MichalJes/MichalJes` in a browser (or run
`gh browse -R MichalJes/MichalJes`) and visually confirm the Highlights section renders
correctly with no broken image icons.
