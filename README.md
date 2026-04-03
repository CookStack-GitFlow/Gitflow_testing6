# Git & GitHub Convention Guide — Team CookStack

> **Above and Beyond School** · Course: Git / GitHub

## Team Members

| Name | Role |
|------|------|
| Choub Ponhvorntey | Team Leader |
| Oeun Chansokhan | Team Member |
| Phat Lymeng | Team Member |
| Lim Hongseng | Team Member |
| Rin Christpor | Team Member |
| Chhorn Sann | Team Member |

---

## Overview

This repository follows the **GitFlow** branching strategy — a structured workflow that organizes development into well-defined branch types, each with a specific role. This convention ensures that the main codebase stays stable, new features are developed safely in isolation, and bugs are resolved in a structured way before reaching production.

---

## Table of Contents

1. [What is Git?](#what-is-git)
2. [What is Git Flow?](#what-is-git-flow)
3. [Branch Overview](#branch-overview)
4. [Full Workflow — Step by Step](#full-workflow--step-by-step)
5. [Best Practices](#best-practices)

---

## What is Git?

Git is a **distributed version control system** that allows developers to track every change made to their code over time. Created by Linus Torvalds in 2005, Git has become the most widely used version control tool in the software industry.

Rather than simply saving file differences, Git works as a **series of snapshots** — every time you commit, Git captures the full state of your project at that moment.

**Why use Git?**
- Track every change — see who changed what and when
- Revert mistakes — roll back to any previous state
- Collaborate safely — multiple people can work simultaneously
- Branching — develop new features without touching the stable codebase

### Initial Setup

```bash
# Initialize a Git repository in your project folder
git init

# Set your global identity (run once on your machine)
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Initialize Git Flow (sets up main and develop branches)
git flow init
```

> `git flow init` automatically creates both the `main` and `develop` branches and configures all branch prefixes.

---

## What is Git Flow?

Git Flow is a **branching model and workflow strategy** built on top of Git. It defines a strict set of branch types — each serving a specific purpose — so that teams can work in parallel without stepping on each other's work.

**Why Branching Matters**

Working directly on `main` is risky — a mistake could break the entire project for everyone. By branching, each team member gets an independent copy of the code to work on. Once the work is tested and reviewed, it is safely merged back.

| Benefit | Description |
|---------|-------------|
| Code Safety | The main codebase is never touched during development |
| Parallel Work | Multiple team members can work simultaneously without conflicts |
| Code Review | Pull Requests ensure every change is reviewed before merging |
| Clear History | Each branch and tag tells a clear story of the project's evolution |
| Easy Rollback | Tags and commits make it simple to revert to any previous release |

---

## Branch Overview

CookStack uses **six branch types** following the GitFlow model. Each branch has a clearly defined purpose, a specific set of rules, and a defined lifecycle.

| Branch | Created From | Merges Into | Purpose |
|--------|-------------|-------------|---------|
| `main` | — | — | Stable, production-ready code only |
| `develop` | `main` | — | Integration branch for ongoing work |
| `feature/*` | `develop` | `develop` (via PR) | New features or changes |
| `release/*` | `develop` | `main` + `develop` (PR) | Final testing & release prep |
| `bugfix/*` | `release/*` | `release/*` (via PR) | Fixing bugs found during QA |
| `hotfix/*` | `main` | `main` + `develop` (PR) | Urgent fixes to production code |

### Branch Flow Diagram

```
main     ●────────────────────────────────────────●────────●
          \                                       ↑    ↑   ↑
hotfix     \                               ●──────    /   /
            \                             ↑          /   /
bugfix       \                    ●                 /   /
              \                   ↑  ↓             /   /
release        \         ●────────●────────────────   /
                \        ↑                 ↓          /
develop  ●────────────────●──────────────────●───────●
                  ↓      ↑
feature           ●──────●
```

### Main Branch

The `main` branch is the single source of truth for stable, deployed code. Only fully tested and approved code lives here.

- Never commit directly — all changes arrive via PR from `release` or `hotfix` branches
- Every merge into `main` must be tagged with a version number (e.g. `v1.0.0`)
- Always deployable — every commit represents a stable project state
- **Protected branch** — only the team leader merges into `main`

### Develop Branch

The `develop` branch is the active integration branch where all ongoing work comes together.

- All feature branches start from `develop`
- Always pull the latest `develop` before starting any new feature
- Completed features merge back into `develop` via GitHub Pull Request
- When ready for a release, `develop` is branched into `release/*`

### Feature Branch

A feature branch is created for every new piece of work — a new page, component, or any change.

- Always created from: `develop`
- Named descriptively: `feature/navbar`, `feature/login-page`, etc.
- Published to GitHub so the team leader can review via Pull Request
- Deleted locally and remotely after the PR is merged
- Keep branches short-lived — one feature or task per branch

### Release Branch

A release branch is created by the team leader when `develop` contains all features planned for the next release. Used for final QA testing and release prep. **No new features** should be added here.

- Created from: `develop` (by the team leader only)
- Named with a version: `release/1.0.0`, `release/2.0.0`, etc.
- Used for QA testing — team members track it and test thoroughly

### Bugfix Branch

Created during the release phase when a bug is discovered during QA. Branches off the active `release/*` branch.

- Created from: `release/*` (the active release branch)
- Named descriptively: `bugfix/fix-nav-overlap`, `bugfix/fix-form-submit`, etc.
- Merged back into `release/*` via a Pull Request
- Deleted after the PR is merged
- Repeat the bugfix cycle for each bug found during QA

### Hotfix Branch

Used to fix **urgent bugs** already affecting the production system. Two PRs are required: one to `main` and one to `develop`.

- Created from: `main` (only by the team leader)
- Named with the patch version: `hotfix/1.0.1`, `hotfix/2.0.1`, etc.
- Merged into **BOTH** `main` and `develop` — skipping `develop` causes future regressions
- Tagged with a patch version number on `main` (e.g. `v1.0.1`) after merging
- Even in an emergency, a PR review by the leader is still required

---

## Full Workflow — Step by Step

### Phase 0 — Initial Setup (One Time Only)

Each team member performs this setup once when joining the project:

```bash
# Step 0.1 — Initialize Git Flow in the repository
# (Team leader runs this once when creating the project)
git flow init

# Use the following defaults when prompted:
#   Production  branch: main
#   Development branch: develop
#   Feature  prefix:    feature/
#   Release  prefix:    release/
#   Hotfix   prefix:    hotfix/
#   Bugfix   prefix:    bugfix/
```

---

### Phase 1 — Feature Workflow

Every team member follows these steps when working on a new task or feature:

**Step 1: Sync with remote develop**
```bash
git checkout develop
git pull origin develop
```

**Step 2: Start your feature branch**
```bash
# Replace 'navbar' with your actual feature name
git flow feature start navbar
git flow feature publish navbar
```
> Publishing the branch immediately makes it visible on GitHub so the team leader can track your progress.

**Step 3: Do your work and commit**
```bash
git add .
git commit -m "feat: add navbar"
git push
```
> Use the prefix `feat:` for features, `fix:` for bug fixes, `chore:` for maintenance tasks.

**Step 4: Open a Pull Request on GitHub**

Go to GitHub and open a PR from your feature branch into `develop`. The team leader reviews and merges it. **Never merge your own PR.**

`feature/navbar` → `develop` (Leader merges)

**Step 5: Clean up after merge**
```bash
git checkout develop
git pull origin develop
git fetch --prune
git branch -D feature/navbar
```

---

### Phase 2 — Release Workflow

When `develop` contains all planned features, the team leader starts a release:

**Step 1: Start the release (Leader)**
```bash
git checkout develop
git pull origin develop
git flow release start 1.0.0
git flow release publish 1.0.0
```

**Step 2: Team members track the release**
```bash
git fetch
git flow release track 1.0.0
```

**Step 3: Perform Quality Assurance Testing**

Every team member tests the project thoroughly. Document any bugs found as GitHub Issues and assign them.

---

### Phase 3 — Bugfix Workflow (During Release)

If a bug is found during QA testing on the release branch:

**Step 1: Create a bugfix branch from release**
```bash
git checkout release/1.0.0
git pull origin release/1.0.0
git checkout -b bugfix/fix-nav-overlap
git push -u origin bugfix/fix-nav-overlap
```

**Step 2: Fix the bug and push**
```bash
git add .
git commit -m "fix: nav overlap on mobile"
git push
```

**Step 3: Open a PR: `bugfix` → `release`**

On GitHub: `bugfix/fix-nav-overlap` → `release/1.0` (Leader merges)

**Step 4: Clean up**
```bash
git checkout release/1.0.0
git pull origin release/1.0.0
git fetch --prune
git branch -D bugfix/fix-nav-overlap
```

> Repeat steps 1–4 for every bug found. Only move forward when all bugs are resolved.

---

### Phase 4 — Finish the Release

Once all QA is complete and all bugs are fixed, the team leader finishes the release:

**Step 1: Create both PRs on GitHub (IMPORTANT — do both before merging)**
- PR 1: `release/1.0.0` → `main`
- PR 2: `release/1.0.0` → `develop`

**Step 2: Merge in the correct order**
- First merge: `release/1.0.0` → `main`
- Then merge: `release/1.0.0` → `develop`

**Step 3: Create a version tag (Leader)**
```bash
git checkout main
git pull origin main
git tag -a v1.0.0 -m "Release 1.0.0"
git push origin v1.0.0
```

**Step 4: Clean up the release branch**
```bash
git checkout develop
git pull origin develop
git fetch --prune
git branch -D release/1.0.0
```

---

### Phase 5 — Hotfix Workflow

> A hotfix can be started by any team member, but must follow team rules and be reviewed and merged by the team leader.

**Step 1: Start the hotfix branch**
```bash
git checkout main
git pull origin main
git flow hotfix start 1.0.1
git push origin hotfix/1.0.1
```

**Step 2: Fix the critical bug**
```bash
git add .
git commit -m "fix: critical production bug"
git push
```

**Step 3: Open PR: `hotfix` → `main`**

On GitHub: `hotfix/1.0.1` → `main` (Leader merges)

**Step 4: Open PR: `hotfix` → `develop` (VERY IMPORTANT)**

On GitHub: `hotfix/1.0.1` → `develop` (Leader merges)

This step is critical. Merging hotfix into `develop` ensures the bug fix is included in all future development and will not reappear in the next release.

**Step 5: Create a patch version tag (Leader)**
```bash
git checkout main
git pull origin main
git tag -a v1.0.1 -m "Hotfix 1.0.1"
git push origin v1.0.1
```

**Step 6: Clean up the hotfix branch**
```bash
git branch -d hotfix/1.0.1
git fetch --prune
```

> After the hotfix is complete, verify the fix is live in production and communicate the update to the team.

---

### Final Branch Flow Summary

| Flow | Direction | Method |
|------|-----------|--------|
| Feature work | `feature/*` → `develop` | GitHub Pull Request |
| Release prep | `develop` → `release/*` | `git flow release start` |
| Bug fixes | `bugfix/*` → `release/*` | GitHub Pull Request |
| Release to prod | `release/*` → `main` | GitHub Pull Request |
| Sync fixes back | `release/*` → `develop` | GitHub Pull Request |
| Version tagging | tag on `main` after release | `git tag -a vX.X.X` |
| Hotfix to prod | `hotfix/*` → `main` | GitHub Pull Request |
| Hotfix to dev | `hotfix/*` → `develop` | GitHub Pull Request (critical) |
| Patch tag | tag on `main` after hotfix | `git tag -a vX.X.X` |

---

## Best Practices

### For All Team Members
- Always **pull before you start** — run `git pull origin develop` before creating any branch
- Write **clear commit messages** — use prefixes: `feat:`, `fix:`, `chore:`, `docs:`
- Never work directly on `main` or `develop` — always use a branch
- Test your changes locally before pushing
- Delete branches after they are merged to keep the repository clean

### For Feature Branches
- Keep feature branches short-lived — days, not weeks
- One task or feature per branch — avoid mixing unrelated changes
- Use descriptive names: `feature/login-page`, `feature/contact-form`
- Publish your branch early with `git flow feature publish` so the leader can track it

### For Pull Requests
- Write a clear PR description — what changed and why
- Reference the related GitHub Issue number in the PR description
- **Never merge your own PR** — always wait for the leader's approval
- Respond promptly to review comments
- Resolve all requested changes before asking for re-review

### For Hotfixes
- Only create a hotfix for critical production bugs — not for minor issues
- Always merge hotfix into **both** `main` and `develop` — skipping `develop` causes future regressions
- Even in an emergency, a PR review is still required before merging
- Tag the `main` branch with a patch version immediately after the hotfix merge
- Communicate the hotfix to the whole team after it is deployed

### Version Numbering (Semantic Versioning)

Use the format: `MAJOR.MINOR.PATCH` (e.g. `v1.0.0`)

| Rule | When to increment |
|------|------------------|
| **MAJOR** | Large breaking changes |
| **MINOR** | New features added |
| **PATCH** | Bug fixes and hotfixes only |

Always tag the `main` branch after every release and hotfix merge.

**Release Tags (features):** `v1.0.0`, `v1.1.0`, `v1.2.0`, `v2.0.0`

**Hotfix Tags (bug fixes):** `v1.0.1`, `v1.0.2`, `v1.1.1`, `v2.0.1`

---

## Conclusion

Git Flow gives Team CookStack a clear framework for collaboration. By defining six branch types — `main`, `develop`, `feature`, `release`, `bugfix`, and `hotfix` — every team member knows exactly where to work, how to contribute, and how changes move from development to production.

Following this convention consistently means the team can work in parallel without breaking each other's work, every change is reviewed before it reaches the shared codebase, and the project history is always clean, organized, and easy to understand.

---

*This document was prepared by Team CookStack · Above and Beyond School · Git/GitHub Course*
